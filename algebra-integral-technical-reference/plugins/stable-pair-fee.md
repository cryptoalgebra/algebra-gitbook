# Stable Pair Fee

### Overview <a href="#overview" id="overview"></a>

The purpose of the Stable Pair Fee Plugin is to price swaps in pools whose two assets are expected to hold the same value (USDC/USDT, ETH/stETH), without reading an external oracle at swap time.

The DEX configures a **reference price** - the price the pair is expected to trade at. The plugin then picks a fee for every swap so that the price the trader gets, fee included, stays inside a narrow corridor around the reference price. Liquidity providers therefore never sell an asset below its fair value, even when the pool price has drifted away from it.

The fee is calculated per direction and depends on where the pool price sits:

* **Inside the corridor**: both directions pay a small fee, and the trader is priced exactly at the corridor boundary.
* **Outside the corridor**: a swap that pushes the price further away pays nothing, while a swap that brings the price back pays an elevated fee that decays block by block.

### Details <a href="#details" id="details"></a>

The stable pair fee module works through a single Algebra plugin hook, `beforeSwap`. It sets `overrideFee`, so the pool must have the `DYNAMIC_FEE` flag enabled.

Notation used below, with all prices expressed as "how much token1 for one token0":

| Symbol | Meaning |
| ------ | ------- |
| `P` | Current pool price |
| `RP` | Reference price, from the plugin configuration |
| `f` | `optimalFee`, the corridor half-width |
| `L = RP * (1 - f)` | Lower corridor boundary |
| `U = RP / (1 - f)` | Upper corridor boundary |

The fee is derived from the **pre-impact price**: the price a trader gets once the fee is applied, ignoring slippage. Selling token0 (`zeroToOne`) yields `P * (1 - fee)`, buying token0 (`oneToZero`) costs `P / (1 - fee)`. The plugin never picks a fee directly - it picks a target pre-impact price and solves for the fee.

### Fee inside the corridor <a href="#fee-inside-the-corridor" id="fee-inside-the-corridor"></a>

While `L <= P <= U`, a seller of token0 always receives exactly `L` and a buyer always pays exactly `U`. The spread the trader sees is constant; only its split between price and fee changes.

$$
fee_{sell} = 1 - (1 - f) * RP / P
$$

$$
fee_{buy} = 1 - (1 - f) * P / RP
$$

At `P = RP` both directions pay exactly `f`. As the pool price rises, selling token0 gets more expensive and buying it gets cheaper; below the reference price it is the other way around.

Example for `RP = 1` and `optimalFee = 90` (0.009%), a corridor of \[0.99991; 1.00009]:

| Pool price | Sell fee | Seller receives | Buy fee | Buyer pays |
| ---------- | -------- | --------------- | ------- | ---------- |
| 1.00000 | 0.0090% | 0.99991 | 0.0090% | 1.00009 |
| 1.00005 | 0.0140% | 0.99991 | 0.0040% | 1.00009 |

### Fee outside the corridor <a href="#fee-outside-the-corridor" id="fee-outside-the-corridor"></a>

Outside the corridor the two directions are treated differently. A swap moving the price further from the reference pays **zero fee**: it hands the pool a better-than-fair price, so charging it would only drive that flow elsewhere. A swap moving the price back is the arbitrage leg - it takes the mispricing out of the pool - and it pays the decaying fee.

Three fee levels are computed for that corrective swap, where `r = min(P, RP) / max(P, RP)`:

| Level | Formula | Pre-impact price it produces |
| ----- | ------- | ---------------------------- |
| Close boundary fee | `1 - r / (1 - f)` | The nearer corridor boundary |
| Far boundary fee | `1 - (1 - f) * r` | The farther corridor boundary, maximum LP protection |
| Target fee | `far - close * m / 100` | Farther boundary plus `m`% of how far the pool price sits beyond the corridor |

`m` is `targetMultiplier`. The sign of the close boundary fee also decides which mode applies: zero or below means the price is inside the corridor, above zero means it is outside.

The fee starts at the far boundary fee and decays toward the target fee, keeping a fraction `k` of its excess every block:

$$
fee(n) = target + k^n * (start - target)
$$

This is a **Dutch auction** on the right to close the mispricing. The lot opens at the far boundary fee, where the pool keeps nearly all of the deviation, and gets cheaper every block until the reserve price - the target fee. The first arbitrageur for whom the trade clears fee and gas takes it, so competition runs on timing rather than on bid size, and no auctioneer is needed: a bid is just an ordinary swap. Short-lived deviations are therefore closed near the opening price and most of the arbitrage stays with liquidity providers, while a lasting deviation - a real depeg, or a stale reference price - keeps getting cheaper to close instead of leaving the pool stuck behind a prohibitive fee.

