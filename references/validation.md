# Validation Registry

Job lifecycle management — create jobs, submit proofs, verify completion, and manage MIP-compliant decentralized validation.

## Endpoints

### `init_job(job_id, agent_nonce, service_id?)`
- Creates a new job assigned to an agent
- Accepts payment (EGLD or ESDT via `payableInTokens: ["*"]`)
- `service_id` is optional (u32), references a specific service config
- Gas: 15,000,000

### `submit_proof(job_id, proof)`
- Submit proof (typically a hash) for a completed job
- Only callable by the assigned agent
- Gas: 10,000,000

### `verify_job(job_id)` *(owner or authorized validator)*
- Mark a job as verified
- Gas: 6,000,000

### `validation_request(validator_address, request_uri, hash)`
- Request external third-party validation for an off-chain computation or artifact
- Emits request event and creates request tracking entry

### `validation_response(request_id, status, response_uri)`
- Validator submits evaluation verdict (`0 = Rejected`, `1 = Approved`) along with verifiable `response_uri` metadata
- Updates on-chain verification record

### `clean_old_jobs(job_ids...)` 
- Batch cleanup of old job entries
- Gas: 6,000,000 + per-job overhead

## View Functions

### `is_job_verified(job_id) → bool`
Returns whether a job has been verified.

### `get_job_data(job_id) → JobData?`
Returns:
```typescript
JobData {
  status: JobStatus;  // New (0), Pending (1), Verified (2)
  proof: bytes;
  employer: Address;
  creation_timestamp: u64;
  agent_nonce: u64;
  response_uri?: string;
}
```

## Job Status Flow

```
New (0) ──submit_proof()──▶ Pending (1) ──verify_job() / validation_response()──▶ Verified (2)
```
