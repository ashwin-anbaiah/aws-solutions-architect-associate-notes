# Amazon Kinesis Data Streams

## What Is Kinesis Data Streams?

- **Amazon Kinesis Data Streams** — managed service to **ingest real-time streaming data** (logs, metrics, events, clickstreams) and make it available to consumers.
- Supports multiple **independent consumers** reading the same stream at their own pace.
- Data is retained in the stream for up to **365 days** (default: 24 hours).
- Data can be **replayed** and **reprocessed** — records are not deleted when read (only when expired).
- Data **cannot** be manually deleted before expiry.

## Architecture: Shards

- A Kinesis Data Stream is divided into **shards** — the fundamental units of capacity.
- Each shard provides:
  - **Write:** 1 MB/sec or 1,000 records/sec
  - **Read:** 2 MB/sec
- A **partition key** routes records to a specific shard, ensuring ordering within that shard.
- Total stream capacity = number of shards × per-shard limits.

## Capacity Modes

| Mode | Description | Best For |
|---|---|---|
| **Provisioned** | Manually define shard count; scale via shard split/merge | Predictable, steady streaming workloads |
| **On-Demand** | Auto-scales based on observed traffic (last 30 days); no capacity planning | Unpredictable or variable workloads |

## Producers and Consumers

**Producers** (write to stream):
- AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent
- AWS services: DynamoDB Streams, CloudWatch, IoT, databases

**Consumers** (read from stream):
- AWS SDK, Kinesis Client Library (KCL)
- AWS Lambda, Amazon Data Firehose, Amazon MSF (Apache Flink), EC2

## Use Cases

- Fraud detection in financial services
- Real-time customer behavior analysis and personalized recommendations
- Log monitoring and alerting
- IoT data processing — anomaly and malfunction detection
- Social media trending topics
- Connected vehicles — driver behavior monitoring
- Industrial IoT — equipment health monitoring

## Kinesis Data Streams vs Amazon Data Firehose

| Feature | Kinesis Data Streams | Amazon Data Firehose |
|---|---|---|
| Consumer logic | Custom (KCL, Lambda, Flink) | Managed — delivers to fixed destinations |
| Replay | Yes — data retained for up to 365 days | No replay — near-real-time delivery only |
| Latency | ~200ms | Buffer (60 sec or 1 MB minimum) |
| Destinations | Any (custom consumer code) | S3, Redshift, OpenSearch, Splunk, HTTP |
| Complexity | Higher (you build consumers) | Lower (AWS manages delivery) |
| Best for | Custom real-time processing, multiple consumers | Simple capture-and-load into storage/analytics |

---

## Key Points / Exam Tips

- **Trigger:** "real-time streaming ingestion," "multiple consumers," "replay data" → **Kinesis Data Streams**
- **Trigger:** "fully managed, just deliver data to S3/Redshift/OpenSearch" → **Amazon Data Firehose**
- Kinesis Data Streams = **you build consumers**; Firehose = **AWS manages delivery**
- Data in Kinesis streams is **not deleted on read** — consumers maintain their own position (sequence number)
- Shard = unit of capacity; more shards = more throughput
- **Partition key** determines shard assignment — use high-cardinality keys for even distribution
- Kinesis Streams supports **Provisioned and On-Demand** capacity modes (like DynamoDB)
