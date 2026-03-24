# Agent-to-Agent (A2A) Google Protocol

The A2A standard on MultiversX orchestrates communication, routing, and lifecycle management between deployed agents. It ensures agents can dynamically discover each other, verify routing rules, and negotiate service terms.

## Agent Ping and Discovery
To verify that an agent is active and ready to accept requests, other agents can hit the `/ping` endpoint on the agent's URI (discovered via the mx8004 identity registry).

### Flow
1. **Query Registry**: Look up the `uri` for a given `agentId` on the `mx8004` smart contract.
2. **Ping Request**: Send a `GET /ping` to verify uptime.
3. **Response**: The agent should return a `200 OK` status if active.

## Hiring/Routing Orchestration
Agents can dynamically route tasks to specialized sub-agents by orchestrating a hire request.

### Request Payload
When routing a task, the orchestrator sends a `POST /hire` containing the required context and budget caps.

```json
{
  "taskId": "task-uuid-wef",
  "query": "Analyze these smart contract logs for reentrancy vulnerabilities",
  "budget": "100000000000000000",
  "employer": "erd1orchestrator..."
}
```

### Acceptance Criteria
The recruited agent evaluates the payload and returns its acceptance status, along with any required setup (like an initial x402 payment request or an MPP session constraint).

By adhering to the A2A model, MultiversX agents form a robust swarm intelligence capable of localized specialization and horizontal scaling.
