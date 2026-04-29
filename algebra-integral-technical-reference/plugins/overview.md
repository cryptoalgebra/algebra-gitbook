# Overview

Plugins for Algebra Integral are smart contracts that can expand or limit the functionality of liquidity pools. The plugin can be connected to a liquidity pool, then the pool will be able to call the corresponding plugin methods before and after the main actions in the pool:

* swap
* mint
* burn
* flash

A more detailed general description of plugins can be found in the section: [Core logic - plugins](../core-logic/plugins.md)

## Base pools and plugins

When a DEX deploys Algebra Integral, it comes with a set of **base pools**. These are the main pools users interact with - they are created through the standard factory and have a shared **default plugin** attached automatically.

The default plugin is what gives these pools their core features: dynamic fees, volatility oracle, farming support, ALM integration and security controls. By default, base pools on a DEX use this shared default plugin, so they can benefit from updates and improvements simultaneously.

Each pool can have only one plugin attached at a time. Because of this, several independent features cannot be connected to the same pool as separate plugins. If one pool needs multiple features, the logic that could otherwise live in several plugins must be composed inside a single plugin.

Algebra's recommended way to combine this logic is to split the plugin into independent **modules**. Each module covers one feature area, so functionality from multiple plugins can be assembled into one plugin without turning it into one monolithic contract. The default plugin follows this module architecture.

The plugin itself is deployed as a Beacon Proxy, so upgrading the logic for all pools at once requires only a single transaction, with no address changes and no loss of stored state.

The source code for all official modules is available in the [plugins monorepo](https://github.com/cryptoalgebra/plugins-monorepo).

## Contributing a Module to the Default Plugin

If you want your functionality to be used across **all base pools on a DEX**, implement it as a module for the default plugin. Modules follow the Implementation + Connector + Storage pattern and are composed into `AlgebraUpgradeablePlugin`.

Before starting development, study the general plugin mechanics in [Core logic - plugins](../core-logic/plugins.md). That page explains how plugins are attached to pools, how hooks are called and how plugin settings work.

Developers cannot replace the plugin on an existing DEX's base pools by themselves. Plugin deployment, default-plugin changes, and upgrades for base pools are usually performed by the Algebra team.

The process:

1. Implement your module: Implementation contract and Connector, following the architecture conventions
2. Integrate the module into your local fork of `AlgebraUpgradeablePlugin`: `defaultPluginConfig`, `initialize`, and the relevant pool hook handlers
3. Write tests for the integrated module
4. Contact the Algebra team to submit the module for review
5. After review and approval, Algebra can replace or upgrade the plugin on a selected DEX
6. Once approved the module can be listed on the **Plugin Marketplace**

For details on how to write a module, see the [Plugin Module Development guide](../guides/plugin-modules/module-development.md).

To explore already-listed plugins and modules, visit the [Plugin Marketplace](../../plugin-marketplace.md).

## Creating Custom Plugins

Custom plugins are used when functionality should run on a separate pool rather than on the DEX's base pools. Any developer can build a custom plugin for a DEX that has deployed an **EntryPoint** contract. The EntryPoint lets you create a pool with your own plugin attached without replacing the DEX's default plugin.

This is the right path if you want to:

* Experiment with a new fee model or hook logic
* Build a specialized pool for your own protocol
* Prototype functionality before proposing it as a module for the default plugin

For a step-by-step walkthrough, see the [Custom Plugin Guides](../guides/custom-plugins/).

For local development and tests, use the [algebra-plugin-template](https://github.com/cryptoalgebra/algebra-plugin-template). The template includes a `DynamicFeePlugin` example, a plugin factory, fixtures, and Hardhat tests that can be used as a starting point for custom plugin development.
