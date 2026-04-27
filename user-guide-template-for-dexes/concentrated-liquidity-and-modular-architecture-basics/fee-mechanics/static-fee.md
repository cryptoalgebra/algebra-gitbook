---
icon: pause
---

# Static Fee

{% hint style="info" %}
**Note for DEX Teams:**

This mechanic is a part of a Dynamic Fee Plugin. It utilizes classic Fee Tiers & Ticks combinations (in this example: 0.05%, 0.3% &&#x20;1%).&#x20;

The exact Tiers & Ticks can be tuned by dex according to the needs of each pool.
{% endhint %}

### An Example of User-Oriented Description

#### Swap Fee Distribution <a href="#swap-fee-distribution" id="swap-fee-distribution"></a>

Swap fees are distributed proportionally among all **active liquidity positions** at the time of each trade. When the market (spot) price moves outside a position’s selected range, that liquidity becomes inactive and temporarily stops earning fees. Once the price returns within range, the position becomes active again and resumes fee generation.

#### Fee Collection & Reinvestment <a href="#fee-collection-and-reinvestment" id="fee-collection-and-reinvestment"></a>

Fees earned from swaps are **not auto-compounded**. Instead, they accumulate separately from the main pool liquidity and must be manually claimed by the LP via their **LP Token**. This design provides flexibility and supports integrations with advanced liquidity management strategies.

#### Customizable Pool Fee Tiers <a href="#customizable-pool-fee-tiers" id="customizable-pool-fee-tiers"></a>

When creating a pool, user can choose from preset fee tiers when initializing a new pool, depending on the expected volatility and behavior of the token pair:

* **0.05% fee, tick spacing 10** — Best suited for stable pairs with minimal price fluctuation.
* **0.3% fee, tick spacing 60** — Balanced default option for most token pairs.
* **1% fee, tick spacing 200** — Ideal for volatile assets or launch-phase tokens with high anticipated trading activity.

> **Note:** _Tick spacing_ defines how granular the price intervals are within a pool. For instance, a tick spacing of 60 represents a \~0.6% difference between each successive tick, which affects how precisely LPs can set their price ranges.
