# Amazon CloudWatch Overview

## What is Amazon CloudWatch?

**Amazon CloudWatch** is AWS's primary **monitoring and observability** service. It collects metrics, logs, and events from AWS resources and applications, then lets you visualize, analyze, and act on that data.

> "CloudWatch is the single pane of glass for AWS monitoring."

## What CloudWatch Monitors

| Source | Examples |
|---|---|
| **AWS Services** | EC2 (CPU, network), RDS (connections, IOPS), Lambda (duration, errors), S3 (requests) |
| **EC2 / On-premises** | Memory, disk, processes (via CloudWatch Agent) |
| **Containers** | ECS, EKS, Kubernetes (via Container Insights) |
| **Applications** | Custom metrics via `put-metric-data` API |
| **Logs** | Any application log file, VPC Flow Logs, CloudTrail, API Gateway access logs |

## CloudWatch Feature Overview

| Feature | Purpose |
|---|---|
| **Metrics** | Time-series performance data (CPU, memory, requests, etc.) |
| **Alarms** | Trigger actions when metrics breach thresholds |
| **Logs** | Collect, store, and search log data |
| **Logs Insights** | SQL-like queries on log data |
| **Dashboards** | Visual monitoring views |
| **Metric Streams** | Continuously stream metrics to external services |
| **Container Insights** | Deep metrics for ECS/EKS/Kubernetes |
| **Lambda Insights** | Deep metrics for Lambda functions |
| **Synthetics** | Canary scripts to monitor endpoints proactively |
| **Anomaly Detection** | ML-based anomaly detection on metrics |
| **ServiceLens** | Traces + metrics + logs correlation (integrates X-Ray) |
| **RUM** | Real User Monitoring for web applications |

## How CloudWatch Works

```
AWS Resources / Applications
        ↓  emit metrics & logs
Amazon CloudWatch
        ├── Metrics  →  Alarms  →  Actions (SNS, Auto Scaling, EC2 actions)
        ├── Logs     →  Log Insights / Subscriptions / S3 Export
        └── Dashboards (visual)
```

## Monitoring Modes (EC2)

| Mode | Frequency | Cost |
|---|---|---|
| **Basic monitoring** | Every 5 minutes | Free (default) |
| **Detailed monitoring** | Every 1 minute | Additional charge |

## CloudWatch Agent

- Required to collect **OS-level metrics** from EC2 or on-premises servers
- Collects: Memory, Disk usage, Swap, Network connections, Running processes
- Also collects **log files** from the instance
- EC2 instance needs an **IAM role** with `CloudWatchAgentServerPolicy`

> Note: EC2 **does not** send RAM or disk metrics to CloudWatch by default — you need the agent.

## Key Points / Exam Tips

- CloudWatch is the **primary** monitoring service in AWS — know it well
- EC2 sends CPU, network, disk I/O by default; **RAM and disk space require the CloudWatch Agent**
- **Basic monitoring = 5 min** (free); **Detailed monitoring = 1 min** (paid)
- CloudWatch covers metrics, logs, dashboards, alarms, and insights — all in one service
- CloudWatch **does not** store infrastructure configuration state (that's AWS Config)
- CloudWatch **does not** record API calls (that's CloudTrail)

## Trigger Words

| Keyword | Think |
|---|---|
| "Monitor EC2 CPU, network" | CloudWatch Metrics |
| "RAM/memory metric on EC2" | CloudWatch Agent required |
| "Send alert when CPU > 80%" | CloudWatch Alarm |
| "View metrics across resources" | CloudWatch Dashboard |
| "Collect application logs" | CloudWatch Logs |
