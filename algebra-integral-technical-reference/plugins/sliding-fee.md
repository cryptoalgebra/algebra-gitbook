# Sliding Fee

## Overview

The sliding fee is calculated for each swap based on the price change of the last block and the swap direction. The fee is increased if the direction of the trade matches with the direction of the price change of the previous block, and decreased for swaps in the opposite direction. DEX sets the initial value of the fee, which is used as the base value for calculating the sliding fee. DEX can also set the coefficient of the price change impact on the fee value.

<figure><img src="../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

ZeroToOne and oneToZero feeFactors were introduced to keep track of fee changes. FeeFactors is changed to feeFactorImpact at each swap depending on the price movement. If the price of the pair has increased, oneToZeroFeeFactor will be increased and zeroToOneFeeFactor will be decreased and vice versa. FeeFactorImpact is calculated by the following formula.

$$
feeFactorImpact = (currentPrice/lastPrice - 1) * priceChangeFactor
$$

$$
slidingFeeValue = feeFactor * baseFee
$$

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

## How to configure sliding fee

### Contract Interaction

To set default baseFee,  it is necessary to call **pluginFactory.setDefaultBaseFee(uint16 newDefaultBaseFee)**, where **newDefaultBaseFee** is fee value in hundredths of a bip, i.e. 1e-6 (so 3000 is 0.3%)

To change the base fee of a specific pool, it is necessary to call the corresponding function of the plugin connected to the pool.

**plugin.setBaseFee(uint16 newBaseFee)**

To change the priceChangeFactor of a specific pool, it is necessary to call the corresponding function of the plugin connected to the pool. PriceChangeFactor is coefficient in thousandths(so 1000 is 1)

**plugin.setPriceChangeFactor(uint16 newPriceChangeFactor)**

### Additional materials

Learn more about Sliding Fee in our article

[The Impact of Market Conditions and Fee Algorithms on the Design of a Competitive AMM](https://algebra.finance/static/the-impact-of-market-conditions-and-fee-algorithms-on-the-design-of-a-competitive-amm.pdf)
