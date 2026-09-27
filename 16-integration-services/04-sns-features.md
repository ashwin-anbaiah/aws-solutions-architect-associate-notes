# SNS Features (FIFO, Fan-out, Message Filtering)

## SNS Fan-out Pattern

**Fan-out** means "publish once, deliver to many" — the same message is distributed to multiple downstream systems simultaneously.

**Why use fan-out instead of publishing to each system individually?**
- No risk of partial delivery if the producer crashes mid-send
- Simplifies producer logic — one publish call instead of N
- Easy to add new consumers without changing the producer
- Each consumer can scale independently

### SNS + SQS Fan-out (Most Common Pattern)

Publish to an SNS topic → SNS delivers to multiple SQS queues → each queue has its own consumer fleet.

**Example — Order processing:**

```
Order Service --publish--> SNS Topic (OrderPlaced)
                               |
              +----------------+----------------+
              |                |                |
         Payment Queue    Billing Queue    Shipping Queue
              |                |                |
         Payment Svc      Billing Svc      Shipping Svc
```

**Benefits:**
- Each service processes orders independently and at its own pace
- If one service is down, its SQS queue buffers messages until it recovers
- Easy to add an Inventory Queue later without changing Order Service

### SNS + Kinesis Data Firehose Fan-out

- Subscribe **Kinesis Data Firehose** delivery streams to SNS topics
- Fan-out events to S3, Redshift, OpenSearch for analytics and storage
- Up to 5 Firehose subscriptions per standard SNS topic

```
Event Producer --publish--> SNS Topic
                               |
              +----------------+
              |                |
         S3 (via Firehose)  Redshift (via Firehose)
```

## SNS FIFO Topic

Use SNS FIFO when you need **strictly ordered, exactly-once delivery** to SQS FIFO queues.

**Characteristics:**
- Messages are ordered within **message groups** (group ID required)
- No duplicate messages delivered
- Only **SQS FIFO queues** can subscribe to a FIFO topic
- Throughput: ~300 msg/sec or 10 MB/sec

**Use case example:** Booking/reservation system where customer booking events must be processed in the exact order received to avoid double-booking.

## Message Filtering in Detail

Without filtering, every subscriber receives every message. With **filter policies**, each subscription receives only the messages it cares about.

**How filter policies work:**
1. Publishers include **Message Attributes** in the SNS message (key-value metadata)
2. SNS evaluates each subscription's filter policy against the message attributes
3. If the filter matches → message is delivered to that subscriber
4. If the filter does not match → message is skipped for that subscriber

**Filter policy operators:**
- Exact match: `"eventType": ["OrderCreated"]`
- Multiple values (OR): `"priority": ["high", "critical"]`
- Prefix match: `"username": [{"prefix": "admin"}]`
- Numeric range: `"amount": [{"numeric": [">", 100, "<=", 500]}]`
- Anything-but: `"status": [{"anything-but": ["failed", "cancelled"]}]`
- Exists/not exists: `"errorCode": [{"exists": true}]`

**Example — Support ticket routing:**

```
Customer creates ticket → SNS Topic (SupportIssue)
                                |
        Priority = Critical     |    Priority = Medium
            +-------------------+-------------------+
            |                                       |
  PagerDuty HTTP endpoint              Slack message webhook
  (on-call engineer)                 (support channel)
```

## SNS vs SQS Comparison

| Feature | SNS | SQS |
|---|---|---|
| **Model** | Pub/Sub (push) | Queue (pull) |
| **Direction** | One producer → many subscribers | Many producers → one consumer group |
| **Message persistence** | No (delivered or lost) | Yes (up to 14 days) |
| **Consumer polling** | No (push delivery) | Yes (consumer polls) |
| **Ordering** | Standard (best-effort) or FIFO | Standard (best-effort) or FIFO |
| **Fan-out** | Native feature | Not native (use SNS+SQS together) |
| **Best for** | Event notification, fan-out | Job queues, decoupling, rate-limiting |

## Key Points / Exam Tips

- **SNS + SQS Fan-out** is the canonical pattern for "same message to multiple consumers" — critical exam pattern
- SNS does not buffer messages — if SQS subscriber is not attached, consider message loss
- **FIFO Topic** only supports **FIFO SQS** subscribers — cannot deliver to Lambda or HTTP from a FIFO topic
- Message filtering reduces cost and complexity — subscribers only process relevant events
- When you see "fan-out", "multiple downstream systems processing the same event", or "decouple notification from processing" → SNS + SQS
- For event-driven analytics pipelines, SNS + Kinesis Data Firehose routes events to S3/Redshift

## Trigger Words

- "Same event processed by multiple independent services" → SNS Fan-out (SNS + multiple SQS queues)
- "Order events must be processed in sequence with no duplicates" → SNS FIFO + SQS FIFO
- "Only deliver high-priority alerts to pager, all to queue" → SNS Message Filtering
- "Decouple notification from the backend processing queue" → SNS → SQS
- "Fan-out to data lake and analytics" → SNS → Kinesis Data Firehose → S3/Redshift
