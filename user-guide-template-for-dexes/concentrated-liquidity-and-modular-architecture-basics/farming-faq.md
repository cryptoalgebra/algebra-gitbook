---
icon: comments-question-check
---

# Farming FAQ

{% hint style="info" %}
**Note for DEX Teams:**&#x20;

Farming functionality on Algebra-powered DEXes is highly customizable. While this guide outlines the **default mechanics**, DEX teams can **adjust farming formulas, reward distribution logic, token multipliers, and update intervals** based on their unique economic models or ecosystem goals.
{% endhint %}

<details>

<summary>Do I need to take any extra steps to participate in farming?</summary>

No, once you mint a liquidity position in a pool with farming enabled, it automatically participates. No additional steps are needed from your side.

</details>

<details>

<summary>How does farming work on an Algebra-powered DEX?</summary>

Farming acts as a boost to your LP fee earnings. If farming is active for a pool, you'll receive additional reward tokens (defined by the farming campaign) based on the fees your position earns — as long as your liquidity stays within the active price range.

</details>

<details>

<summary>How is the farming boost calculated?</summary>

The farming boost is calculated based on the farming multiplier:

_rewardToken = farming.rewardRate \* INTERVAL \* rewardToken.derivedToken_

_farmingMultiplier=rewardsTOKEN / FeeCollectedTotalTOKEN_

**Key Terms:**

* `farming.rewardRate`: Amount of reward token distributed per interval
* `INTERVAL`: Length of time between updates (e.g., every 3 hours)
* `rewardToken.derivedToken`: Value of the reward token expressed in the DEX’s main token (e.g., ETH)
* `feeCollectedTotal`: Total fees collected across the pool during the interval

</details>

<details>

<summary>Do I still earn farming rewards if there’s no trading activity?</summary>

No. Farming rewards depend on trading activity. If no trades occur, no rewards are generated.

</details>

<details>

<summary>What happens if my liquidity position goes out of range?</summary>

If your position is out of range, it won’t earn farming rewards or trading fees. Once the price moves back into range, rewards resume automatically. No action is needed to restart farming.

</details>

<details>

<summary>Can multiple positions earn farming rewards at once?</summary>

Yes. Each liquidity position is treated separately and can earn farming rewards individually. There's no cap on how many positions can participate.

</details>

<details>

<summary>How do I claim farming rewards?</summary>

You can claim rewards for eligible positions on either the **Pools** or **Farms** page. Each LP position must be claimed separately.

</details>

<details>

<summary>Do I have to pay gas fees to claim rewards?</summary>

Yes. Each reward claim requires a small blockchain transaction fee. To save on costs, you can wait until the farming period ends and claim all rewards in one batch using the **Ended** tab.

</details>

<details>

<summary>Does my selected price range affect my farming rewards?</summary>

Yes. A narrower price range concentrates your liquidity, giving you a larger relative share of the pool's active liquidity and higher farming rewards. Wider ranges are more passive but typically earn fewer rewards due to lower concentration.

</details>



