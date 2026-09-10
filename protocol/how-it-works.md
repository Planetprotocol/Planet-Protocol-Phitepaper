# How It Works

Planet Protocol operates through a **batch-based financing lifecycle** that connects DeFi capital providers with agricultural producers in Southeast Asia. Each batch represents a single, self-contained financing operation with a defined timeline, asset type, and return profile.

***

## The Batch Lifecycle

### Phase 1 — Origination

An agricultural originator — typically a cooperative, aggregator, or farm operator — submits a financing request to Planet Labs. The request specifies:

* Crop type, geography, and production timeline
* Financing amount required (denominated in USDC)
* Expected return and repayment schedule
* Milestone structure (typically aligned with planting, growth, and harvest cycles)

Planet Labs performs due diligence through the [5-layer verification architecture](verification.md), assessing the originator's track record, operational capacity, and the viability of the underlying agricultural operation.

### Phase 2 — Batch Deployment

Upon approval, Planet Labs deploys a new batch on-chain via the **BatchFactory** contract. This single transaction atomically creates:

* A new **PlanetRWA1155** token contract representing fractional participation in the batch
* A linked **PrimarySale** contract for token distribution
* A dedicated **EscrowVault** to hold and release funds according to the milestone schedule
* A linked **ClaimVault** for returns distribution upon completion

Each batch is a completely independent on-chain entity. There is no cross-collateralization or shared state between batches.

### Phase 3 — Primary Sale

The batch enters a funding window during which DeFi participants can purchase ERC-1155 RWA tokens through the PrimarySale contract. Key mechanics:

* **Denomination:** All transactions are in USDC
* **Fractional Access:** Each token represents an equal fractional share of the batch
* **Fixed Price:** Token price is set at batch creation and does not change during the sale
* **Minting Fee:** Each purchase is split in one transaction — the fee goes to the Planet Labs treasury and the remainder goes straight into the EscrowVault. The rate is set per batch and capped at 3%. See [Unit Economics](../economics/unit-economics.md#minting-fee-and-batch-shortfall) for the resulting batch shortfall and [Business Model](../economics/business-model.md) for the full fee structure
* **Share Cap, No Minimum:** The batch has a maximum number of shares and a deadline. There is **no minimum raise** — a batch can be finalized and proceed at whatever amount was actually sold

An under-subscribed batch does not refund automatically. Unwinding one requires an operator to mark the escrow failed, which opens a 180-day refund window for the net amount still held in escrow. See [Unit Economics](../economics/unit-economics.md#there-is-no-minimum-raise).

### Phase 4 — Milestone Execution

Once fully funded, the EscrowVault transitions through a series of milestone states:

```
Funded → Milestone 1 → Milestone 2 → Milestone 3
```

At each milestone:

1. The originator submits evidence of milestone completion (e.g., planting confirmation, growth inspection, harvest documentation)
2. Planet Labs verifies the evidence through its verification process
3. Upon confirmation, the smart contract releases the corresponding tranche of funds to the originator

This staged release mechanism ensures that capital is disbursed only as real-world progress is demonstrated. See [EscrowVault](escrowvault.md) for the full state machine specification.

### Phase 5 — Returns Distribution (Burn-to-Claim)

Upon successful completion of all milestones:

1. The originator deposits the agreed return amount (principal + yield) into the **ClaimVault**
2. The EscrowVault reaches its terminal state (Milestone 3 complete)
3. An operator opens the claim window, which snapshots the token supply and fixes the per-share payout. The window runs for **180 days**
4. RWA token holders burn their tokens through the ClaimVault to claim their proportional share of the returns. Unclaimed funds are swept to a platform-configured address after the window closes

The burn-to-claim mechanism ensures a clean, deterministic settlement: one token equals one claim, and burning it extinguishes the obligation. See [Burn-to-Claim](burn-to-claim.md) for details.

***

## Failure Handling

If a batch fails between funding and the final milestone — due to crop failure, originator default, or force majeure — the EscrowVault transitions to a **Failed** state. In this scenario:

* Any unreleased funds remaining in the EscrowVault are made available for participant withdrawal
* Funds already disbursed to the originator in prior milestones are subject to off-chain recovery processes
* The originator's staked $PNT collateral (see [Originator Security](../economics/originator-security.md)) may be slashed as a partial loss mitigation mechanism

A default **after** the final milestone works differently. By that point the escrow has released every tranche and holds nothing, so there is no on-chain refund path; recovery depends on the SPV legal structure and originator staking instead. This is set out in [Default at the Settlement Stage](escrowvault.md#default-at-the-settlement-stage).

Planet Protocol does not guarantee principal return. Each batch carries independent risk, and participants should evaluate each batch on its own merits.

***

## Participant Roles

| Role                 | Description                                                                                  |
| -------------------- | -------------------------------------------------------------------------------------------- |
| **DeFi Participant** | Purchases RWA tokens to fund batches; earns yield upon successful completion                 |
| **Originator**       | Agricultural producer or aggregator seeking financing; must pass verification and stake $PNT |
| **Planet Labs**      | Platform operator; deploys batches, manages verification, operates the frontend              |
| **Smart Contracts**  | Autonomous on-chain enforcement of fund flows, state transitions, and claims                 |

***

## Key Properties

* **Non-custodial for batch capital:** Participant capital sits in smart contracts, not in Planet Labs' custody. The Minting Fee is a separate matter — it is transferred to the Planet Labs treasury at the moment of purchase.
* **Deterministic:** Fund releases follow predefined rules. No manual override of contract state.
* **Isolated:** Each batch is economically independent. One batch's failure does not cascade to others.
* **Transparent:** All state transitions, fund movements, and claims are visible on Ethereum.
