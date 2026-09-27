# Amazon Timestream

## What Is Timestream?

- **Amazon Timestream** — fully managed, serverless **time-series database** service.
- Purpose-built for storing and analyzing data points that are tracked over time (timestamped measurements).
- Up to **1,000x faster** and **1/10th the cost** of relational databases for time-series workloads.
- **InfluxDB-compatible** — open-source time-series tool compatibility.

## Key Features

- **Serverless** — auto-scales storage and compute; no infrastructure management.
- **Tiered storage** — recent data in memory tier (fast queries); older data moved to magnetic tier (cost-efficient).
- **Built-in time-series analytics** — smoothing, approximation, interpolation functions built in.
- **Scheduled queries** — automate recurring computations for dashboards.
- High write throughput — optimized for ingesting millions of data points per second.

## Time-Series Data Model

- Each record contains: a **timestamp**, one or more **dimensions** (metadata like device ID, region), and one or more **measures** (the actual values like temperature, CPU %).
- Example: `{timestamp: 2026-09-27T10:00:00Z, device: "sensor-42", temperature: 72.5, humidity: 65.2}`

## Use Cases

| Use Case | Example |
|---|---|
| **IoT data ingestion** | Temperature, pressure, motion sensors |
| **Real-time analytics** | Live application performance metrics |
| **DevOps monitoring** | EC2 CPU, memory, network utilization over time |
| **Web traffic analysis** | Page views, click rates, error rates per minute |
| **Financial analytics** | Stock prices, trading volume, tick data |
| **Industrial IoT** | Equipment health, oil rig sensors, factory floor metrics |

## Timestream vs Other AWS Databases

| Scenario | Best Choice |
|---|---|
| Timestamped IoT measurements at scale | **Timestream** |
| Structured relational OLTP | RDS / Aurora |
| Key-value NoSQL at scale | DynamoDB |
| Analytics on historical data | Redshift |
| Real-time streaming processing | Kinesis Data Streams + MSF |

---

## Key Points / Exam Tips

- **Trigger:** "time-series data," "IoT metrics," "timestamped measurements," "operational metrics over time" → **Amazon Timestream**
- **Trigger:** "1,000x faster than relational for time-series" → this is a Timestream claim
- Timestream is **serverless** — no provisioning needed
- Timestream automatically moves older data to cheaper magnetic storage (tiered storage)
- InfluxDB-compatible — relevant for migrations from InfluxDB to AWS
- Use Timestream when the primary dimension of your data is **time** (not relationships, not documents, not key-value)
