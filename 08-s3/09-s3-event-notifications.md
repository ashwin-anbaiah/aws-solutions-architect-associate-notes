# S3 Event Notifications and EventBridge

## What are S3 Event Notifications?

S3 can publish notifications when certain events happen in a bucket (object created, deleted, restored, etc.). These notifications drive event-driven architectures — for example, automatically generating thumbnails when a user uploads a photo.

---

## Notification Destinations (Direct S3 Notifications)

| Destination | Permission Required on Target |
|---|---|
| **Amazon SNS** | `sns:Publish` (SNS access policy must allow `s3.amazonaws.com`) |
| **Amazon SQS** | `sqs:SendMessage` (SQS resource policy must allow S3) |
| **AWS Lambda** | `lambda:InvokeFunction` (Lambda resource-based policy must allow S3) |

Each destination requires a **resource-based policy** on the target allowing the S3 service principal (`s3.amazonaws.com`) to invoke it.

---

## Supported S3 Event Types

| Event | Description |
|---|---|
| `s3:ObjectCreated:*` | New object uploaded (PUT, POST, COPY, multipart complete) |
| `s3:ObjectRemoved:*` | Object deleted (including delete markers) |
| `s3:ObjectRestore:*` | Glacier restore initiated or completed |
| `s3:Replication:*` | Replication success or failure |
| `s3:Lifecycle*` | Lifecycle transition or expiration events |
| `s3:IntelligentTiering` | Intelligent-Tiering archive events |

---

## Notification Filters

- **Object prefix**: e.g., `images/` — only notify for objects under this prefix
- **Object suffix**: e.g., `.jpg` — only notify for objects with this extension
- If versioning is enabled, notifications are sent for each object version

---

## Notification Delivery Characteristics

- **At-least-once delivery** — a notification may be delivered more than once (design consumers to be idempotent)
- **No ordering guarantee** — notifications may arrive out of order
- **Notification contains metadata** (object key, size, event type) — NOT the object data itself
- **Near-real-time** — typically seconds after the event

---

## Amazon EventBridge Integration

S3 automatically sends **all API events** to Amazon EventBridge (source: `aws.s3`). EventBridge offers more power than direct S3 notifications:

| Feature | Direct S3 Notifications | Amazon EventBridge |
|---|---|---|
| Targets | SNS, SQS, Lambda (3 targets) | 25+ targets (Step Functions, ECS, Kinesis, API destinations, etc.) |
| Filtering | Prefix/suffix only | Advanced filtering on any JSON event field |
| Cross-account routing | Not supported | Supported |
| Archive and replay | Not supported | Supported |
| Multiple targets per event | Not easily | Yes, fan-out to multiple targets |

**When to use EventBridge instead of direct notifications:**
- Need more than 3 target types
- Cross-account event routing needed
- Advanced filtering logic required (e.g., filter by object size, metadata, tags)
- Need to trigger a workflow (Step Functions)

---

## Exam Scenarios

| Scenario | Solution |
|---|---|
| Generate thumbnail when profile picture uploaded to `images/` | S3 Event Notification → Lambda |
| Audit every S3 API call and route to central audit account | Amazon EventBridge (cross-account routing) |
| Queue uploaded videos for async processing by worker fleet | S3 Event Notification → SQS |
| Fan-out new article upload to email, SMS, mobile push | S3 Event Notification → SNS topic with multiple subscriptions |
| Trigger monthly billing workflow when CSV lands in `reports/` | EventBridge → Step Functions (multi-account workflow) |
| Process archived files when Glacier restore completes | S3 Event Notification → Lambda (s3:ObjectRestore:Completed) |

---

## Key Points / Exam Tips

- Direct S3 notifications support **only SNS, SQS, Lambda** — for anything else, use EventBridge
- EventBridge receives **all** S3 API events automatically (must be enabled per bucket)
- The destination (SNS/SQS/Lambda) must have a **resource-based policy** allowing S3 to invoke it
- Notifications contain **object metadata only** — not the actual object content
- Delivery is **at-least-once** — design consumers to handle duplicate messages
- Versioning: separate notification per version

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Trigger Lambda when object uploaded to S3" | S3 Event Notification → Lambda |
| "Route S3 events to 5 different downstream services" | Amazon EventBridge (25+ targets) |
| "Cross-account S3 event routing" | Amazon EventBridge |
| "Fan-out S3 events to email and SMS" | S3 Event Notification → SNS (with multiple subscriptions) |
| "Buffer S3 upload events for async processing" | S3 Event Notification → SQS |
| "React to Glacier restore completion" | S3 Event Notification (s3:ObjectRestore:Completed) |
| "Advanced filtering on S3 event fields" | Amazon EventBridge |
