# Amazon EventBridge

## What is Amazon EventBridge?

**Amazon EventBridge** is a serverless **event bus** service that routes events between AWS services, SaaS applications, and custom applications using rules — without requiring point-to-point integrations.

- **Event-driven architecture**: services communicate through events (JSON objects) rather than direct API calls
- Integrates with **130+ event sources** and **40+ targets**
- Handles **retries with exponential backoff** for failed deliveries (up to 24 hours)
- Supports **event archiving, replaying, schema discovery**, and **input transformation**

## Event Bus Types

| Bus Type | Description | Sources |
|---|---|---|
| **Default Event Bus** | Auto-created per account | All AWS services (EC2, S3, RDS, etc.) |
| **Partner Event Bus** | Receive events from SaaS partners | Zendesk, Datadog, Shopify, GitHub, etc. |
| **Custom Event Bus** | For your own applications | Custom apps, cross-account events |

## Events

Events are **JSON objects** with a standard structure:
```json
{
  "source": "aws.ec2",
  "detail-type": "EC2 Instance State-change Notification",
  "detail": {
    "instance-id": "i-0abcd1234ef567890",
    "state": "stopped"
  }
}
```

## Rules

**Rules** determine when and how events are routed to targets.

### Rule Types

| Rule Type | Description |
|---|---|
| **Event Pattern–Based** | Triggers when incoming event matches a JSON pattern |
| **Schedule-Based** | Triggers on a time schedule (cron or rate expression) |

### Event Pattern Matching

EventBridge supports rich content-based filtering:
- **Exact match**: `"detail": {"state": ["stopped"]}`
- **Prefix match**: `"detail": {"username": [{"prefix": "admin"}]}`
- **Anything-but**: `"detail": {"status": [{"anything-but": ["failed", "cancelled"]}]}`
- **Numeric match**: `"detail": {"amount": [{"numeric": [">", 100]}]}`
- **IP/CIDR match**: `"detail": {"sourceIp": [{"cidr": "10.0.0.0/16"}]}`
- **Exists check**: `"detail": {"errorCode": [{"exists": true}]}`
- **AND/OR logic**: use arrays for OR (any match) and nesting for AND

### Rule Targets (40+)

A single rule can deliver to **multiple targets simultaneously**:
- Lambda functions
- Amazon SQS queues
- Amazon SNS topics
- Step Functions state machines
- Kinesis Data Streams / Firehose
- API Gateway (HTTP destinations)
- EventBridge event buses (cross-account)
- CloudWatch Logs
- CodeBuild, CodePipeline

## Input Transformation

Rules support **input transformation** — modify the event structure before delivering to the target:
- Rename fields: `OrderNumber` → `OrderId`
- Extract specific fields: send only relevant data to the target
- Construct custom payloads from event attributes
- Useful when targets expect a specific format

## Schema Registry

EventBridge automatically discovers and stores **event schemas** (structure definitions):
- Stores schemas for all events flowing through your event buses
- Supports versioning — track schema evolution over time
- **Code bindings**: generate strongly-typed classes in Java, Python, TypeScript
- Reduces integration errors by giving consumers a clear event contract
- Supports schema types: OpenAPI, JSON Schema, Apache Avro

## Event Archiving and Replay

EventBridge can **archive events** as they flow through an event bus:
- Archive all events or only events matching a filter pattern
- **Retention**: up to 7 years
- **Replay**: re-process archived events through rules and targets
- Use cases: debugging failures, onboarding new consumers to historical events, disaster recovery, compliance auditing

## EventBridge vs SNS vs SQS

| Feature | EventBridge | SNS | SQS |
|---|---|---|---|
| **Model** | Event routing (rules-based) | Pub/Sub (topics) | Queue (pull) |
| **Event filtering** | Rich content-based filtering | Message attribute filtering | No native filtering |
| **SaaS integration** | Yes (130+ sources) | No | No |
| **Schema registry** | Yes | No | No |
| **Archiving/replay** | Yes (7 years) | No | No |
| **Scheduling** | Yes (cron/rate) | No | No |
| **Best for** | Complex event routing, orchestration, SaaS | Fan-out notifications | Job queues, decoupling |

## Key Points / Exam Tips

- EventBridge is the answer when you see: "route AWS service events to multiple targets", "SaaS integration", "scheduled event-driven workflows"
- **Default Event Bus** receives events from all AWS services automatically — no setup required
- **Rules fan-out**: one event can trigger multiple targets simultaneously
- EventBridge retries failed deliveries for **up to 24 hours** with exponential backoff
- **Scheduled rules** can replace cron jobs (EventBridge Scheduler is even more powerful for scheduling)
- For "detect EC2 state changes and notify" → EventBridge rule on `aws.ec2` source + SNS/Lambda target
- EventBridge is fully serverless — no infrastructure to manage

## Trigger Words

- "Trigger Lambda when EC2 instance stops" → EventBridge rule (EC2 state change)
- "Run a workflow every Monday at 9am" → EventBridge Schedule-based rule
- "Route events from Shopify/Zendesk to AWS" → EventBridge Partner Event Bus
- "Filter and route events based on content" → EventBridge rules with event patterns
- "Replay past events for new consumer" → EventBridge Event Archiving + Replay
- "Discover event schema for coding" → EventBridge Schema Registry
