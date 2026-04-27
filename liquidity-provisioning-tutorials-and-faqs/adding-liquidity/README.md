---
icon: water-arrow-down
---

# Adding Liquidity

{% hint style="info" %}
**Note for DEX Teams:**

This section provides a **user-friendly walkthrough of how to supply liquidity using the Algebra-powered system**. It’s essential for onboarding new users with clarity and confidence, helping them participate in your DEX’s liquidity pools safely and efficiently.

An Algebra-powered DEX can support two modes of liquidity provisioning: **Manual & Automated.** Already integrated liquidity managers supported by Algebra are [Gamma](https://x.com/GammaStrategies), [ICHI](https://x.com/ichifoundation), [Krystal](https://x.com/KrystalDeFi), [Steer Protocol](https://x.com/steerprotocol)). When creating documentation for automated liquidity management, it will be necessary to take into account the selected partners and the liquidity management strategies that have become available.

Visuals are based on Algebra’s default testnet interface and **should be adapted** to reflect your product’s live UX.
{% endhint %}

## Introduction to Liquidity Provisioning

Concentrated Liquidity pools allow liquidity providers (LPs) to supply liquidity within **custom price ranges**, instead of across the entire 0 to ∞ range like traditional constant product AMMs (such as Uniswap V2-style DEXes).

By selecting a specific price range, LPs can **maximize capital efficiency** and **earn higher fees** when the market price stays within their chosen range. However, if the price moves outside that range, the position becomes **inactive** and stops earning fees until the price re-enters the range.

This model gives LPs greater flexibility, precision, and earning potential—but also requires more strategic input and occasional rebalancing.

## Liquidity Provisioning Modes

An Algebra-powered DEX allows for **Manual and Automated** modes of liquidity provisioning.&#x20;

## What is an Automated Liquidity Manager (ALM)?

An **Automated Liquidity Manager (ALM)** is a tool or strategy that automatically manages your liquidity position on a DEX. Instead of manually adjusting your price ranges or rebalancing assets, the ALM does it for you based on market data and predefined strategies.

#### Why Use One?

By using an ALM from one of our trusted DEX partners, you get:

**Hands-Free Optimization** — Your position stays within profitable ranges automatically\
**Higher Fee Earnings** — ALMs aim to maximize APR by keeping liquidity active\
**Risk Management** — Smart rebalancing helps reduce impermanent loss\
**Trusted Technology** — Built and battle-tested by top DeFi teams for better performance and security

Whether you're new to providing liquidity or looking to optimize your returns, an ALM makes it easier to earn more—without constant monitoring.

## Guides

{% content-ref url="manual-mode.md" %}
[manual-mode.md](manual-mode.md)
{% endcontent-ref %}

{% content-ref url="automated-mode.md" %}
[automated-mode.md](automated-mode.md)
{% endcontent-ref %}
