# Risks

Participation in Planet Protocol involves significant risks. This section provides a comprehensive overview of the risks that participants should understand before purchasing RWA tokens or engaging with the protocol. This is not an exhaustive list, and additional risks may emerge as the protocol evolves.

***

## Smart Contract Risk

Planet Protocol's smart contracts have **not yet been audited** by a third-party security firm. Unaudited smart contracts may contain bugs, vulnerabilities, or logic errors that could result in loss of funds. Even after audit, no smart contract can be guaranteed to be free of vulnerabilities.

**Specific risks include:**

* Undiscovered bugs in escrow, sale, or claim logic
* Reentrancy or access control vulnerabilities
* Unforeseen interactions between linked contracts
* Dependency on OpenZeppelin library correctness

**Mitigation:** A comprehensive third-party audit is planned prior to production batch deployment. Contracts are built on widely-used, battle-tested OpenZeppelin standards.

***

## Originator Default Risk

The originator may fail to repay the agreed return amount. Default may result from:

* Crop failure (disease, pests, drought, flooding)
* Market price decline below the originator's cost basis
* Operational mismanagement
* Fraud or misrepresentation
* Force majeure events (natural disasters, political instability)

**Consequence:** Partial or total loss of participant capital. Funds already disbursed in prior milestones are not recoverable through the smart contract.

