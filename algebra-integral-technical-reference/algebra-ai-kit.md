---
icon: robot
---

# Algebra AI Kit

The Algebra AI Kit makes any Algebra-powered DEX natively accessible to AI agents. It exposes the full DEX functionality — swaps, liquidity management, pool creation, protocol analytics — as a set of MCP tools that any AI agent can discover and call through natural language.

The Kit is built on the [Model Context Protocol (MCP)](https://modelcontextprotocol.io/), the open standard adopted by Anthropic, OpenAI, Google, and Microsoft. Any MCP-compatible client — Claude Code, Cursor, Codex, or custom agent frameworks — connects to your DEX endpoint and interacts with it programmatically.

**Live reference deployment:** `https://integral-mcp.algebra.finance/mcp`

### Quickstart

#### Connect with Claude Code

Run this one command in your terminal:

```bash
claude mcp add algebra-mcp --transport http https://integral-mcp.algebra.finance/mcp
```

Claude Code will discover all available tools automatically.

#### Connect with Cursor

Open **Cursor Settings → MCP → Add New Server** and fill in:

| Field | Value                                      |
| ----- | ------------------------------------------ |
| Name  | `algebra-mcp`                              |
| Type  | HTTP / Streamable HTTP                     |
| URL   | `https://integral-mcp.algebra.finance/mcp` |

Save and restart Cursor.

#### Connect with any MCP client

```
Transport: Streamable HTTP
URL: https://integral-mcp.algebra.finance/mcp
MCP spec: 2025-03-26
```

***

### Wallet Setup

Read-only operations — pool data, quotes, position queries — work without a wallet. For transaction execution, the agent needs a wallet to sign. Private keys are never stored on or transmitted through the MCP server.

#### Coinbase Agentic Wallet (recommended)

The agent gets a dedicated wallet with configurable permissions. You define spending limits, restrict to specific contracts, and fund it with ETH for gas. This is the model we use internally and have tested end-to-end with full tool execution.

* Docs: [docs.cdp.coinbase.com/agentic-wallet](https://docs.cdp.coinbase.com/agentic-wallet/cli/welcome)

#### OKX OnchainOS

DeFi-focused agentic wallet infrastructure.

* Docs: [web3.okx.com/onchainos](https://web3.okx.com/onchainos)

#### Privy Agentic Wallet

Embedded wallet for autonomous agents.

* Docs: [docs.privy.io/recipes/agent-integrations](https://docs.privy.io/recipes/agent-integrations/agentic-wallets)

#### Private key (testing only)

For quick testnet experiments, you can pass a private key from a test wallet directly to the agent via an environment file. Use a dedicated test wallet with a small balance only.

***

### Tools Reference

The MCP server exposes 11 tools across 5 categories.

| Category      | Tool                      | Description                                       |
| ------------- | ------------------------- | ------------------------------------------------- |
| **Tokens**    | `getTokens`               | Returns the list of tokens available on the DEX   |
|               | `approveTokens`           | Prepares an ERC-20 approval transaction           |
| **Trading**   | `getPrice`                | Returns the current price for a token pair        |
|               | `quoteSwap`               | Returns a full swap quote with execution calldata |
| **Pools**     | `getPools`                | Returns all pools with metadata                   |
|               | `getPoolAPR`              | Returns APR data for pools                        |
|               | `addLiquidityToPool`      | Adds liquidity to a specified pool and range      |
|               | `removeLiquidityFromPool` | Removes liquidity from a position                 |
|               | `createPool`              | Creates a new pool with a specified initial price |
| **Positions** | `getPositions`            | Returns all open LP positions for an address      |
| **Protocol**  | `getProtocolStats`        | Returns protocol-wide stats: volume, fees, TVL    |

`createPool` is a paid tool gated via x402 micropayments.

***

### x402 Micropayments

`createPool` is protected by the [x402 protocol](https://x402.org/). When an agent calls the tool without payment, the server returns the exact payment requirement. x402-compatible clients handle this automatically — the agent pays, retries, and proceeds without any manual step.

This means your DEX earns direct revenue from every agentic pool creation.

**Default price:** 1 USDC per pool creation (configurable at deployment time).

**Flow:**

```
Agent calls createPool
  → Server returns 402 + payment requirements
  → Agent pays via x402
  → Server executes createPool
  → Agent receives transaction hash
```

***

### Security

Security is central to deploying agents with real capital. Here is how the Algebra AI Kit addresses the key risk areas.

#### Key custody

Private keys never touch the MCP server at any point. The server prepares transaction calldata — the wallet holds the keys and signs independently. The MCP layer has no access to funds and cannot initiate transactions on its own.

This means the attack surface of the MCP server is limited to read operations and calldata preparation. Even if the server were fully compromised, an attacker cannot move funds without access to the wallet.

#### No new contracts

The MCP server is an API layer over existing Algebra Integral contracts. No new smart contracts are deployed. There is nothing new to audit from a contract perspective.

#### Transaction atomicity

The server prepares calldata; the wallet signs and broadcasts. If the server goes down mid-flow, or a broadcast fails for any reason, the transaction is simply not submitted. There is no partial execution state.

#### Wallet-level permission controls

Permission boundaries are enforced at the wallet level. Depending on the agentic wallet you use, you can:

* Set per-transaction spending limits
* Restrict the agent to specific contract addresses
* Require manual approval for transactions above a threshold
* Revoke agent access at any time

This means operators can run agents with narrow, well-defined permissions — for example, an LP management agent that can only interact with a specific pool contract and has a $10,000 per-transaction cap.

#### Prompt injection

Agents operating in DeFi environments can be exposed to malicious inputs — for example, a token whose name or description contains instructions designed to manipulate the agent's behavior. Standard precautions apply: use system prompts that define a strict operational scope, avoid passing unvalidated user input directly to the agent, and use wallet-level limits as a backstop.

***

### What Agents Can Do

Once connected, agents can reason about your DEX and execute operations through natural language. Below are examples that go beyond simple commands — these illustrate the kind of compound reasoning and strategic execution that becomes possible.

#### Pool Management

* _"I have $10,000 to put to work. Which pool on this DEX gives the best fee APR right now, and how much would I earn per day at current rates?"_
* _"Our USDC/ETH pool price has drifted 2% from market. Find the minimum swap needed to correct it and execute."_
* _"Which of our pools haven't had any volume in the last 24 hours? List them with their current TVL."_

#### Liquidity

* _"I want to add $20,000 to the WBTC/ETH pool. Based on the last 30 days of price movement, what range would have kept me in range the longest while still concentrating liquidity enough to earn meaningful fees?"_
* _"My portfolio has 15 open LP positions. Close everything that's been out of range for more than 48 hours and is earning zero fees."_
* _"Redistribute my liquidity from the two underperforming pools into the one with the highest fee APR over the last 7 days."_

#### Trading

* _"I want to swap $50,000 of ETH to USDC. Break it into chunks to minimize price impact and tell me the optimal execution plan."_
* _"What's the realistic slippage on a 500 ETH sell right now? Compare across the top 3 ETH pools."_

#### Analytics

* _"Which pools generated the most fee revenue relative to TVL this week? I want to understand where capital is being used most efficiently."_
* _"Compare our protocol's volume and TVL trend over the last month. Where are we gaining and losing share?"_

***

### Example Agent Strategies

The MCP server is the execution layer. The strategies below illustrate what builders can construct on top of it using any agent framework.

#### Auto-LP Agent

**What it does:** Monitors your LP positions continuously and rebalances when they go out of range.

**How it works:** The agent polls `getPositions` on a schedule. When a position exits its price range and stops earning fees, the agent calls `removeLiquidityFromPool`, calculates a new optimal range based on current price and recent volatility, and calls `addLiquidityToPool` with the updated parameters. This runs 24/7 without manual intervention.

**Why it matters for a DEX:** Higher fee capture for LPs means better LP retention, which means deeper liquidity and tighter spreads for traders.

#### Yield Optimizer

**What it does:** Scans all pools and reallocates liquidity toward the highest-performing opportunities.

**How it works:** The agent calls `getPoolAPR` across all pools, ranks them by fee APR adjusted for liquidity depth, and moves capital from underperforming positions to high-APR ones. Can be configured with constraints — for example, only allocate to pools with TVL above $500k, or never hold more than 30% in a single pool.

**Why it matters:** Gives LPs a passive management option and increases utilization across the protocol.

#### News/Sentiment Trading Agent

**What it does:** Reads external data sources and executes swaps based on emerging signals.

**How it works:** The agent connects to news feeds, Twitter/X search, or on-chain event streams. When a configured signal fires — a large protocol announcement, a significant wallet movement, a price deviation — it calls `quoteSwap` to check execution conditions and then executes if parameters are met. All within a defined risk budget.

**Why it matters:** Brings a new category of sophisticated trader to your DEX — algo desks and quant teams that currently require custom API integrations to participate.

#### DCA Agent

**What it does:** Buys a specified token at regular intervals regardless of price.

**How it works:** A simple scheduled agent that calls `quoteSwap` at each interval, checks slippage is within tolerance, and executes. Optionally splits large orders across multiple pools to minimize impact.

**Why it matters:** Creates consistent, predictable volume — particularly valuable during low-activity periods.

#### DEX Admin Agent

**What it does:** Automates routine DEX management tasks that currently require a developer.

**How it works:** The DEX team connects the MCP server to Claude or another assistant and issues commands in plain language. Common workflows that become one-line operations:

* Correcting pool prices after a large imbalancing trade
* Creating new pools for token launches with the correct initial price encoding
* Closing all liquidity positions from a test wallet before a contract upgrade
* Getting a full protocol health summary before a public update

**Why it matters:** Reduces the technical overhead for DEX operators and makes protocol management accessible to non-developer team members.

#### Building Your Own Strategy

Any agent framework that supports MCP over HTTP can connect to the server and build on top of these tools. The MCP server handles the protocol complexity — your strategy logic sits entirely on the agent side, in whatever language or framework you prefer.

For technical details on connecting programmatically, see the MCP Server deployment guide.

***

### Get the AI Kit for Your DEX

The AI Kit is available to any Algebra-powered DEX as a white-label deployment. To discuss the setup that fits your protocol, reach out via the [contact form on algebra.finance](https://algebra.finance) or directly.
