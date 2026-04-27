# API Reference v2.0 - Directional Fees

Welcome to Algebra Protocol API v2.0 — Directional Fees Reference!

## Algebra V2 API changelog

This document briefly describes major changes to Algebra V2 contracts that may be important for projects integrating the protocol.

[Algebra V2 branch](https://github.com/cryptoalgebra/Algebra/tree/v2.0.0)

#### Solidity 0.8.17

The most basic change that affected almost all contracts is the transition to a new version of Solidity (0.8.17). In order to maintain backward compatibility, the version directive of interfaces has been kept as >=0.5.0 where possible.

#### Gas optimizations

When contracts were changed, significant gas optimization was carried out. The effectiveness of optimizations becomes stronger over time.

### AlgebraPool changes

#### Supported tokens

The new version of the protocol **does not** support tokens whose total supply can exceed "2^128 - 1" (max value of `uint128`). This limitation allows us to make contracts more gas-efficient and predictable for the vast majority of tokens.

The new version **has wider support for rebase tokens**: in the event of a rebase and an increase in the pool balance, the resulting excess will be distributed among active liquidity providers. In the first version of Algebra, as in Uniswap V3 and other implementations of concentrated liquidity, this excess of tokens would simply be stuck on the pool contract forever.

#### GlobalState structure

Due to other pool changes, the `globalState` structure has changed. Added field `prevInitializedTick` - the previous initialized tick in linked list (relative to the current global tick). Fields `communityFeeToken0` and `communityFeeToken1` replaced with single field `communityFee`.

#### Changeable tickspacing

In the first version of the protocol, tickspacing was an immutable constant within pools. In version 2, tickspacing can be changed at any time. This allows the protocol to more accurately and efficiently set up pools for different kinds of tokens. The default tickspacing is 60. Contracts interacting with Algebra V2 pools must take into account the possibility that tickspacing will change. MAX\_LIQUIDITY\_PER\_TICK is fixed based on tickspacing value 1.

#### Data in ticks

Removed field `outerTickCumulative` from Tick structure. Added fields required for the implementation of a doubly linked list and limit orders. See the contract documentation for details.

#### Tick data structure

Now ticks are stored as a doubly linked list - each tick contains the indices of the next (`nextTick`) and previous (`prevTick`) ticks. The `tickTable` mapping is saved and used in the search tree when inserting and deleting new ticks. The `tickTable` mapping structure is similar to the same mapping in the first version of the protocol.

#### Position structure

Removed the `lastLiquidityAddTimestamp` field due to the removal of the entire liquidity cooldown mechanism.

Changed the data type of the `liquidity` field to `uint256`. **Important:** now the Position structure can be used to store information not only about liquidity positions, but also about limit orders. A limit order is a position with the same upper and lower ticks. In the case of a limit order, the `liquidity` field has a completely different structure and meaning (two packed `uint128` values).

#### Timepoints and TWAP oracle data

Now, to get data about timepoints, you need to access the corresponding DataStorageOperator contract directly. It is recommended to use the method `getTimepoints(uint32[] memory secondsAgos)`. In the first version, it was possible to access the timepoints via pool. Now you need to access the DataStorageOperator, the address of which for a given pool can be found through the immutable `dataStorage` variable in pool. The `Timepoint` structure has been slightly changed: fields `secondsPerLiquidityCumulative` and `volumePerLiquidityCumulative` have been removed. See contract documentation for more details.

#### Custom errors

The second version of the protocol uses custom errors added to Solidity instead of of text messages in `require`. This allows us to increase the readability and informativeness of errors, while reducing the size of the bytecode. Pool errors moved to a separate interface **IAlgebraPoolErrors**.

#### Community fee setup and transfer

1. Now community fee is regulated by one parameter (`communityFee`) instead of two.
2. You can also now configure what value of `communityFee` will be set in new pools by default. This makes it easier to set up new pools.
3. In order to save gas, community fee is no longer sent with each swap, but is accumulated and sent at a specified frequency.
4. Now all community fee is sent to a pre-specified contract AlgebraCommunityVault. This greatly reduces gas costs and increases the security of the protocol.

#### Adaptive fee configuration

The adaptive fee configuration has been simplified. Params `volumeBeta` and `volumeGamma` have been removed along with the corresponding sigmoid.

#### Liquidity cooldown removed

The new version of the protocol removed the liquidity cooldown mechanic, which was intended to protect against JIT-liquidity in the first version. Practice has shown that this is not necessary and not effective.

### Farming changes

#### Limit farmings removed

In the new version of the protocol, one type of farming is left - eternal (V2-like) farming. Limit farms were almost never used and their removal allows to significantly simplify contracts and increase security.

#### Transfer of position NFT is no longer needed

In the new version, users no longer need to send the NFT of their position to the farming address. In addition, users can increase and decrease liquidity without having to exit and re-enter farming.

### Roles and AccessControl

Now the AlgebraFactory contract implements the IAccessControl interface and the role system. This allows the protocol to fine-tune the permissions to perform individual actions in the protocol.

The `owner` of the AlgebraFactory is a superuser and has the rights to all actions in the standard contracts of the Algebra V2 protocol. We recommend transferring the owner's rights to a multisig or a special contract to reduce risks.

Protocol can assign the following roles to different addresses:

* `keccak256('POOLS_ADMINISTRATOR')` - allows to change tickspacing and community fee in pools
* `keccak256('COMMUNITY_FEE_WITHDRAWER')` - allows you to withdraw accumulated community fees from the AlgebraCommunityVault contract
* `keccak256('FEE_CONFIG_MANAGER')` - allows to change adaptive fee configuration in pools
* `keccak256('NONFUNGIBLE_POSITION_MANAGER_ADMINISTRATOR_ROLE')` - allows to change FarmingCenter address in NonfungiblePositionManager contract
* `keccak256('INCENTIVE_MAKER_ROLE')` - allows to create new farmings, change rates and deactivate old farmings in AlgebraEternalFarming contract
* `keccak256('FARMINGS_ADMINISTRATOR_ROLE')` - allows to change FarmingCenter address in AlgebraEternalFarming contract and withdraw unspent rewards from farmings

In addition, the protocol can create new roles and use them in its other contracts.

### CommunityVault

Accumulated community fee is now sent to a pre-specified contract AlgebraCommunityVault. This greatly reduces gas costs and increases the security of the protocol. An address with the appropriate rights (AlgebraFactory owner or `keccak256('COMMUNITY_FEE_WITHDRAWER')`) can withdraw tokens from this vault.

### Built-in limit orders

The second version of the Algebra protocol adds new functionality - built-in limit orders. These limit orders are closed automatically on-chain and do not require outside participation.

Limit orders can be placed on ticks and are executed when that tick is crossed during swaps. For finer tuning, limit orders use their own tickspacing - `tickSpacingLimitOrders`. If `tickSpacingLimitOrders` is set to `type(int24).max`, then limit orders in the pool will be completely prohibited.

The presence of limit orders can lead to the fact that swaps in different directions will not be "symmetrical" - it is not guaranteed that after a swap in one direction, you can return to the original price with the same equal opposite swap. This can make it harder to manipulate price with flashloans, as well as using pools for "temporary" swaps within flashloans.

To create a limit order, cancel it or withdraw the result, the same methods are used as for a regular liquidity position, but the used tick is indicated both as the lower and upper ticks of the position (this looks like a position with the same upper and lower ticks) .
