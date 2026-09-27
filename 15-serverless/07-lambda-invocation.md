# Lambda Invocation (Synchronous, Asynchronous, Event Source Mapping)

## Synchronous Invocation

The caller **waits** for Lambda to finish and receives the response immediately.

- Caller is blocked until Lambda returns or times out
- On Lambda throttling: caller receives **HTTP 429 TooManyRequestsException**
- On Lambda error: error is returned directly to the caller — **no automatic retry**

**Services that invoke Lambda synchronously:**
- API Gateway
- Application Load Balancer (ALB)
- Lambda Function URLs
- Cognito

**Use cases**: Real-time APIs, authentication checks, data lookups

## Asynchronous Invocation

Lambda **queues the event internally** and processes it later — the caller is not blocked.

- Lambda's internal queue holds the event and retries on failure
- On processing failure: Lambda **retries 2 more times** (3 attempts total)
- **Retry intervals**: 1 minute between 1st and 2nd retry, 2 minutes between 2nd and 3rd
- After all retries fail: event sent to **Dead-Letter Queue (DLQ)** or **on-failure destination** (SQS, SNS, EventBridge, or another Lambda)
- If throttled (429) or system error (5xx): Lambda retries for up to **6 hours** with exponential backoff (1 sec to 5 min intervals)

**Services that invoke Lambda asynchronously:**
- **S3** (object event notifications)
- **SNS** (topic subscriptions)
- **EventBridge** (rules and scheduled events)
- **CloudWatch Events**
- **DynamoDB Streams** (via Event Source Mapping — effectively async)
- **Kinesis** (via Event Source Mapping)

**Use cases**: Event processing, data pipelines, order processing, file processing

## Event Source Mapping (ESM)

Lambda polls certain **streaming and queue-based sources** using an Event Source Mapping. Lambda manages the polling — you do not write polling code.

**Supported sources for ESM:**
- **Amazon SQS** (standard and FIFO)
- **Amazon Kinesis Data Streams**
- **Amazon DynamoDB Streams**
- **Amazon MSK (Managed Kafka)**
- **Self-managed Kafka**

### ESM with SQS

- Lambda polls SQS and invokes the function with a **batch of messages**
- **Batch size**: 1–10,000 messages per invocation (configurable)
- Lambda automatically **scales concurrency** based on queue depth
- Lambda deletes messages only after **successful processing**
- Failed messages can be retried or sent to a **DLQ** after MaxReceiveCount is exceeded
- Supports **partial batch response** — Lambda can report which specific messages failed so only those are retried

### ESM with Kinesis/DynamoDB Streams

- Lambda reads **shards** in the stream
- One Lambda instance per shard (up to the number of shards)
- Processes records **in order** within a shard
- Failed batches block processing until resolved or the record expires (24 hours for DynamoDB, 7 days for Kinesis)
- Configure **bisect on error** to split batches and isolate failing records

## Invocation Method Comparison

| | Synchronous | Asynchronous | Event Source Mapping |
|---|---|---|---|
| **Caller waits?** | Yes | No | N/A (Lambda polls) |
| **Retry on failure** | No (caller handles) | 2 automatic retries | Depends on source |
| **DLQ support** | No | Yes | Yes (for SQS) |
| **Max retry duration** | — | 6 hours | Varies by source |
| **Services** | API GW, ALB | S3, SNS, EventBridge | SQS, Kinesis, DynamoDB Streams |

## Dead-Letter Queue (DLQ)

- For **asynchronous invocations**: failed events (after all retries) sent to an SQS queue or SNS topic
- For **SQS ESM**: messages exceeding the MaxReceiveCount in SQS are sent to the SQS DLQ (configured on the SQS queue, not Lambda)
- Useful for: troubleshooting, auditing failed events, manual reprocessing

## Key Points / Exam Tips

- **Synchronous** = API Gateway / ALB → Lambda; throttle → 429 to caller; no auto-retry
- **Asynchronous** = S3 / SNS / EventBridge → Lambda; 2 automatic retries; then DLQ
- **Event Source Mapping** = Lambda polls SQS/Kinesis/DynamoDB Streams; managed by Lambda
- For SQS + Lambda: Lambda **deletes messages only on success** — failed messages become visible again
- Asynchronous retry window: up to **6 hours** for throttle/system errors (1 sec to 5 min backoff)
- **Partial batch response** for SQS allows only failed messages to be retried (not the whole batch)

## Trigger Words

- "Lambda triggered by S3 upload — retry on failure" → Asynchronous invocation + DLQ
- "Lambda processes SQS messages" → Event Source Mapping (Lambda polls SQS)
- "Lambda reads Kinesis stream in order" → Event Source Mapping (per-shard ordering)
- "Caller gets immediate response from Lambda" → Synchronous invocation (API Gateway)
- "Lambda failed after all retries — capture failed events" → DLQ (SQS or SNS)
