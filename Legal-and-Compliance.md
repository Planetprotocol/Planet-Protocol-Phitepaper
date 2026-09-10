# Legal and Compliance

Planet Protocol is developed and operated by **Planet Digital Labs Limited**, a company incorporated in Hong Kong. The protocol is designed to operate within the regulatory frameworks applicable to digital assets, virtual asset services, and financial technology in Hong Kong and relevant Southeast Asian jurisdictions.

---

## Jurisdiction

**Primary Jurisdiction:** Hong Kong Special Administrative Region

Planet Digital Labs Limited is subject to the laws and regulations of Hong Kong, including but not limited to:

- Companies Ordinance (Cap. 622)
- Securities and Futures Ordinance (Cap. 571)
- Anti-Money Laundering and Counter-Terrorist Financing Ordinance (Cap. 615)
- Personal Data (Privacy) Ordinance (Cap. 486)

---

## Token Classification

### RWA Tokens (ERC-1155 Batch Participation Tokens)

RWA tokens issued through Planet Protocol represent **fractional participation rights** in a specific batch financing operation. Each token entitles the holder to a proportional share of the returns distributed through the ClaimVault upon batch completion.

Planet Protocol's position is that RWA tokens are structured to function as participation instruments within a defined batch lifecycle, rather than as collective investment scheme interests. Participants should note that the tokens are technically transferable — the token contract does not restrict transfers — even though Planet Protocol operates no secondary market for them. The classification of digital tokens under securities law varies by jurisdiction, turns partly on transferability and on how an instrument is marketed and traded in practice, and is subject to evolving regulatory interpretation. Planet Protocol's characterisation is its own view and is not a determination by any regulator.

Participants are responsible for determining whether their acquisition of RWA tokens complies with the laws and regulations of their own jurisdiction.

### Transferability and Holder Screening

Because RWA tokens can be transferred freely, a token may come to be held by a party that Planet Protocol has not screened. Claims and refunds are paid to whoever holds the tokens at the time of the transaction. Participant-level sanctions screening is not currently implemented; see the KYC/AML section below.

### Planet Points

Planet Points are non-transferable, non-tradeable units with no monetary value. They function solely as a metric for determining proportional allocation in a future $PNT token airdrop. Points are explicitly not securities, tokens, or financial instruments.

### $PNT Governance Token (Planned)

The $PNT token has not yet been issued. When issued, $PNT is intended to function as a governance and utility token within the Planet Protocol ecosystem. The legal classification and regulatory treatment of $PNT will be assessed and disclosed prior to issuance.

---

## KYC/AML Status

Planet Protocol does **not currently implement** Know Your Customer (KYC) or Anti-Money Laundering (AML) procedures for DeFi participants. The platform operates on a permissionless basis — any wallet holder can participate in primary sales without identity verification.

**Planned Implementation:**

KYC/AML integration with a third-party identity verification provider is planned for a future protocol update. This will include:

- Identity verification for participants above defined thresholds
- Sanctions screening against HKMA and international sanctions lists
- Ongoing transaction monitoring for suspicious activity
- Record-keeping in compliance with applicable regulations

The timeline for KYC/AML implementation is subject to regulatory guidance and operational readiness.

---

## SPV Structure

Each batch financing operation is backed by a **special purpose vehicle (SPV)** corporate entity. The SPV is the counterparty to the originator's financing agreement and holds the contractual claim to repayment.

This structure exists because the smart contracts govern fund custody and release, but cannot compel an originator to repay. The SPV provides a legal claim that is enforceable through ordinary contract law and is independent of the on-chain contracts, and it isolates each financing operation as a distinct legal unit, consistent with the batch isolation enforced on-chain.

The SPV is the primary recovery mechanism where the smart contracts have no reach — in particular, where an originator completes all milestones but fails to deposit repayment into the ClaimVault. See [Default at the Settlement Stage](Protocol/EscrowVault.md#default-at-the-settlement-stage).

Participants should note that a legal claim is not a guarantee of recovery. Enforcement depends on jurisdiction, legal process, and the solvency of the counterparty, and may take considerable time or fail to recover the full amount. See [Risks](Risks.md).

---

## Originator Compliance

Agricultural originators onboarded to Planet Protocol are subject to:

- Business registration verification in their jurisdiction of operation
- Identity verification of key principals
- Screening against applicable sanctions lists
- Off-chain financing agreements governing repayment obligations, representations, and warranties

Originator agreements are governed by the laws specified in each agreement and are enforced through off-chain legal mechanisms.

---

## Sanctions Compliance

Planet Protocol is committed to compliance with applicable sanctions regimes. The platform does not knowingly facilitate transactions involving:

- Individuals or entities designated on the HKMA sanctions list
- Individuals or entities designated on the United Nations Security Council sanctions list
- Individuals or entities subject to sanctions by other relevant authorities

Sanctions screening is currently performed during originator onboarding. Participant-level sanctions screening will be implemented as part of the planned KYC/AML integration.

---

## Data Protection

Planet Protocol processes personal data in accordance with the Personal Data (Privacy) Ordinance (Cap. 486) of Hong Kong. The Privacy Policy published on the Planet Protocol website details:

- Categories of personal data collected
- Purposes and legal bases for processing
- Data retention periods
- Data subject rights and access requests
- Third-party data sharing

See the full Privacy Policy at [planetprotocol.net](https://planetprotocol.net).

---

## Points Program Compliance

The Planet Points Program is designed to comply with Hong Kong's Pyramid Schemes Prohibition Ordinance (Cap. 617) and has been structured to avoid characteristics of prohibited multi-level marketing or pyramid schemes:

- Referral depth is limited to two layers
- Referral earnings are capped at 35% of total points
- Points have no cash value and are non-transferable
- The program does not require any monetary payment for enrollment

---

## No Investment Advice

Nothing published by Planet Protocol, Planet Labs, or Planet Digital Labs Limited constitutes investment, financial, legal, or tax advice. All content is provided for informational purposes only. Participants should consult qualified professional advisors before making any decisions related to their participation in Planet Protocol.

---

## Regulatory Developments

The regulatory environment for digital assets and decentralized finance is evolving. Planet Protocol monitors regulatory developments in Hong Kong and relevant jurisdictions and will update its compliance framework as necessary. Material changes to the regulatory environment or to Planet Protocol's compliance posture will be communicated through official channels.
