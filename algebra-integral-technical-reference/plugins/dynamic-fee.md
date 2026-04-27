# Dynamic Fee

## Overview

In order to calculate the fee value, information about the price change for the last 24 hours from oracle is used. Based on dynamic fee configuration and pool’s volatility data from the oracle, the fee value is calculated and applied to the swaps. DEX owners can configure different behaviors for the dynamic fee: set the minimum and maximum value of the fee and control volatility’s impact on the fee.

## How to set up specific formula behavior

### How to make a fixed fee?

set `alpha1` and `alpha2` as 0, set `baseFee` as you want

### How to increase/decrease max fee?

change `alpha1` and/or `alpha2`

### How to increase/decrease min fee?

change `baseFee`

### Adaptive fee is overreacting

Increase `beta1` and/or `beta2`. Also it's better to change the `gamma` accordingly

### Adaptive fee is weakly responsive to volatility

Decrease `beta1` and/or `beta2`. Also it's better to change the `gamma` accordingly

### When will adaptive fee “calm down”?

Formulas take into account volatility and volume over the last 24 hours



## How to tweak formula behaviour

Sigmoid coefficients Fee calculation can be tuned by the following parameters:

```
  struct Configuration {
    uint16 alpha1; // max value of the first sigmoid
    uint16 alpha2; // max value of the second sigmoid
    uint32 beta1; // shift along the x-axis for the first sigmoid
    uint32 beta2; // shift along the x-axis for the second sigmoid
    uint16 gamma1; // horizontal stretch factor for the first sigmoid
    uint16 gamma2; // horizontal stretch factor for the second sigmoid
    uint16 baseFee; // minimum possible fee
  }
```

Adaptive fee function is a combination of several sigmoids. The plot of the standard sigmoid function looks like this:

![Adaptive fee sigmoid](<../../.gitbook/assets/2560px-Sigmoid-function-2.svg (1).png>)

We use a more flexible formula for sigmoids:

![Flexible formula](https://lh4.googleusercontent.com/YnM6CV4_WlDTCWrIYEz_L8NAhHadZYc_A1-BV70_SIgUocb9RRMzo9RSAgN5bZFgD2_EWNzvJg76hzCYQrSAabqg-VxjukwW2k65Ocrp-aaRxA6wns8mV3E1DdK6c8i8bWTfR-mXyJMvPcg6duJPbhYUusyfEGSD3SQ5Gfh4pXSBqFcOH0TXwSMb8g)

So additional params (alpha, gamma, beta) allow us to shift, stretch and modify sigmoids. By changing the coefficients, it is possible to provide a wide range of different options:

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption><p>Sigmoids</p></figcaption></figure>

Default params:

```
      2900, // alpha1
      12000, // alpha2
      360, // beta1
      60000, // beta2
      59, // gamma1
      8500, // gamma2
      100 // baseFee
```

So

**max fee** value is: `baseFee` + `alpha1` + `alpha2`&#x20;

**min fee** value is: `baseFee`

**midpoint** of the first sigmoid: `beta1`              **midpoint** of the second sigmoid: `beta2`

sigmoid “jump” “starts” around `beta - 6 * gamma`, ends around `beta + 6 * gamma`

![Sigmoid jumps](https://lh3.googleusercontent.com/D0z4h9kG5RqtDOPjLT6fTIpPVYKSIw5v7M64Y0uYd2eELLco11Np4z8PLBo0lFIpFpYCZU52uSOiGnGR1PkjXJz5vRAqmswHxqxe0zsFeFBVwcVzYUj2Y625gzldZKdZxQYJbeT3fnNPkCGUizY-Gy6MgV-_nNRiAecag0kXFgxpTlfWegvjo3fHpQ)

`alpha1`, `alpha2`, `beta1`, `beta2`, `gamma1`, `gamma2` – coefficients for volatility sigmoids.

### Contract Interaction

To change the adaptive fee configuration of a specific pool, it is necessary to call the corresponding function of the plugin connected to the pool.

**plugin.changeFeeConfiguration(AlgebraFeeConfiguration calldata \_config)**

To change the default adaptive fee config that will be applied to the new pools, it is necessary to call the corresponding function of the pluginFactory.

**pluginFactory.setDefaultFeeConfiguration(AlgebraFeeConfiguration calldata newConfig)**

### Additional materials

Learn more about Adaptive Fee in our Tech Paper

[Algebra Protocol Tech Paper](https://algebra.finance/static/Algerbra%20Tech%20Paper-15411d15f8653a81d5f7f574bfe655ad.pdf)
