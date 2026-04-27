# AlgebraFactory

Algebra factory

Is used to deploy pools and its dataStorages

## Modifiers

### onlyOwner

`modifier onlyOwner()` internal

## Variables

### address owner

Returns the current owner of the factory

_Developer note: Can be changed by the current owner via setOwner_

### address poolDeployer immutable

Returns the current poolDeployerAddress

### address farmingAddress

_Developer note: Is retrieved from the pools to restrict calling certain functions not by a tokenomics contract_

### address vaultAddress

### struct AdaptiveFee.Configuration baseFeeConfiguration

### mapping(address => mapping(address => address)) poolByPair

Returns the pool address for a given pair of tokens and a fee, or address 0 if it does not exist

_Developer note: tokenA and tokenB may be passed in either token0/token1 or token1/token0 order_

## Functions

### constructor

`constructor(address _poolDeployer, address _vaultAddress) public` public

| Name           | Type    | Description |
| -------------- | ------- | ----------- |
| \_poolDeployer | address |             |
| \_vaultAddress | address |             |

### createPool

`function createPool(address tokenA, address tokenB) external returns (address pool)` external

Creates a pool for the given two tokens and fee _Developer note: tokenA and tokenB may be passed in either order: token0/token1 or token1/token0. tickSpacing is retrieved from the fee. The call will revert if the pool already exists, the fee is invalid, or the token arguments are invalid._

| Name   | Type    | Description                                     |
| ------ | ------- | ----------------------------------------------- |
| tokenA | address | One of the two tokens in the desired pool       |
| tokenB | address | The other of the two tokens in the desired pool |

**Returns:**

| Name | Type    | Description                           |
| ---- | ------- | ------------------------------------- |
| pool | address | The address of the newly created pool |

### setOwner

`function setOwner(address _owner) external` external

Updates the owner of the factory _Developer note: Must be called by the current owner_

| Name    | Type    | Description                  |
| ------- | ------- | ---------------------------- |
| \_owner | address | The new owner of the factory |

### setFarmingAddress

`function setFarmingAddress(address _farmingAddress) external` external

_Developer note: updates tokenomics address on the factory_

| Name             | Type    | Description                         |
| ---------------- | ------- | ----------------------------------- |
| \_farmingAddress | address | The new tokenomics contract address |

### setVaultAddress

`function setVaultAddress(address _vaultAddress) external` external

_Developer note: updates vault address on the factory_

| Name           | Type    | Description                    |
| -------------- | ------- | ------------------------------ |
| \_vaultAddress | address | The new vault contract address |

### setBaseFeeConfiguration

`function setBaseFeeConfiguration(uint16 alpha1, uint16 alpha2, uint32 beta1, uint32 beta2, uint16 gamma1, uint16 gamma2, uint32 volumeBeta, uint16 volumeGamma, uint16 baseFee) external` external

Changes initial fee configuration for new pools _Developer note: changes coefficients for sigmoids: α / (1 + e^( (β-x) / γ)) alpha1 + alpha2 + baseFee (max possible fee) must be <= type(uint16).max gammas must be > 0_

| Name        | Type   | Description                                         |
| ----------- | ------ | --------------------------------------------------- |
| alpha1      | uint16 | max value of the first sigmoid                      |
| alpha2      | uint16 | max value of the second sigmoid                     |
| beta1       | uint32 | shift along the x-axis for the first sigmoid        |
| beta2       | uint32 | shift along the x-axis for the second sigmoid       |
| gamma1      | uint16 | horizontal stretch factor for the first sigmoid     |
| gamma2      | uint16 | horizontal stretch factor for the second sigmoid    |
| volumeBeta  | uint32 | shift along the x-axis for the outer volume-sigmoid |
| volumeGamma | uint16 | horizontal stretch factor the outer volume-sigmoid  |
| baseFee     | uint16 | minimum possible fee                                |
