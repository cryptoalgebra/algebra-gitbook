---
icon: laptop-code
---

# Plugin Development

## Introduction

Algebra introduces **plugins**, custom smart contracts connected to a pool, allowing developers to inject custom logic into pool lifecycle events (e.g., swaps, liquidity provision). Plugins enable advanced features like dynamic fees, limit orders, TWAP oracles, and more.&#x20;

In this guide we are going to walk through the process of creating and testing Algebra Plugin. As an example, let's take a plugin that determines the commission in a pool&#x20;

Which questions we will answer:&#x20;

* How to start writing a plugin&#x20;
* How the pool interacts with a plugin&#x20;
* What are the flags
* How to earn fees with a plugin

## Setup

Algebra provides a repository template with required dependencies, configs and examples of source code and tests.

Clone this repository to setup a project:

```bash
git clone https://github.com/cryptoalgebra/algebra-plugin-template
cd algebra-plugin-template
// requires node with npm
npm install
```

## Basic things

Let's create our plugin <kbd>DynamicFeePlugin.sol</kbd>  by starting with this code:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

import '@cryptoalgebra/integral-core/contracts/libraries/Plugins.sol';
import '@cryptoalgebra/abstract-plugin/contracts/AbstractPlugin.sol';

contract DynamicFeePlugin is AbstractPlugin {
    using Plugins for uint8;

    uint8 public constant override defaultPluginConfig =
    uint8(Plugins.BEFORE_SWAP_FLAG | Plugins.BEFORE_POSITION_MODIFY_FLAG | Plugins.DYNAMIC_FEE);

    constructor(
        address _pool,
        address _pluginFactory
    ) AbstractPlugin(_pool, _pluginFactory) {}
}

```

Above code snippet does the following:

* Imports <kbd>Plugins</kbd> library and AbstractPlugin abstract contract
* Sets `defaultPluginConfig` with before\_swap, before\_position\_modify, dynamic\_fee flags
* Initialises `AbstractPlugin's` constructor by providing pool's and plugin factory's addresses

Setting such flags means that we would like a pool to call <kbd>beforeSwap</kbd>, <kbd>beforeModifyPosition</kbd> hooks and that our plugin is going to manipulate a pool's fees. We chose these particular hooks because we would like to demonstrate how to alter fees on swap and burn interactions.

Pool's and plugin factory's addresses are going to be passed when the plugin is deployed by plugin factory.

Now we can proceed to adding HOOOKS!!!

Firstly, we should add <kbd>beforeInitialize</kbd> hook handler. This one is going to be called when the pool is initialized. In that handler we want to set plugin config (flags) in the pool. Primarily, this is done to limit hooks which are called on the plugin to save gas.

```solidity
/// @dev Will be called regardless of flags
function beforeInitialize(address, uint160) external override onlyPool returns (bytes4) {
    // Here we initially set the flags
    _updatePluginConfigInPool(defaultPluginConfig);
    return IAlgebraPlugin.beforeInitialize.selector;
}
```

Then, the same way we are doing with all other hooks that we would not like to process:

```solidity
/// @dev Considering defaultPluginConfig, is unused
function afterInitialize(address, uint160, int24) external override onlyPool returns (bytes4) {
    // Add this line to reset config since the hook is unused
    _updatePluginConfigInPool(defaultPluginConfig);
    return IAlgebraPlugin.afterInitialize.selector;
}

/// @dev Considering defaultPluginConfig, is unused
function afterModifyPosition(address, address, int24, int24, int128, uint256, uint256, bytes calldata) external override onlyPool returns (bytes4) {
    // Add this line to reset config since the hook is unused
    _updatePluginConfigInPool(defaultPluginConfig);
    return IAlgebraPlugin.afterModifyPosition.selector;
}

/// @dev Considering defaultPluginConfig, is unused
function afterSwap(address, address, bool, int256, uint160, int256, int256, bytes calldata) external override onlyPool returns (bytes4) {
    // Add this line to reset config since the hook is unused
    _updatePluginConfigInPool(defaultPluginConfig);
    return IAlgebraPlugin.afterSwap.selector;
}

/// @dev Considering defaultPluginConfig, is unused
function beforeFlash(address, address, uint256, uint256, bytes calldata) external override onlyPool returns (bytes4) {
    // Add this line to reset config since the hook is unused
    _updatePluginConfigInPool(defaultPluginConfig);
    return IAlgebraPlugin.beforeFlash.selector;
}

/// @dev Considering defaultPluginConfig, is unused
function afterFlash(address, address, uint256, uint256, uint256, uint256, bytes calldata) external override onlyPool returns (bytes4) {
    // Add this line to reset config since the hook is unused
    _updatePluginConfigInPool(defaultPluginConfig);
    return IAlgebraPlugin.afterFlash.selector;
}
```

Implied, that these hook would not be called at all. But if for some reason the config is set wrong in a pool. We should set config in a pool to our  defaultPluginConfig, disabling unwanted hooks.

## Custom Logic

### Authorization

Finally, we have implemented everything needed to proceed to our custom logic. Although, if we try to compile the code, we will get an error:

