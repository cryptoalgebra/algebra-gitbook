# FAQ

<details>

<summary>How to get a DEX listed</summary>

**The first and foremost form to fill is** [**Geckoterminal's**](https://docs.google.com/forms/d/e/1FAIpQLSfhkTVTPsceZ0-qVy470MMeu0vGouFE2EdzU6xEgm3CWcgnrA/viewform)\
Afterwards, you can fill in any order:

* [CoinGecko](https://support.coingecko.com/hc/en-us/articles/4497658821273-Guide-CoinGecko-Exchange-Listing-Request-Form)
* DEXScreener \
  [Recommended version of adapter](https://github.com/cryptoalgebra/dexscreener-adapter/tree/feature/reserves-support) (requires the latest version of AlgebraSubgraph)\
  [Cut version of adapter](https://github.com/cryptoalgebra/dexscreener-adapter/tree/main) without reserves (optional) and event indexes.
* [CoinMarketCap](https://support.coinmarketcap.com/hc/en-us/requests/new?ticket_form_id=360000493132)
* DefiLlama - examples for [tvl](https://github.com/DefiLlama/dimension-adapters/blob/master/dexs/blackhole-CL.ts) and [fees](https://github.com/DefiLlama/dimension-adapters/blob/master/fees/blackhole-CL.ts)

</details>

<details>

<summary>How to change the price in a pool when the current tick in on the boundary</summary>

1. Create a new position.\
   Input the minimum and maximum price manually to set the range without using a preset, ensuring it's close to the correct price. A small amount of liquidity is sufficient. It will be a single token deposit since the current pool tick is not between the position ticks.
2. Make a swap. \
   If you deposited 1 `token0` in step 1, you should proceed to a swap _from_ `token1` _to_ an amount less than 1 of `token0`.



</details>

<details>

<summary>How to get the swap fee</summary>

## For Integral 1.2.

### **Onchain**:

no way to get it, because

\- fee can be set by plugin

\- fee set by plugin may depend on swap (amount, direction).

\- plugin can set fee via .setFee() method of pool, writing the value to pool storage

\- plugin can return fee via overrideFee

\- plugin can set pluginFee

Since plugins (anyone can make them) are not required to have some sort of .getFee function with swap parameters that would return what the fee would be on a given swap, the only reliable way to find out the fee is on the beforeSwap() hook. Since it is called in the actual swap, it will set the fee via .setFee() or overrideFee.

Unfortunately, there is no way to call it onchain.

### **Offchain**:

1. Make a call to a quoter (the fee set by beforeSwap via .setFee() will be visible)
2. Make a call via RPC to the beforeSwap method on the pool plugin, specifying from=pool\_address (because there is a check there). The values of overrideFee and pluginFee will be returned.
3. If `0` is returned from p2, then use the fee from p1. This means that the plugin does not override, but sets it via .setFee().



## For Integral 1.2.1.

### Onchain:

No way to get it, because

\- fee can be set by plugin

\- fee set by plugin may depend on swap (amount, direction).

\- plugin can return fee via overrideFee (if DynamicFee flag is on)

\- plugin can set pluginFee

Since plugins (anyone can make them) are not required to have some sort of .getFee function with swap parameters that would return what the fee would be on a given swap, the only reliable way to find out the fee is on the beforeSwap() hook. Since it is called in the actual swap, it will set the fee via overrideFee.

Unfortunately, there is no way to call it onchain.

### **Offchain**:

Make a call via RPC to the beforeSwap method on the pool plugin, specifying from=pool\_address (because there is a check there). The values of overrideFee and pluginFee will be returned.

</details>

<details>

<summary>Farming: How to obtain position's uncollected reward amounts</summary>

Reward are collected on certain events:

a tick cross, when a user adds/removes liquidity; reward rate change or reward amount change by the owner.

The static call of `CollectRewards()` updates rewards as well, ensuring you receive the actual amounts

`getRewardInfo()` has the info from the last time one of the mentioned events happened, doesn't include all uncollected rewards&#x20;

</details>
