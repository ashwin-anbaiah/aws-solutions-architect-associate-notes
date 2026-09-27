# Amazon SNS Overview

## What is Amazon SNS?

**Amazon Simple Notification Service (SNS)** is a fully managed **Pub/Sub (publish-subscribe)** notification and messaging service.

- **Publishers** send a message to an SNS **Topic** once
- SNS delivers the message to **all subscribed endpoints simultaneously**
- Decouples publishers from subscribers — publisher does not know who is subscribed
- Supports both **Application-to-Application (A2A)** and **Application-to-Person (A2P)** messaging

## SNS Topics

A **Topic** is the central communication channel — publishers send to a topic, subscribers receive from it.

### Standard Topic
- **Best-effort ordering** (not guaranteed)
- **At-least-once delivery** (duplicates possible)
- Nearly **unlimited throughput**
- Supports: SQS, Lambda, HTTP/S endpoints, Email, SMS, Mobile Push, Kinesis Data Firehose
- Cost: Lower

### FIFO Topic
- **Strictly ordered** messages within message groups
- **Exactly-once delivery** (no duplicates)
- Throughput: ~300 messages/sec or 10 MB/sec
- Supports: **Only FIFO SQS queues** as subscribers
- Cost: Higher
- Use when: order-critical workflows (booking systems, financial processing)

## SNS Subscriptions

Subscribers receive all messages published to a topic (unless message filtering is applied).

**Supported subscription protocols:**
- **Amazon SQS** (Standard or FIFO) — most common A2A pattern
- **AWS Lambda** — serverless processing
- **HTTP/HTTPS** — push to web endpoints
- **Email** / **Email-JSON** — human notification
- **SMS** — mobile text messages
- **Mobile Push** — push to mobile devices (iOS, Android, GCM, ADM, Baidu)
- **Amazon Kinesis Data Firehose** — fan-out to storage/analytics

## Message Filtering

By default, all subscribers receive every message. **Message filtering** lets subscribers specify which messages they want using a **filter policy** (JSON):

- Filter policy is attached to a **subscription** (not the topic)
- SNS evaluates message attributes against filter policies and only delivers matching messages
- Reduces unnecessary processing — each subscriber only receives relevant messages

**Filter policy example:**
```json
{
  "eventType": ["OrderCreated"],
  "source": ["mobile"]
}
```
This subscription only receives messages where `eventType = OrderCreated` AND `source = mobile`.

## Dead-Letter Queue (DLQ) for SNS

- SNS supports a **DLQ** for failed deliveries (e.g., Lambda function error, HTTP endpoint down)
- Configure an **SQS queue as the DLQ** on a subscription
- Failed messages after all retry attempts are stored in the DLQ for inspection

## A2P Messaging

SNS supports direct messaging to human endpoints:
- **SMS**: Send text messages to phone numbers worldwide
- **Email**: Notify operations teams or on-call engineers
- **Mobile Push**: Send push notifications to iOS/Android apps via APNS, FCM, ADM, Baidu

## Key Points / Exam Tips

- SNS is **push-based** — it pushes messages to subscribers; SQS is **pull-based** (consumers poll)
- **Standard Topic** → use for fan-out to SQS queues, Lambda, HTTP endpoints
- **FIFO Topic** → use only when ordered delivery to FIFO SQS is required
- SNS + SQS together = **Fan-out pattern** — one message published to SNS, delivered to multiple SQS queues
- Message filtering makes architectures more efficient — subscribers only get relevant messages
- SNS does **not** persist messages (unless delivered to SQS/Kinesis); if a subscriber is down, messages may be lost (except for retries)
- SNS is ideal for **event notification** (alerts, status changes) and **fan-out architectures**

## Trigger Words

- "Send one message to multiple downstream systems" → SNS Fan-out
- "Notify multiple services when an event occurs" → SNS Topic
- "Send email/SMS alert on system event" → SNS (A2P)
- "Ordered delivery with no duplicates for notifications" → SNS FIFO Topic
- "Only deliver relevant events to specific subscribers" → SNS Message Filtering
- "Push notification to mobile app" → SNS Mobile Push
