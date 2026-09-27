# Amazon DynamoDB

## What Is DynamoDB?

- **Amazon DynamoDB** — fully managed, serverless **NoSQL (key-value)** database service.
- **Single-digit millisecond** read/write latency at any scale — millions of requests/sec, trillions of rows, 100s of TB of storage.
- Serverless: no provisioning or capacity management, encryption at rest by default, 99.999% availability.

## Data Model

- **Table** — basic storage entity.
- **Item** — a row; contains Partition Key, optional Sort Key, and attributes (key-value pairs).
- **Partition Key** (Hash Key) — determines which physical partition stores the item.
- **Sort Key** (Range Key) — optional; allows multiple items with the same Partition Key; enables range queries.
- **Primary Key** = Partition Key alone, OR Partition Key + Sort Key.

## Indexes

| Index | Description | Key difference |
|---|---|---|
| **GSI (Global Secondary Index)** | Different Partition Key and optional Sort Key from the base table; own read/write capacity | Can be created at any time; supports any query pattern |
| **LSI (Local Secondary Index)** | Same Partition Key as base table, different Sort Key | Must be created at table creation time; shares table's read/write capacity |

- GSI: "I need a completely different access pattern (query by a non-key attribute)"
- LSI: "I want to sort the same partition's data by a different attribute"

## Read/Write Capacity Modes

| Mode | Description | Best For |
|---|---|---|
| **Provisioned (default)** | Pre-define RCUs and WCUs; risk of throttling if traffic spikes | Predictable, steady traffic |
| **On-Demand** | Auto-scales; pay per request; higher per-request cost | Unpredictable or spiky workloads |

## Table Classes

| Class | Storage cost | Read/Write cost | Best for |
|---|---|---|---|
| **Standard (default)** | Higher | Lower | Frequently accessed data |
| **Standard-IA (Infrequent Access)** | Lower | Higher | Rarely accessed or cold data |

## DynamoDB Global Tables

- **Multi-region, multi-active (active-active)** tables.
- Can read **and write** to any replica in any region.
- 99.999% availability; DynamoDB handles conflict resolution automatically.
- Use case: global applications requiring low-latency writes and reads from any region, region-level disaster recovery.

## DynamoDB Streams

- Captures **item-level modifications** in a time-ordered sequence.
- Stores changes for **24 hours** exactly once, strictly ordered per partition.
- Operates asynchronously — no performance impact on the table.
- Use cases: real-time monitoring, triggering Lambda on data changes, CDC (Change Data Capture), backup pipelines to Kinesis.

## DynamoDB TTL (Time To Live)

- Automatically deletes expired items based on a **numeric Unix epoch timestamp attribute**.
- No extra cost for TTL deletions.
- Use cases: user sessions/tokens, rate-limiting counters, OTP/verification codes.

## DynamoDB Backup

| Type | Description |
|---|---|
| **PITR (Point-in-Time Recovery)** | Continuous backup; restore to any second in the last 35 days; creates a **new table** |
| **On-Demand Backup** | Full manual snapshot; retained indefinitely; can use AWS Backup for cross-region copy |

## DynamoDB Export / Import

- **Export to S3** — uses PITR snapshots; does not consume RCU/WCU; useful for analytics, Athena queries, long-term storage.
- **Import from S3** — bulk load from CSV, DynamoDB JSON, or ION; does not use WCUs; always creates a new table (cannot overwrite existing).

## Query vs Scan

| Operation | Description | Performance |
|---|---|---|
| **Query** | Fetch items by Partition Key (+ optional Sort Key conditions) | Fast — only reads matching partition |
| **Scan** | Read every item in the table | Slow and expensive for large tables |

---

## Key Points / Exam Tips

- **Trigger:** "NoSQL, serverless, millisecond latency, any scale" → **DynamoDB**
- **Trigger:** "active-active writes across multiple regions" → **DynamoDB Global Tables** (not Aurora Global DB)
- **Trigger:** "TTL to auto-expire items" → **DynamoDB TTL**
- **Trigger:** "react to table changes in real-time" → **DynamoDB Streams** + Lambda
- **Trigger:** "export DynamoDB data for Athena analytics" → **DynamoDB Export to S3**
- GSI can be created at **any time**; LSI must be created **at table creation only**
- PITR restore and import from S3 both create a **new table** — they cannot overwrite an existing one
- Predictable traffic → Provisioned mode (cheaper); Spiky/unknown → On-Demand mode (no throttling)
- **Standard-IA** table class: lower storage cost, higher per-read cost — for cold/archival data
