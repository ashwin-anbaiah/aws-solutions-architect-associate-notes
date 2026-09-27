# CloudWatch Insights and Dashboards

## CloudWatch Logs Insights

**CloudWatch Logs Insights** lets you run interactive, **SQL-like queries** on CloudWatch Logs data — directly in the console or via API — without exporting or moving data.

### Key Characteristics

- Queries run **in place** — no ETL, no export needed
- Can query **multiple log groups simultaneously**
- Supports: filtering, parsing, sorting, aggregation, visualization
- Results can be displayed as tables or charts
- **Read-only** — queries never modify log data
- Best for: **ad-hoc investigation and debugging**

### Sample Query Syntax

```
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 20
```

**Common operations:**
- `fields` — select specific fields
- `filter` — filter by condition (supports regex with `like /pattern/`)
- `sort` — order results
- `stats` — aggregate (count, avg, sum, min, max)
- `parse` — extract fields from unstructured text

### Logs Insights vs Subscription Filters

| Feature | Logs Insights | Subscription Filters |
|---|---|---|
| Purpose | Ad-hoc querying | Real-time streaming |
| Data movement | None (in place) | Streams to Lambda/Kinesis/Firehose |
| Use case | Debugging, investigation | Processing pipeline, archival |

---

## CloudWatch Insights Extensions

CloudWatch offers service-specific **Insights** that combine logs and metrics for deeper visibility:

| Insight Type | Target | Key Metrics |
|---|---|---|
| **Container Insights** | ECS, EKS, Kubernetes | CPU, memory, network, pod/node metrics |
| **Lambda Insights** | Lambda functions | Memory usage, cold starts, duration, concurrency, errors |
| **Application Insights** | EC2 + EBS + ELB based apps | Latency, errors — auto-detects issues |
| **Database Insights** | RDS and Aurora | Load, waits, SQL performance, top queries |
| **Contributor Insights** | Logs and metrics | Identifies **top contributors** (e.g., which IP causes most 5XX errors) |

### Container Insights

- Collects metrics at container, pod, node, and cluster levels
- Works with **Amazon ECS**, **Amazon EKS**, and **self-managed Kubernetes**
- Data is sent to CloudWatch as **embedded metric format** logs, then surfaced as metrics

### Lambda Insights

- Provides per-invocation performance data: init duration (cold start), duration, memory used/allocated
- Requires the **Lambda Insights extension layer** to be added to the function

### Contributor Insights

- Analyzes log data to find **top N contributors** to a metric
- Example: "Which source IPs are making the most requests?" or "Which API path generates the most errors?"
- Works with CloudWatch Logs or AWS service logs (VPC Flow Logs, etc.)

---

## CloudWatch Dashboards

**CloudWatch Dashboards** provide customizable, **visual monitoring views** that can show metrics and alarms from across services, regions, and accounts on a single screen.

### Dashboard Types

| Type | Description |
|---|---|
| **Automatic** | Pre-built dashboards AWS creates for some services |
| **Custom** | You create and arrange widgets for your workloads |

### Widget Types

- Line/area charts for time-series metrics
- Number displays (current metric value)
- Alarms status widgets
- Log query result widgets (embed a Logs Insights query result)
- Text widgets (for labels/documentation)

### Cross-Region and Cross-Account

- Dashboards can display metrics from **multiple AWS regions**
- With appropriate setup, dashboards can aggregate data from **multiple AWS accounts**

### Sharing Dashboards

- Share with **specific users** via email — they log in with a generated username/password
- Share **publicly** via URL — anyone with the link can view (no AWS credentials needed)
- All shared dashboards are **read-only**
- Sharing can be **revoked** at any time

## Key Points / Exam Tips

- **Logs Insights** = ad-hoc SQL-like queries on log data in place — great for debugging
- Container Insights, Lambda Insights, etc. = service-specific deeper visibility (beyond default metrics)
- **Contributor Insights** = find top contributors to traffic/errors (useful for identifying DDoS or bad actors)
- Dashboards can span **multiple regions and accounts** in a single view
- Dashboards can be shared **publicly** (URL) or via email — always read-only
- Lambda cold start duration is visible in **Lambda Insights** (not in default Lambda metrics)

## Trigger Words

| Keyword | Think |
|---|---|
| "Query logs with SQL-like syntax" | CloudWatch Logs Insights |
| "Which IP is causing the most errors?" | Contributor Insights |
| "Lambda cold start and memory metrics" | Lambda Insights |
| "Container CPU and memory per pod" | Container Insights |
| "Share monitoring dashboard publicly" | CloudWatch Dashboard sharing |
| "Multi-region metrics in single view" | CloudWatch Dashboard |
