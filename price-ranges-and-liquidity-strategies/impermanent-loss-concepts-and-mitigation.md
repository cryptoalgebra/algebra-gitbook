---
icon: chart-line-down
---

# Impermanent Loss: Concepts & Mitigation

{% hint style="info" %}
**Note for DEX Teams:**

**De-risking liquidity provisioning through smarter design.** This section allows your users to learn what impermanent loss is, how it impacts LPs, and how to mitigate it. Essential for teams shaping LP education and retention.
{% endhint %}

## What Is Impermanent Loss?

Impermanent loss occurs when the **value of your assets in a liquidity position is lower at withdrawal than it would have been if you simply held them**. It’s called _“impermanent”_ because it only becomes a realized loss if you **exit the position** while prices are still unfavorable.

In some cases, holding assets in your wallet would have been more profitable — especially during strong market trends.

## Why Does Impermanent Loss Happen?

Impermanent loss happens when the **price of one or both tokens in a pool changes**, and the pool’s algorithm rebalances the asset ratio. This is a natural part of **automated market maker (AMM)** mechanics, especially with volatile assets.

In **concentrated liquidity pools (CLAMMs)**, such as those in Algebra-powered DEXes, liquidity is provided within a specific price range. As prices move, the proportion of tokens in your position shifts accordingly — and this is where impermanent loss emerges.

## Types of Impermanent Loss in Algebra-Powered DEXes

**1. Minimal IL (In-Range):**\
As the price fluctuates _within_ your defined range, your position remains active and earns fees. However, the value of your current token holdings may be lower than the original deposit due to the changed asset ratio. This is common and can often be offset by trading fees.

**2. Out-of-Range IL:**\
If the market price moves _outside_ your specified range, your position becomes inactive — holding only one asset and no longer earning fees. This creates a deeper impermanent loss since you're now exposed to the underperforming token without compensation from trading activity.

To minimize this loss, you can **withdraw and redeploy liquidity with a new price range**, effectively rebalancing and resuming fee generation.

## Should You Hold or Provide Liquidity?

The answer depends on market conditions and your goals:

* **HODLing** may outperform LPing in strong uptrends (bull markets).
* **Providing liquidity** can be more rewarding during sideways or range-bound markets, thanks to fee generation.
* **Active rebalancing** and careful range selection can reduce risk and optimize returns.

Liquidity providers have the advantage of turning volatility into yield — but only when positions are managed thoughtfully.
