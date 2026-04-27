---
icon: briefcase
---

# Modularity: Use Cases

## Examples of Plugin Capabilities:

Extend your DEX beyond traditional trading with plug-and-play functionality. Connect the plugin when needed with the ability to stop using it at any time to optimize gas costs (for example, to use incentive plugins only during campaigns)

* **Dynamic & Elastic Fees**\
  Adjust fees automatically based on volatility, trading volume, or time.
* **Whitelist / Blacklist Systems**\
  Enable access control, KYC layers, or token-gated trading.
* **Gas Discount Mechanisms**\
  Subsidize or refund gas to drive specific behaviors.
* **Just-In-Time (JIT) Liquidity Protection**\
  Prevent frontrunning and liquidity sniping.
* **Impermanent Loss Mitigation / LVR Solutions**\
  Protect LPs from common DeFi risks through specialized logic.
* **Perpetual Contracts**\
  Layer on advanced financial products like perps for new revenue channels.

## Plugin Use Cases in Detail

Explore what's already available with Algebra Integral.

<details>

<summary>Dynamic Fee Based on Trading Volume</summary>

_Retain users through tiered rewards and personalized fee structures._

**How it works:**\
Using ZK coprocessors (e.g., Brevis), the DEX tracks user volume and applies reduced fees to high-volume traders. Great for VIP programs.

**Benefits:**

* **For Users:** Lower fees, VIP perks, loyalty incentives.
* **For DEXes:** Improved retention, higher volume, flexible monetization.

</details>

<details>

<summary>Dynamic Fee Plugin (Volatility-Based)</summary>

_Real-time fee adjustment based on asset behavior._

**How it works:**\
The DEX monitors volatility, volume, and liquidity to set optimal trading fees on each pool—maximizing LP rewards and DEX earnings.

**Benefits:**

* **Competitive Edge:** Attract traders with optimized fees.
* **Higher LP Revenue:** Boost LP earnings during volatility.

</details>

<details>

<summary>Farming Plugin</summary>

_Launch flexible reward programs without modifying pool logic._

**How it works:**\
LPs receive ERC-721 tokens for their positions. These tokens can be staked in farming contracts with dynamic reward logic tied to time and range activity.

**Benefits:**

* **Incentive Alignment:** Projects can reward liquidity depth and stability.
* **Community Engagement:** Run seasonal or token-launch campaigns.
* **LP Rewards:** Users earn passive income beyond swap fees.

</details>

<details>

<summary>NFT Fee Discount</summary>

_Turn NFTs into utility assets with tangible in-DEX benefits._

**How it works:**\
This plugin checks a user’s NFT ownership and applies a custom swap fee. It uses Brevis ZK coprocessors to verify NFT conditions in a trustless, private, and gas-efficient way.

**Benefits:**

* **Increased Liquidity:** Lower trading fees attract more users and deepen liquidity.
* **NFT Utility:** Adds value to NFTs by linking them to real DeFi perks.
* **User Engagement:** Encourages NFT holders to stay active in the DEX.
* **Marketing Leverage:** A unique incentive for partnerships and community building.

</details>

<details>

<summary>LVR Plugin</summary>

_Programmable MEV and intent-based trading with fairness by design._

**How it works:**\
Built by FastLane, Atlas mitigates Loss-Versus-Rebalancing (LVR) and redistributes MEV via programmable, permissionless infrastructure. It also supports intent-based execution, enabling smarter, more complex trades.

**Benefits:**

* **Fairer Trading:** Reduces LVR impact for traders and LPs.
* **Advanced Strategies:** Enables intent-based trades and custom execution logic.
* **DEX Performance:** Improves execution quality and trust in the platform.
* **Developer-Friendly:** EVM-compatible and highly customizable.

</details>

<details>

<summary>Managed Swap Fee</summary>

_Customize fees on a per-swap basis for any use case._

**How it works:**\
Authorized services (e.g., KYC systems, AI agents, or apps) can initiate swaps with custom fees that override pool defaults. All changes are cryptographically verified and limited to the given swap.

**Benefits:**

* **Custom Business Logic:** Power advanced fee logic without affecting all users.
* **Gas-Efficient:** Minimal overhead per swap.
* **DEX Control:** Fine-tune incentives per user or use case.

</details>

<details>

<summary>Position Limit Orders</summary>

_Enable fully on-chain limit orders using AMM positions._

**How it works:**\
Users create liquidity positions outside the current price range, effectively setting buy-low or sell-high limit orders. Orders execute automatically when price enters the range.

**Benefits:**

* **Precision Trading:** Allows users to set strategic entry/exit points.
* **LP Utility:** Liquidity becomes a tool for order execution.
* **On-Chain Transparency:** No off-chain matching or middlemen.

</details>

<details>

<summary>Safety Switch</summary>

_Built-in emergency response system for DEX protection._

**How it works:**\
Pools can be paused manually or automatically based on real-time threat signals from security platforms like Hexagate. "Guard" role can trigger a pause, while "Admin" controls full recovery.

**Benefits:**

* **Real-Time Threat Response:** Automated or manual action during attacks.
* **Security Integration:** Links with on-chain monitoring tools.
* **DEX Resilience:** Prevents exploit damage while keeping control in admin hands.

</details>

<details>

<summary>Sliding Fee</summary>

_Dynamic, directional fees to balance arbitrage and trader fairness._

**How it works:**\
Fees adjust based on trade direction, frequency, and behavior—increasing profits from arbitrage while remaining user-friendly. The plugin shifts fee boundaries according to external market movement.

**Benefits:**

* **LP Earnings Boost:** Up to 15% more revenue from arbitrageurs.
* **Trader-Friendly:** Keeps swaps affordable for regular users.
* **Arb Resistance:** Disincentivizes high-frequency frontrunning.

</details>

<details>

<summary>Whitelist Fee Discount</summary>

_Reward trusted traders with personalized fee tiers._

**How it works:**\
Set custom static fees for specific addresses on specific pools. Target high-volume traders or partners and offer them reduced costs to incentivize large, repeat trades.

**Benefits:**

* **DEX Volume Growth:** Incentivize big swappers and market-makers.
* **LP Benefit:** Volume increase can outweigh fee discounts.
* **Trader Loyalty:** Keeps key users engaged and incentivized.

</details>

## Why Plugins Matter

With modularity, DEX builders no longer need to fork or rebuild to experiment, scale, or pivot. Instead, they can plug in features as needed and disconnect them to optimize gas or upgrade logic later.

**This approach transforms a DEX from a static product into an adaptive, evolving platform.**

Ready to extend your protocol? Explore [available plugins](https://market.algebra.finance/) or [build your own](https://algebra.finance/plugins).
