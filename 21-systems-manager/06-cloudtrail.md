# AWS CloudTrail

## What is AWS CloudTrail?

**AWS CloudTrail** records **every API call and user action** made in your AWS account — capturing *who* did *what*, *when*, and *from where* using *which service*.

> "CloudTrail is the audit log for your AWS account."

## What CloudTrail Records

- AWS Management Console actions
- AWS CLI commands
- AWS SDK calls
- Actions by AWS services on your behalf (cross-service actions)

**Example events captured:**
- Who launched an EC2 instance and when
- Who logged into the AWS Console
- Who deleted an S3 bucket
- Who created a DB snapshot
- Who modified a security group

## How CloudTrail Stores Events

### Event History (Default)

- **Free**, automatically enabled for every AWS account
- Shows the **last 90 days** of **management events**
- Searchable in the console by resource type, user, time, etc.
- No configuration required

### Trails (Long-Term Storage)

A **Trail** delivers event history beyond 90 days to:

| Destination | Use Case |
|---|---|
| **Amazon S3** | Long-term storage, compliance, archival |
| **CloudWatch Logs** | Real-time monitoring, metric filters, alerting |
| **Amazon EventBridge** | Event-driven automation |

**Trail scope:**
- **All Regions** (recommended) — captures events from every region
- **Single Region** — only the region where the trail is created

> Best practice: Create a **multi-region trail** delivering to S3 for compliance and audit.

## CloudTrail Event Types

| Event Type | Description | Default? |
|---|---|---|
| **Management Events** | Control-plane API calls: create, modify, delete resources. Examples: `RunInstances`, `CreateBucket`, `PutBucketPolicy` | **Yes** (enabled by default) |
| **Data Events** | Data-plane operations on resources. Examples: `GetObject`, `PutObject` on S3; Lambda `Invoke` | **No** (must enable, extra cost) |
| **Insights Events** | Unusual API activity detected by ML | No (must enable) |

### Management Events

- Track resource-level changes — the most important event type for security and compliance
- Separated into **Read** (describe, list) and **Write** (create, delete, modify) events
- Can be filtered to log only Write events (reduces volume)

### Data Events

- Track access to data inside resources
- S3: `GetObject`, `PutObject`, `DeleteObject` per object
- Lambda: function invocations
- DynamoDB: `GetItem`, `PutItem`, `DeleteItem`
- Must be explicitly enabled; generates **high log volume**

## CloudTrail Insights

**CloudTrail Insights** uses **ML** to detect **unusual or anomalous API activity**:

- Baseline: CloudTrail learns normal API call rates for management events
- When activity spikes abnormally, an Insights event is generated
- Examples: sudden spike in `TerminateInstances`, unusual `CreateUser` activity
- Works on **management events only**
- Insights events are delivered to S3 and EventBridge

## CloudTrail Lake

**CloudTrail Lake** is a fully managed, immutable **event data store** with SQL querying:

- Events stored in **Apache ORC format** for efficient querying
- Uses **SQL (Trino-based)** queries directly in the console or API
- Supports long-term retention: **7-year** and **10-year** options
- Data is **immutable** — cannot be deleted or modified (compliance)
- More powerful querying than S3 + Athena approach

## Integration with Other Services

| Service | Integration |
|---|---|
| **Amazon S3** | Trail log delivery destination; use S3 Object Lock for compliance |
| **CloudWatch Logs** | Deliver trail events; create metric filters; set alarms |
| **Amazon Athena** | Query CloudTrail logs stored in S3 using SQL |
| **AWS Config** | Uses CloudTrail to detect configuration changes |
| **Amazon EventBridge** | Trigger automation based on specific API calls |
| **IAM Access Analyzer** | Generates least-privilege policies from CloudTrail activity (up to 90 days) |
| **Security Hub** | CloudTrail findings sent to Security Hub |

## Key Points / Exam Tips

- CloudTrail answers **"who did what, when, and from where"** in your AWS account
- **Event History** = free, last 90 days, management events only
- **Trail** = required for data events, long-term storage, cross-region, compliance
- **Management events** = default; **Data events** = must enable (extra cost)
- **CloudTrail Insights** = ML-based anomaly detection on management events
- **CloudTrail Lake** = immutable SQL-queryable event store (vs S3 + Athena)
- Multi-region trail is the **best practice** for governance
- CloudTrail logs are **not real-time** — typically delivered within 15 minutes

## Trigger Words

| Keyword | Think |
|---|---|
| "Who deleted the S3 bucket?" | CloudTrail |
| "Audit API activity in AWS account" | CloudTrail |
| "Long-term audit log storage" | CloudTrail Trail → S3 |
| "Detect unusual API activity" | CloudTrail Insights |
| "SQL query over CloudTrail events" | CloudTrail Lake |
| "S3 object-level access logging" | CloudTrail Data Events |
