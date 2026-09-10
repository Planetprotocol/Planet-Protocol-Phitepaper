# Unit Economics

This section describes the economic structure of a single Planet Protocol batch — the fundamental unit of financing on the platform. Understanding batch-level economics is essential for both DeFi participants evaluating investment opportunities and originators structuring financing requests.

---

## Batch as Economic Unit

Each batch on Planet Protocol is a self-contained financing operation with its own:

- Funding target (denominated in USDC)
- Token supply and price
- Milestone schedule and release percentages
- Repayment terms, set independently by the originator
- Independent risk profile

There is no portfolio-level pooling, cross-subsidization, or shared liability between batches. Each batch succeeds or fails on its own merits.

**Repayment terms are not standardized.** The yield an originator agrees to pay is negotiated per batch and disclosed in that batch's listing. This document does not project a typical or expected return — actual terms vary by crop, originator, and market conditions, and are disclosed at batch creation. See [Risks](../Risks.md) for the full disclaimer on projected versus actual outcomes.

---

## Minting Fee and Batch Shortfall

The platform's only fee is the **Minting Fee**, charged to participants on each primary sale purchase and routed to the Planet Labs treasury in the same transaction. The rate is set per batch at deployment and capped at 3% by the contract. The remainder of each purchase goes to the EscrowVault. See [Business Model](Business-Model.md) for the full fee structure.

This has a direct consequence for batch sizing: **a batch's net financing is 97% of its gross funding target.**

### Illustrative Example

| Parameter | Value |
|---|---|
| Crop Type | Thai Jasmine Rice |
| Funding Target | 100,000 USDC |
| Token Supply | 10,000 RWA tokens |
| Token Price | 10 USDC |
| Minting Fee | 3% (charged to participants, deducted at each purchase) |
| Batch Duration | 6 months (1 crop cycle) |

### Fund Flow

```
DeFi Participants
       │
       │ each purchase: price × shares (gross)
       ▼
   PrimarySale ──────── Minting Fee routed per purchase ──────► Planet Labs treasury
       │                 (3% of gross → 3,000 USDC total)
       │ net forwarded per purchase (97% → 97,000 USDC total)
       ▼
   EscrowVault
       │
       │ 97,000 USDC released across 3 milestones — no further fee deducted
       ▼
   Originator
       │
       │ principal + yield, per the batch's disclosed repayment terms
       ▼
   ClaimVault
       │
       │ distributed via burn-to-claim
       ▼
DeFi Participants
```

### The Shortfall, Explained

The originator in this example requested **100,000 USDC** of financing. Because the 3% Minting Fee is deducted from each purchase before the remainder reaches escrow, the escrow accumulates **97,000 USDC** — a **3,000 USDC (3%) shortfall** against the requested amount.

Planet Protocol does not automatically increase the funding target to compensate for the fee. This is a deliberate design choice, not an oversight: it keeps the fee mechanism simple and fully transparent (100 tokens always means exactly 100 tokens' worth raised, minus a fixed, disclosed 3%). The practical implication is that **originators should size their funding target to their actual capital need, accounting for the fact that net proceeds will be approximately 97% of the gross target.** This is disclosed to originators during the origination process described in [Verification](../Protocol/Verification.md).

---

### There Is No Minimum Raise

The PrimarySale contract enforces a maximum number of shares, but **no minimum**. A batch can be finalized and proceed to milestone releases at whatever amount was actually sold. If a batch sells only a fraction of its target, the originator receives that fraction and the batch continues.

There is no automatic refund triggered by a batch failing to reach its stated target. A batch that raises poorly can only be unwound by an operator manually marking it failed, which is a discretionary action, not an automatic contract behaviour. Participants should not assume a shortfall in subscription will return their capital.

---

## Milestone Release, Post-Fee

Because the Minting Fee is collected upstream at each purchase, **no further fee is deducted at milestone release.** The full net amount that reached the EscrowVault is released to the originator across the milestone schedule. The milestone percentages are set when the batch is finalized; the final milestone releases whatever balance remains, so rounding never leaves funds stranded in the vault.

**Example 3-milestone schedule (30/35/35), applied to the 97,000 USDC net proceeds above:**

| Milestone | Release | Cumulative |
|---|---|---|
| Milestone 1 | 29,100 USDC | 29,100 USDC |
| Milestone 2 | 33,950 USDC | 63,050 USDC |
| Milestone 3 | 33,950 USDC | 97,000 USDC |
| **Total** | **97,000 USDC** | — |

---

## Yield Disclaimer

Planet Protocol does not publish a typical, expected, or target yield. Each batch's repayment terms are set by the originator and disclosed at batch creation. Actual returns depend on:

- The specific repayment terms disclosed for that batch
- Crop performance and harvest quality
- Commodity market prices at the time of sale
- Originator's operational execution
- External factors (weather, logistics, policy changes)
- Originator's ability and willingness to repay

A batch may return less than its disclosed terms, return only partial principal, or result in total loss of capital. See [Risks](../Risks.md) for full risk disclosures.

---

## Comparison with Traditional Agricultural Finance

| Dimension | Traditional | Planet Protocol |
|---|---|---|
| Minimum investment | $50,000–$500,000+ | As low as 1 token (~$10 equivalent) |
| Geographic access | Local banks, regional DFIs | Global, permissionless |
| Transparency | Quarterly reports, limited visibility | Real-time on-chain state |
| Settlement | 30–90 day manual processes | Deterministic smart contract execution |
| Intermediaries | Multiple (banks, brokers, agents) | Direct participant-to-originator via smart contract |
| Fee extraction points | Multiple (origination, servicing, collection) | Single, fixed, disclosed at mint |
| Liquidity | Illiquid, fixed-term commitments | Tokens are transferable, but no trading venue is operated or guaranteed |
