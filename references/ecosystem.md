# MultiversX Ecosystem Skills

This document defines generalized skills required by OpenClaw and Moltbot agents for seamless operation within the broader MultiversX ecosystem, extending beyond payments and identity into network insights and arbitrary contract execution.

## 1. Generalized Smart Contract Execution
Agents often need to interact with arbitrary DApps (DeFi routers, NFT marketplaces, Staking modules) that may not conform to specific agentic standards.

### Generic Querying
Using the `SmartContractController` from SDK v15, agents can query any public endpoint.
- **Inputs**: Contract Address, Function Name, Hex-encoded Arguments.
- **Outputs**: Raw returned generic data to be parsed by the agent's LLM or specific formatters.

### Generic Execution
Using the `SmartContractTransactionsFactory`, agents can construct and sign arbitrary transaction intents.
- **Inputs**: Sender, Contract, Function, Values (EGLD/ESDT), Gas Limits.
- **Execution**: The agent signs with its local PEM or delegates to a relayer.

## 2. ElasticSearch Analytics
Agents use MultiversX ElasticSearch nodes (or localized indexers) to gather financial and performance metrics about themselves or other agents.

### Analytics Endpoints
- **Revenue Tracking**: Aggregating all incoming transfers or claimed MPP session vouchers over a specific timeframe.
- **Spend Tracking**: Identifying all outbound capital routed to sub-agents or service providers.
- **Performance Evaluation**: Utilizing historical on-chain interactions to dynamically tune an agent's internal pricing oracle.

## 3. Network Awareness
Agents actively monitor network configuration to ensure transactions are built with the correct parameters (Chain ID, Min Gas Price) and verify the final status of a dispatch via observer nodes.
