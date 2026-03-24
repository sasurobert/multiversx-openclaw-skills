# x402 Protocol Integration Manifest

This manifest outlines the workflow for utilizing the x402 protocol on MultiversX, enabling AI agents to handle 402 Payment Required HTTP errors seamlessly.

## 1. x402 Interception (The 402 Response)
- The agent makes a standard HTTP request to a paid API or service.
- **Response**: The server responds with `402 Payment Required` and a standard `x402` HTTP header.
    - Example: `x402 address="erd1...", amount="1000000000", token="USDC-123456"`

## 2. Parsing and Evaluation
- **Tool**: `x402:parseHeader` -> The agent extracts the required payment details (recipient address, token identifier, and amount).
- **Logic**: The agent verifies if the requested amount falls within its configured operational budget limits.

## 3. Payment Execution
- **Tool**: `x402:pay` -> The agent constructs a transaction to the recipient address. This can be handled internally via the local `wallet.pem` or relayed via `OpenClaw Relayer` for gasless abstraction.
- Wait for transaction confirmation.

## 4. Authenticated Request
- **Tool**: `x402:sign` -> The agent generates a cryptographic signature using the transaction hash.
- **Request**: The agent replays the original HTTP request, appending the new authorization header:
    - Example: `Authorization: x402 tx="hash", sig="signature"`

## 5. Service Delivery
- The server verifies the transaction on the MultiversX blockchain.
- The server validates the cryptographic signature.
- The server delivers the requested data, compute, or service payload back to the agent.
