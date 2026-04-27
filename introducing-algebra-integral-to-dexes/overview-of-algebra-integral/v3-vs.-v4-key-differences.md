---
icon: up-down
---

# V3 vs. V4: Key Differences

**V3 DEXes** (like Uniswap V3 or Algebra V3) introduced _concentrated liquidity_, enabling LPs to earn more by deploying capital within custom price ranges. However, core mechanics — like fee models, incentive logic, and routing — were still hardcoded into monolithic contracts. This made upgrades and experimentation difficult and expensive, requiring new deployments, liquidity migrations and costly audits of entire new codebase.

**V4 DEXes**, while keeping CLAMM at its core, use a **modular plugin system**. Features like dynamic fees, farming logic, or token-specific handling can be added, removed, or updated without touching the base contracts or migrating liquidity. This unlocks unprecedented flexibility for builders and protocols.

#### Why Modularity Matters for DEX Teams

With a modular architecture like **Algebra V4**, partnering DEX gains:

* **Plug-and-play upgrades** — Introduce new features without redeploying your entire DEX
* **Custom strategies** — Offer different mechanics for different pools or ecosystems
* **Faster innovation cycles** — Test, iterate, and deploy new ideas within weeks
* **No forced migrations** — Liquidity stays in place, users stay onboard

#### Algebra: The First Modular CLAMM Engine

Algebra pioneered modularity before it was mainstream — launching the first CLAMM engine with full plugin support in production. Over 20+ DEXes now run on Algebra, each fine-tuned with pool-specific logic and features.

From dynamic fees to referral systems and native farming, Algebra gives DEXes all the flexibility of V4 architecture.
