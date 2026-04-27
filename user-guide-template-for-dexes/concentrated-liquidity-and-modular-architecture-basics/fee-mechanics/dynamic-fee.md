---
icon: scale-unbalanced
---

# Dynamic Fee

{% hint style="info" %}
**Note for DEX Teams:**

This plugin adapts to volatility, liquidity, and trading activity. By adjusting fees based on market behavior, it allows DEXes to maintain a competitive edge and attract more trading volume.

Customize it per pool using your parameters and make sure to reflect current logic in your frontend.

Available via **Plugins Marketplace**: [https://market.algebra.finance/plugin/dynamic-fee/](https://market.algebra.finance/plugin/dynamic-fee/)
{% endhint %}

### User-Oriented Overview

In pools where **Dynamic Fees** are enabled, the trading fee adjusts automatically based on market activity.

* If trading is heavy or the market is volatile, the fee may increase slightly to account for risk.
* In calmer conditions, the fee drops to make swaps more affordable.

This ensures that fees remain fair and reflect the current market reality. You don’t need to track or tweak anything—the system adjusts automatically to keep things optimized for both traders and liquidity providers.

> 🔄 Fee recalculations happen automatically after each transaction.
