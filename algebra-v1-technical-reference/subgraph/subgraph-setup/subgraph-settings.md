# Subgraph Settings

Before deploying subgraphs you need to tweak some things to your needs:

## Algebra

### Constants.ts

[Algebra/src/utils/constants.ts](https://github.com/cryptoalgebra/Algebra_Subgraph/blob/main/Algebra/src/utils/constants.ts)

`FACTORY_ADDRESS` is the address of the AlgebraFactory.sol contract

```
export const FACTORY_ADDRESS = '0x9742E5C4452ccA62ce115d302756e9150CbA36Aa'
```

### Pricing.ts

[Algebra/src/utils/pricing.ts](https://github.com/cryptoalgebra/Algebra_Subgraph/blob/main/Algebra/src/utils/pricing.ts)

`WMatic_ADDRESS` is the address of your network Native Token

```
const WMatic_ADDRESS = '0x0d500b1d8e8ef31e21c99d1db9a6444d3adf1270'
```

`USDC_WMatic_03_POOL` is the address of the Native Token / USDC pair pool

```
const USDC_WMatic_03_POOL = '0xc3c4074fbc2d504fb8ccd28e3ae46914a1ecc5ed'
```

`WHITELIST_TOKENS` is the array of tokens, which many tokens usually are paired with, so we can trust their volume and liquidity

```
export let WHITELIST_TOKENS: string[] = [
  '0x0d500b1d8e8ef31e21c99d1db9a6444d3adf1270', // WMATIC
  '0x2791bca1f2de4661ed88a30c99a7a9449aa84174', // USDC
  '0xc2132d05d31c914a87c6611c10748aeb04b58e8f' // USDT 
]

```

`STABLE_COINS` is the array of stablecoins

```
let STABLE_COINS: string[] = [
  '0x2791bca1f2de4661ed88a30c99a7a9449aa84174', // USDC
  '0xc2132d05d31c914a87c6611c10748aeb04b58e8f' // SUDT
]
```

`MINIMUM_Matic_LOCKED` is the required number of native tokens in the pool to include it when calculating prices

```
let MINIMUM_Matic_LOCKED = BigDecimal.fromString('0')
```

### Subgraph.yaml

[Algebra/subgraph.yaml](https://github.com/cryptoalgebra/Algebra_Subgraph/blob/main/Algebra/subgraph.yaml)

`network` is your network name

```
network: matic
```

For every contract you should change its `startBlock` - the block to sync from, and `address` from your contract deployment

```
 source:
    ...
      address: '0x9742E5C4452ccA62ce115d302756e9150CbA36Aa'
      startBlock: 31656556 
```

## AlgebraFarming

### Constants.ts

[AlgebraFarming/src/utils/constants.ts](https://github.com/cryptoalgebra/Algebra_Subgraph/blob/main/AlgebraFarming/src/utils/constants.ts)

`FarmingCenterAddress` is the FarmingCenter contract address from your contracts deployment

```
export const FarmingCenterAddress = Address.fromString("0xB1d4784473277c162E36656fa066dfEd21B1EC89")
```

### Subgraph.yaml

[AlgebraFarming/subgraph.yaml](https://github.com/cryptoalgebra/Algebra_Subgraph/blob/main/AlgebraFarming/subgraph.yaml)

`network` is your network name

```
network: matic
```

For every contract you should change its `startBlock` - the block to sync from, and `address` from your contract deployment

```
 source:
    ...
      address: '0x9742E5C4452ccA62ce115d302756e9150CbA36Aa'
      startBlock: 31656556 
```

## PolygonBlocks

### Subgraph.yaml

[polygonBlocks/subgraph.yaml](https://github.com/cryptoalgebra/Algebra_Subgraph/blob/main/polygonBlocks/subgraph.yaml)

`network` is your network name

```
network: matic
```
