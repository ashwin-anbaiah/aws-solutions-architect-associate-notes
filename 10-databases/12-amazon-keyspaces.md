# Amazon Keyspaces

## What Is Amazon Keyspaces?

- **Amazon Keyspaces (for Apache Cassandra)** — fully managed, serverless, **Apache Cassandra-compatible** database service.
- Allows you to run Cassandra workloads on AWS without managing any infrastructure.
- Uses the **Cassandra Query Language (CQL)** — existing Cassandra apps can migrate with minimal code changes.

## Apache Cassandra Basics

- Apache Cassandra is an open-source, distributed **wide-column** NoSQL database.
- Designed for high write throughput, linear scalability, and no single point of failure.
- Data is organized in **keyspaces** (databases) → **tables** → **rows** with flexible columns.
- Common use cases: IoT data, time-series data, large-scale event logging, user activity tracking.

## Key Features

- **Serverless** — no cluster provisioning, patching, or capacity planning.
- **CQL-compatible** — use existing Cassandra drivers and queries.
- **Automatic scaling** — throughput scales up and down based on actual traffic.
- **Two capacity modes:**
  - **On-Demand** — pay per request; for unpredictable workloads
  - **Provisioned** — define read/write capacity; for predictable workloads
- **HA** — data replicated across 3 AZs by default.
- **Encryption at rest** (KMS) and **in transit** (TLS).
- **PITR (Point-in-Time Recovery)** — restore to any point in the last 35 days.

## Use Cases

- Migrating existing on-premises or self-managed Apache Cassandra clusters to AWS
- IoT sensor data ingestion at high write throughput
- User activity logs, event tracking at scale
- Time-series workloads (as an alternative to managing your own Cassandra cluster)

## Keyspaces vs DynamoDB

| Feature | Keyspaces | DynamoDB |
|---|---|---|
| API / Query Language | CQL (Cassandra-compatible) | DynamoDB API |
| Migration path | Cassandra → Keyspaces (minimal change) | New DynamoDB app |
| Data model | Wide-column | Key-value / document |
| Best for | Cassandra workload migration | AWS-native NoSQL at scale |

---

## Key Points / Exam Tips

- **Trigger:** "Apache Cassandra," "CQL," "migrate Cassandra to AWS managed service" → **Amazon Keyspaces**
- **Trigger:** "wide-column NoSQL, no servers to manage, Cassandra-compatible" → **Keyspaces**
- Keyspaces is the **managed Cassandra** service — same as DocumentDB is managed MongoDB, and Neptune is managed graph
- PITR supported for up to **35 days**
- Data is replicated across **3 AZs** automatically
- Both On-Demand and Provisioned capacity modes are available (same pattern as DynamoDB)
