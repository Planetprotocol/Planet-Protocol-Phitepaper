# EscrowVault

The EscrowVault is the core fund management contract in Planet Protocol. It implements a **7-state finite state machine** that governs the complete lifecycle of batch capital — from initial deposit through milestone-based disbursement to final settlement or failure recovery.

***

## State Machine

```
                 ┌────────┐
                 │ Empty  │
                 └───┬────┘
                     │ PrimarySale deposits funds
                     ▼
                 ┌────────┐
           ┌─────│Funding │─────┐
           │     └───┬────┘     │
           │         │ target   │ target not met /
           │         │ reached  │ window expired
           │         ▼         │
           │     ┌────────┐     │
           │     │ Funded │     │
           │     └───┬────┘     │
           │         │ M1       │
           │         │ confirmed│
           │         ▼         │
           │   ┌───────────┐    │
           │   │Milestone 1│    │
           │   └─────┬─────┘    │
           │         │ M2       │
           │         │ confirmed│
           │         ▼         │
           │   ┌───────────┐    │
           │   │Milestone 2│    │
           │   └─────┬─────┘    │
           │         │ M3       │
           │         │ confirmed│
           │         ▼         │
           │   ┌───────────┐    │
           │   │Milestone 3│    │
           │   └───────────┘    │
           │                    │
           │     ┌────────┐     │
           └────►│ Failed │◄────┘
                 └────────┘
```

***

## State Definitions

| State           | Description                                                                                                                                                                            |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Empty**       | Initial state. The vault has been deployed but no purchase has yet been made.                                                                                                          |
| **Funding**     | The PrimarySale is active. Each purchase forwards its net amount (purchase minus Minting Fee) into this vault immediately, so the balance accumulates as the sale progresses.          |
| **Funded**      | The sale has closed and an operator has called `finalize()`, setting the originator address and the milestone percentages. Funds are locked and awaiting first milestone confirmation. |
| **Milestone 1** | First tranche has been released to the originator (e.g., planting phase funded).                                                                                                       |
| **Milestone 2** | Second tranche released (e.g., growth/maintenance phase funded).                                                                                                                       |
| **Milestone 3** | Final tranche released. Batch lifecycle is complete. Returns are expected from the originator.                                                                                         |
| **Failed**      | Terminal failure state. Triggered by funding shortfall, originator default, or operational failure.                                                                                    |

***

## State Transitions

### Valid Transitions

| From        | To          | Trigger                                                                                                                         |
| ----------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Empty       | Funding     | First purchase deposits net proceeds into the vault                                                                             |
| Funding     | Funded      | Sale is closed and an operator calls `finalize()`. There is no minimum raise: finalization succeeds at whatever amount was sold |
| Funding     | Failed      | An operator calls `markFailed()`. This is a discretionary action, not an automatic response to a missed target                  |
| Funded      | Milestone 1 | Operator confirms M1 evidence                                                                                                   |
| Funded      | Failed      | Originator defaults or batch is cancelled                                                                                       |
| Milestone 1 | Milestone 2 | Operator confirms M2 evidence                                                                                                   |
| Milestone 1 | Failed      | Originator defaults at M1                                                                                                       |
| Milestone 2 | Milestone 3 | Operator confirms M3 evidence                                                                                                   |
| Milestone 2 | Failed      | Originator defaults at M2                                                                                                       |

### Invalid Transitions

The following transitions are explicitly prohibited by the contract:

