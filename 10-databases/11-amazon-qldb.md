# Amazon QLDB

## What Is QLDB?

- **Amazon QLDB (Quantum Ledger Database)** — fully managed **ledger database** with an immutable, **cryptographically verifiable** transaction log.
- Maintains a **complete and verifiable history** of all changes made to your data.
- Designed for systems of record where data integrity and auditability are critical.

## Key Concepts

- **Journal** — an append-only, immutable log of every data change; the source of truth.
- **Cryptographic verification** — each block in the journal is chained using SHA-256 hashing; any tampering is mathematically detectable.
- **No deletions** — past states of data are permanently recorded; you can query any historical state.
- **Current state** — a materialized view of the latest data derived from the journal.

## How It Differs from a Traditional Database

| Feature | Traditional DB | QLDB |
|---|---|---|
| History | Often lost after updates/deletes | Complete history preserved forever |
| Tampering | Possible (can alter records) | Mathematically detectable via crypto verification |
| Audit trail | Manual (triggers, CDC) | Built-in — every change automatically journaled |

## Use Cases

| Use Case | Why QLDB? |
|---|---|
| **Financial records** | Immutable audit trail for transactions, transfers |
| **Supply chain systems** | Track and trace inventory, spare parts movement |
| **Claim history** | Insurance claim lifecycle with full history |
| **HR / Payroll records** | Immutable employee data change history |
| **Healthcare** | Patient record history with verifiable integrity |

---

## Key Points / Exam Tips

- **Trigger:** "immutable," "cryptographically verifiable," "audit log," "ledger," "tamper-proof" → **Amazon QLDB**
- **Trigger:** "trace every change to data, verify no tampering" → **QLDB**
- QLDB is **not a blockchain** — it is a centralized ledger managed by AWS; blockchain is decentralized multi-party consensus
- QLDB uses **SHA-256 hashing** to chain journal entries — any modification is detectable
- QLDB journal is **append-only** — no deletes, no updates to history
- Use QLDB when you need: "prove this record has never been altered" or "show complete history of a record"