**Default after the final milestone is a distinct case.** If an originator completes all milestones but fails to deposit repayment into the ClaimVault, the EscrowVault is already empty and no smart contract mechanism recovers participant capital. Recourse is entirely off-chain, via the SPV legal structure and originator staking. Legal enforcement depends on jurisdiction, process, and counterparty solvency; it may take considerable time and may not recover the full amount. See [Default at the Settlement Stage](../protocol/escrowvault.md#default-at-the-settlement-stage).

**Mitigation:** 5-layer verification architecture, milestone-based disbursement, originator staking, SPV corporate structure with an enforceable repayment claim, direct partnership with vetted farms in the current phase, and off-chain legal agreements.

***

## Under-Subscription Risk

The PrimarySale contract enforces a maximum number of shares but **no minimum**. A batch can be finalized and proceed to milestone releases at whatever amount was sold, including a small fraction of its stated target.

An under-funded agricultural operation is more likely to fail than a fully funded one, and there is no automatic mechanism that returns capital when a batch raises poorly. Unwinding an under-subscribed batch depends on an operator choosing to mark it failed.

***

## Agricultural and Climate Risk

Agricultural operations are inherently exposed to environmental and biological risks:

* Weather events (typhoons, drought, excessive rainfall)
* Pest and disease outbreaks
* Soil degradation
* Climate change impacts on growing seasons and yields

These risks can cause partial or total crop failure, which directly impacts the originator's ability to repay.

***

## Market and Commodity Price Risk

Agricultural returns depend on the sale price of harvested commodities. If market prices decline significantly between the time of financing and the time of sale, the originator may generate insufficient revenue to cover repayment obligations.

***

## Stablecoin Risk

All Planet Protocol transactions are denominated in **USDC**. USDC is issued by Circle and is subject to:

* **Issuer risk** — Circle could face regulatory action, insolvency, or operational failure
* **Redemption risk** — USDC may trade below par during stress events
* **Freeze risk** — Circle has the ability to freeze USDC held in specific addresses pursuant to law enforcement requests or regulatory orders
* **Smart contract risk** — The USDC contract itself could have vulnerabilities

Planet Protocol does not control or guarantee the stability or availability of USDC.

***

## Liquidity Risk

RWA tokens are transferable at the contract level, but **Planet Protocol does not operate a secondary market and no trading venue is guaranteed to exist** for any batch. In practice, a participant wanting to exit before the batch concludes must find a counterparty themselves.

Any liquidity that does emerge is likely to be thin, given small batch sizes, few participants, and no market maker support. Prices in such a market may bear little relation to the underlying batch value.

Because transfers are permitted, tokens may also end up held by addresses Planet Labs has not screened. Claims and refunds pay out to whoever holds the tokens at the time.

***

## Claim and Refund Window Risk

Both settlement paths run on a **180-day** clock:

* **Successful batch** — the claim window opens when an operator calls `openClaim()` and runs for 180 days
* **Failed batch** — the refund window opens when an operator marks the batch failed and runs for 180 days

In both cases, once the window closes an operator can sweep the unclaimed balance to a receiver address configured by Planet Labs. **Participants who do not act within the window forfeit their funds to that address.**

This creates a structural conflict of interest that participants should weigh: unclaimed participant capital accrues to a platform-controlled address, so Planet Labs benefits financially when participants fail to claim.

Two further points:

* Pausing the token does not extend either deadline. A prolonged pause during a window compresses the time available to act.
* Claiming requires an on-chain transaction from the wallet holding the tokens. Losing access to that wallet, or not monitoring the batch when the window opens, means missing the window.

***

## Regulatory and Legal Risk

* Regulatory frameworks for digital assets, tokenized securities, and DeFi protocols are evolving rapidly and vary by jurisdiction
* Planet Protocol may be subject to regulatory actions in Hong Kong or other jurisdictions
* Changes in law or regulation could restrict protocol operations, require participant identification, or prohibit participation from certain jurisdictions
* The classification of RWA tokens as securities, utility tokens, or other instruments is not definitively settled in all jurisdictions

***

## Fee Structure and Incentive Timing Risk

The platform's Minting Fee is collected at the moment of each purchase — before the sale has even closed, before any milestone is confirmed, and before the batch's ultimate outcome is known. This means **Planet Labs' fee revenue does not depend on a batch reaching its target or completing successfully.** Unlike a fee structure gated to milestone completion, this model does not create a direct financial penalty to Planet Labs if verification proves inadequate and a batch later fails. Participants should weigh this when assessing the strength of Planet Labs' incentive to verify originators rigorously; that incentive currently rests on reputational and repeat-business considerations rather than fee-at-risk alignment.

Separately, because the Minting Fee is deducted before funds reach the EscrowVault, originators receive approximately 97% of what was actually sold. This is disclosed in [Unit Economics](../economics/unit-economics.md#minting-fee-and-batch-shortfall) but represents a real reduction in financing relative to the headline target.

**The fee is not refundable.** If a batch is later marked failed, the refund pool is the net amount held in escrow. The fee already paid to the treasury is not returned, so a participant in a failed batch loses the fee in addition to any capital already released to the originator.

***

## Operational Risk

Planet Protocol is currently operated by **Planet Labs**, a small team. Operational risks include:

* Key person dependency
* Operational errors in verification or milestone confirmation
* Data entry errors (current data entry is manual)
* Communication failures with originators
* Infrastructure downtime or frontend unavailability

***

## Ethereum Network Risk

Planet Protocol is deployed exclusively on Ethereum mainnet. Risks include:

* Network congestion causing high gas fees or delayed transactions
* Ethereum protocol changes that affect contract behavior
* MEV (maximal extractable value) attacks on transactions
* Potential chain reorganizations or consensus failures

***

## Concentration Risk

During early operations, Planet Protocol may rely on a small number of originators and geographic regions. Poor performance by a single originator or adverse conditions in a single market could disproportionately impact platform performance and reputation.

***

## $PNT Token Risk

The $PNT governance token has not yet been issued. Risks related to the future $PNT token include:

* The token may never be issued
* Token characteristics, supply, and distribution may change from current descriptions
* $PNT may have no liquid market or may trade significantly below expectations
* Regulatory developments may restrict $PNT issuance or distribution

***

## No Insurance or Guarantee

Planet Protocol does not provide deposit insurance, principal guarantees, or any form of return guarantee. Participation in any batch may result in partial or total loss of invested capital.

***

## General Disclaimer

This risk disclosure section is provided for informational purposes and does not constitute legal, financial, or investment advice. Participants should conduct their own due diligence and consult qualified advisors before participating in Planet Protocol. See [Disclaimers](disclaimers.md) for full legal disclaimers.
