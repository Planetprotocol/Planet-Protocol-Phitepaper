# Originator Security

Planet Protocol implements an **originator staking requirement** using the $PNT governance token to align originator incentives with platform performance and provide a first-loss buffer for DeFi participants.

---

## The Incentive Problem

In agricultural financing, the originator receives capital upfront and repays later. This creates a temporal asymmetry: the participant bears capital risk from the moment of investment, while the originator's obligation is deferred. Without a counterbalancing mechanism, this asymmetry favors originators who may underperform, delay repayment, or default.

Traditional finance addresses this through collateral requirements, credit scoring, and legal enforcement. Planet Protocol supplements its [verification architecture](../Protocol/Verification.md) with an on-chain economic mechanism: **$PNT staking**.

---

## How It Works

### Staking Gate

Before an originator can have a batch deployed on Planet Protocol, they must stake a defined amount of $PNT tokens. This staked amount is locked for the duration of the batch lifecycle and serves as:

1. **Skin in the game** — The originator has direct economic exposure to the batch's success or failure
2. **First-loss buffer** — In the event of default, staked $PNT can be liquidated to partially compensate participants
3. **Quality signal** — The willingness to stake tokens demonstrates originator confidence in their own operation

### Staking Parameters

| Parameter | Description |
|---|---|
| Staking token | $PNT (Planet governance token) |
| Staking amount | Defined per batch, proportional to financing amount |
| Lock period | Duration of the batch lifecycle (from deployment to Milestone 3 completion or failure resolution) |
| Release condition | Full repayment of returns to the ClaimVault |
| Slash condition | Batch enters Failed state with unreleased escrow funds |

### Lifecycle

```
   Originator stakes $PNT
           │
           ▼
   Batch deployed ──── Batch succeeds ──── $PNT returned to originator
           │
           └──── Batch fails ──── $PNT slashed (partial/full)
                                      │
                                      ▼
                              Proceeds distributed to
                              affected participants
```

---

## Slashing Mechanics

If a batch transitions to the **Failed** state:

1. The originator's staked $PNT is marked for slashing
2. The slashed $PNT is liquidated (sold for USDC) through a defined process
3. Liquidation proceeds are distributed proportionally to affected token holders
4. Slashing occurs automatically per the smart contract logic — no manual intervention is required

### Slashing Limitations

The staked $PNT provides **partial** loss mitigation, not full insurance. The recoverable amount depends on:

- The staking ratio relative to the batch size
- The market price of $PNT at the time of liquidation
- The portion of escrow funds already released to the originator prior to failure

Planet Protocol does not guarantee that slashing proceeds will cover participant losses. See [Risks](../Risks.md).

---

## Staking as Reputation

Beyond its economic function, originator staking creates a **reputation signal**:

- Originators with a track record of successful batches accumulate unlocked $PNT, demonstrating reliability
- Repeat originators may access better terms (lower staking ratios, larger batch sizes) as their on-chain track record grows
- First-time originators must meet higher staking thresholds to compensate for the lack of historical performance data

This creates a natural progression from higher-friction, higher-collateral early batches to more efficient, relationship-based financing over time.

---

## $PNT Token Status

The $PNT governance token has **not yet been issued**. It is planned as a separate future token launch, distinct from RWA batch tokens. Key details:

- $PNT will function as a governance and utility token for the Planet Protocol ecosystem
- $PNT is used as the **airdrop weight** for the [Planet Points Program](Points-Program.md) — points earned by participants will convert to $PNT allocation at a future token generation event
- The originator staking mechanism will be activated when $PNT is live

Until $PNT is issued, originator security is managed through off-chain agreements, verification processes, and Planet Labs' operational oversight.

---

## Design Rationale

| Alternative | Why Not |
|---|---|
| USDC collateral | Reduces originator's working capital; defeats the purpose of providing financing |
| NFT-based reputation only | No economic consequence for poor performance |
| Third-party insurance | Expensive, slow, and not available for most Southeast Asian agricultural operations |
| **$PNT staking** | **Aligns incentives, creates economic consequences, builds reputation, and connects to protocol governance** |
