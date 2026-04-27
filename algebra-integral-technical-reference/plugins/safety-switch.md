# Safety Switch

## Overview

The purpose of Safety Switch Plugin is to add a way of mitigating potential exploits of Integral. This is achieved by providing a functionality of stopping a certain pool(s) or all the pools (which have the plugin connected).

Briefly, a security vendor will have the ability to disable swap/flash/burn/mint operations on pools when they detect a security threat. Further, another authority (e.g. DEX admin) will be able to enable either burns only (liquidity withdrawals) or all operations.



<figure><img src="../../.gitbook/assets/safety_switch (2).svg" alt=""><figcaption></figcaption></figure>

## Details

### Security Registry

There is a Security Registry contract which is accessed by Safety Switches of different pools to get a _status_ of it’s pool. _Status_ might be equal to _ENABLED_, _DISABLED_ (all operations with hooks are disabled) _or BURN\_ONLY_ (only liquidity decreasing is allowed).

### Safety Switch

Pause works by executing `revert` in _beforeSwap_, _beforeModifyPos_ и _beforeFlash_ hooks.

To disable swaps, flashes and liquidity modification it is necessary for certain flags in a pool to be set. Otherwise hooks will not be triggered.&#x20;

If the status is set to _BURN\_ONLY_ the Safety Switch will only `revert` in _beforeModifyPos_ hook if liquidity is greater than 0.

## _How to configure Safety Switch_

Security Registry is introduced to provide a single entrypoint of a “security system”. It has the following methods:

* setPoolsStatus(address\[] pools, Status\[] new\_statuses) - a method to update _status_ of provided pools
* setGlobalStatus(Status) - a method to set a _status_ of all pools at once
* getPoolStatus(address pool) - a method to get a specific pool’s _status_

If a _globalStatus_ is set to _DISABLED_ then all the pools are _DISABLED_

If a _globalStatus_ is set to _BURN\_ONLY_ then all the pools are _BURN\_ONLY_

If a _globalStatus_ is set to _ENABLED_ then each pool’s _status_ is defined by it’s own _status_

### Roles

There are two roles: _Guard_ and _Admin_

_Guard_ can only set the _status_ to _DISABLED_

_Admin_ (Factory Owner might be used) can set any _status_
