# Amazon SQS Overview

## What is Amazon SQS?

**Amazon Simple Queue Service (SQS)** is a fully managed, highly available distributed **message queue** service that enables decoupled, asynchronous communication between application components.

- Supports **multiple producers** (writers) and **consumers** (readers) for the same queue
- Enables **loose coupling**: producers and consumers operate independently
- **Absorbs traffic spikes**: queue buffers messages during peak load
- **At-least-once delivery** (Standard Queue) or **exactly-once processing** (FIFO Queue)

## How SQS Works

1. **Producer** sends a message to the SQS queue
2. Message is stored in the queue for up to the **retention period** (default 4 days)
3. **Consumer** polls (requests) messages from the queue
4. Requested message becomes **invisible** to other consumers (Visibility Timeout starts)
5. Consumer **processes** the message
6. Consumer **deletes** the message using the `DeleteMessage` API
7. If consumer **fails** or crashes before deleting, the message becomes **visible again** after Visibility Timeout expires — another consumer can pick it up

## Standard Queue vs FIFO Queue

| Feature | Standard Queue | FIFO Queue |
|---|---|---|
| **Throughput** | Nearly unlimited (millions/sec) | Up to 300 TPS; 3,000 TPS with batching |
| **Message ordering** | Best-effort (not guaranteed) | Strict FIFO within message groups |
| **Delivery** | At-least-once (duplicates possible) | Exactly-once (no duplicates) |
| **Cost** | Lower | Higher |
| **Use when** | High throughput, duplicates/reordering acceptable | Order-critical workflows, financial transactions |
| **Name suffix required** | None | `.fifo` (e.g., `MyQueue.fifo`) |

## Message Visibility Timeout

After a consumer requests a message, SQS makes it **temporarily invisible** to other consumers:

- **Default**: 30 seconds
- **Range**: 0 seconds to 12 hours
- While invisible: the consumer processes the message and deletes it
- If not deleted within timeout: message becomes **visible again** (available for another consumer)
- Consumer can extend timeout using `ChangeMessageVisibility` API before it expires
- **Too high**: slow re-processing on consumer crash
- **Too low**: same message processed multiple times (duplicates)

## Key SQS Parameters

| Parameter | Default | Range |
|---|---|---|
| **Message retention** | 4 days | 1 minute – 14 days |
| **Max message size** | 256 KB | Up to 256 KB (SQS Extended Client for up to 2 GB via S3) |
| **Visibility timeout** | 30 seconds | 0 sec – 12 hours |
| **Delivery delay** | 0 seconds | 0 – 15 minutes (Delay Queue) |
| **Long polling wait** | 0 seconds | 0 – 20 seconds |

## SQS Security

- **Encryption in transit**: All API calls secured via HTTPS
- **Encryption at rest**:
  - **SSE-SQS**: AWS-managed SQS key (automatic management and rotation)
  - **SSE-KMS**: Customer-managed KMS key for more control and auditing
- **IAM policies**: Control who can SendMessage, ReceiveMessage, DeleteMessage
- **Queue Resource Policy**: Allow cross-account access or allow AWS services (SNS) to send messages

## Architecture Patterns

1. **Lambda + SQS**: Event Source Mapping — Lambda polls and scales concurrency with queue depth
2. **EC2 Auto Scaling + SQS**: Scale EC2 fleet based on CloudWatch metric for queue depth
3. **API Gateway → SQS**: Use SQS as a buffer to absorb spiky API requests; Lambda consumes asynchronously
4. **SNS → SQS Fan-out**: One SNS topic publishes to multiple SQS queues for parallel processing

## Key Points / Exam Tips

- **Standard** = high throughput, at-least-once delivery; **FIFO** = exact order, exactly-once
- FIFO queue names must end in **`.fifo`**
- Visibility Timeout ensures only one consumer processes a message at a time
- SQS does not "push" messages — consumers must **poll** (pull model)
- Default message retention is **4 days**; maximum is **14 days**
- SQS is **not** a real-time, ultra-low latency system — it is designed for decoupled async workflows

## Trigger Words

- "Decouple application components" → SQS
- "Buffer requests during traffic spikes" → SQS
- "Process messages in exact order" → SQS FIFO
- "At-least-once vs exactly-once delivery" → Standard vs FIFO
- "Message visible multiple times" → Visibility Timeout too low
- "Lambda scales with queue depth" → SQS + Lambda Event Source Mapping
