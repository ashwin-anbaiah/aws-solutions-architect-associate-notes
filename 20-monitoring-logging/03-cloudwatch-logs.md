# CloudWatch Logs: Log Groups, Streams, Subscription Filters

## What is CloudWatch Logs?

**Amazon CloudWatch Logs** is the centralized log management service in AWS. It collects, stores, monitors, and analyzes log data from AWS services and your own applications.

## Log Structure

### Log Groups

- A **Log Group** is a logical container for logs — typically one per application or service
- Example: `/aws/lambda/my-function`, `/aws/ec2/application/`, `/aws/ecs/webapp/`
- Log retention policies are set at the **log group level** (1 day to 10 years; default = never expire)
- Encryption is set at the **log group level** using KMS (customer-managed or AWS-managed key)

### Log Streams

- A **Log Stream** is a sequence of log events from a **single source** within a log group
- Examples: a specific EC2 instance ID, a Lambda function invocation, a container task
- Multiple log streams exist under one log group

```
Log Group: /aws/ec2/application/
    ├── Log Stream: i-0abc123def456789a   ← EC2 instance 1
    ├── Log Stream: i-0def456abc789012b   ← EC2 instance 2
    └── Log Stream: i-0ghi789abc012345c   ← EC2 instance 3
```

## Log Sources

| Source | How Logs Arrive |
|---|---|
| EC2 / On-premises | **CloudWatch Agent** (must install manually) |
| Lambda | Automatic (no agent needed) |
| ECS | CloudWatch Logs driver in container definition |
| API Gateway | Enable access logging |
| VPC Flow Logs | Configure VPC to send to CloudWatch Logs |
| CloudTrail | Configure trail destination |
| Route 53 | Query logging |

> EC2 logs are **not** automatically sent to CloudWatch. You must install the **CloudWatch Agent** and attach an IAM role with `logs:PutLogEvents` permissions.

## CloudWatch Agent — Extra Metrics

Installing the CloudWatch Agent on EC2 also unlocks OS-level metrics not available by default:

- **Memory** (RAM): free, used, total, cached
- **Disk**: space free/used/total, I/O reads/writes/bytes/IOPS
- **Network**: TCP/UDP connections, packets, bytes
- **Processes**: total, running, sleeping, blocked, dead
- **Swap**: free, used, used %

## Log Storage and Retention

- Default retention: **never expire** (logs stored indefinitely)
- Configurable retention: 1 day, 3 days, 5 days, 1 week, 1 month, ... 10 years
- Storage classes: **Standard** and **Infrequent Access (Logs-IA)** — IA is cheaper for rarely accessed logs
- Logs are **encrypted by default** (AWS-managed key); optionally use customer-managed KMS key

## S3 Export

- Export CloudWatch Logs to an **Amazon S3 bucket** for long-term archival
- Export can take **up to 12 hours** — not real-time
- Supports **SSE-KMS** and **SSE-S3** encryption on the S3 side
- Use with **S3 Object Lock** for compliance / immutable retention
- Use **Athena** to query exported logs in S3

> Tip: S3 storage is cheaper than CloudWatch Logs — export old logs to S3 for cost savings.

## CloudWatch Logs Subscription Filters

**Subscription Filters** stream log events in **real-time** from a log group to another AWS service.

- Filter by pattern (e.g., only send `ERROR` lines)
- **Max 2 subscription filters per log group**

### Supported Destinations

| Destination | Use Case |
|---|---|
| **AWS Lambda** | Real-time processing / transformation |
| **Kinesis Data Streams** | Buffer and fan out to multiple consumers |
| **Amazon Data Firehose** | Deliver to S3, Redshift, OpenSearch, Splunk |

### Cross-Account Log Streaming

- Logs can be streamed **across AWS accounts**
- For **Kinesis Data Streams** target: create a cross-account IAM role in the destination account
- For **Firehose** target: configure a resource-based IAM policy on the Firehose allowing `logs.amazonaws.com`
- **Lambda** targets do **not** support cross-account subscriptions

## Log Aggregation

- CloudWatch Logs can aggregate logs from **multiple accounts** and **multiple regions**
- Use subscription filters in each account → Kinesis Data Stream / Firehose → centralized destination

```
Account A (Region 1)  →  Subscription Filter  →
Account B (Region 2)  →  Subscription Filter  →  Kinesis → Firehose → S3
Account C (Region 3)  →  Subscription Filter  →
```

## Metric Filters

- **Metric Filters** scan log events for a pattern and **publish a custom metric** when matched
- Example: Count the number of `ERROR` occurrences per minute and publish as a metric → set an alarm on it
- Applied at the **log group level**

## Key Points / Exam Tips

- **Log Group** = container (application); **Log Stream** = individual source (instance, function)
- EC2 logs require **CloudWatch Agent** + IAM role — they do not auto-appear
- S3 export is **not real-time** (up to 12 hours delay); use **Subscription Filters** for real-time streaming
- Subscription filters support **Lambda, Kinesis Data Streams, Firehose** — max 2 per log group
- **Metric Filters** convert log patterns into CloudWatch metrics (e.g., count ERRORs)
- Cross-account streaming via Kinesis requires **cross-account IAM role** in destination account
- Retention policies are set **per log group**; default is indefinite

## Trigger Words

| Keyword | Think |
|---|---|
| "Collect logs from EC2" | CloudWatch Agent |
| "Real-time log streaming to S3/Splunk" | Subscription Filter → Firehose |
| "Long-term log archival" | CloudWatch Logs → S3 Export |
| "Count errors in logs, set alarm" | Metric Filter → CloudWatch Alarm |
| "Aggregate logs across accounts" | Subscription Filter → Kinesis |
| "Log retention policy" | Set on Log Group |
