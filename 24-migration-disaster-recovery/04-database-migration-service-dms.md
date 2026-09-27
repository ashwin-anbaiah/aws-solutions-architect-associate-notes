# AWS Database Migration Service (DMS) and Schema Conversion Tool (SCT)

## What Is DMS?

**AWS Database Migration Service (DMS)** migrates databases to AWS with minimal downtime. It supports both homogeneous migrations (same engine type) and heterogeneous migrations (different engine types), and can keep source and target in sync via **Change Data Capture (CDC)** while the application remains live.

---

## Migration Types

### Homogeneous Migration (Like-to-Like)
- Source and target are the **same database engine**.
- Schema is directly compatible — no conversion needed.
- **Examples:**
  - On-premises MySQL → Amazon RDS for MySQL
  - On-premises Oracle → Amazon RDS for Oracle
  - On-premises PostgreSQL → Amazon Aurora PostgreSQL

### Heterogeneous Migration (Different Engines)
- Source and target are **different database engines**.
- Schema must be converted first using **AWS Schema Conversion Tool (SCT)**.
- **Examples:**
  - On-premises Oracle → Amazon Aurora PostgreSQL
  - On-premises SQL Server → Amazon Aurora MySQL
  - On-premises Oracle → Amazon Redshift

---

## Schema Conversion Tool (SCT)

- **SCT** converts the database schema (DDL, stored procedures, views, triggers) from one engine to another.
- Identifies objects that cannot be automatically converted and marks them for manual review.
- SCT is installed locally (desktop application) — not a cloud service.
- **Only needed for heterogeneous migrations** — not required when source = target engine.

---

## DMS Migration Steps

```
1. Create Target Database (e.g., RDS instance, Aurora cluster)
2. Migrate Schema (use SCT for heterogeneous, or export/import for homogeneous)
3. Set up Replication Instance (DMS EC2-based server)
4. Initiate Full Load — copy all existing data from source to target
5. Enable Change Data Capture (CDC) — capture ongoing changes during migration
6. Switch Application to new database (cutover)
```

---

## The Replication Instance

- DMS uses an **EC2-based replication instance** that sits between source and target.
- It reads from the source, transforms if needed, and writes to the target.

| Instance Family | Best For |
|---|---|
| General Purpose (T3) | Small workloads, testing, dev |
| Memory Optimized (R5/R6i) | High-throughput migrations, heavy CDC, large in-memory data |
| Compute Optimized (C5/C6i) | Compute-intensive transformations, many parallel tasks |

- **Multi-AZ replication instance:** synchronous replication between primary and standby replication instances — for high availability during the migration.

---

## Change Data Capture (CDC)

- **CDC** captures ongoing changes (INSERTs, UPDATEs, DELETEs) on the source database during and after the full load.
- Ensures target stays in sync with source up to the moment of application cutover.
- Enables near-zero-downtime migrations — the app can stay live on the source DB until cutover.

---

## DMS with Snowball Edge

- For very large databases where network bandwidth is a constraint, DMS integrates with **AWS Snowball Edge**.
- Data is loaded onto a Snowball Edge device locally, shipped to AWS, ingested into S3, then DMS picks up from S3.
- Useful for petabyte-scale database migrations.

---

## Supported Sources and Targets

**Sources (partial list):** Oracle, SQL Server, MySQL, PostgreSQL, MariaDB, MongoDB, SAP, IBM DB2, on-premises or in the cloud.

**Targets (partial list):** Amazon RDS (any engine), Aurora, Amazon Redshift, DynamoDB, S3, Kinesis Data Streams, OpenSearch.

---

## MGN vs DMS Comparison

| Dimension | AWS MGN | AWS DMS |
|---|---|---|
| Migrates | Entire servers (OS + app) | Databases only |
| Replication | Block-level | Row/transaction-level (CDC) |
| Heterogeneous support | No | Yes (with SCT) |
| Migration type | Rehost (Lift & Shift) | Replatform |
| Target | Amazon EC2 | RDS, Aurora, Redshift, DynamoDB |
| Schema conversion | N/A | SCT for heterogeneous |

---

## Key Points / Exam Tips

- **DMS = database migration;** MGN = server migration. Do not mix them up.
- **SCT is only needed for heterogeneous migrations** — same-engine migrations don't need it.
- CDC keeps source and target in sync during the migration window, enabling near-zero-downtime cutover.
- The DMS replication instance is EC2-based — choose memory-optimized for high-throughput workloads.
- **Multi-AZ replication instance** = HA for the DMS process itself (not the database).
- DMS can write to DynamoDB and S3 as targets — not just relational databases.
- **Full pattern for "SQL Server → Aurora PostgreSQL, minimal downtime":** SCT (schema) + DMS (data + CDC) + Babelfish for query compatibility.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Migrate Oracle/SQL Server to Aurora with minimal downtime" | DMS + SCT (heterogeneous) + CDC |
| "Migrate MySQL to RDS MySQL, same engine" | DMS (homogeneous, no SCT needed) |
| "Keep source DB live during migration, near-zero downtime" | DMS with CDC |
| "Schema conversion from Oracle to PostgreSQL" | AWS Schema Conversion Tool (SCT) |
| "Migrate large DB, limited bandwidth" | DMS + Snowball Edge integration |
