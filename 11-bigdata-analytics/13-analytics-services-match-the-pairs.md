# Analytics Services — Match the Pairs

## Quick Reference: Trigger Words → Service

| Trigger Words | AWS Service |
|---|---|
| "ETL, serverless, transform data, schema discovery" | **AWS Glue** |
| "Big data, Spark, Hadoop, Hive, Presto at scale" | **Amazon EMR** |
| "SQL queries on S3, no servers, pay per query" | **Amazon Athena** |
| "BI dashboards, visualizations, business intelligence" | **Amazon QuickSight** |
| "Data warehouse, petabyte analytics, MPP" | **Amazon Redshift** |
| "Real-time streaming ingestion, multiple consumers, replay" | **Kinesis Data Streams** |
| "Capture and deliver streams to S3/Redshift/OpenSearch, no code" | **Amazon Data Firehose** |
| "Video streams, cameras, real-time video analytics" | **Kinesis Video Streams** |
| "Apache Kafka managed, Kafka-compatible" | **Amazon MSK** |
| "Apache Flink, stateful stream processing, windowed aggregations" | **Amazon MSF** |
| "Fine-grained data lake governance, column/row-level security" | **AWS Lake Formation** |
| "Incremental ETL — only process new data" | **Glue Job Bookmarks** |
| "Central metadata catalog for analytics services" | **Glue Data Catalog** |
| "Query Redshift data in S3 without loading" | **Redshift Spectrum** |
| "SPICE in-memory BI engine" | **Amazon QuickSight SPICE** |

---

## Common Architecture Patterns

### Real-Time Streaming Pipeline
```
Sources (IoT/Clickstream/Logs)
  → Kinesis Data Streams (ingest)
  → MSF/Apache Flink (process/transform)
  → Firehose (deliver)
  → S3 (store as Parquet)
  → Athena (SQL queries)
  → QuickSight (dashboards)
```

### Batch ETL Pipeline
```
OLTP Sources (RDS, DynamoDB)
  → AWS Glue (ETL + catalog)
  → S3 Data Lake (raw and transformed)
  → Redshift (load via COPY)
  → QuickSight / Tableau (BI)
```

### Kafka-Based Real-Time Pipeline
```
Producers → Amazon MSK (Kafka topics)
  → MSF (Apache Flink) — real-time processing
  → Firehose → S3 / Redshift / OpenSearch
```

---

## Key Comparisons to Know

### Kinesis Data Streams vs Firehose vs MSK

| Feature | Kinesis Data Streams | Firehose | Amazon MSK |
|---|---|---|---|
| API | Kinesis SDK | Kinesis/direct PUT | Kafka API |
| Replay | Yes | No | Yes (configurable retention) |
| Consumer code | You write it | AWS manages | You write it |
| Best for | Custom processing, multiple consumers | Simple delivery to storage | Kafka migration, multi-consumer |

### Glue vs EMR vs Athena

| Scenario | Answer |
|---|---|
| Serverless, low-code ETL, schema discovery | **Glue** |
| Run Spark/Hadoop/Hive at scale, full framework flexibility | **EMR** |
| Ad-hoc SQL on S3, no cluster needed | **Athena** |
| SQL on historical data for BI, petabyte scale | **Redshift** |

### Athena vs Redshift

| Use Ad-hoc / occasional queries on S3 | Use Regular/frequent BI queries on structured data |
|---|---|
| Amazon Athena | Amazon Redshift |
| Pay per query | Pay per cluster uptime or RPU |
| No setup | Cluster or Serverless setup |
