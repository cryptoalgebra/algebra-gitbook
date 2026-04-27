---
icon: slider
---

# Sliding Fee

{% hint style="info" %}
**Note for DEX Teams:**

This plugin helps distinguish between arbitrageurs and regular users, adjusting fees accordingly. It allows to significantly increase profitability compared to the classic fee mechanics, ranging from 2–10% per unit of invested liquidity by capturing more revenue from arbitrageurs while maintaining competitive fees for casual users.

Can be tuned per pool to strike a balance between user fairness and LP profitability. Customize it per pool using your parameters and make sure to reflect current logic in your frontend.

Available via **Plugins Marketplace**: [https://market.algebra.finance/plugin/sliding-fee/](https://market.algebra.finance/plugin/sliding-fee/)
{% endhint %}

### User-Oriented Overview:

This fee logic adjusts the cost of your swap based on **market movement and your trade direction**:

* Swaps **in the direction of price momentum** (e.g. price rising, and you're buying) may have a **slightly higher fee**.
* Swaps **against the trend** could be **cheaper**.

This discourages exploitative arbitrage and rewards regular users who are not front-running the price. For liquidity providers, this means **higher returns**, as the system is designed to capture more value from arbitrage trades without penalizing most users.

> 📈 Fees are calculated based on recent trades and market shifts.
