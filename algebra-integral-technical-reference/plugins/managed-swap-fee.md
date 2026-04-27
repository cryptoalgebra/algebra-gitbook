# Managed Swap Fee

## Overview

This plugin lets an external service such as AI Agent, dApp or KYC platform initiate a swap with the custom swap fee that takes effect only for that swap.&#x20;With this plugin swap fee can be set per-swap and not just per-address thus giving a great flexibility and enabling many business scenarios.

#### Functionality Overview

The user can obtain data signed by the backend and pass it to the swap. The plugin parses this data and validate the signature. If successful, the fee will be applied to the swap.

<figure><img src="../../.gitbook/assets/managed-swap-fee-plugin (1).svg" alt=""><figcaption></figcaption></figure>

#### Implementation Details

The plugin uses the `beforeSwap` hook, which receives `pluginData` containing the following parameters:

* **Nonce**: A unique number preventing replay attacks
* **Fee value**: Applied to `overrideFee`
* **User address**: The address initiating the swap
* **Expiration time**: The deadline for signature validity
* **ECDSA signature**: A private signature from an "authorized" address covering the above parameters

#### Plugin Workflow

1. `beforeSwap` is called with the provided data.
2. Plugin checks if sender is the router and plugin data length is greater than 0&#x20;
3. The data is parsed into five parameters:
   * `nonce`
   * `fee`
   * `userAddress`
   * `expireTime`
   * `signature`&#x20;
4. The plugin verifies that the `nonce` has not been used before.
5. The parameters (`nonce`, `fee`, `userAddress`, `expireTime`) are encoded into a message (hash).
6. The signer's address recovered from signature using the hash from step 5.&#x20;
7. The recovered address is checked for authorization
   * If authorized:
     * The `nonce` is marked as used.
     * The `fee` is applied to the swap.
   * If unauthorized:
     * The transaction is reverted.

## How to configure Managed Swap Fee Plugin

To change the router's address, you need to call `function setRouterAddress(address _router)`

To whitelist an address whose signatures will be accepted by the plugin, you need to call `function setWhitelistStatus(address _address, bool status)`
