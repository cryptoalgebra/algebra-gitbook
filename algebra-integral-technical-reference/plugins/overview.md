# Overview

Plugins for Algebra Integral are smart contracts that can expand or limit the functionality of liquidity pools. The plugin can be connected to a liquidity pool, then the pool will be able to call the corresponding plugin methods before and after the main actions in the pool:

* swap
* mint
* burn
* flash

A more detailed general description of plugins can be found in the section: [Core logic - plugins](../core-logic/plugins.md)

## Creating Custom Plugins

Any developer can build a custom plugin for any DEX that has deployed an **EntryPoint** contract. The EntryPoint gives you the ability to create a pool with your own plugin attached - without touching the DEX's base pools or requiring any special permissions from the DEX team.

This is the right path if you want to:

* Experiment with a new fee model or hook logic
* Build a specialized pool for your own protocol&#x20;
* Test a new module before proposing it for inclusion in the default plugin

For a step-by-step walkthrough, see the [Custom Plugin Guides](../guides/custom-plugins/).

## Base pools

When a DEX deploys Algebra Integral, it comes with a set of **base pools**. These are the main pools users interact with — they are created through the standard factory and have a shared **default plugin** attached automatically.

The default plugin is what gives these pools their core features: dynamic fees, a volatility oracle, farming support, ALM integration, and security controls. Every base pool on a DEX runs the same plugin, so all pools benefit from updates and improvements simultaneously.

The default plugin is built from independent **modules**. Each module covers one feature area and can be upgraded or extended without affecting the others. The plugin itself is deployed as a Beacon Proxy, so upgrading the logic for all pools at once requires only a single transaction, with no address changes and no loss of stored state.

The source code for all official modules is available in the [plugins monorepo](https://github.com/cryptoalgebra/plugins-monorepo).

## Contributing a Module to the Default Plugin

If you want your plugin logic to be used across **all base pools on a DEX** - rather than just custom pools - you need to implement it as a module following the Connector + Implementation + Storage pattern described above.

The process:

1. Implement your module: Implementation contract and Connector, following the architecture conventions
2. Integrate the module into your local fork of `AlgebraUpgradeablePlugin`: wire the constructor, `defaultPluginConfig`, `initialize`, and the relevant pool hook handlers
3. Write tests for the integrated module
4. Contact the Algebra team to submit the module for review
5. Once approved, the module is merged into the repository and listed on the **Plugin Marketplace**

For details on how to write a module, see the [Plugin Module Development guide](../guides/plugin-modules/module-development.md).

To explore already-listed plugins and modules, visit the [Plugin Marketplace](../../plugin-marketplace.md).
