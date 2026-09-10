# Business Model

Planet Protocol generates revenue through a single, transparent fee mechanism: the **Minting Fee**. There are no hidden charges, token-based extraction mechanisms, or speculative revenue dependencies.

---

## Minting Fee

The Minting Fee is a percentage charged to participants on each purchase in the primary sale. It is deducted from the purchase amount at the moment of purchase and routed directly to the Planet Labs treasury; the remainder is forwarded to the batch's EscrowVault in the same transaction.

| Parameter | Value |
|---|---|
| Fee Type | Percentage of each purchase in the primary sale |
| Rate | Set per batch at deployment, capped at 3% by the contract (`MAX_FEE_BPS = 300`) |
| Charged To | Participants (deducted from the purchase amount) |
| Collection Point | At each individual purchase, inside the PrimarySale contract, before the net amount reaches the EscrowVault |
| Recipient | Planet Labs treasury wallet |
| If the batch later fails | The fee is not returned. Refunds cover only the net amount held in escrow |

### Key Properties

**Participant-borne:** The Minting Fee is deducted from the participant's purchase amount. Participants pay the stated token price; the fee reduces how much of that payment reaches the batch, rather than being added on top of the price. A purchase of 10,000 USDC at a 3% rate sends 300 USDC to the treasury and 9,700 USDC to the EscrowVault.

**Collected at purchase, not at settlement:** The fee is transferred to the treasury within the same transaction as the purchase. It is not held in escrow, not contingent on the batch reaching its funding target, and not contingent on the batch succeeding. See [Risks](../Risks.md) for what this means for incentive alignment and for refunds on failed batches.

**Set per batch, capped at 3%:** The rate is configured for each batch at deployment and is fixed for that batch's lifetime. The smart contract rejects any rate above 3%. Individual batches may be deployed at lower rates.

**One-directional:** The fee applies only at purchase. It is not charged again on holding, claiming, or burning. Originators are not charged any fee — see [What Planet Protocol Does Not Charge](#what-planet-protocol-does-not-charge) below.

**Batch shortfall as a consequence:** Because the fee is deducted before funds reach escrow, a batch that sells 100,000 USDC of tokens at a 3% rate delivers 97,000 USDC into escrow, not 100,000. This is a structural feature of the fee model, not an error — see [Unit Economics](Unit-Economics.md#minting-fee-and-batch-shortfall) for the full mechanics and its implications for originators sizing a financing request.

**Batch shortfall as a consequence:** Because the fee is deducted before funds reach escrow, a batch that sells 100,000 USDC of tokens at a 3% rate delivers 97,000 USDC into escrow, not 100,000. This is a structural feature of the fee model, not an error — see [Unit Economics](Unit-Economics.md#minting-fee-and-batch-shortfall) for the full mechanics and its implications for originators sizing a financing request.

---

## Revenue Alignment

**Alignment with originators:** Because the fee is charged to participants at purchase rather than deducted from milestone disbursements, originators receive 100% of the net proceeds that reach the EscrowVault across all milestones — there is no further fee deduction at any later stage of the batch lifecycle. This directly reflects Planet Protocol's positioning against informal and intermediary-heavy agricultural credit channels, where costs are frequently extracted at multiple points.

**Alignment with participants:** The rate for a batch is fixed at deployment and disclosed before purchase, and the PrimarySale contract exposes a `getQuote()` function returning the exact gross, fee, and net amounts for any purchase size. There are no variable spreads, hidden charges, or retroactive adjustments.

**What this fee structure does not do:** Because the fee is collected at purchase rather than on milestone completion, Planet Labs' revenue from a batch is settled before any milestone is verified and does not depend on the batch succeeding. See [Fee Structure and Incentive Timing Risk](../Risks.md#fee-structure-and-incentive-timing-risk).

**No token dependency:** Planet Labs' core revenue model does not depend on the appreciation or trading volume of any token. Platform sustainability is grounded in real economic activity — a fixed fee on primary sale volume.

---

## What Planet Protocol Does Not Charge

| Fee Type | Status |
|---|---|
| Originator financing fee | None — originators are not charged at batch creation, milestone release, or repayment |
| Participant exit fee | None |
| Token transfer fee | None |
| Claim/burn fee | None |
| Performance fee on yield | None |
| Originator onboarding fee | None |

---

## Future Revenue Considerations

As the protocol matures, additional revenue streams may be introduced. These are not currently implemented and will be subject to governance review and community input:

- **Secondary market fee** — A small transaction fee on secondary trading of RWA tokens, once secondary markets are enabled
- **Data and analytics services** — Premium data feeds or reporting for institutional participants
- **Verification-as-a-service** — Offering Planet's verification infrastructure to third-party RWA protocols

Any new fee mechanism will be announced in advance and reflected in updated protocol documentation.
