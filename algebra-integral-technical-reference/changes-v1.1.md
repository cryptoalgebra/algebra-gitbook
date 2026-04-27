---
icon: up-from-line
---

# Changes V1.1

The main change in Integral v1.1 is the ability to create multiple pools per pair. Plugin developers can create an unlimited number of pools with different plugins. There are several types of pools in v1.1:

* Base Pools. Dex pools that use the default plugins. One pool per pair can be created. Token0 and token1 addresses are used to calculate the pool address.
* Custom Pools. Pools of plugin builders, can be created one pool per pair with the specified custom deployer/custom plugin factory. Token0, token1 and custom pool deployer addresses are used to calculate the pool address.

Integral v1.1 code can be found in the [integral-v1.1](https://github.com/cryptoalgebra/Algebra/tree/integral-v1.1) branch

### Custom pool creation

For pools creation the plugin builder needs to create a special **customPoolDeployer** contract that will interact with[ AlgebraCustomPoolEntryPoint](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.1/src/periphery/contracts/AlgebraCustomPoolEntryPoint.sol) contract.

**customPoolDeployer** contract should have the following functionality:

1. A method to call [createCustomPool()](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.1/src/periphery/contracts/AlgebraCustomPoolEntryPoint.sol#L26) on the entryPoint contract
2. A method [beforeCreatePoolHook()](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.1/src/periphery/contracts/AlgebraCustomPoolEntryPoint.sol#L39), which can only be called by entryPoint contract and must return the address of the plugin.
3. Functionality that allows calling setTickSpacing(), setPlugin(), setPluginConfig(), setFee() on entryPoint contract. The ability to call these methods should only be available to trusted persons/deployer. If necessary, it is also possible to restrict the creation of pools.
4. The CUSTOM\_POOL\_DEPLOYER role is given to a contract AlgebraCustomPoolEntryPoint

### After pool creation hook

Another small change to integral 1.1 is the addition of an after pool creation hook to AlgebraFactory contract.
