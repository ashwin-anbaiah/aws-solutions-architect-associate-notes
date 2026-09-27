# Amazon Data Firehose

## What Is Amazon Data Firehose?

- **Amazon Data Firehose** (formerly Kinesis Data Firehose) — fully managed service that **captures, transforms, and delivers streaming data** to destinations.
- No consumer code to write — AWS manages the delivery pipeline end-to-end.
- Near-real-time delivery with configurable **buffer size** and **buffer time** to control speed and batch size.

## Sources

- Amazon Kinesis Data Streams
- Amazon MSK (Kafka)
- AWS IoT
- CloudWatch Logs and Events
- Direct PUT via AWS SDK / Kinesis Agent

## Destinations

- **Amazon S3** — long-term storage, data lake
- **Amazon Redshift** — loads data via S3 COPY command; near-real-time reporting
- **Amazon OpenSearch Service** — log search and visualization
- **HTTP Endpoints** — any HTTP endpoint (custom targets)
- **Third-party:** Splunk, Datadog, New Relic, Dynatrace

## Key Features

- **Format conversion** — converts JSON to Parquet or ORC automatically before delivering to S3 (built-in, no Lambda needed).
- **Compression** — GZIP, Snappy, ZIP to reduce S3 storage cost.
- **Inline transformation** — use **AWS Lambda** to transform records before delivery.
- **Backup** — optionally send all source records (or failed records) to a separate S3 bucket.
- **Supports multiple formats:** JSON, CSV, Parquet, ORC, raw text, binary.

## Buffering

- Firehose buffers data before delivering:
  - **Buffer size:** 1–128 MB
  - **Buffer interval:** 60–900 seconds
- Delivers when EITHER threshold is reached (whichever comes first).
- Minimum latency: ~60 seconds (not truly real-time).

## Firehose vs Kinesis Data Streams

| Feature | Kinesis Data Streams | Amazon Data Firehose |
|---|---|---|
| Consumer code | You write custom consumers | AWS manages delivery — no consumer code |
| Replay | Yes (up to 365 days) | No replay |
| Latency | ~200ms | 60 sec minimum buffer |
| Destinations | Any (custom code) | Fixed: S3, Redshift, OpenSearch, HTTP, SaaS |
| Format conversion | No (you handle in consumer) | Yes — JSON → Parquet/ORC built-in |
| Use case | Custom real-time processing | Simple capture-and-load into storage |

---

## Key Points / Exam Tips

- **Trigger:** "capture streaming data and load into S3/Redshift/OpenSearch, no custom processing" → **Amazon Data Firehose**
- **Trigger:** "convert JSON to Parquet before landing in S3" → **Firehose** (built-in format conversion)
- **Trigger:** "send logs to Splunk, Datadog, or OpenSearch from a stream" → **Firehose**
- Firehose has a **minimum ~60-second latency** — it is near-real-time, not real-time
- No data replay in Firehose — use Kinesis Data Streams if you need replay capability
- **AWS Lambda** can be attached to Firehose for custom per-record transformations before delivery
- Firehose is the go-to for "capture streaming data → store in S3 as Parquet for Athena queries"
