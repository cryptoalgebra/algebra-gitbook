---
icon: calculator-simple
---

# How APR is Calculated

{% hint style="info" %}
**Note for DEX Teams:**

Here you can explain how your platform calculates the APR shown to liquidity providers.

**Tailor the section to reflect your specific UI and reward mechanics. Ensure that any APR displayed on your frontend is clearly broken down and matches this explanation.**
{% endhint %}

In an Algebra-powered DEX with concentrated liquidity, each liquidity provider position earns its own fee share based on its specific price range. The displayed APR reflects a combination of earned swap fees and any additional rewards from active farming campaigns.

### LP Fee APR <a href="#lp-fee-apr" id="lp-fee-apr"></a>

<figure><img src="../.gitbook/assets/image (49).png" alt="" width="331"><figcaption></figcaption></figure>

• **dayFees**: daily fees in the pool (USD)

• **liquidity**: total liquidity in the pool

• **positionLiquidity**: liquidity provided by the position (in range)

• **amount0USD**: value of token 0 in USD

• **amount1USD**: value of token 1 in USD

{% hint style="info" %}
APR is calculated **every 2 minutes**
{% endhint %}
