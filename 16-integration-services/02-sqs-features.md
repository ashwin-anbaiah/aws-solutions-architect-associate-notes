# SQS Features (Visibility Timeout, DLQ, Delay Queue, Long Polling)

## Long Polling

Without long polling, a consumer that finds no messages gets an empty response immediately and must poll again — wasting API calls and cost.

**Long Polling** makes SQS **wait up to 20 seconds** for a message to arrive before returning:

- Reduces the **number of SQS API calls** → lower cost
- Reduces **application latency** — message returned as soon as it appears in the queue
- Configured at the **queue level** or per-request using the `WaitTimeSeconds` parameter
- Recommended over short polling for all production workloads

| | Short Polling (default) | Long Polling |
|---|---|---|
| Returns | Immediately (even if empty) | Waits up to 20 sec for a message |
| API calls | Many (constant polling) | Fewer (reduced by waiting) |
| Cost | Higher | Lower |
| Latency | Low (but with empty responses) | Low (message returned immediately when available) |

## Delay Queues

A **Delay Queue** postpones message visibility for a configured duration after it is sent.

- **Range**: 0 to 15 minutes delay
- Producers send messages normally; consumers **cannot see** them until delay expires
- Configured at the **queue level** (applies to all messages) or per-message (using `DelaySeconds` parameter in `SendMessage`)
- **Per-message delay** overrides the queue-level delay

**Use cases:**
- Undo/cancel window (e.g., allow user to cancel an action within 5 minutes of triggering it)
- Processing workflows where downstream systems need time to prepare
- Email "send" button with undo window

## Dead-Letter Queue (DLQ)

A **Dead-Letter Queue** captures messages that repeatedly fail processing.

**How it works:**
1. Each time a message is received and not deleted within the Visibility Timeout, SQS increments its **receive count**
2. When the receive count exceeds the **MaxReceiveCount** threshold (configured on the source queue's Redrive Policy), SQS moves the message to the configured DLQ
3. Messages in the DLQ can be inspected, debugged, reprocessed, or alerted on

**Configuration:**
- Create a separate SQS queue to act as the DLQ
- Configure a **Redrive Policy** on the source queue with:
  - `deadLetterTargetArn`: ARN of the DLQ
  - `maxReceiveCount`: number of attempts before moving to DLQ

**Important:**
- DLQ must be the **same type** as the source queue (Standard DLQ for Standard, FIFO DLQ for FIFO)
- DLQ has its own **retention period** — set it high enough (e.g., 14 days) to allow investigation
- Messages in DLQ are not automatically retried — you must manually reprocess or use Lambda to reprocess them

**Use cases:**
- Poison message isolation (malformed messages that will always fail)
- Debugging failed processing logic
- Alerting on processing failures (CloudWatch alarm on DLQ depth)

## Batch Operations

SQS supports sending, receiving, and deleting **up to 10 messages in a single API call**:

| API | Max batch |
|---|---|
| `SendMessageBatch` | 10 messages |
| `ReceiveMessage` | 10 messages |
| `DeleteMessageBatch` | 10 messages |

- Reduces SQS API costs (fewer API calls for the same throughput)
- Improves application performance (fewer round trips)

## Lambda with SQS (Event Source Mapping Details)

- Lambda's ESM polls SQS and invokes the function with a **batch of messages**
- Default batch size: 10; configurable up to 10,000 for Standard, 10 for FIFO
- Lambda **scales concurrency** automatically based on queue depth (up to 1,000 concurrent)
- Lambda deletes messages **only after successful processing**
- **Partial batch response**: Lambda can report individual failed message IDs so only those are retried (not the whole batch)
- On failure, messages become visible again (or go to DLQ after MaxReceiveCount)

## Key Points / Exam Tips

- **Long Polling** (WaitTimeSeconds up to 20 sec) reduces empty responses and lowers cost — always prefer over short polling
- **Delay Queue** = postpone message visibility (0-15 min) after the producer sends it
- **DLQ** = captures messages that exceed MaxReceiveCount — use for poison message handling and debugging
- DLQ must match the source queue type (Standard → Standard DLQ; FIFO → FIFO DLQ)
- High MaxReceiveCount = more retry attempts before DLQ; Low = fail fast to DLQ
- Lambda + SQS: Lambda deletes messages only on success — no explicit `DeleteMessage` call needed in your code

## Trigger Words

- "Reduce empty SQS poll responses" → Long Polling
- "Delay message processing for X minutes after sending" → Delay Queue
- "Messages keep failing and clogging the queue" → Dead-Letter Queue (DLQ)
- "Undo window / cancel before processing" → Delay Queue
- "Inspect failed messages" → DLQ + CloudWatch alarm on DLQ depth
- "Lambda only retries failed messages, not entire batch" → Partial Batch Response
