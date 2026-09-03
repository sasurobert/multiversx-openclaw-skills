# MPP Sessions (State Channels)

MPP Sessions enable high-frequency, micro-payments between agents without the latency and cost of on-chain transactions for every unit of service. They use a **State Channel** model where funds are locked in escrow, and cumulative vouchers are exchanged off-chain.

## 🏛️ Architecture

1. **Open**: Employer locks funds (`amount_locked`) in the `mpp-session-mvx` contract.
2. **Transact**: Employer sends signed **vouchers** to the Receiver off-chain. Each voucher is cumulative (authorizes total `X` so far).
3. **Settle**: Receiver submits the **latest** voucher to the contract to claim cumulative earnings without closing the channel.
4. **Close**: Mutual immediate closure via `close` with the latest voucher, or 2-phase unilateral closure via `request_close` followed by `finalize_close`.

## 🆔 Channel ID

The `channel_id` is a deterministic `keccak256` hash computed by the contract:
`keccak256(employer_address + receiver_address + channel_nonce)`

## 🎟️ Voucher Primitive

A voucher is a cryptographic Ed25519 signature over the following binary payload:

```
keccak256(
    "mpp-session-v1" + 
    contract_address (32 bytes) + 
    channel_id (32 bytes) + 
    cumulative_amount (32 bytes big-endian zero-padded) + 
    nonce (8 bytes u64 big-endian)
)
```

- **Domain Separator**: `mpp-session-v1` (ASCII bytes)
- **Contract Address**: 32-byte public key of the `mpp-session-mvx` contract.
- **Cumulative Amount**: Canonical 32-byte BE zero-padded integer representation (preventing encoding mismatch reverts).
- **Complexity**: Vouchers are strictly monotonic and cumulative.

## 🛠️ Skills & Endpoints

### Smart Contract Endpoints

| Endpoint | Arguments | Description |
|:---|:---|:---|
| `open` | `receiver`, `token`, `amount` | Locks funds and opens a payment channel. |
| `top_up` | `channel_id` | Adds additional funds to an active channel. |
| `settle` | `channel_id`, `amount`, `nonce`, `signature` | Claims cumulative funds via voucher without terminating channel. |
| `close` | `channel_id`, `amount`, `nonce`, `signature` | Closes session immediately, pays receiver cumulative amount and refunds remainder to employer. |
| `request_close` | `channel_id` | Starts 2-phase closing challenge window (`Closing = 2`) if counterparty is unresponsive. Receiver can still settle valid vouchers during the window. |
| `finalize_close` | `channel_id` | Terminate session and refund remaining funds to employer after challenge deadline expires. |

### Facilitator API

| Type | Endpoint | Description |
|:---|:---|:---|
| `POST` | `/sessions` | Register a new session on the facilitator. |
| `POST` | `/sessions/{id}/vouchers` | Submit a new signed voucher (off-chain). |
| `GET` | `/sessions/{id}` | Query latest voucher and session state. |

## 💡 Example: Agentic Micropayments

1. **Employer Agent**: "I need 1000 AI inferences. I'll open a session for 100 EGLD."
2. **Setup**: Employer calls `open(receiver, "EGLD", 100)`.
3. **Execution**: For each inference, Employer sends an off-chain voucher:
   - Inference 1: `signVoucher(amount=0.1, nonce=1)`
   - Inference 2: `signVoucher(amount=0.2, nonce=2)`
   - ...
4. **Mutual Closure**: After 1000 inferences, Receiver calls `close(channel_id, amount=10, nonce=1000, signature)` to receive 10 EGLD while returning 90 EGLD refund immediately to Employer.