Example for `RP = 1`, `optimalFee = 90`, `k = 0.99`, `targetMultiplier = 50` and a pool price of 1.001, i.e. token0 trading 0.1% above fair value. Buying token0 pays no fee here; selling it pays:

| Blocks since the deviation | Sell fee | Seller receives |
| -------------------------- | -------- | --------------- |
| 0 | 0.1089% | 0.999910 (= `L`) |
| 69 (half-life) | 0.0862% | 1.000138 |
| 200 | 0.0695% | 1.000304 |
| 500 | 0.0637% | 1.000362 |

### Fee state <a href="#fee-state" id="fee-state"></a>

The plugin keeps three values per pool, written once per block on the first swap: the pool price at the start of that block, the current decaying fee, and the block number. Later swaps in the same block are priced from that stored price, so splitting one large swap into several gives no fee advantage.

On the first swap of a new block, when the price is outside the corridor, the auction's opening price is chosen by comparing the current price with the previous block:

| Price movement | Decay starts from | Reason |
| -------------- | ----------------- | ------ |
| Just left the corridor, or jumped across the reference price | Far boundary fee | A new deviation, maximum protection |
| Moved further away from the reference price | Previous fee, raised by the price movement | The arbitrage leg must not get a better price just because the pool drifted further |
| Moved back toward the reference price, previous fee above the new far boundary fee | Far boundary fee | The pre-impact price never gets worse than the farther boundary |
| Moved back toward the reference price, or did not move | Previous fee | Decay continues |

The starting fee is never below the target fee, and decay counts every block since the last write, including blocks with no swaps. Returning inside the corridor drops the decaying fee, so the next exit starts the auction over at the far boundary fee. Updating the configuration resets the state the same way.

### How To Configure the Stable Pair Fee Plugin <a href="#how-to-configure-stable-pair-fee-plugin" id="how-to-configure-stable-pair-fee-plugin"></a>

The whole configuration is one call on the pool's plugin:

**plugin.setStableFeeConfig(StableFeeConfig calldata feeConfig)**

| Parameter | Units | Allowed values | Controls |
| --------- | ----- | -------------- | -------- |
| `referenceSqrtPriceX96` | `sqrt(raw price) * 2^96` | ~4.32e9 to 1.454e48 | The fair price, center of the corridor |
| `optimalFeeE6` | 1 = 0.0001% | 0 to 10 000 (1%) | Corridor width, and the fee charged at `P = RP` |
| `k` | Q24, `2^24` = 1.0 | 1 to 16 777 215 | How fast the auction price falls |
| `targetMultiplier` | percent | 0 to 100 | The auction's reserve price |


`k` is the share of the excess fee that survives one block; its half-life is `ln 2 / (-ln k)` blocks. From a desired half-life of `n` blocks: `k = floor(2^24 * 0.5^(1/n))`.

| `k` | Value in the config | Half-life, blocks | On a 2s chain |
| --- | ------------------- | ----------------- | ------------- |
| 0.9 | 15099494 | 6.6 | ~13 s |
| 0.99 | 16609443 | 69 | ~2.3 min |
| 0.999 | 16760438 | 693 | ~23 min |

`targetMultiplier` decides where a long-lasting deviation settles. At 0 the reserve price equals the opening price, so the fee never decays and the trader is always priced at the farther corridor boundary. At 100 the corridor effectively follows the pool price. At 50 half of the deviation beyond the corridor is passed on to the trader.

{% hint style="warning" %}
The pool's own fee must be set to 0. In Algebra an override fee of 0 means "no override", so the pool falls back to its own fee, and the swaps that are supposed to be free would pay it. The plugin does not check this.
{% endhint %}

Further operational notes:

* Swaps revert with `StableFeeNotConfigured` until the configuration is set, so it should be set right after the pool is created.
* If the fair price of the pair moves over time (wstETH/ETH and other yield-bearing tokens), the reference price has to be refreshed, otherwise the pool leaves the corridor and honest swaps are charged the elevated fee.
* The plugin replaces the LP fee entirely, so it cannot be combined with other fee modules such as Dynamic Fee, Sliding Fee or Managed Swap Fee in one plugin.

Current values are exposed through `getStableFees()` - the fee the next swap would pay in each direction, in hundredths of a bip, for interfaces and routers - plus `stableFeeConfig()` and `stableFeeState()`.

### Roles <a href="#roles" id="roles"></a>

The plugin has a single admin role: `ALGEBRA_BASE_PLUGIN_MANAGER` can call `setStableFeeConfig(...)`.
Configuration is a per-pool decision - each pool keeps its own reference price and fee settings in its own plugin.