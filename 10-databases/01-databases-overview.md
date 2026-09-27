# AWS Databases Overview

## Purpose-Built Databases

AWS offers a wide range of purpose-built managed database services. Modern applications rarely need just one type of database.

| Database Type | AWS Service | Best For |
|---|---|---|
| **Relational (OLTP)** | Amazon RDS, Aurora | Transactional apps, ERP, CRM, banking |
| **In-Memory Cache** | Amazon ElastiCache (Redis/Memcached) | Leaderboards, session store, real-time analytics |
| **Key-Value / NoSQL** | Amazon DynamoDB | Shopping cart, product catalog, gaming, IoT |
| **Document** | Amazon DocumentDB | User profiles, content management, catalogs |
| **Graph** | Amazon Neptune | Social networks, fraud detection, recommendations |
| **Time Series** | Amazon Timestream | IoT metrics, DevOps monitoring, clickstreams |
| **Ledger** | Amazon QLDB | Financial records, supply chain audit, claim history |
| **Wide-Column (Cassandra)** | Amazon Keyspaces | Cassandra migration, time-series, IoT at scale |
| **Data Warehouse (OLAP)** | Amazon Redshift | Historical analytics, BI reporting, data marts |

## Managed vs Self-Managed Databases

### Why Use a Managed Service?

With a self-managed database (on EC2), **you** handle:
- OS patching
- Database software upgrades
- Backup and recovery
- High availability and failover
- Scaling (storage and compute)
- Security and compliance configurations

With a **fully managed** AWS database service, **AWS** handles all of the above. You focus only on:
- Schema design
- Query construction and optimization

## OLTP vs OLAP

| Dimension | OLTP | OLAP |
|---|---|---|
| Full name | Online Transaction Processing | Online Analytical Processing |
| Purpose | Process transactions | Analyze aggregated data |
| Write pattern | Continuous small writes | Batch writes, large reads |
| Data model | Normalized | Denormalized |
| Data volume | GBs | TBs to PBs |
| Examples | Order management, banking, ATM | Data warehouse, market trends |
| AWS services | RDS, Aurora, DynamoDB | Redshift, Athena + S3 |

## Data Warehouse vs Data Lake

- **Data Warehouse** — structured, denormalized data from multiple OLTP sources loaded via ETL; optimized for SQL queries by analysts.
- **Data Lake** — centralized repository storing raw data in any format (CSV, JSON, Parquet, binary); supports ML, analytics, and exploration.

---

## Key Points / Exam Tips

- **Trigger:** "relational, transactions, ACID" → **RDS / Aurora**
- **Trigger:** "NoSQL, millisecond latency at scale, serverless" → **DynamoDB**
- **Trigger:** "JSON documents, MongoDB-compatible" → **DocumentDB**
- **Trigger:** "graph — relationships, nodes, edges" → **Neptune**
- **Trigger:** "time-series, IoT sensor data" → **Timestream**
- **Trigger:** "immutable audit log, cryptographically verifiable" → **QLDB**
- **Trigger:** "Cassandra workload migration" → **Amazon Keyspaces**
- **Trigger:** "petabyte-scale analytics, SQL queries on historical data" → **Redshift**
- Choose the database **based on the access pattern**, not habit — a relational DB is not always the right choice
