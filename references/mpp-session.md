# MPP Sessions (State Channels)

MPP Sessions enable high-frequency, micro-payments between agents without the latency and cost of on-chain transactions for every unit of service. They use a **State Channel** model where funds are locked in escrow, and cumulative vouchers are exchanged off-chain.

## 🏛️ Architecture

1.  **Open**: Employer locks funds (`amount_locked`) in the `mpp-session-mvx` contract.
2.  **Transact**: Employer sends signed **vouchers** to the Receiver off-chain. Each voucher is cumulative (Authorizes total `X` so far).
3.  **Settle**: Receiver submits the **latest** voucher to the contract to claim funds.
4.  **Close**: One party closes the session to release remaining funds.

## 🆔 Channel ID

The `channel_id` is a deterministic `keccak256` hash:
`keccak256(employer_address + receiver_address + token_identifier + token_nonce)`

## 🎟️ Voucher Primitive

A voucher is a cryptographic signature over the following payload:

```
keccak256(
    "mpp-session-v1" + 
    contract_address + 
    channel_id + 
    cumulative_amount + 
    nonce
)
```

- **Domain Separator**: `mpp-session-v1` (string)
- **Contract**: The address of the `mpp-session-mvx` contract.
- **Complexity**: Vouchers are cumulative. If Voucher 1 is for 10 tokens and Voucher 2 is for 15, the contract only cares about the latest one (15).

## 🛠️ Skills & Endpoints

### Smart Contract Endpoints

| Endpoint | Arguments | Description |
|:---|:---|:---|
| `open` | `receiver`, `token`, `amount` | Locks funds and opens a channel. |
| `top_up` | `channel_id` | Adds more funds to an existing channel. |
| `settle` | `channel_id`, `amount`, `nonce`, `signature` | Claims cumulative funds via voucher. |
| `close` | `channel_id` | Terminate session (final settlement + release). |
| `request_close` | `channel_id` | Start a challenge period if the other party is unresponsive. |

### Facilitator API

| Type | Endpoint | Description |
|:---|:---|:---|
| `POST` | `/sessions` | Register a new session on the facilitator. |
| `POST` | `/sessions/{id}/vouchers` | Submit a new signed voucher (off-chain). |
| `GET` | `/sessions/{id}` | Query latest voucher and session state. |

## 💡 Example: Agentic Micropayments

1. **Employer Agent**: "I need 1000 AI inferences. I'll open a session for 100 EGLD."
2. **Setup**: Employer calls `open(receiver, "EGLD", 100)`.
3. **Execution**: For each inference, Employer sends a voucher:
   - Inference 1: `signVoucher(amount=0.1, nonce=1)`
   - Inference 2: `signVoucher(amount=0.2, nonce=2)`
   - ...
4. **Settlement**: After 1000 inferences, Receiver calls `settle(channel_id, amount=10, nonce=1000, signature)`.
5. **Cleanup**: Receiver calls `close(channel_id)` to get paid and return change to Employer.
