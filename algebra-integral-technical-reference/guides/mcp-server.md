---
icon: openai
---

# MCP server

## Deploy Algebra MCP Server

Use this guide to run `algebra-mcp-server` from a published Docker image in GitHub Container Registry (`ghcr.io`).

### Overview

The service exposes:

* `POST /mcp`
* `GET /health`
* `GET /metrics`

By default, the container listens on port `8080`.

{% hint style="info" %}
Examples below use `ghcr.io/cryptoalgebra/algebra-mcp-server`. Replace the tag if you want to deploy a specific version.
{% endhint %}

#### Tooling

Free tools:

* `getTokens`
* `approveTokens`
* `getPrice`
* `quoteSwap`
* `getPools`
* `getPoolAPR`
* `addLiquidityToPool`
* `removeLiquidityFromPool`
* `getPositions`
* `broadcastTx`

Paid tools:

* `createPool`

#### x402

`createPool` is protected with x402. The first unpaid call returns payment requirements. Retry the same request with the signed payment object in `x402_payment`.

### Before You Start

Make sure the target machine has:

* Docker installed
* Network access to `ghcr.io`
* A valid runtime `.env` file

You also need a GitHub token that can read packages.

### Step 1: Authenticate to GHCR

To pull a private image from GitHub Container Registry, log in first.

#### Required token

Use a GitHub `Personal Access Token (classic)` with:

* `read:packages`

The GitHub account that owns the token must also have access to the package itself.

#### Login command

```bash
docker login ghcr.io -u <github-username>
```

### Step 2: Prepare Environment Variables

Create a `.env` file on the deployment host.

Use `.env.example` as the source of truth for all supported variables and comments.

### Step 3: Pull the Image

Pull the default image:

```bash
docker pull ghcr.io/cryptoalgebra/algebra-mcp-server
```

To pull a specific tag:

```bash
docker pull ghcr.io/cryptoalgebra/algebra-mcp-server:<tag>
```

#### ARM hosts

On Apple Silicon or another ARM-based host, you may need to force the image platform if only `amd64` is available:

```bash
docker pull --platform linux/amd64 ghcr.io/cryptoalgebra/algebra-mcp-server
```

{% hint style="info" %}
Use `--platform linux/amd64` if a normal `docker pull` or `docker run` fails because of an architecture mismatch.
{% endhint %}

### Step 4: Run the Container

#### Recommended: pass environment with `--env-file`

```bash
docker run -d \
  --name algebra-mcp-server \
  --restart unless-stopped \
  -p 8080:8080 \
  --env-file .env \
  ghcr.io/cryptoalgebra/algebra-mcp-server
```

If you are pinning a specific tag:

```bash
docker run -d \
  --name algebra-mcp-server \
  --restart unless-stopped \
  -p 8080:8080 \
  --env-file .env \
  ghcr.io/cryptoalgebra/algebra-mcp-server:<tag>
```

#### ARM hosts: run with explicit platform

```bash
docker run -d \
  --platform linux/amd64 \
  --name algebra-mcp-server \
  --restart unless-stopped \
  -p 8080:8080 \
  --env-file .env \
  ghcr.io/cryptoalgebra/algebra-mcp-server
```

#### Alternative: pass variables inline

If you do not want to use an env file, you can pass variables with `-e`:

```bash
docker run -d \
  --name algebra-mcp-server \
  --restart unless-stopped \
  -p 8080:8080 \
  -e ETH_RPC_ENDPOINT=https://... \
  -e EXPLORER_URL=https://... \
  -e GRAPHQL_INFO_URL=https://... \
  -e CONTRACT_NFT_POSITION_MANAGER=0x... \
  -e CONTRACT_SWAP_ROUTER=0x... \
  -e CONTRACT_QUOTER_V2=0x... \
  -e X402_FACILITATOR_URL=https://... \
  -e X402_PAYMENT_ADDRESS=0x... \
  -e X402_CREATE_POOL_PRICE=1.00 \
  ghcr.io/cryptoalgebra/algebra-mcp-server
```

### Step 5: Verify the Deployment

Check that the container is running:

```bash
docker ps
```

Check health:

```bash
curl http://localhost:8080/health
```

Expected response:

```json
{"status":"ok"}
```

The MCP endpoint is available at:

```
http://localhost:8080/mcp
```

### References

* [GitHub Container Registry docs](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
* [GitHub Packages permissions](https://docs.github.com/en/packages/learn-github-packages/about-permissions-for-github-packages)
