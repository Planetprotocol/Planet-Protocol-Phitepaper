# Smart Contracts

Planet Protocol's on-chain infrastructure consists of **five core smart contracts** that together govern the complete lifecycle of a batch financing operation. All contracts are deployed on **Ethereum mainnet**, written in **Solidity ^0.8.20**, and built on top of **OpenZeppelin v4** standard libraries.

---

## Architecture Overview

```
                    ┌──────────────┐
                    │ BatchFactory │
                    └──────┬───────┘
                           │ deploys (minimal proxy clone)
              ┌────────────┼────────────┐
              ▼            ▼            ▼
      ┌──────────────┐ ┌───────────┐ ┌────────────┐
      │ PlanetRWA1155│ │PrimarySale│ │ EscrowVault│
      └──────────────┘ └───────────┘ └─────┬──────┘
                                           │
                                    ┌──────▼──────┐
                                    │  ClaimVault  │
                                    └─────────────┘
```

Each batch deployment creates a new set of linked contract instances. The **minimal proxy clone pattern** (EIP-1167) is used to minimize gas costs — each new instance delegates to a pre-deployed implementation contract rather than redeploying the full bytecode.

---

## Contract Specifications

### 1. BatchFactory

The factory contract is the single entry point for batch creation. It orchestrates the atomic deployment of all four child contracts for each new batch.

**Responsibilities:**
- Deploys minimal proxy clones of PlanetRWA1155, PrimarySale, EscrowVault, and ClaimVault
- Links the four contracts to each other during initialization
- Stores a registry of all deployed batches
- Enforces deployment-time parameters (funding target, token supply, milestone schedule, fee rate)

**Access:** Restricted to authorized deployers (Planet Labs admin role).

### 2. PlanetRWA1155

The token contract implements the **ERC-1155** multi-token standard to represent fractional participation in a batch.

**Responsibilities:**
- Mints RWA tokens during the primary sale
- Tracks ownership and balances of fractional batch participation
- Supports burn operations for the claim process
- Stores batch-level metadata (URI, batch parameters)

**Design Rationale:** ERC-1155 was chosen over ERC-20 or ERC-721 for several reasons:
- A single contract can represent multiple token types (enabling future multi-tranche batches)
- Native batch transfer support reduces gas costs for portfolio operations
- Metadata extensibility through token URI standards

**Transferability:** The token contract does not restrict transfers. Holders can transfer RWA tokens to any address at any time, subject only to a global pause. No secondary market venue is operated by Planet Protocol, and no venue is guaranteed to exist for any batch, but transfers themselves are not blocked by the contract.

**Single token ID:** Each batch deploys its own token contract using a single ID. Multi-tranche batches are not implemented.

### 3. PrimarySale

The sale contract manages the initial distribution of RWA tokens to participants.

**Responsibilities:**
- Accepts USDC from participants and processes each purchase atomically
- On each purchase: deducts the Minting Fee and transfers it to the Planet Labs treasury, forwards the net amount to the EscrowVault, and mints the corresponding RWA tokens via PlanetRWA1155 — all in one transaction
- Enforces the sale deadline and the share cap
- Exposes `getQuote()` so a participant can see the exact gross, fee, and net amounts before buying

**No minimum raise:** The contract enforces a maximum (`capShares`) but not a minimum. A sale that closes below its target does not automatically fail or refund. Unwinding an under-subscribed batch requires an operator to mark the escrow failed.

**Admin functions:** `withdrawFunds()` allows the admin role to move any USDC held by this contract. In normal operation the balance is zero, because each purchase forwards the net amount to escrow immediately.

**Denomination:** All primary sales are denominated in USDC (ERC-20 stablecoin).

### 4. EscrowVault

The escrow contract is the core fund management mechanism. It holds all batch capital and releases funds to the originator according to a predefined milestone schedule.

**Responsibilities:**
- Receives net proceeds (post-fee) from the PrimarySale contract, purchase by purchase, as the sale progresses
- Manages a **7-state finite state machine** governing the batch lifecycle
- Releases milestone-based tranches to the originator upon authorized confirmation, in full — no further fee is deducted at this stage
- Handles failure transitions and participant refunds, with a 180-day refund window
- Sweeps any unrefunded residual to a configured receiver address once that window closes

**Configurable after deployment:** Milestone percentages are set when the batch is finalized, not at deployment. The originator's payout address can be changed by the escrow admin role via `setFarmer()`.

See [EscrowVault](EscrowVault.md) for the complete state machine specification.

### 5. ClaimVault

The claim contract manages the distribution of returns to token holders after batch completion.

**Responsibilities:**
- Receives return deposits (principal + yield) from the originator or EscrowVault
- Accepts repayment deposits from the address holding `DEPOSITOR_ROLE`, which the factory grants to the batch's SPV
- Opens the claim window when an operator calls `openClaim()`, which snapshots the token supply and fixes the per-share payout. The contract requires a non-zero deposit and a locked token supply, but does not itself verify that the deposit matches the agreed repayment — that judgement is operational
- Once the window is open, no further deposits are accepted, so the per-share rate cannot change
- Runs a **180-day** claim window from the moment `openClaim()` is called
- Sweeps any unclaimed residual to a configured receiver address once the window closes
- Processes burn-to-claim redemptions: token holders burn their RWA tokens and receive proportional USDC
- Ensures exact accounting between total deposits and total claims
- Prevents double-claiming through burn mechanics (burned tokens cannot be reused)

See [Burn-to-Claim](Burn-to-Claim.md) for the full redemption mechanism.

---

## Security Model

### Role-Based Access Control

All contracts implement OpenZeppelin's **AccessControl** module with the following roles:

| Role | Scope | Assigned To |
|---|---|---|
| `DEFAULT_ADMIN_ROLE` | Full administrative control | Planet Labs multisig (post-handover) |
| `DEPLOYER_ROLE` | Batch creation via BatchFactory | Planet Labs deployer |
| `OPERATOR_ROLE` | Milestone confirmations, state transitions | Planet Labs operator |
| `PAUSER_ROLE` | Emergency pause functionality | Planet Labs admin |

### Post-Deployment Admin Handover

After initial deployment and configuration, the deployer account renounces its elevated privileges and transfers administrative control to a designated multisig wallet. This ensures that no single key can unilaterally modify contract state or access controls after the system is live.

### Upgrade Path

Current contracts are **non-upgradeable**. The deployed logic of a batch cannot be changed. Protocol-level changes are introduced through new implementation contracts that future batches will clone from, while existing batches continue to operate under their original logic.

This is distinct from configuration: several parameters remain adjustable by role holders after deployment, including milestone percentages (set at finalization), the originator payout address, the residual receiver, and the treasury address.

---

## Dependencies

| Library | Version | Usage |
|---|---|---|
| OpenZeppelin Contracts | ^4.9 | ERC-1155, AccessControl, ReentrancyGuard, Pausable, Clones, Initializable |
| Solidity | ^0.8.20 | Language version |
| EIP-1167 | — | Minimal proxy clone pattern |

---

## Audit Status

Smart contracts have **not yet been audited** by a third-party security firm. A comprehensive audit is planned prior to mainnet launch of production batches. See [Risks](../Risks.md) for details on unaudited contract risk.
