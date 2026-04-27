---
icon: star-shooting
---

# Plugin Deployment

## Introduction

This guide will walk you through the steps required to deploy your plugin. Follow the instructions carefully to ensure a successful deployment.

## Set up

Before starting, you need to create a `.env` file in the root directory of your project. This file will store environment variables such as private keys, API keys, or other sensitive information required for deployment.

To deploy your plugin, you need to configure the network settings in the `hardhat.config.ts` file. Add the desired network (e.g., Ethereum, Polygon, etc.) and its corresponding configuration (e.g., RPC URL, chain ID, etc.) to the `networks` section of the file.

## Deployment

The deployment script for the test plugin can be found in the `scripts/deploy.js:`

```typescript
const hre = require("hardhat");

const ENTRYPOINT_ADDRESS = ""

async function main() {
    const pluginFactoryFactory = await hre.ethers.getContractFactory("DynamicFeePluginFactory");
    const pluginFactory = await pluginFactoryFactory.deploy(ENTRYPOINT_ADDRESS);

    await pluginFactory.waitForDeployment()

    console.log("DynamicFeePluginFactory to:", pluginFactory.target);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

To deploy your plugin on a specific Decentralized Exchange (DEX), you need to find the address of the `EntryPoint` contract. This address is required for the deployment process.

1. Locate the `EntryPoint` contract address for the DEX you are targeting. This information is typically available on the DEX's official documentation.
2. Once you have the `EntryPoint` contract address, update the deployment script. In the provided template, you can find the relevant line in the `scripts/deploy.ts` file at [this location](https://github.com/cryptoalgebra/algebra-plugin-template/blob/main/scripts/deploy.ts#L3). Replace the placeholder with the actual `EntryPoint` contract address.

After completing the above steps, you are ready to deploy your plugin. Run the deployment script using the following command:

```
npx hardhat run scripts/deploy.js --network <network_name>
```

Replace `<network_name>` with the name of the network you configured in `hardhat.config.ts`.
