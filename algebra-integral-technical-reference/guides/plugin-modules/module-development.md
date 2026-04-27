---
icon: block-brick
---

# Module Development

This guide walks through building a module for the Algebra Integral upgradeable plugin architecture. By the end you will have a working **Implementation contract** and **Connector**: the two components that make up every module.

You can study existing official modules as reference implementations in the [plugins monorepo](https://github.com/cryptoalgebra/plugins-monorepo).

## Architecture Overview

The default Algebra plugin is an upgradeable Beacon Proxy. Each pool has its own proxy instance, but all proxies share logic from a single implementation. To keep things composable and upgradeable, logic is split into independent modules.

Every module has two parts:

```
┌─────────────────────────────────────────────────────┐
│              AlgebraUpgradeablePlugin                │
│  (BeaconProxy → inherits all Connectors)            │
│                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────┐  │
│  │  Connector A │  │  Connector B │  │   ...     │  │
│  │  (abstract)  │  │  (abstract)  │  │           │  │
│  └──────┬───────┘  └──────┬───────┘  └─────┬─────┘  │
│         │ delegatecall    │ delegatecall    │         │
└─────────┼─────────────────┼────────────────┼─────────┘
          ▼                 ▼                ▼
   Implementation A  Implementation B    ...
   (standalone        (standalone
    contract)          contract)

Storage is namespaced per module via ERC-7201 - lives
in the proxy, shared between Connector and Implementation.
```

The proxy holds all state. When the connector calls `_delegateCall(implementationAddress, ...)`, the implementation runs in the proxy's context - it reads and writes the proxy's storage while executing the implementation's logic. This lets you upgrade logic without touching storage.

## Part 1: Implementation Contract

The implementation contract contains all logic for your module. State is stored using [ERC-7201 namespaced storage](https://eips.ethereum.org/EIPS/eip-7201): each module defines a storage library with a unique namespace slot, so module states never collide regardless of inheritance order or future upgrades.

The implementation reads and writes storage exclusively through this library, making it safe to call via `delegatecall`.

{% hint style="info" %}
The ERC-7201 namespace slot is computed offline:\
`keccak256(abi.encode(uint256(keccak256("erc7201:algebra.storage.<yourmodule>")) - 1)) & ~bytes32(uint256(0xff))`\
Use a script or [erc7201.xyz](https://erc7201.xyz) to generate it. By convention, use `erc7201:algebra.storage.<modulename>` as the namespace string.
{% endhint %}

```solidity
// SPDX-License-Identifier: BUSL-1.1
pragma solidity =0.8.20;

import './interfaces/IMyModulePluginImplementation.sol';

// ERC-7201 namespaced storage - defined alongside the implementation
library MyModuleStorage {
  /// @dev keccak256(abi.encode(uint256(keccak256("erc7201:algebra.storage.mymodule")) - 1)) & ~bytes32(uint256(0xff))
  bytes32 internal constant NAMESPACE = 0x/* compute offline */00;

  struct Layout {
    uint256 someValue;
    address someAddress;
    bool isActive;
  }

  function layout() internal pure returns (Layout storage l) {
    bytes32 position = NAMESPACE;
    assembly { l.slot := position }
  }
}

contract MyModulePluginImplementation is IMyModulePluginImplementation {

  /// @notice Initialize module state. Called once during plugin initialization
  function initializeMyModule(uint256 initialValue, address initialAddress) external {
    MyModuleStorage.Layout storage s = MyModuleStorage.layout();
    s.someValue = initialValue;
    s.someAddress = initialAddress;
    s.isActive = true;
  }

  /// @notice Core module logic. Called from the assembled plugin's afterSwap handler
  function processAfterSwap(bool zeroToOne) external {
    MyModuleStorage.Layout storage s = MyModuleStorage.layout();
    if (!s.isActive) return;
    // ... your logic here
    s.someValue += 1;
  }

  /// @notice Admin function. Updates configuration
  function setSomeValue(uint256 newValue) external {
    MyModuleStorage.layout().someValue = newValue;
  }
}
```

{% hint style="warning" %}
The implementation contract must **not** have a constructor that sets storage - it is called via `delegatecall`, so any constructor state goes into the implementation's own storage, not the proxy's. Only the functions called via `delegatecall` write to the proxy's namespaced storage.
{% endhint %}

**Conventions:**

* Function names should be clear and specific - they appear in `abi.encodeCall` inside the connector
* Never use regular `storage` variables in the implementation - always go through the storage library
* Keep the implementation stateless at the contract level (no `immutable` values that affect logic)
* Read-only views can be implemented directly in the connector without `delegatecall` - they call `MyModuleStorage.layout()` directly, which resolves to the same slot in the proxy's context

## Part 2: Connector

The connector is an `abstract` contract that is inherited by the default plugin. It:

* Stores the implementation address as an `immutable` (set once in constructor, shared across all proxy instances)
* Exposes the module's public interface and admin functions
* Routes all state-changing calls to the implementation via `_delegateCall()`
* Declares the module's plugin config flags

```solidity
// SPDX-License-Identifier: BUSL-1.1
pragma solidity =0.8.20;

import '@cryptoalgebra/integral-core/contracts/libraries/Plugins.sol';
import '@cryptoalgebra/abstract-plugin/contracts/BaseConnector.sol';
import './libraries/MyModuleStorage.sol';
import './interfaces/IMyModuleManager.sol';
import './interfaces/IMyModulePluginImplementation.sol';

abstract contract MyModuleConnector is BaseConnector, IMyModuleManager {
  using Plugins for uint8;

  string internal constant MY_MODULE_NAME = 'My Module Plugin';

  /// @dev Which hooks this module needs. Combined with other modules' configs in defaultPluginConfig().
  uint8 internal constant MY_MODULE_PLUGIN_CONFIG = uint8(Plugins.AFTER_SWAP_FLAG);

  /// @dev Immutable - shared across all proxy instances, set in the assembled plugin's constructor
  address internal immutable myModuleImplementation;

  constructor(address _myModuleImplementation) {
    myModuleImplementation = _myModuleImplementation;
  }

  // ========== Internal helpers called by the assembled plugin ==========

  function _initializeMyModule(uint256 initialValue, address initialAddress) internal {
    _delegateCall(
      myModuleImplementation,
      abi.encodeCall(IMyModulePluginImplementation.initializeMyModule, (initialValue, initialAddress))
    );
  }

  function _processAfterSwap(bool zeroToOne) internal {
    _delegateCall(
      myModuleImplementation,
      abi.encodeCall(IMyModulePluginImplementation.processAfterSwap, (zeroToOne))
    );
  }

  // ========== Public interface (IMyModuleManager) ==========

  /// @inheritdoc IMyModuleManager
  function setSomeValue(uint256 newValue) external override {
    _authorize(); // access control - resolved by UpgradeableAbstractPlugin
    _delegateCall(
      myModuleImplementation,
      abi.encodeCall(IMyModulePluginImplementation.setSomeValue, (newValue))
    );
    emit SomeValueUpdated(newValue);
  }

  /// @inheritdoc IMyModuleManager
  function someValue() external view override returns (uint256) {
    // View functions read storage directly - no delegatecall needed
    return MyModuleStorage.layout().someValue;
  }
}
```

### Plugin Config Flags

The `MY_MODULE_PLUGIN_CONFIG` constant declares which hooks the pool should call on your module. Available flags, from `Plugins.sol`:

| Flag                        | Value      | Hook triggered         |
| --------------------------- | ---------- | ---------------------- |
| `BEFORE_SWAP_FLAG`          | `1`        | `beforeSwap`           |
| `AFTER_SWAP_FLAG`           | `1 << 1`   | `afterSwap`            |
| `BEFORE_POSITION_MODIFY_FLAG` | `1 << 2` | `beforeModifyPosition` |
| `AFTER_POSITION_MODIFY_FLAG` | `1 << 3`  | `afterModifyPosition`  |
| `BEFORE_FLASH_FLAG`         | `1 << 4`   | `beforeFlash`          |
| `AFTER_FLASH_FLAG`          | `1 << 5`   | `afterFlash`           |
| `AFTER_INIT_FLAG`           | `1 << 6`   | `afterInitialize`      |
| `DYNAMIC_FEE`               | `1 << 7`   | enables dynamic fee    |

Combine flags with `|`:

```solidity
uint8 internal constant MY_MODULE_PLUGIN_CONFIG =
  uint8(Plugins.AFTER_SWAP_FLAG | Plugins.DYNAMIC_FEE);
```

### `_authorize()` and `_delegateCall()`

Both are inherited from `BaseConnector` (for `_delegateCall`) and resolved at the assembled plugin level (for `_authorize`):

* **`_delegateCall(address impl, bytes memory data)`** - executes a `delegatecall`, propagates reverts with the original error. Never call an untrusted address here.
* **`_authorize()`** - abstract in `BaseConnector`, implemented in `UpgradeableAbstractPlugin`. It checks that `msg.sender` has `ALGEBRA_BASE_PLUGIN_MANAGER` role in the factory. Call it at the top of every admin function in your connector.

## Part 3: Integration into the Assembled Plugin

This part covers how to integrate your module into `AlgebraUpgradeablePlugin`. After completing the integration and writing tests, submit the result to the Algebra team for review.

**Constructor**: add the implementation address as a parameter and pass it to your connector:

```solidity
constructor(
  address _factory,
  address _pluginFactory,
  address _volatilityOracleImpl,
  address _dynamicFeeImpl,
  address _myModuleImpl,   // add
  // ...
)
  UpgradeableAbstractPlugin(_factory, _pluginFactory)
  VolatilityOracleConnector(_volatilityOracleImpl)
  DynamicFeeConnector(_dynamicFeeImpl)
  MyModuleConnector(_myModuleImpl)  // add
  // ...
{}
```

**`defaultPluginConfig()`**: your module's config flags:

```solidity
function defaultPluginConfig() public pure override returns (uint8) {
  return
    VOLATILITY_ORACLE_PLUGIN_CONFIG |
    DYNAMIC_FEE_PLUGIN_CONFIG       |
    FARMING_PROXY_PLUGIN_CONFIG     |
    MY_MODULE_PLUGIN_CONFIG;
}
```

**`initialize()`**: call your module's initializer if needed:

```solidity
function initialize(
  AlgebraFeeConfiguration calldata feeConfig,
  address securityRegistry,
  uint256 myModuleInitialValue,
  address myModuleInitialAddress
) external initializer onlyPluginFactory {
  _initializeDynamicFee(feeConfig);
  _initializeSecurity(securityRegistry);
  _initializeMyModule(myModuleInitialValue, myModuleInitialAddress);
}
```

**Hooks**: wire your module's logic into the relevant pool hook handler:

```solidity
function afterSwap(
  address sender,
  address recipient,
  bool zeroToOne,
  int256 amountSpecified,
  uint160 sqrtPriceX96,
  int256 amount0,
  int256 amount1,
  bytes calldata data
) external override onlyPool returns (bytes4) {
  // existing module calls...
  _updateVirtualPoolTick(zeroToOne, tick);
  _triggerAlmRebalance(tick);

  // add your module's call here
  _processAfterSwap(zeroToOne);

  return IAlgebraPlugin.afterSwap.selector;
}
```

**`getActiveModuleNames()`**: add your module's name to the list:

```solidity
function getActiveModuleNames() external pure override returns (string[] memory) {
  string[] memory names = new string[](6); // increase array size
  names[0] = VOLATILITY_ORACLE_MODULE_NAME;
  names[1] = DYNAMIC_FEE_MODULE_NAME;
  names[2] = FARMING_PROXY_MODULE_NAME;
  names[3] = ALM_MODULE_NAME;
  names[4] = SECURITY_MODULE_NAME;
  names[5] = MY_MODULE_NAME;
  return names;
}
```
