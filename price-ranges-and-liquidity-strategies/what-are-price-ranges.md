---
icon: question
---

# What Are Price Ranges

{% hint style="info" %}
**Note for DEX Teams:**

The section below explains how **Price Range Mechanics** work in concentrated liquidity pools, highlighting their advantages by comparing them to the traditional **Full Range (V2-style)** approach. This contrast illustrates how Algebra-powered DEX unlocks greater capital efficiency and strategic control for liquidity providers.
{% endhint %}

## V2-Style Liquidity Pools (Full Range)

In traditional V2 pools, assets are distributed evenly across the entire price curve — from 0 to ∞ — making liquidity always available but often underutilized.

* **Set-and-Forget Simplicity**: Once deposited, your position runs passively without the need for active management.
* **Constant 50/50 Allocation**: Assets maintain a balanced ratio at all times, regardless of price movements.
* **Equal APR for All**: Every LP earns a uniform share of swap fees, regardless of their capital efficiency.

## V3 & V4-Style Liquidity Pools (Concentrated Liquidity)

\[DEX Name] enables _concentrated liquidity_, where LPs can provide assets within chosen **price ranges** to increase capital efficiency and potential returns.

* **Customizable Price Ranges**: Liquidity can be focused within any price band, allowing strategic placement where trading is most active.
* **Dynamic Asset Ratios**: Asset composition adjusts as price moves within the range (e.g., shifting from 50:50 to 100:0 or vice versa).
* **Active Positioning**: LPs earn fees only when the current market price is inside their selected range.
* **Variable APR**: Narrower ranges can result in higher fee capture, but also higher exposure to impermanent loss.

## Example: How Does It Work?

Suppose the current price of a token pair is **$6.87**, and you provide liquidity within a **$6.17–$7.65** price range using a 50:50 deposit split. Your **liquidity only participates in trades as long as the price stays within this range** — earning fees accordingly. When it moves out of the range, the position remains open but inactive, earning no fees unless the price gets back to the range.

* If the price **drops to $6.17**, you end up holding mostly the base token (e.g., TON), fully exposed to its price.
* If the price **rises to $7.65**, your position shifts to mostly the quote asset (e.g., USDT), and any price movement beyond this range stops fee generation unless you rebalance your position.

To stay effective, LPs need to **monitor market prices** and adjust their ranges as needed.

## Benefits of Ranged Liquidity

#### **Lower Slippage for Traders**

With liquidity focused where it’s needed most, traders enjoy better pricing and lower slippage compared to spread-out V2 liquidity.

#### **Higher Potential Returns**

By concentrating assets in tighter ranges, LPs can earn **more fees with less capital**. This results in significantly higher capital efficiency and better APRs than traditional models.

#### **Flexible Risk Management**

LPs can align their strategy with their market outlook — opting for narrow, high-reward ranges or broader, more conservative bands to reduce out-of-range risk.
