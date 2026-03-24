# Agentic Commerce Protocol (ACP) Integration Manifest

This manifest maps the ecosystem flow for the Agentic Commerce Protocol (ACP) on MultiversX, bridging on-chain physical or digital goods with AI agents via standardized e-commerce APIs.

## 1. Product Discovery
- **Tool**: `acp:shop:browse` -> The agent navigates to the merchant's `products.json` endpoint (the ACP Feed Generator).
- The agent parses the returned JSON containing the product catalog, standard attributes, and pricing in specific ESDT or EGLD.
- **Action**: The agent selects the desired product(s) or service(s) to purchase.

## 2. Cart and Checkout
- **Tool**: `acp:cart:add` -> The agent constructs a standard POST request to the merchant's `/cart` endpoint.
- **Tool**: `acp:checkout:initiate` -> The agent requests checkout at `/checkout` with shipping/billing data if applicable.
- The server responds with a serialized **Transaction Payload** required for payment.

## 3. Payment Signing
- **Tool**: `mpp:wallet:signAndSend` -> The agent extracts the transaction payload (e.g., `receiver`, `data`, `value`), signs it, and broadcasts it to the MultiversX network.
- The agent captures the resulting transaction hash.

## 4. Verification and Fulfillment
- **Tool**: `acp:checkout:verify` -> The agent polls the merchant server with the transaction hash to confirm the order status.
- The merchant's ACP Gateway verifies the smart contract interaction (SC call to NFT marketplace or merchant wallet).
- The merchant fulfills the digital or physical order and returns a success confirmation via the ACP API.

## 5. Analytics
- **Tool**: `acp:analytics:recordOrder` -> High-level dashboards index the successful ACP transaction using Elasticsearch indexing on the merchant smart contracts, generating ecosystem reports.
