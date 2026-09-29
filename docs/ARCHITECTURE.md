# Architecture — Digital Wallet P2P Transfer

High-level reference for evaluating **digital wallet P2P transfer** inside a peer-to-peer mobile wallet. Educational only; vendor designs vary.

## Logical Components

```
┌──────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│ Wallet app / API │────►│ Transfer intent  │────►│ Risk / KYC /    │
│ send UI          │     │ ID + fee quote   │     │ velocity engine │
└──────────────────┘     └────────┬─────────┘     └────────┬────────┘
                                  │                        │
                                  ▼                        ▼
                         ┌──────────────────┐     ┌─────────────────┐
                         │ Sender ledger    │────►│ Receiver ledger │
                         │ hold → debit     │     │ credit          │
                         └──────────────────┘     └────────┬────────┘
                                                           │
                                                           ▼
                                                  ┌─────────────────┐
                                                  │ Receipts &      │
                                                  │ notifications   │
                                                  └─────────────────┘
```

## Suggested Transfer Metadata

| Field | Meaning |
|-------|---------|
| `transfer_intent_id` | Durable ID created before ledger moves |
| `sender_wallet_id` | Source wallet |
| `receiver_wallet_id` | Destination wallet (resolved from alias/MSISDN) |
| `gross_amount` | Amount requested by sender |
| `fee_amount` | Disclosed platform or corridor fee |
| `net_credit` | Amount credited to receiver |
| `status` | `quoted` → `authorized` → `pending` → `completed` / `reversed` |

## Audit Events Worth Keeping

- Intent create and fee quote snapshot  
- Risk decision (pass / soft decline / hard decline)  
- Sender hold and final debit  
- Receiver credit confirmation  
- Notification delivery refs  
- Reverse / expire of abandoned intents  

## Failure Modes to Design For

- Alias resolving to the wrong party (confirmation UX required)  
- Timeout after hold but before credit (idempotent status poll)  
- Receiver KYC freeze mid-transfer  
- Duplicate client retries without shared intent ID  

## Related Reading

Live product reading: [evdsystem.com](https://evdsystem.com/), [About MoboGage](https://evdsystem.com/about-mobogage/).

Platforms in the MoboGage / EVD System family keep wallet ledgers append-only so support can reconstruct any P2P send without rewriting history.