```bash
TypeError: Contract "DynamicFeePlugin" should be marked as abstract.
 --> contracts/DynamicFeePlugin.sol:7:1:
  |
7 | contract DynamicFeePlugin is AbstractPlugin {
  | ^ (Relevant source part starts here and spans across multiple lines).
Note: Missing implementation:
  --> @cryptoalgebra/abstract-plugin/contracts/AbstractPlugin.sol:40:3:
   |
40 |   function _authorize() internal view virtual;
   |   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

This is because we have to implement authorization mechanism in our plugin. Authorization is necessary since it determines who is able to collect plugin fees inside <kbd>AbstractPlugin</kbd>. Additionally we are going to need authorization for persmissioned actions with our plugin. Algebra suggests such implementation:

```solidity
function _authorize() internal override {
    require(msg.sender == pluginFactory, 'Unauthorized');
}
```

Such implementation will not allow anyone except plugin factory to call authorised functions. Thus as a plugin developer you should add needed functionality to a plugin factory to be able to call these functions later. Or you can implement authorization the other way, for instance like that:

```solidity
function _authorize() internal view override {
    require(msg.sender == pluginFactory.owner(), 'Unauthorized');
}
```

The above one requires a caller to be the owner of plugin factory (it should adhere to <kbd>Ownable</kbd> ). After all, it's up to you how to implement authorization mechanism in plugin, but it has to be secure.

### Custom variables and setters

Proceeding to a custom logic, as a reminder, we would like to develop a plugin which manipulates a pool's fee. Let's introduce some variables inside our plugin:

<pre class="language-solidity"><code class="lang-solidity">contract DynamicFeePlugin is AbstractPlugin {
    // ... //
    
<strong>    uint24 public overrideFee;
</strong>    uint24 public pluginFee;

    constructor(
        address _pool,
        address _pluginFactory,
        uint24 _overrideFee,
        uint24 _pluginFee
    ) AbstractPlugin(_pool, _pluginFactory) {
        overrideFee = _overrideFee;
        pluginFee = _pluginFee;
    }
    
    // ... //
}
</code></pre>

Above, we added `overrideFee` and `pluginFee` which are going to be used later. `overrideFee` is going to define LP's fee. `pluginFee` is going to define fee that belongs to a plugin. A total fee of a swap would be a sum of `overrideFee` and `pluginFee`. You should also consider that plugin will probably not receive the exact fee amount defined by `pluginFee`. Because in reality `communityFee` (protocol fee) is going to be deducted from calculated plugin fee amount. As well as from LP's fee.

Since we now have some variables, we should add setters to them in case we would want to change their values:

```solidity
function setParams(uint24 _overrideFee, uint24 _pluginFee) external {
    _authorize();
    overrideFee = _overrideFee;
    pluginFee = _pluginFee;
}
```

The code above is self-explainatory with a little addition in the form of our previously implemented <kbd>\_authorize()</kbd> function.

### Hooks

Now we are ready to define a logic of handling <kbd>beforeSwap</kbd> and <kbd>beforeModifyPosition</kbd>. Let's start with <kbd>beforeSwap</kbd>:

```solidity
function beforeSwap(
    address,
    address,
    bool zeroToOne,
    int256,
    uint160,
    bool,
    bytes calldata
) external override onlyPool returns (bytes4, uint24, uint24) {
    if (zeroToOne) {
        // If zeroToOne then we return override fee 2 times lower
        return (IAlgebraPlugin.beforeSwap.selector, overrideFee / 2, pluginFee);
    } else {
        return (IAlgebraPlugin.beforeSwap.selector, overrideFee, pluginFee);
    }
}
```

<kbd>beforeSwap</kbd> hook has to return: it's selector, override fee, plugin fee. For illustration purposes, we implemented the hook in a way that:

* if someone swaps in a `zeroToOne` direction, plugin sets LP fee to `overrideFee / 2`
* if someone swaps in a `oneToZero` direction, plugin sets LP fee to `overrideFee`

<mark style="background-color:orange;">**If you would not like to manipulate LP fee at all in your plugin, it just has to return 0 as overrideFee.**</mark>

Further, since we want to charge plugin fee also on burn operations, we have to implement <kbd>beforeModifyPosition</kbd> hook:

```solidity
function beforeModifyPosition(address, address, int24, int24, int128, bytes calldata) external override onlyPool returns (bytes4, uint24) {
    return (IAlgebraPlugin.beforeModifyPosition.selector, pluginFee);
}
```

Here as well the hook returns it's selector and plugin fee. <kbd>beforeModifyPosition</kbd> is called on both mint and burn operations, but plugin fee is charged only on burn.

### Plugin Fees

<kbd>AbstractPlugin</kbd> provides basic functionality for plugin fees, that could be overridden optionally.

Firstly, it is possible to override `handlePluginFee`:

```solidity
/// @inheritdoc IAlgebraPlugin
function handlePluginFee(uint256, uint256) external view override onlyPool returns (bytes4) {
  return IAlgebraPlugin.handlePluginFee.selector;
}
```

This function in permitted only to a pool and is called every time when plugin fees are transferred to a plugin. Implementation of this handler inside <kbd>AbstractPlugin</kbd> does not do anything with plugin fees, so they are just accrued on its balance.

Secondly, there is an option to override `collectPluginFee`:

```solidity
/// @inheritdoc IAbstractPlugin
function collectPluginFee(address token, uint256 amount, address recipient) external override {
  _authorize();
  SafeTransfer.safeTransfer(token, recipient, amount);
}
```

Considering how `handlePluginFee` works by default, <kbd>AbstractPlugin</kbd> provides a functionality to collect plugin fee which is essentially a transfer of any token from a plugin to a provided recipient.

## Plugin Factory

Plugin Factories play essential role in Algebra Integral architecture. Since Algebra sticks to a approach with dedicated plugins for each pool, plugin factories deploy plugins for new or existing pools. Depending on a chosen authorisation mechanism in a plugin, plugin factory might be also a managment point of its plugins.

In order to be able to deploy our <kbd>DynamicFeePlugin</kbd>, lets create <kbd>DynamicFeePluginFactory</kbd> starting with this code:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

import './DynamicFeePlugin.sol';

import '@cryptoalgebra/abstract-plugin/contracts/AbstractCustomPluginFactory.sol';

contract DynamicFeePluginFactory is AbstractCustomPluginFactory {
    uint24 public defaultOverrideFee = 10000;
    uint24 public defaultPluginFee = 10000;

    constructor(address _entryPoint) AbstractCustomPluginFactory(_entryPoint) {}

    function _createPlugin(address pool) internal override returns (address) {
        DynamicFeePlugin plugin = new DynamicFeePlugin(
            pool,
            address(this),
            defaultOverrideFee,
            defaultPluginFee
        );
        return address(plugin);
    }
}
```

