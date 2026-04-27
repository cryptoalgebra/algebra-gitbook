---
icon: chart-candlestick
---

# How Price Moves Affect Liquidity

{% hint style="info" %}
**Note for DEX Teams:**

This section helps users understand how their liquidity behaves over time and what it means for their earnings. By clearly explaining the difference between **active** and **inactive** liquidity—and outlining user-friendly strategies for managing each state—your DEX can build user confidence and reduce friction in onboarding new LPs.

Because Algebra-powered DEXes don’t allow mid-position edits (to maintain protocol efficiency and integrity), it’s essential that users know how to **actively manage or exit** positions. This knowledge reduces frustration and improves retention by making users feel in control.
{% endhint %}

{% hint style="success" %}
By integrating alert tools (like **notification bots**), visual status indicators, and clear guidance around **rebalancing**, **passive holding**, or **adding liquidity**, your DEX can attract both active and passive LPs, each with different levels of engagement and risk tolerance—while maximizing overall liquidity depth and fee generation across pools.
{% endhint %}

Once you choose a price range for your liquidity position, it’s important to keep an eye on market movements to ensure your liquidity stays **active** and continues earning fees. If you prefer a more passive approach and don’t want to monitor the market regularly, you can choose to provide liquidity across a **wide or full range**, though this typically offers lower returns.

> On most Algebra-powered DEXes, **liquidity positions are not editable after creation** — meaning the price range and token amounts are fixed once the position is submitted. To change these parameters, you’ll need to **withdraw your existing position and create a new one** with updated settings.

Once a position is live, it will either be in an **active** state (within your selected range and earning fees) or **inactive** (outside the range and not earning). Understanding how this works is key to making the most of your liquidity.

## Active Liquidity = Active Earnings

On Algebra-powered DEXes, liquidity providers (LPs) earn trading fees _only when_ the market price is within the price range they selected. This is called **active liquidity**.

When your liquidity is active, you automatically earn fees from trades and may also participate in **LP rewards or farming programs** (if enabled by the DEX).

### **Example**

Let’s say the current price of a token pair (e.g., TOKEN/USDT) is **$6.00**. You provide liquidity by adding:

* 166 TOKEN
* 1000 USDT\
  &#xNAN;_(Total ≈ $2000)_

You then set a **narrow price range** from **$5.70 to $6.60**.\
This means:

* You’ll earn fees _only when_ the price is inside this range.
* The narrower the range, the **higher your potential APR** — but also, the **greater the risk** that the price moves out of it.

📈 If your range earns an estimated **100% APR**, and the price stays in range for a month, you’ll collect a portion of that APR in fees.

📍 If the price **exits the range**, your position becomes **inactive** — no fees are earned.

* If price **rises above** the range → You’ll hold mostly **USDT**.
* If price **falls below** the range → You’ll hold mostly **TOKEN**.

### Inactive Liquidity = No Earnings

If the market moves outside your selected price range, your position becomes **inactive** — it won’t earn fees until the price returns to the range.

But what can you do when that happens? Here are your options:

## Option 1: Hold Your Position (Passive Approach)

You can **leave your position unchanged** and wait for the price to return to your range.

**Pros**:\
✅ No need to pay gas or fees to adjust\
✅ Starts earning fees again automatically once the price re-enters your range

**Cons**:\
❌ No earnings while out of range\
❌ Exposure may shift to just one asset, which may not match your market outlook

## Option 2: Adjust Your Range (Active Approach)

Withdraw and re-add liquidity at a **new price range** that aligns with current trends. This can be done using **dual-asset** or **single-sided** liquidity.

**Pros**:\
✅ Resume earning fees if the price stays in the new range\
✅ Maintain exposure to desired asset direction

**Cons**:\
❌ You’ll incur transaction fees\
❌ Price may shift again, requiring further adjustments

## Option 3: Withdraw & Hold

Exit the pool by **withdrawing your liquidity**. You can hold assets or use them elsewhere.

**Pros**:\
✅ Avoid potential impermanent loss if you expect further price shifts\
✅ Flexibility to invest in other opportunities

**Cons**:\
❌ No more fee income\
❌ Potential gas fees or tax events, depending on jurisdiction

## Option 4: Add More Liquidity

You can **add new liquidity** at a different price range while keeping your original position untouched.

**Pros**:\
✅ Cover a wider price range and capture more fees\
✅ Diversify your strategy across multiple price zones

**Cons**:\
❌ Greater exposure to market risk\
❌ More capital required

## Stay in Control

Keep track of your positions to avoid long periods of inactive liquidity.
