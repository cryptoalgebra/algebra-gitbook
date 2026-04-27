# Deployment Scripts

Deployment scripts are used to automaticaly change constants and addresses in contracts. Before deployment, please change these addresses to meet your needs:

### Core

[core/scripts/deploy.js](https://github.com/cryptoalgebra/Algebra/blob/master/src/core/scripts/deploy.js)

`vault` is the address, which will get fees from performed swaps

```
const vault = "0xBe56E9aA7792B2f1F4132631B7A0E1927090D78A";
```

### Periphery

[periphery/scripts/deploy.js](https://github.com/cryptoalgebra/Algebra/blob/master/src/periphery/scripts/deploy.js)

`WNativeTokenAddress` is the address of wrapped native token of the specific chain

```
 const WNativeTokenAddress = "0x0d500b1d8e8ef31e21c99d1db9a6444d3adf1270";
```

### Farming

[tokenomics/scripts/deploy.js](https://github.com/cryptoalgebra/Algebra/blob/master/src/tokenomics/scripts/deploy.js)

`incentiveMaker` is the address which has access to creating and managing farmings

```
  const incentiveMaker = "0xDeaD1F5aF792afc125812E875A891b038f888258";
```