Breaking down the code above:

* Import `AbstractCustomPluginFactory` which simplifies the development process
* Define `defaultOverrideFee` and `defaultPluginFee` which represent values with which new plugins are going to be deployed
* Initialize `entryPoint` variable with its value in the constructor
* Initialises `AbstractCustomPluginFactory's` constructor by providing <kbd>AlgebraCustomPoolEntryPoint</kbd> address

As we mentioned earlier, plugin factory is not only a main point of deploying custom pools with plugins but also a managment point. Thats why we also have to implement some authorization to it, for example using <kbd>Ownable</kbd> by OpenZeppelin:

```solidity
// ... //
import '@openzeppelin/contracts/access/Ownable.sol';

contract DynamicFeePluginFactory is AbstractCustomPluginFactory, Ownable {
    // ... //
}
```

Now our plugin factory has authorization mechanism and we can proceed to implementing differrent managment functions. Let's start with the ones required by `AbstractCustomPluginFactory` :

```solidity
function setTickSpacing(address pool, int24 newTickSpacing) onlyOwner external {
    IAlgebraCustomPoolEntryPoint(entryPoint).setTickSpacing(pool, newTickSpacing);
}

function setPlugin(address pool, address newPluginAddress) onlyOwner external {
    IAlgebraCustomPoolEntryPoint(entryPoint).setPlugin(pool, newPluginAddress);
}

function setPluginConfig(address pool, uint8 newConfig) onlyOwner external {
    IAlgebraCustomPoolEntryPoint(entryPoint).setPluginConfig(pool, newConfig);
}

function setFee(address pool, uint16 newFee) onlyOwner external {
    IAlgebraCustomPoolEntryPoint(entryPoint).setFee(pool, newFee);
}
```

We implemented:

* `setTickSpacing` - sets a tick spacing in a provided pool
* `setPlugin` - sets a plugin in a provided pool
* `setPluginConfig` - sets plugin config in a provided pool
* `setFee` - sets fee in a provided pool. <kbd>DYNAMIC\_FEE</kbd> flag must be `False` in a particular pool in order to be able to call setFee on this pool.

Let's also add a method to collect fees from the plugin.

```solidity
function collectPluginFeeFromPlugin(address plugin, address token, uint256 amount, address recipient) onlyOwner external{
    IAbstractPlugin(plugin).collectPluginFee(token, amount, recipient);
}
```

<mark style="background-color:orange;">`pool`</mark> <mark style="background-color:orange;"></mark><mark style="background-color:orange;">parameter has to be a pool which is authorized to that factory, thus it can be only the pool which is deployed by the that plugin factory.</mark>

As you can see, all the functions above are protected by `onlyOwner` modifier.

Now, we can add some custom logic to our plugin factory. In our case we might want change the default parameters (`overrideFee` and `pluginFee`) used to deploy new plugins. Therefore, let's add a setter:

```solidity
function setDefaultParams(uint24 _defaultOverrideFee, uint24 _defaultPluginFee) onlyOwner external {
    defaultOverrideFee = _defaultOverrideFee;
    defaultPluginFee = _defaultPluginFee;
}
```

For simplicity let it be the only one function which sets both parameters. This one is protected by `onlyOwner` as well.

**Next Steps**

Well Done! You have developed your very first Algebra Plugin! The next step is to thoroughly test it. Check out [plugin-testing.md](plugin-testing.md "mention") section for help.
