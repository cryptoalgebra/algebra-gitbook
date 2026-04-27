---
icon: microscope
---

# Plugin Testing

## Introduction

Auto-testing is a critical component of smart contract development, ensuring that your Algebra Plugin functions as intended and are free from vulnerabilities. Automated tests simulate interactions with your hook contract, validate edge cases, and verify gas efficiency. This guide covers how to set up and run auto-tests for a Algebra Plugin using **Hardhat**, a popular Ethereum development framework.

## Setting Up

Algebra provides a basic test suite in the [algebra-plugin-template repo](https://github.com/cryptoalgebra/algebra-plugin-template). It contains _fixture_ for deployments before each test and some utility functions that you might want to use.

Developing auto-tests with Hardhat usually starts with writing a _fixture_. In the repo it is present as <kbd>test/shared/fixtures.ts</kbd> file. Firstly, we have to add some imports from Algebra core package to extract bytecode and ABI for Pool and a types for some contracts:

```typescript
import {ethers} from 'hardhat';
import {entrypointFixture, TokensFixture} from "./externalFixtures";
import AlgebraPool
  from "@cryptoalgebra/integral-core/artifacts/contracts/AlgebraPool.sol/AlgebraPool.json";
import {
    DynamicFeePluginFactory,
    DynamicFeePlugin,
    IAlgebraPool, IAlgebraFactory
} from '../../typechain-types';
```

Then, we define an interface for our _fixture_.

```typescript
interface PluginFixture extends TokensFixture {
    pluginFactory: DynamicFeePluginFactory,
    plugin: DynamicFeePlugin,
    pool: IAlgebraPool
}
```

With this one we will be able to further acces Plugin Factory, Plugin, a Pool it is attached to and Tokens which represent a Pool.

Preparations are ready, now we can implement the actual logic of the _fixture_:

```typescript
export const pluginFixture: Fixture<PluginFixture> = async function (): Promise<PluginFixture> {
    const {customEntrypoint, factory, token0, token1} = await entrypointFixture();

    const pluginFactoryFactory = await ethers.getContractFactory('DynamicFeePluginFactory');
    const pluginFactory = (await pluginFactoryFactory.deploy(customEntrypoint)) as any as DynamicFeePluginFactory;

    await pluginFactory.createCustomPool(
        ZERO_ADDRESS,
        await token0.getAddress(),
        await token1.getAddress(),
        '0x'
    );

    const poolAddress = await factory.customPoolByPair(
        await pluginFactory.getAddress(),
        await token0.getAddress(),
        await token1.getAddress(),
    )

    const poolFactory = await ethers.getContractFactory(AlgebraPool.abi, AlgebraPool.bytecode);
    const pool = poolFactory.attach(poolAddress) as any as IAlgebraPool

    const pluginTypeFactory = await ethers.getContractFactory('DynamicFeePlugin');
    const pluginAddress = await pool.plugin();
    const plugin = pluginTypeFactory.attach(pluginAddress) as any as DynamicFeePlugin

    return {
        pluginFactory,
        plugin,
        pool,
        token0,
        token1
    };
};
```

Let's break down the code above:

* Executes <kbd>entrypointFixture</kbd> which provides us a Custom Entrypoint, a Pool Factory and a Tokens addresses.&#x20;
* Deploys a Plugin Factory using artifacts. Custom Entrypoint's adderss is required as a constructor argument.
* Deploys a Custom Pool via a call to Plugin Factory contract.
* Then it fetches deployed pool's address via Factory.
* On the nexts rows it creates a pool object for the later usage when writing tests.&#x20;
* Similarly, it fetches a Plugin address and creates its object.

## Writing Tests

Our fixture is ready to be used in tests to deploy a fresh set of contracts before each test. We will walk through some tests present in the <kbd>test/DynamicFeePlugin.spec.ts</kbd> file.&#x20;

We start with defining some variables used in tests and `before`, `beforeEach` hooks for a convinince:

```typescript
describe('DynamicFeePlugin', () => {
  let wallet: Wallet, other: Wallet;

  let plugin: DynamicFeePlugin;
  let pluginFactory: DynamicFeePluginFactory;
  let pool: IAlgebraPool;

  before('prepare signers', async () => {
    [wallet, other] = await (ethers as any).getSigners();
  });

  beforeEach('deploy test DynamicFeePlugin', async () => {
    ({ plugin, pool, pluginFactory } = await loadFixture(pluginFixture));
  });
  
  // tests... //
}
```

* `wallet` represents a wallet which acts as a signer (origin) of every transaction. `other` is some random wallet. In case you would like to use. For example to track their balances or find its address in some event.
* `plugin`, `pluginFactory`, `pool` are initialized in `beforeEach` hook (beofre each test).

Finally, let's write some tests for `beforeSwap` hook:

```typescript
describe('#BeforeSwap', async () => {
    it('returns right fee for zeroToOne swap', async () => {
      const poolSigner = new ethers.VoidSigner(await pool.getAddress(), ethers.provider)
    
      const result = await plugin.connect(poolSigner).beforeSwap.staticCall(
          ZeroAddress,
          ZeroAddress,
          true, // zeroToOne
          0,
          0,
          false,
          '0x',
      )
    
      expect(result[1]).to.equal(new bn('5000'));
      expect(result[2]).to.equal(new bn('10000'));
    });
    it('returns right fee for oneToZero swap', async () => {
      const poolSigner = new ethers.VoidSigner(await pool.getAddress(), ethers.provider)
    
      const result = await plugin.connect(poolSigner).beforeSwap.staticCall(
          ZeroAddress,
          ZeroAddress,
          false, // oneToZero
          0,
          0,
          false,
          '0x',
      )
    
      expect(result[1]).to.equal(new bn('10000'));
      expect(result[2]).to.equal(new bn('10000'));
    });
});
```

Here we have 2 tests checking that our Plugin returns right fee values on `zeroToOne` and `oneToZero` swaps.

The key thing here is that to able to call <kbd>beforeSwap</kbd> hook on our plugin we have to "impersonate" the pool. Send a transaction where from is the pool's address. This is because <kbd>beforeSwap</kbd> hook is protected by `onlyPool` modifier. So we make a `staticCall` (simulation) which allows us to do so.

Inside the tests whe check that:

* in case of `zeroToOne` the plugin returns overrideFee = 0.5%, pluginFee = 1%
* in case of `oneToZero` the plugin returns overrideFee = 1%, pluginFee = 1%

**Next Steps**

Congratulations on building and testing your very first Algebra Plugin! Now you are ready to proceed to a deployment of your masterpiece. [plugin-deployment.md](plugin-deployment.md "mention") section will help you with that task.
