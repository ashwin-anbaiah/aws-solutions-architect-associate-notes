# Amazon Athena

## What Is Athena?

- **Amazon Athena** — serverless, interactive **SQL query service** for analyzing data directly in **Amazon S3** (and other sources) without setting up or managing any servers.
- Built on open-source **Trino** and **Presto** engines.
- **Pay-per-query pricing:** $5.00 per TB of data scanned.
- Supports: CSV, JSON, Parquet, ORC, and other formats.
- Integrates with **Amazon QuickSight** for data visualization.

## Key Features

### Partitioning (Cost Optimization)
- Partition S3 data by date, region, or other dimensions to reduce data scanned per query.
- Example: `/reviews/products/X/2026/01/` scanned for a date-filtered query = only 1 GB instead of 6 GB.
- Always use partitioning + Parquet format to minimize query cost.

### Athena Federated Queries
- Query data in sources **other than S3** (DynamoDB, RDS, CloudWatch Logs, custom/on-premises).
- Uses **Data Source Connectors** that run on **AWS Lambda**.
- Query results are written to Amazon S3.

### Athena for AWS Log Analysis
- Query **CloudTrail logs**, **VPC Flow Logs**, **ALB access logs**, **S3 access logs**, **Network Firewall logs**, and more.
- Common exam pattern: query AWS service logs for security auditing or troubleshooting.

## Cost Optimization Best Practices

| Practice | Benefit |
|---|---|
| Use **columnar formats** (Parquet, ORC) | Scan only relevant columns — lower cost |
| **Partition** data in S3 | Scan only matching partitions — lower cost |
| **Compress** files (GZIP, Snappy) | Smaller files = less scanned = lower cost |
| Use **Glue Data Catalog** for metadata | No need to define schemas manually |

## Athena vs Redshift

| Dimension | Athena | Redshift |
|---|---|---|
| Type | Serverless SQL query engine | Managed data warehouse |
| Data location | Data stays in S3 | Data loaded into Redshift cluster |
| Best for | Ad-hoc queries, log analysis, infrequent queries | Regular BI reports, complex analytical queries |
| Cost model | Per query (TB scanned) | Cluster uptime or Serverless RPU |
| Setup | Zero — serverless | Cluster or Serverless setup required |

---

## Key Points / Exam Tips

- **Trigger:** "query data in S3 with SQL, no servers" → **Amazon Athena**
- **Trigger:** "analyze CloudTrail logs, VPC Flow Logs, ALB logs with SQL" → **Athena**
- **Trigger:** "query data across DynamoDB, RDS, S3 in a single SQL query" → **Athena Federated Query**
- Reduce Athena cost: use **Parquet format + S3 partitioning** — reduces bytes scanned = lower bill
- Athena is **serverless** — no clusters, no setup, no idle costs
- Athena uses the **Glue Data Catalog** as its metadata store when available
- For regular/frequent BI reporting with heavy queries → **Redshift** is more cost-effective; for ad-hoc/occasional → **Athena**
