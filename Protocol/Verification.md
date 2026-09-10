# Verification Architecture

Planet Protocol employs a **5-layer verification architecture** to bridge the trust gap between on-chain smart contracts and off-chain agricultural operations. Each layer addresses a different dimension of risk and operates at a different stage of the batch lifecycle.

---

## The Trust Problem

DeFi protocols operating with purely digital assets can verify state entirely on-chain. Real-world asset protocols face a fundamentally different challenge: the underlying asset exists in the physical world, and its status cannot be natively read by a smart contract.

Planet Protocol must answer questions that blockchains alone cannot:

- Does this farm actually exist?
- Is this originator who they claim to be?
- Has the crop actually been planted?
- Is the harvest actually happening?
- Will the originator actually repay?

The 5-layer verification architecture is designed to provide progressively increasing confidence across these dimensions.

---

## The Five Layers

### Layer 1 — Originator Identity Verification

**When:** Before batch creation
**What:** Verification of the originator's legal identity, business registration, and operational history

This layer establishes that the originator is a real, identifiable entity with a verifiable track record. Planet Labs performs:

- Business registration verification against local registries
- Identity verification of key principals
- Operational history assessment (years in operation, prior financing track record)
- Bank account and payment channel verification
- Reference checks with supply chain partners where available

**Output:** Originator approved or rejected for batch creation.

### Layer 2 — Asset & Operation Verification

**When:** During batch structuring, before deployment
**What:** Verification of the underlying agricultural operation and its economic viability

This layer confirms that the financing request is backed by a real, economically viable agricultural operation. Assessment includes:

- Physical site verification (on-ground inspection or satellite imagery confirmation)
- Crop type and growing season validation
- Historical yield data for the region and crop type
- Supply chain offtake arrangements (buyer contracts or market access confirmation)
- Financial model review (cost structure, expected revenue, margin analysis)

**Output:** Batch parameters finalized and approved for deployment.

### Layer 3 — Milestone Evidence Verification

**When:** At each milestone during the batch lifecycle
**What:** Real-time verification that milestone conditions have been met before fund release

This is the most operationally critical layer. At each milestone, the originator submits evidence that the milestone has been achieved. Evidence types include:

- **Milestone 1 (Planting):** Planting confirmation photos, seed/input purchase receipts, agronomist reports
- **Milestone 2 (Growth):** Growth-stage inspection photos, satellite imagery updates, input application records
- **Milestone 3 (Harvest):** Harvest documentation, warehouse receipts, buyer delivery confirmations, sales invoices

Planet Labs verifies this evidence through a combination of:
- Direct inspection by local field agents
- Third-party agronomist review
- Satellite and remote sensing data cross-referencing
- Document authenticity verification

**Output:** Milestone confirmed on-chain, triggering fund release via EscrowVault.

### Layer 4 — Financial Reconciliation

**When:** Post-harvest, before returns distribution
**What:** Verification that the originator's repayment matches the agreed terms

This layer ensures that the financial settlement is accurate and complete:

- Confirmation that the originator's return deposit matches the agreed amount
- Reconciliation of actual sales proceeds against projected returns
- ClaimVault balance confirmation before opening claims

No platform fee is calculated or deducted at this stage. The 3% Minting Fee is collected once, upstream at the primary sale, and no further fee applies at settlement — see [Business Model](../Economics/Business-Model.md).

**Output:** ClaimVault funded and claims enabled for token holders.

### Layer 5 — Continuous Monitoring & Oracle Integration

**When:** Throughout the batch lifecycle
**What:** Ongoing risk monitoring and anomaly detection

This layer provides continuous oversight beyond discrete milestone checkpoints:

- **Weather and climate monitoring** — Tracking adverse weather events, drought, flooding, or pest outbreaks that could impact crop performance
- **Market price monitoring** — Tracking commodity prices relevant to the batch's crop type
- **Originator behavior monitoring** — Unusual activity patterns, communication gaps, or early warning signals
- **Oracle integration** — Where available, integration with third-party data oracles for automated data feeds (weather data, commodity prices, satellite indices)

**Output:** Risk alerts and early intervention triggers for Planet Labs operators.

---

## Verification vs. Guarantee

It is important to note that verification reduces risk but does not eliminate it. Planet Protocol's verification architecture provides:

- **Reasonable assurance** that the originator and operation are legitimate
- **Evidence-based confirmation** of milestone progress
- **Continuous monitoring** for emerging risks

It does **not** provide:

- A guarantee of originator repayment
- Insurance against crop failure, natural disaster, or market collapse
- Legal enforcement of off-chain agreements (this is handled through separate legal frameworks)

See [Risks](../Risks.md) for a full discussion of residual risks and [Originator Security](../Economics/Originator-Security.md) for the $PNT staking mechanism that provides additional loss mitigation.

---

## Current Status & Roadmap

| Layer | Current Implementation | Planned Enhancement |
|---|---|---|
| Layer 1 — Identity | Manual verification by Planet Labs | KYC/AML integration with third-party providers |
| Layer 2 — Asset | Manual inspection + documentation | Satellite imagery API integration |
| Layer 3 — Milestone | Manual evidence review | Automated evidence scoring; decentralized verification network |
| Layer 4 — Financial | Manual reconciliation | Automated USDC flow tracking |
| Layer 5 — Monitoring | Manual monitoring | Oracle-based automated alerts; IoT sensor integration |

Planet Protocol's verification architecture is designed to become progressively more automated and decentralized over time, while maintaining human oversight at critical decision points.
