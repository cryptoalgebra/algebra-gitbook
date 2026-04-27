# AlgebraFactory

Algebra factory

Is used to deploy pools and its dataStorages

_Developer note: Version: Algebra V2-directional-fee_

## Variables

### bytes32 POOLS\_ADMINISTRATOR\_ROLE constant

role that can change communityFee and tickspacing in pools

### address poolDeployer immutable

Returns the current poolDeployerAddress

### address communityVault immutable

Returns the current communityVaultAddress

### address farmingAddress

_Developer note: Is retrieved from the pools to restrict calling certain functions not by a tokenomics contract_

### uint8 defaultCommunityFee

Returns the default community fee

### uint256 renounceOwnershipStartTimestamp

### struct AlgebraFeeConfiguration defaultFeeConfiguration

_Developer note: values of constants for sigmoids in fee calculation formula_

### mapping(address => mapping(address => address)) poolByPair

Returns the pool address for a given pair of tokens, or address 0 if it does not exist

_Developer note: tokenA and tokenB may be passed in either token0/token1 or token1/token0 order_

## Functions

### constructor

`constructor(address _poolDeployer) public` public

| Name           | Type    | Description |
| -------------- | ------- | ----------- |
| \_poolDeployer | address |             |

### owner

`function owner() public view returns (address)` view public

Returns the current owner of the factory _Developer note: Can be changed by the current owner via transferOwnership(address newOwner)_

**Returns:**

| Name | Type    | Description                      |
| ---- | ------- | -------------------------------- |
| \[0] | address | The address of the factory owner |

### hasRoleOrOwner

`function hasRoleOrOwner(bytes32 role, address account) public view returns (bool)` view public

_Developer note: Returns \`true\` if \`account\` has been granted \`role\` or \`account\` is owner._

| Name    | Type    | Description |
| ------- | ------- | ----------- |
| role    | bytes32 |             |
| account | address |             |

**Returns:**

| Name | Type | Description |
| ---- | ---- | ----------- |
| \[0] | bool |             |

### createPool

`function createPool(address tokenA, address tokenB) external returns (address pool)` external

Creates a pool for the given two tokens _Developer note: tokenA and tokenB may be passed in either order: token0/token1 or token1/token0. The call will revert if the pool already exists or the token arguments are invalid._

| Name   | Type    | Description                                     |
| ------ | ------- | ----------------------------------------------- |
| tokenA | address | One of the two tokens in the desired pool       |
| tokenB | address | The other of the two tokens in the desired pool |

**Returns:**

| Name | Type    | Description                           |
| ---- | ------- | ------------------------------------- |
| pool | address | The address of the newly created pool |

### setFarmingAddress

`function setFarmingAddress(address newFarmingAddress) external` external

_Developer note: updates tokenomics address on the factory_

| Name              | Type    | Description                         |
| ----------------- | ------- | ----------------------------------- |
| newFarmingAddress | address | The new tokenomics contract address |

### setDefaultCommunityFee

`function setDefaultCommunityFee(uint8 newDefaultCommunityFee) external` external

_Developer note: updates default community fee for new pools_

| Name                   | Type  | Description                                             |
| ---------------------- | ----- | ------------------------------------------------------- |
| newDefaultCommunityFee | uint8 | The new community fee, _must_ be <= MAX\_COMMUNITY\_FEE |

### setDefaultFeeConfiguration

`function setDefaultFeeConfiguration(struct AlgebraFeeConfiguration newConfig) external` external

Changes initial fee configuration for new pools _Developer note: changes coefficients for sigmoids: α / (1 + e^( (β-x) / γ)) alpha1 + alpha2 + baseFee (max possible fee) must be <= type(uint16).max and gammas must be > 0_

| Name      | Type                           | Description                                                                 |
| --------- | ------------------------------ | --------------------------------------------------------------------------- |
| newConfig | struct AlgebraFeeConfiguration | new default fee configuration. See the #AdaptiveFee.sol library for details |

### startRenounceOwnership

`function startRenounceOwnership() external` external

Starts process of renounceOwnership. After that, a certain period of time must pass before the ownership renounce can be completed.

### stopRenounceOwnership

`function stopRenounceOwnership() external` external

Stops process of renounceOwnership and removes timer.

### renounceOwnership

`function renounceOwnership() public` public

_Developer note: Leaves the contract without owner. It will not be possible to call \`onlyOwner\` functions anymore. Can only be called by the current owner if RENOUNCE\_OWNERSHIP\_DELAY seconds have passed since the call to the startRenounceOwnership() function._
