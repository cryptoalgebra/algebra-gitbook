# Subgraph Deployment

Algebra Protocol has 3 Subgraphs:

* Algebra
* AlgebraFarming
* Blocklytics (polygonBlocks)

For each directory you need to run the same proccess:

```
$ yarn
$ yarn codegen
$ yarn build 
```

#### Deploy

For deploy you need to run:

```
$ yarn graph deploy --node https://api.thegraph.com/deploy/ --ipfs https://api.thegraph.com/ipfs/ --access-token <access-token> <graph-name> subgraph.yaml
```

### AlgebraFarming

Before building you need to make a few changes:

Update FarmingCenterAddress in AlgebraFarming/src/utils/constants.ts Update network, startBlock and addresses in subgraph.yaml

After that you need to run:

```
$ yarn
$ yarn codegen
$ yarn build 
```

#### Deploy

For deploy you need to run:

```
$ yarn graph deploy --node https://api.thegraph.com/deploy/ --ipfs https://api.thegraph.com/ipfs/ --access-token <access-token> <graph-name> subgraph.yaml
```
