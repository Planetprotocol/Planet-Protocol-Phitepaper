# Why Planet

Planet Protocol is not the first project to tokenize real-world assets, nor is it the first to bring DeFi to agricultural markets. What distinguishes Planet is the specificity of its design — every architectural decision, from the escrow state machine to the burn-to-claim settlement, was built for the operational reality of agricultural financing in emerging markets.

---

## Competitive Landscape

### Existing RWA Protocols

Most active RWA protocols today focus on assets that are already well-served by traditional finance:

| Protocol Category | Typical Assets | Limitation for Agriculture |
|---|---|---|
| Treasury tokenization | US T-bills, money market funds | Low yield; no emerging market exposure |
| Real estate | Property-backed tokens | Long duration; complex legal structures |
| Private credit | Corporate loans, trade finance | Institutional-only; large minimum tickets |
| Carbon credits | Voluntary carbon offsets | Volatile pricing; verification complexity |

These protocols solve legitimate problems, but none address the structural financing gap for agricultural producers in Southeast Asia.

### Traditional Agricultural Finance

Existing agricultural financing channels in Southeast Asia include:

| Channel | Limitation |
|---|---|
| Commercial banks | High minimum loan size; extensive documentation requirements; slow approval |
| Microfinance institutions | Low ticket sizes; high interest rates (20–40%+); limited crop-specific expertise |
| Government programs | Bureaucratic; often politically allocated; insufficient scale |
| Informal lenders | Exploitative rates (30–60%+); no transparency; predatory practices |
| Trade finance platforms | Focus on post-harvest export; do not cover pre-harvest production financing |

Planet occupies a distinct position: **DeFi-native, pre-harvest to post-harvest, batch-structured, and directly accessible** to global capital providers.

---

## Planet's Differentiators

### 1. Batch Isolation

Every financing operation on Planet is an independent batch with its own contracts, escrow, and lifecycle. This is a deliberate design choice:

- No pooled risk across batches
- No contagion from one originator's failure to another's success
- Participants can evaluate and choose individual batches based on their risk appetite
- Clear, auditable per-batch performance history

Most credit protocols pool assets, which obscures individual loan performance and creates systemic risk.

### 2. Milestone-Based Escrow

Planet's EscrowVault releases capital in stages tied to real-world milestones. This is fundamentally different from lump-sum disbursement or automated time-based release:

- Capital is released only when evidence of progress is verified
- The originator is incentivized to perform at each stage
- Participants retain partial protection through unreleased escrow in early failure scenarios
- On-chain state reflects real-world operational progress

### 3. Burn-to-Claim Settlement

The burn-to-claim mechanism provides a clean, deterministic settlement process:

- No snapshot dependencies or airdrop coordination
- No gas-intensive push distributions
- Self-service, pull-based claiming at the participant's convenience
- Token destruction ensures mathematical precision in distribution

### 4. Agricultural Specialization

Planet is purpose-built for agricultural financing, not adapted from a general-purpose lending protocol:

- Batch durations align with crop cycles (3–9 months)
- Milestone structures map to agricultural phases (planting, growth, harvest)
- Verification architecture is designed for rural, emerging-market conditions
- Originator onboarding process accounts for the realities of agricultural operations

### 5. Transparent, Non-Custodial Architecture

- All fund flows are on-chain and auditable
- Planet Labs never takes custody of participant capital
- Smart contracts enforce all state transitions — no manual override capability
- A minting fee charged to participants at purchase, capped at 3% by the contract, is the platform's only revenue mechanism — originators are charged nothing

### 6. Southeast Asian Focus with Global Capital Access

Planet is operationally focused on Southeast Asian agricultural markets — a region with massive financing demand, strong agricultural productivity, and growing digital infrastructure — while opening access to global DeFi capital without geographic restriction.

---

## What Planet Is Not

Clarity on what Planet Protocol is **not** is as important as what it is:

| Planet is NOT | Explanation |
|---|---|
| A lending protocol | Planet does not lend. It facilitates batch financing between participants and originators. |
| A yield aggregator | Returns come from a single, identified source (the originator's repayment), not from aggregated or layered strategies. |
| A speculative instrument | RWA tokens represent participation in a real financing operation, not exposure to token price speculation. |
| A stablecoin or money market | Planet does not promise capital preservation. Each batch carries independent risk. |
| A DAO-governed protocol (yet) | Planet is currently operated by Planet Labs. Decentralized governance is a long-term goal. |

---

## The Vision

Planet Protocol's long-term vision is to become the **standard infrastructure layer for on-chain agricultural financing** — starting with Southeast Asia and expanding to other agricultural markets globally.

The path to this vision is incremental and evidence-driven:

1. **Prove the model** — Demonstrate successful batch cycles with verified originators in Thailand
2. **Build the track record** — Accumulate on-chain performance data that speaks for itself
3. **Expand geography** — Extend to Vietnam, Indonesia, and other ASEAN markets
4. **Diversify crops and structures** — Support a broader range of agricultural products and financing structures
5. **Decentralize operations** — Progressively move verification, governance, and operations from Planet Labs to community and protocol-level control
