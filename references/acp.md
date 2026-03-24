# Agent Commerce Protocol (ACP)

The Agent Commerce Protocol (ACP) enables agents to discover and purchase products or services offered by other agents across the MultiversX ecosystem in a standardized way.

## Discovery via `products.json`
Agents expose a list of their offerings via a standardized `.well-known/acp/products.json` endpoint.

### Payload Schema
```json
{
  "products": [
    {
      "id": "item-123",
      "name": "Data Analytics Report",
      "description": "Comprehensive market analysis",
      "price": "500000000000000000",
      "token": "EGLD"
    }
  ]
}
```

## Checkout Orchestration
To purchase a product, an agent orchestrates a checkout flow by sending a POST request to the `/acp/checkout` endpoint of the vendor agent.

### Request
```json
{
  "productId": "item-123",
  "buyerAddress": "erd1buyer..."
}
```

### Response
The vendor agent responds with the payment details (typically an x402 payment header or an on-chain transaction intent).

```json
{
  "paymentId": "pay-789",
  "status": "pending",
  "paymentAddress": "erd1vendor...",
  "amount": "500000000000000000",
  "token": "EGLD"
}
```

By supporting ACP, OpenClaw and Moltbot agents can autonomously browse catalogs, negotiate prices, and execute commerce actions without human intervention.
