# Amazon Redshift

## What Is Redshift?

- **Amazon Redshift** — fully managed, **petabyte-scale data warehouse** service.
- Uses **Massively Parallel Processing (MPP)** architecture with a SQL interface for queries.
- Designed for **OLAP** (Online Analytical Processing) — historical analytics and business intelligence.
- Integrates with BI tools: **Amazon QuickSight**, Tableau, and others.
- Supports zero-ETL / no-code data ingestion across data lakes, databases, and streaming data.
- Data sharing across AWS regions, teams, and third-party warehouses without data movement.

## Cluster Options

### Redshift Serverless
- No cluster to manage; fast setup, minimal admin effort.
- Uses **RPU (Redshift Processing Units)** — scales CPU, memory, and network automatically.
- Best for: sporadic, unpredictable, or bursty analytical workloads.

### Redshift Provisioned Cluster
- **Leader node** — distributes queries across compute nodes; handles client connections.
- **Compute nodes** — execute query fragments in parallel.
- Choose node type and node count; manual and scheduled scaling.
- Best for: steady, predictable analytical workloads with consistent usage.

## Redshift Spectrum

- Query data **directly in Amazon S3** without loading it into Redshift.
- Automatically scales compute independently of the Redshift cluster.
- Enables **separation of storage (S3) and compute (Redshift)**.
- Best for: large, infrequently accessed data in the data lake.

## Loading Data into Redshift

- **COPY command** — bulk load from S3 into Redshift tables (most efficient method).
- **Auto Copy Jobs** — use S3 Event Integration to automatically COPY new files as they arrive in S3.
- **Zero-ETL integration** — direct integration with RDS, Aurora, and DynamoDB without building ETL pipelines.

## Redshift vs Athena

| Dimension | Redshift | Athena |
|---|---|---|
| Type | Data warehouse (OLAP) | Serverless SQL query engine |
| Data location | Loaded into Redshift | Data stays in S3 |
| Best for | Regular BI, complex analytics, dashboards | Ad-hoc queries, log analysis |
| Cost model | Cluster uptime / RPU | Per query ($5/TB scanned) |
| Performance | Optimized for repeated queries | Optimized for ad-hoc queries |

---

## Key Points / Exam Tips

- **Trigger:** "data warehouse," "petabyte analytics," "OLAP," "BI reporting," "SQL on historical data" → **Amazon Redshift**
- **Trigger:** "query S3 data without loading into Redshift" → **Redshift Spectrum**
- **Trigger:** "no cluster management for Redshift, sporadic queries" → **Redshift Serverless**
- Redshift uses **MPP** — queries are distributed across multiple compute nodes for speed
- For loading large datasets into Redshift efficiently: use the **COPY command** from S3
- Redshift is a **columnar** database — stores data by column for fast analytical queries
- Redshift is NOT for OLTP — use RDS/Aurora for transactional workloads
- Redshift integrates with **QuickSight** and **Tableau** for business intelligence dashboards