* **Empty → Failed** — A batch with no funds cannot fail; it simply remains uninitialized.
* **Milestone 3 → Failed** — Once all milestones are complete, the escrow has discharged its function: every tranche has been released and the vault is empty. There is nothing left for the vault to refund, so Failed is not a meaningful state here. This is a deliberate scoping decision, not an oversight — see [Default at the Settlement Stage](escrowvault.md#default-at-the-settlement-stage) below.
* **Failed → any state** — Failed is a terminal state. No recovery or restart is possible.
* **Any backward transition** — States are strictly sequential. A batch cannot move from Milestone 2 back to Milestone 1.

***

## Default at the Settlement Stage

The EscrowVault's responsibility ends at Milestone 3. The originator's repayment obligation is settled afterwards, by depositing into the ClaimVault — an event this state machine does not model and cannot enforce.

If an originator completes all milestones but then fails to deposit the agreed repayment, the on-chain position is that the batch shows Milestone 3, the EscrowVault is empty, and the ClaimVault holds nothing for holders to claim. **No smart contract mechanism recovers participant capital in this scenario.** Recourse at this stage is entirely off-chain, and rests on three measures established before the batch is deployed:

1. **Originator staking** — Batch issuance requires the originator to stake platform tokens, which are at risk in a default (see [Originator Security](../economics/originator-security.md))
2. **SPV structure** — Each financing operation is backed by a special purpose vehicle (SPV) corporate entity, creating a legal claim independent of the smart contracts
3. **Vetted counterparties** — In the current phase, batches are issued with farms that Planet Labs partners with directly, rather than through open originator onboarding

Participants should understand the distinction: milestone-stage protection is enforced by code, settlement-stage protection is enforced by contract law and collateral. The latter depends on legal process and counterparty solvency, and offers neither the speed nor the certainty of the former. See [Risks](../project/risks.md).

***

## Fund Release Mechanics

Each milestone transition triggers a proportional fund release to the originator. The release schedule is defined at batch creation and encoded in the contract.

**Example 3-milestone schedule:**

| Milestone   | Tranche | Cumulative Release |
| ----------- | ------- | ------------------ |
| Milestone 1 | 30%     | 30%                |
| Milestone 2 | 35%     | 65%                |
| Milestone 3 | 35%     | 100%               |

At each release:

1. The full tranche amount is transferred to the originator's designated wallet — no fee is deducted at this stage, since the 3% Minting Fee is already collected upstream at the primary sale (see [Business Model](../economics/business-model.md))
2. The state transition is recorded on-chain with a timestamp

***

## Failure Handling

Failure is handled differently depending on whether the primary sale had already closed.

A batch can be marked failed from the Funding, Funded, Milestone 1, or Milestone 2 states. When `markFailed()` is called:

1. Token minting is locked and the token supply is snapshotted
2. The **remaining balance** (net proceeds less any milestone tranches already released) is fixed as the refund pool
3. A **180-day refund window** opens, during which any token holder can call `claimRefund()`
4. **Funds already released** in prior milestones are not recoverable through the smart contract — these are subject to off-chain recovery processes and originator staking penalties

Two properties of the refund matter to participants:

**The Minting Fee is not refunded.** The refund pool is the net amount that reached the vault, so a participant recovers at most their contribution less the fee already paid to the treasury.

**Refunds are all-or-nothing.** `claimRefund()` burns the caller's entire token balance in a single transaction. There is no partial refund.

The participant's recoverable amount in a failure scenario is:

```
recoverable = (participant_tokens / snapshot_supply_at_failure) × remaining_balance_at_failure
```

### After the refund window

Once the 180-day window closes, an operator can call `sweepResidual()`, which transfers any unrefunded balance to a receiver address configured by the factory. **Participants who do not claim within the window forfeit their refund to that address.**

Note that pausing the token or the vault does not extend the deadline — the window is fixed at 180 days from the moment failure was marked.

***

## Access Control

| Action                           | Required Role                        |
| -------------------------------- | ------------------------------------ |
| Initialize vault                 | BatchFactory (deployment only)       |
| Transition: Funding → Funded     | Automatic (triggered by PrimarySale) |
| Transition: Funded → Milestone N | `OPERATOR_ROLE`                      |
| Transition: Any → Failed         | `OPERATOR_ROLE`                      |
| Withdraw (failure)               | Any token holder, within 180 days    |
| Sweep residual after window      | `ESCROW_ADMIN_ROLE`                  |
| Emergency pause                  | `PAUSER_ROLE`                        |

***

## Security Properties

* **No discretionary fund extraction from escrow** — Escrow funds move only through milestone releases, refunds, or the post-window residual sweep. There is no open-ended withdrawal function on this contract.
* **Reentrancy protection** — All fund transfer functions are guarded by OpenZeppelin's `ReentrancyGuard`.
* **Single-direction state flow** — The state machine enforces strictly forward transitions, preventing replay or regression attacks.
* **Schedule fixed at finalization** — Milestone percentages are set when `finalize()` is called and cannot be changed afterwards. They must sum to 100%. The final milestone releases the remaining balance rather than a fixed percentage, so rounding cannot strand funds.
* **Originator address is mutable** — The escrow admin role can change the originator payout address via `setFarmer()` at any point before the final release.
