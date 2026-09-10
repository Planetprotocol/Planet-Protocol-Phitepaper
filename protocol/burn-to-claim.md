# Burn-to-Claim

The **Burn-to-Claim** mechanism is Planet Protocol's returns distribution system. It is implemented through the **ClaimVault** contract and provides a deterministic, tamper-proof method for token holders to redeem their share of batch returns.

***

## Mechanism Overview

After a batch completes all three milestones, repayment (principal + yield) is deposited into the ClaimVault by the batch's SPV, which holds the depositor role. An operator then opens the claim window, which snapshots the token supply and fixes the per-share payout. Token holders **burn** their ERC-1155 RWA tokens to claim their proportional share. The window stays open for **180 days**.

The core principle is simple: **one token burned = one proportional claim settled**. Burning extinguishes the token permanently, ensuring that each claim can only be exercised once.

***

## Claim Flow

```
   Originator                 ClaimVault               Token Holder
       │                          │                          │
       │  deposit returns (USDC)  │                          │
       │─────────────────────────►│                          │
       │                          │                          │
       │                          │   burn RWA tokens        │
       │                          │◄─────────────────────────│
       │                          │                          │
       │                          │   transfer USDC          │
       │                          │─────────────────────────►│
       │                          │                          │
```

### Step-by-Step

1. **Return Deposit** — The originator transfers the full return amount (principal + yield) in USDC to the ClaimVault contract.
2. **Claim Initiation** — A token holder calls the `claim()` function on the ClaimVault, specifying the number of tokens to burn.
3. **Token Burn** — The ClaimVault calls `burn()` on the PlanetRWA1155 contract, permanently destroying the specified tokens from the holder's balance.
4. **USDC Transfer** — The ClaimVault calculates and transfers the proportional USDC amount to the holder's wallet.

***

## Claim Calculation

The amount each holder receives is calculated as:

```
claim_amount = (tokens_burned / snapshot_supply) × total_deposited
```

Where:

* `tokens_burned` = number of RWA tokens the holder is burning in this transaction
* `snapshot_supply` = token supply recorded at the moment the claim window opened
* `total_deposited` = total USDC deposited into the ClaimVault before the window opened

**Example (illustrative only — figures are hypothetical and used solely to demonstrate the calculation, not a return projection):**

* Total token supply: 10,000
* Total vault balance: 102,000 USDC — a hypothetical repayment figure. Actual repayment terms are set per batch and disclosed at batch creation; see [Unit Economics](../economics/unit-economics.md)
* Holder burns 500 tokens
* Claim amount: (500 / 10,000) × 102,000 = **5,100 USDC**

***

## Design Properties

### No Double-Claiming

The burn mechanism inherently prevents double-claiming. Once tokens are burned, they are permanently removed from the holder's balance and from total supply. There is no way to re-mint or recover burned tokens.

### Partial Claims

Holders are not required to burn all their tokens at once. They can execute multiple partial claims within the window. Each payout is computed against the snapshot taken when the window opened, so the rate is identical across every claim.

### Atomic Execution

Each claim transaction is atomic — the burn and the USDC transfer occur in the same transaction. If either operation fails, the entire transaction reverts. There is no intermediate state where tokens are burned but funds are not transferred.

### The Rate Is Fixed Before Anyone Can Claim

Opening the claim window snapshots the token supply and fixes the payout per share. From that point the contract rejects further deposits, so the per-share rate cannot move. Every holder redeems at the same rate regardless of when they claim, and there is no advantage to claiming early or late within the window.

One thing to understand about how the window opens: the contract requires a non-zero deposit and a locked token supply, but it does not itself check that the deposit matches the agreed repayment. Ensuring the full amount is present before opening the window is an operational commitment by Planet Labs, not a guarantee enforced by the smart contract. Holders can verify the deposited total on-chain before claiming.

### 180-Day Claim Window

Claims are open for **180 days** from the moment the window is opened. Pausing the token does not extend this deadline — the cutoff is fixed at the opening timestamp plus 180 days.

### Unclaimed Funds After the Window

Once the window closes, an operator can sweep any unclaimed balance to a receiver address configured by the factory. **Holders who do not claim within 180 days forfeit their share to that address.** See [Claim Window Risk](../project/risks.md#claim-and-refund-window-risk).

***

## Edge Cases

| Scenario                                            | Behavior                                                                                                        |
| --------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| Holder tries to claim with zero tokens              | Transaction reverts                                                                                             |
| Holder tries to burn more tokens than their balance | Transaction reverts                                                                                             |
| ClaimVault has zero balance                         | Transaction reverts (no funds to distribute)                                                                    |
| Repayment deposited in instalments                  | Permitted while the window is closed; deposits accumulate. Once the window opens, further deposits are rejected |
| All tokens burned, residual dust in vault           | Handled by integer rounding; negligible amounts may remain                                                      |

***

## Why Burn-to-Claim?

Alternative distribution mechanisms were considered and rejected:

| Approach                       | Issue                                                                                        |
| ------------------------------ | -------------------------------------------------------------------------------------------- |
| Airdrop (push-based)           | Gas cost scales linearly with number of holders; sender pays all gas                         |
| Merkle claim (snapshot)        | Requires off-chain snapshot generation; adds trust assumptions                               |
| Proportional streaming         | Over-engineered for discrete batch settlements                                               |
| **Burn-to-claim (pull-based)** | **Each holder pays their own gas; no snapshot needed; self-enforcing via token destruction** |

The burn-to-claim pattern is the most capital-efficient, trust-minimized, and gas-fair distribution method for Planet's batch settlement model.
