# CloudWatch Metrics, Custom Metrics, and Alarms

## CloudWatch Metrics

### What is a Metric?

A **CloudWatch Metric** is a time-series data point representing a specific system measurement at a specific time.

Examples:
- `CPUUtilization` for EC2
- `NetworkIn` / `NetworkOut` for EC2
- `GetRequests` for S3
- `EBSReadOps` for EBS volumes

### Namespaces

Metrics are organized into **namespaces** — logical groupings by service.

| Namespace | Service |
|---|---|
| `AWS/EC2` | Amazon EC2 |
| `AWS/S3` | Amazon S3 |
| `AWS/EBS` | Amazon EBS |
| `AWS/ELB` | Elastic Load Balancing |
| `AWS/RDS` | Amazon RDS |
| `AWS/Lambda` | AWS Lambda |
| Custom | Your own application metrics |

### Dimensions

A **dimension** is a key-value pair that uniquely identifies a metric within a namespace.

- For `AWS/EC2` namespace: `InstanceId=i-1234567890abcdef0`
- For `AWS/ELB` namespace: `LoadBalancerName=my-load-balancer`
- Dimensions let you filter metrics to a specific resource instance

CloudWatch supports up to **30 dimensions** per metric.

### Metric Resolution

| Type | Frequency | Notes |
|---|---|---|
| **Standard** | 1-minute intervals | Default for most services with detailed monitoring |
| **High-resolution** | 1-second intervals | For custom metrics only (`--storage-resolution 1`) |
| Basic EC2 monitoring | 5 minutes | Default, free |
| Detailed EC2 monitoring | 1 minute | Must be enabled, paid |

## Custom Metrics

- Publish your own metrics to CloudWatch using the `put-metric-data` API
- Common use: application-level metrics (items sold, queue depth, error rate, etc.)

```bash
aws cloudwatch put-metric-data \
  --metric-name ProductsSold \
  --namespace MyApp/Sales \
  --value 42 \
  --timestamp 2026-01-20T11:30:40.000Z
```

- Custom metrics appear in **your chosen namespace** (not `AWS/`)
- Can use standard or high-resolution (1-second) storage

## CloudWatch Alarms

### What is an Alarm?

A **CloudWatch Alarm** watches a single metric and performs one or more actions when the metric falls outside defined thresholds.

### Alarm States

| State | Meaning |
|---|---|
| **OK** | Metric is within the defined threshold |
| **ALARM** | Metric has breached the threshold |
| **INSUFFICIENT_DATA** | Not enough data to determine state (startup, gaps) |

### Alarm Actions

- **Auto Scaling** — increase or decrease EC2 desired count
- **EC2 Actions** — stop, terminate, reboot, or recover an EC2 instance
- **SNS Notification** — send message to an SNS topic (email, Lambda, SQS, etc.)
- **OpsCenter** — create an OpsItem for operational management

### Alarm Configuration

- **Period** — the length of time (in seconds) to evaluate a metric
- **Evaluation Periods** — number of periods to evaluate before transitioning state
- **Threshold** — the value to compare against (e.g., CPU > 80%)
- **Statistic** — how to aggregate data points: Average, Sum, Minimum, Maximum, Percentile

### Example: EC2 CPU Alarm

```
Metric:      AWS/EC2 > CPUUtilization
Dimension:   InstanceId = i-1234567890abcdef0
Threshold:   > 80%
Period:      300 seconds (5 min)
Eval periods: 3 (alarm fires after 15 min of sustained high CPU)
Action:      Notify SNS topic → email alert
```

## Composite Alarms

**Composite Alarms** combine multiple CloudWatch alarms using **logical rules** (AND / OR / NOT).

- They evaluate the **state of other alarms** — not metrics directly
- Actions fire only when the **composite condition** is met
- Reduces **alert noise** — avoid firing on individual spikes

```
CPU_Alarm:  EC2 CPU > 80%          (regular alarm)
ELB_Alarm:  ALB 5XX errors > 50   (regular alarm)

Composite:  ALARM(CPU_Alarm) AND ALARM(ELB_Alarm)
Action:     Send SNS notification only when BOTH fire
```

## CloudWatch Metric Streams

- **Continuously stream** metrics from CloudWatch to external destinations
- Near real-time, low latency
- Delivery via **Amazon Data Firehose**
- Destinations: S3, Redshift, OpenSearch, or third-party SaaS (Datadog, Dynatrace, New Relic, Splunk)
- **Filter by namespace** to stream only a subset of metrics

## Key Points / Exam Tips

- A metric belongs to a **namespace** and is identified by its **dimensions**
- **Basic monitoring** = 5-min intervals (free); **Detailed monitoring** = 1-min intervals (paid)
- RAM and disk are **not** default EC2 metrics — need CloudWatch Agent
- Alarm states: **OK / ALARM / INSUFFICIENT_DATA** — know all three
- **Composite Alarms** reduce noise by combining multiple alarms with AND/OR logic
- Custom metrics can have **high-resolution** (1-second) storage
- Metric Streams use **Firehose** as the delivery mechanism — not direct S3

## Trigger Words

| Keyword | Think |
|---|---|
| "Trigger scaling when CPU > threshold" | CloudWatch Alarm + Auto Scaling action |
| "Alert only when CPU AND error rate are both high" | Composite Alarm |
| "Application-level business metric" | Custom metric via `put-metric-data` |
| "Stream metrics to Datadog/Splunk" | CloudWatch Metric Streams |
| "Metric namespace / dimensions" | CloudWatch Metrics fundamentals |
