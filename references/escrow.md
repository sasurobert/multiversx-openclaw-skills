# Escrow Contract (ACP)

Lock funds for agent jobs, release on verification, refund after deadline.

## Endpoints

### `deposit(job_id, receiver, poa_hash, deadline)`
- Lock EGLD or any ESDT token in escrow
- `payableInTokens: ["*"]` — send payment with the transaction
- `receiver` is the agent's address
- `poa_hash` is a hash of the proof-of-agreement
- `deadline` is a Unix timestamp (seconds) after which refund is allowed
- Gas: 15,000,000

### `release(job_id)`
- Release escrowed funds to the receiver
- Callable by either the employer or the receiver (once the job is verified in the Validation Registry, either party can trigger the settlement payout)
- Job must be verified in the Validation Registry (`job.status == JobStatus::Verified`)
- Gas: 10,000,000

### `refund(job_id)`
- Refund escrowed funds to the employer
- Anyone can call after the deadline passes (allows automated cleanup)
- **Refund Lock Protection**: Reverts with `ERR_JOB_ALREADY_VERIFIED` if the job has already been marked `Verified` in the Validation Registry, preventing employers from waiting out the clock to claw back completed work
- Gas: 10,000,000

## View Functions

### `get_escrow(job_id) → EscrowData`
Returns:
```typescript
EscrowData {
  employer: Address;
  receiver: Address;
  token_id: EgldOrEsdtTokenIdentifier;
  token_nonce: u64;
  amount: BigUint;
  poa_hash: bytes;
  deadline: u64;           // Unix timestamp
  status: EscrowStatus;    // Active (0), Released (1), Refunded (2)
}
```

## Escrow Status Flow

```
Active (0)  ──release() [employer or receiver]──▶  Released (1)
    │
    └──refund() [after deadline if not verified]──▶  Refunded (2)
```

## Events

| Event | Fields |
|:---|:---|
| `escrow_deposited` | job_id, employer, amount |
| `escrow_released` | job_id, receiver, amount |
| `escrow_refunded` | job_id, employer, amount |
