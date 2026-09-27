# Big Data and Analytics Overview

## What Is Big Data?

Big Data refers to extremely large datasets that cannot be managed, processed, or analyzed using traditional data processing techniques.

**The Three V's:**
- **Volume** — massive amounts of data from social media, sensors, transactions
- **Velocity** — speed at which new data is generated and must be processed (real-time, streaming)
- **Variety** — structured (databases), semi-structured (JSON, XML), and unstructured (text, images, video)

## Big Data Frameworks

| Framework | Purpose |
|---|---|
| **Hadoop** | Batch processing; distributed storage (HDFS) and processing (MapReduce) |
| **Apache Spark** | In-memory real-time and batch processing; much faster than Hadoop MapReduce |
| **Apache Hive** | SQL-like query over data in HDFS |
| **Presto** | Distributed SQL engine for interactive analytics (used in Athena) |
| **Apache Flink** | Real-time stream processing with stateful computations |
| **Apache Kafka** | Distributed event streaming platform for high-throughput ingestion |

## OLTP vs OLAP

| Dimension | OLTP | OLAP |
|---|---|---|
| Purpose | Process transactions | Analyze aggregated data |
| Write pattern | Continuous small writes | Batch writes, large reads |
| Data | Normalized | Denormalized |
| Volume | GBs | TBs to PBs |
| AWS services | RDS, Aurora, DynamoDB | Redshift, Athena + S3 |

## Data Storage Formats

| Format | Type | Best For |
|---|---|---|
| **CSV / JSON / TSV** | Row-based | Simple, human-readable; poor for analytics |
| **Apache Parquet** | Columnar | High-performance analytics; supports compression; Athena/Redshift |
| **Apache Iceberg** | Table format (columnar + ACID) | Data lake with ACID transactions, schema evolution, time travel |

## AWS Analytics Architecture: Data Lake + OLAP

```
Sources                     Ingest / Process              Store       Analyze / Visualize
─────────────────────────────────────────────────────────────────────────────────────────
Kinesis / MSK (streaming)   → Glue / EMR (ETL/transform) → S3 (Data Lake) → Athena (SQL)
IoT / Clickstream / Logs    → Firehose (delivery)         → Redshift (DWH) → QuickSight (BI)
RDS / DynamoDB (OLTP)       → Glue Crawler (catalog)      → Glue Catalog  → EMR (ML/Spark)
```

## AWS Analytics Services — Quick Map

| Service | Role |
|---|---|
| **AWS Glue** | Serverless ETL; data catalog; crawlers |
| **Amazon EMR** | Big data processing — Spark, Hadoop, Hive, Presto |
| **Amazon Athena** | Serverless SQL on S3 / federated sources |
| **Amazon QuickSight** | BI dashboards and visualizations |
| **Amazon Redshift** | Data warehouse (OLAP) |
| **Kinesis Data Streams** | Real-time streaming ingestion |
| **Amazon Data Firehose** | Managed streaming delivery to S3/Redshift/OpenSearch |
| **Amazon MSK** | Managed Apache Kafka |
| **MSF (Apache Flink)** | Real-time stream processing and analytics |
| **AWS Lake Formation** | Centralized data lake governance and fine-grained access control |

---

## Key Points / Exam Tips

- **Trigger:** "ETL, transform data, serverless" → **AWS Glue**
- **Trigger:** "big data — Spark, Hadoop, Hive, Presto at scale" → **Amazon EMR**
- **Trigger:** "SQL queries on S3, no servers" → **Amazon Athena**
- **Trigger:** "BI dashboards, visualizations" → **Amazon QuickSight**
- **Trigger:** "data warehouse, petabyte analytics" → **Amazon Redshift**
- **Trigger:** "real-time streaming ingestion" → **Kinesis Data Streams** or **Amazon MSK**
- **Trigger:** "deliver streaming data to S3/Redshift, no custom processing needed" → **Amazon Data Firehose**
- **Trigger:** "fine-grained data lake permissions, column/row-level security" → **AWS Lake Formation**
- Parquet is the **preferred format** for S3-based data lakes — smaller files, faster queries, lower Athena cost
