---
icon: puzzle
---

# How It Works: Core + Plugins

Schematically, the Algebra architecture for a specific pair can be represented as a **core**, which is a crucial part that stores the pair’s liquidity and implements the swap logic. In addition, there are exchangeable modules, known as **plugins**, that interact with the pool.

* **Core** — Immutable logic for liquidity storage and swap calculations. Secure, audited, and unchangeable.
* **Plugins** — Interchangeable smart contracts that extend pool functionality and can be updated independently of the Core.

This design ensures that the most sensitive operations (like token storage and pricing math) remain secure, while peripheral features can evolve quickly to support emerging use cases.

The Algebra ‘Integral’ architecture offers extensive customization options for the logic of all protocol pools, including individual pools. Plugins play an important role in this customization.

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

## What Are Plugins?

Technically, plugins are peripheral smart contracts connected to individual liquidity pools. A plugin can:

* Adjust swap fees dynamically based on volatility
* Act as an on-chain oracle
* Implement farming or reward systems
* Perform additional custom checks
* Interact with external contracts
* Introduce business logic like KYC or whitelisting

Plugins **cannot** access user funds directly, nor can they modify the Core’s pricing logic — maintaining user trust and protocol integrity.

> **Note:** Currently, one plugin can be initialized per pool. Future architecture will support multiple plugins via a **Proxy Plugin** system.

## **The Future of DEX Functionality**

Algebra Integral is more than a protocol — it’s a foundation for long-term innovation. Future plugin modules may include:

* Dynamic fees
* Gas discount or reimbursement systems
* KYC/whitelist/blacklist integration
* LVR (Loss vs Rebalancing) mitigation tools
* Anti-JIT liquidity solutions
* Support for perpetuals and advanced derivatives

Visit [https://market.algebra.finance](https://market.algebra.finance/) to see what's already available with Algebra Integral.

## **How Plugins Connect: The Hook System**

Each pool can be connected to a smart contract that aligns with the plugin specification for the Algebra Protocol. By utilizing **hooks** (calls from a pool before or after specific actions), a plugin can extend, enhance, or modify the standard behavior of liquidity pools. Hooks include:

* `beforeInitialize` / `afterInitialize`
* `beforeModifyPosition` / `afterModifyPosition`
* `beforeSwap` / `afterSwap`
* `beforeFlash` / `afterFlash`

This event-driven architecture ensures Plugins can operate seamlessly with the Pool, reacting to changes and executing custom logic exactly when needed.

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

On the other hand, it is also **possible to operate without any plugins**, creating a pure AMM without unnecessary functionality from the protocol. This flexibility allows for easy adaptation to external requirements and enables maximum gas savings by disabling secondary logic when not needed.

<div data-full-width="true"><figure><img src="../../.gitbook/assets/image (41).png" alt="" width="375"><figcaption></figcaption></figure></div>

The protocol has the capability to replace plugins for pools at any time, allowing for continuous development and the addition of functionality based on user or partner needs. As a result, Algebra AMM can be configured in various ways to accommodate specific market or environmental requirements. However, it maintains a solid and proven core of concentrated liquidity logic that remains consistent across different configurations.
