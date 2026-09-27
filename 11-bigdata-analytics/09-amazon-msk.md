# Amazon MSK (Managed Streaming for Apache Kafka)

## What Is Amazon MSK?

- **Amazon MSK (Managed Streaming for Apache Kafka)** — fully managed **Apache Kafka** service on AWS.
- An alternative to **Amazon Kinesis Data Streams** for real-time streaming ingestion and messaging.
- AWS manages Kafka **brokers**, ZooKeeper/KRaft nodes, patching, and scaling.
- **Compatible** with open-source Apache Kafka APIs, tools, and clients — existing Kafka apps can migrate with no code changes.

## Key Features

- **Multi-AZ clusters** — high availability across multiple AZs.
- **MSK Serverless** — no broker capacity to manage; AWS handles provisioning and scaling.
- **Security** — integrates with IAM for authentication, TLS for in-transit encryption, and KMS for at-rest encryption.
- **Data storage** — data organized in **Kafka topics** and **partitions**.
- **Common consumers** — Apache Flink (MSF), Lambda, Spark on EMR, custom EC2 consumers.
- **Glue Streaming ETL** — can use MSK as a source for Glue Streaming ETL jobs.

## Apache Kafka Concepts

| Concept | Description |
|---|---|
| **Topic** | A named stream or category of messages |
| **Partition** | Each topic is split into partitions for parallelism and ordering within a partition |
| **Broker** | A Kafka server that stores and serves topic partition data |
| **Producer** | Application that writes messages to a topic |
| **Consumer** | Application that reads messages from a topic |
| **Consumer Group** | Group of consumers sharing the work of reading a topic |

## Amazon MSK vs Amazon Kinesis Data Streams

| Feature | Amazon MSK | Kinesis Data Streams |
|---|---|---|
| Underlying tech | Apache Kafka | AWS-proprietary |
| API compatibility | Kafka API (open-source) | Kinesis SDK (AWS-specific) |
| Message retention | Configurable (days to indefinitely) | Up to 365 days |
| Max message size | Up to 10 MB per message | 1 MB per record |
| Scaling | Broker-based (MSK Serverless for auto) | Shard-based (on-demand or provisioned) |
| Migration | Kafka → MSK (no code change) | AWS-native new builds |
| Best for | Existing Kafka workloads, multi-framework | AWS-native streaming applications |

---

## Key Points / Exam Tips

- **Trigger:** "Apache Kafka," "Kafka-compatible," "migrate Kafka to AWS managed service" → **Amazon MSK**
- **Trigger:** "managed Kafka brokers, no ZooKeeper management" → **Amazon MSK**
- MSK is Kafka under the hood; Kinesis Data Streams is AWS-proprietary — both are streaming platforms
- MSK max message size: **10 MB** (Kinesis: 1 MB) — MSK supports larger messages
- MSK integrates with **MSF (Apache Flink)**, **Lambda**, **Glue Streaming ETL**, and **EMR (Spark)**
- Choose MSK when migrating from an existing Kafka setup; choose Kinesis for new AWS-native builds
- **MSK Serverless** = no broker management; AWS scales automatically
