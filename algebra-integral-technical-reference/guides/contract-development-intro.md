# Environment Setup

## Intro

In this step-by-step guide we'll walk through setting up a Hardhat development environment you can use to build the examples in the Guides section, or to start your own Algebra Integral integration.

You have two options: **Quick Start** — clone the boilerplate and jump straight to writing contracts, or **Start from Scratch** — build everything from the ground up.

## Quick Start

[The Algebra boilerplate repo](https://github.com/cryptoalgebra/boilerplate) provides a Hardhat environment with the required Algebra packages already installed. Clone it and install the dependencies:

```bash
git clone https://github.com/cryptoalgebra/boilerplate
cd boilerplate
npm install
```

Then go to the [Local Node with a Polygon Fork](#local-node-with-a-polygon-fork) section below to complete setup and start building.

## Start from Scratch

### Prerequisites

Node.js v22 or later and NPM are required. If you haven't installed them yet:

[Install Node.js and NPM](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)

Once installed, create a new directory and initialize a project:

```bash
mkdir my-algebra-project
cd my-algebra-project
npm init -y
npm pkg set type=module
```

### Install Hardhat

[Hardhat](https://hardhat.org/) is the Ethereum development toolset used for compiling, testing, and deploying contracts:

```bash
npm add --save-dev hardhat
```

Initialize a Hardhat project:

```bash
npx hardhat --init
```

When prompted, choose the JavaScript project template and accept the defaults.

This creates the standard folder structure: `./contracts` for Solidity contracts, `./test` for tests, and `./scripts` for deployment scripts.

### Install Algebra Packages

Add the Algebra Integral contracts as dependencies:

```bash
npm add @cryptoalgebra/integral-periphery @cryptoalgebra/integral-core
```

### Install Hardhat Toolbox

Install the recommended Hardhat 3 bundle for Ethers.js and Mocha:

```bash
npm add --save-dev @nomicfoundation/hardhat-toolbox-mocha-ethers
```

Update your `hardhat.config.js` to use the plugin:

```javascript
import { defineConfig } from "hardhat/config";
import hardhatToolboxMochaEthers from "@nomicfoundation/hardhat-toolbox-mocha-ethers";

export default defineConfig({
  plugins: [hardhatToolboxMochaEthers],
  solidity: {
    version: "0.8.20",
  },
});
```

### Verify the Setup

To make sure the environment is set up correctly, try compiling a basic swap contract. Create `./contracts/Swap.sol` and paste in the following:

```solidity
// SPDX-License-Identifier: GPL-2.0-or-later
pragma solidity =0.8.20;

import '@cryptoalgebra/integral-periphery/contracts/interfaces/ISwapRouter.sol';
import '@cryptoalgebra/integral-periphery/contracts/libraries/TransferHelper.sol';

contract SimpleSwap {
    ISwapRouter public immutable swapRouter;
    address public constant DAI = 0x8f3Cf7ad23Cd3CaDbD9735AFf958023239c6A063;
    address public constant WMATIC = 0x0d500B1d8E8eF31E21C99d1Db9A6444d3ADf1270;

    constructor(ISwapRouter _swapRouter) {
        swapRouter = _swapRouter;
    }

    function swapMATICForDAI(uint256 amountIn) external returns (uint256 amountOut) {
        TransferHelper.safeTransferFrom(WMATIC, msg.sender, address(this), amountIn);
        TransferHelper.safeApprove(WMATIC, address(swapRouter), amountIn);

        ISwapRouter.ExactInputSingleParams memory params =
            ISwapRouter.ExactInputSingleParams({
                tokenIn: WMATIC,
                tokenOut: DAI,
                deployer: address(0),
                recipient: msg.sender,
                deadline: block.timestamp,
                amountIn: amountIn,
                amountOutMinimum: 0,
                sqrtPriceLimitX96: 0
            });
        amountOut = swapRouter.exactInputSingle(params);
    }
}
```

Build it:

```bash
npx hardhat build
```

If successful you'll see:

```
Compilation finished successfully
```

## Local Node with a Polygon Fork

Testing against live on-chain liquidity is critical but expensive. Hardhat lets you run a local Polygon fork for free.

As a prerequisite, you'll need an RPC that supports forking. [Alchemy](https://www.alchemy.com/) includes this on its free tier.

Start the local fork node:

```bash
npx hardhat node --fork https://polygon-mainnet.g.alchemy.com/v2/{YOUR_API_KEY}
```

With the node running, use the `--network localhost` flag to run tests against it:

```bash
npx hardhat test --network localhost
```

## Next Steps

With your environment set up, head into the other guides to start building:

* [Swaps](swaps/README.md)
* [Providing Liquidity](providing-liquidity/README.md)
* [Flashloans](flashloans/README.md)
* [Custom Plugins](custom-plugins/README.md)
