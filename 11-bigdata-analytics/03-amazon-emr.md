# Amazon EMR

## What Is EMR?

- **Amazon EMR (Elastic MapReduce)** — managed big data platform for processing and analyzing large datasets using popular open-source frameworks.
- Distributes data and processing across resizable clusters of **EC2 instances** or **EKS pods**; also has **Serverless** option.
- Supports **auto-scaling** and integrates with **EC2 Spot instances** for cost savings.

## Supported Frameworks

| Framework | Purpose |
|---|---|
| **Apache Hadoop** | Batch processing with MapReduce + HDFS |
| **Apache Spark** | In-memory batch and streaming processing |
| **Apache Hive** | SQL-like queries over distributed data |
| **Apache HBase** | Distributed NoSQL on top of Hadoop |
| **Apache Flink** | Real-time stream processing |
| **Presto** | Fast interactive SQL queries |

## Storage Options

| Storage | Description |
|---|---|
| **HDFS** | Distributed filesystem on EBS or instance store; ephemeral — lost when cluster terminates |
| **EMRFS (S3)** | Use Amazon S3 as persistent cluster storage; data persists after cluster shutdown |

## EMR Cluster Options

- **Provisioned Cluster (EC2)** — you choose instance types and node counts; supports on-demand, reserved, and Spot instances.
- **EKS** — run EMR workloads (Spark) on existing EKS clusters.
- **EMR Serverless** — no cluster management; AWS provisions resources automatically.

## Use Cases

- Log analysis and web indexing
- Data processing for machine learning training
- Financial data analysis and market trend modeling
- Customer behavior and recommendation engine data pipelines
- ETL for large-scale data lake ingestion

## AWS Glue vs Amazon EMR (Revisited)

| When to use Glue | When to use EMR |
|---|---|
| Need serverless, low-code ETL | Need full flexibility of big data frameworks |
| Visual pipeline development | Hadoop migration from on-premises |
| Built-in connectors for 70+ data sources | Expertise in Hive, Presto, HBase beyond Spark |
| Centralized data catalog management | Consistent, large-scale distributed processing |

---

## Key Points / Exam Tips

- **Trigger:** "big data processing, Spark, Hadoop, Hive at scale" → **Amazon EMR**
- **Trigger:** "migrate on-premises Hadoop cluster to AWS" → **Amazon EMR**
- **Trigger:** "Hadoop MapReduce, HDFS" → **EMR** (not Glue — Glue doesn't run Hadoop)
- EMR supports **Spot instances** for worker nodes — significant cost savings for batch jobs
- Use **EMRFS (S3)** for persistent storage so data survives cluster shutdown; HDFS is ephemeral
- EMR Serverless = no cluster provisioning; pay for resources used per job
- Glue is simpler and serverless; EMR is more powerful but requires more expertise
