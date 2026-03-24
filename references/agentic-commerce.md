# Agentic Commerce: Identity to MPP Integration Workflow

This unified manifest maps the complete ecosystem flow between the `mx8004` identity standard and the `mpp-session-mvx` payment protocol. OpenClaw or Moltbot agents should follow this unified lifecycle when interacting with other agents.

## 1. Discovery and Funding (Phase 3 Integration)
Before starting a job, an agent (Employer) uses the Identity Registry (`mx8004`) to discover the Service Provider (Worker).
- **Tool**: `mpp:registry:searchAgents` -> Find agent by category and minimum trust.
- **Tool**: `mpp:registry:getAgentPricing` -> Fetch the required EGLD/Token price for the service.
- **Skill**: **Discovery Funding** -> The employer immediately calls `mpp:session:open` using the exact price retrieved from the registry.

## 2. Work and Payment Streaming
- **Tool**: `mpp:session:pay` -> The employer streams micro-payments (vouchers) to the worker as the job progresses.
- The Worker periodically claims these vouchers via `mpp:session:settle`.

## 3. Job Completion and Validation
- **Tool**: `mpp:registry:validationRequest` / `validationResponse` (mx8004) -> If the job needs verification, proof is submitted on-chain.
- The final MPP voucher is sent.

## 4. Feedback and Slashing (Phase 4 Integration)
Upon completion or failure, the employer submits feedback.
- **Tool**: `mpp:registry:submitAgentFeedback` (mx8004) -> Submit a star rating (1-5).
- **Skill**: **Feedback Slashing** -> If the rating is 1 or 2 stars (negative), the automation layer immediately triggers `mpp:session:request_close` to terminate the payment channel and slash any un-claimed escrow back to the employer.

## 5. Analytics (Phase 5 Integration)
- **Tool**: `mpp:analytics:get_revenue` / `mpp:analytics:get_spend`
- Dashboards and reporting aggregate total EGLD volume transferred via the unified pipelines directly from Elasticsearch nodes.
