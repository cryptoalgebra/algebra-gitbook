---
icon: scroll
---

# Liquidity Scenarios & Risk Profiles

{% hint style="info" %}
**Note for DEX Teams:**

This section provides illustrative simulations to help users understand how concentrated liquidity behaves under different LP strategies—active rebalancing, static, and wide-range. These examples are based on a generic TOKEN/USDT pool and demonstrate how market movements impact position value.&#x20;

While simplified, they offer a valuable reference for educating LPs on impermanent loss, range management, and strategic planning. We recommend customizing these scenarios with your own token pairs to better match your platform's context.
{% endhint %}

## Liquidity Scenarios

_Illustrative examples for a generic TOKEN / USDT pool_

To show how concentrated‑liquidity positions behave under different management styles, we simulated three price paths for a TOKEN / USDT pair. The exercise compares:

| Strategy                       | Description                                                                         |
| ------------------------------ | ----------------------------------------------------------------------------------- |
| **Active LP (Rebalancing)**    | Uses a **narrow range** ($5.40–$6.60) and redeploys whenever price leaves the band. |
| **Static LP (Set‑and‑Forget)** | Same **narrow range**, but **never** rebalances.                                    |
| **Wide‑Range LP**              | Provides liquidity in a **broad range** ($4.80–$7.20) and never rebalances.         |

**Assumptions**

* Initial TOKEN price: **$6.00**
* Initial portfolio value: **$1 000** (50 % TOKEN, 50 % USDT)
* No farming or swap‑fee income is included—only price impact and impermanent loss.

### Scenario #1 – Upward Momentum

Price rises 10 % twice, reaching **$7.26**. The Active LP rebalances once at **$6.60**.

| Strategy      | Ending Value  | % Change    |
| ------------- | ------------- | ----------- |
| Active LP     | **$1 049.40** | **+4.94 %** |
| Static LP     | $1 024.40     | +2.44 %     |
| Wide‑Range LP | **$1 050.00** | **+5.00 %** |

_Result:_ Active LP outperforms static but slightly trails the wide‑range approach in a strong up‑trend.

### Scenario #2 – Downward Momentum

Price falls 10 % twice, touching **$4.86**. The Active LP rebalances once at **$5.40**.

| Strategy      | Ending Value | % Change     |
| ------------- | ------------ | ------------ |
| Active LP     | **$867.28**  | **‑13.27 %** |
| Static LP     | $846.62      | ‑15.34 %     |
| Wide‑Range LP | **$867.77**  | **‑13.22 %** |

_Result:_ Active LP limits losses versus static, but wide‑range still fares slightly better.

### Scenario #3 – Oscillating Market

Price moves up to **$6.60** and then returns to **$6.00**.

| Strategy      | Ending Value | % Change    |
| ------------- | ------------ | ----------- |
| Active LP     | **$954.00**  | **‑4.60 %** |
| Static LP     | $1 000.00    | 0 %         |
| Wide‑Range LP | $1 000.00    | 0 %         |

_Result:_ In a whipsaw, a single rebalance locks in loss; passive ranges preserve capital.

## Key Takeaways

* **Active, narrow ranges** can boost returns in trending markets but require timely rebalancing and carry higher downside risk if trends reverse.
* **Static narrow ranges** are simple yet vulnerable to large moves.
* **Wide ranges** reduce maintenance and price‑out risk but dilute fee capture (not shown in this fee‑free model).

Choose a strategy that matches your market outlook, time commitment, and risk tolerance.
