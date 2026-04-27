---
icon: chess-knight
---

# Matching Your Liquidity Strategy to Market Moves

{% hint style="info" %}
**Note for DEX Teams:**

**Adapting strategy to market conditions.** This section helps users understand how to tailor their liquidity provisioning to bullish, bearish, or sideways markets—ensuring better risk/reward alignment and improving LP satisfaction.
{% endhint %}

The table below shows the Profit and Loss of various positions relative to the 50:50 HODL position, under decreasing️️ 📉, mean-reverting️, and increasing️ 📈prices. “Cash” means a position in stablecoins.

<table data-header-hidden><thead><tr><th width="166.5555419921875"></th><th></th><th></th><th></th></tr></thead><tbody><tr><td><strong>Strategy /</strong> <br><strong>Asset Price</strong></td><td><strong>📉 Decreasing</strong></td><td><strong>➡️ Mean-Reverting</strong></td><td><strong>📈 Increasing</strong></td></tr><tr><td><strong>100% Asset</strong></td><td>🔴🔴<strong>High Loss</strong> – Fully exposed to declining asset.</td><td>🟡 <strong>Moderate Outcome</strong> – Exposure to volatility with unclear net result.</td><td>🟢🟢 <strong>High Gain</strong> – Fully captures upside.</td></tr><tr><td><strong>Cash (Stablecoins)</strong></td><td>🟢🟢 <strong>Moderate Gain</strong> – Avoids decline, preserves value.</td><td>🟡 <strong>Flat to Mild Gain</strong> – No exposure to volatility, no upside captured.</td><td>🔴🔴 <strong>High Loss (Relative)</strong> – Misses all appreciation.</td></tr><tr><td><strong>Wide LP</strong></td><td>🔴 <strong>Moderate Loss</strong> – Suffers impermanent loss, partially offset by fees.</td><td>🟢<strong>Moderate Gain</strong> – Earns fees from price bouncing within range.</td><td>🔴 <strong>Mild Loss</strong> – Suffers impermanent loss from selling rising asset, gains some fees.</td></tr><tr><td><strong>Narrow LP</strong></td><td>🔴🔴 <strong>High Loss</strong> – Goes out of range early, earns no fees.</td><td>🟢🟢 <strong>High Gain</strong> – Maximizes fee income in tight trading band.</td><td>🔴🔴 <strong>High Loss</strong> – Position exits range early, holds poor asset, earns no fees.</td></tr></tbody></table>

## Research Before You Provide Liquidity

Becoming a liquidity provider on an Algebra-powered DEX comes with both opportunity and responsibility. To make informed decisions, it’s important to assess key factors before deploying your assets.

**Token Volatility**\
Pools with volatile or non-stable assets (e.g., TON/USDT) carry a higher risk of impermanent loss. However, these pools can still be highly profitable depending on factors such as trading volume, fee structure, and total liquidity.

**Market Conditions**\
Wider market trends—such as bullish, bearish, or sideways movement—can significantly influence both the volume and direction of trades in a pool.

**Liquidity Depth**\
A higher total value locked (TVL) typically means greater stability for LPs, as large pools are less sensitive to price movements and impermanent loss.

**Price Range**\
A narrower price range increases capital efficiency and fee potential but also raises the risk of falling out of range and losing exposure. Broader ranges provide more flexibility but may yield lower returns per dollar deployed.

> Craft your liquidity strategy based on thorough research and market context to take full advantage of concentrated liquidity.
