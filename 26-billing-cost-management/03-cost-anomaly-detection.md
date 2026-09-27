# AWS Cost Anomaly Detection

## What Is Cost Anomaly Detection?

**AWS Cost Anomaly Detection** is a fully managed service that uses **machine learning** to automatically monitor your AWS spending and detect unusual cost patterns — anomalies — without requiring you to set manual thresholds. It analyzes your historical spending patterns and alerts you when actual costs deviate significantly from what is expected.

---

## How It Works

```
Your AWS usage → Cost data ingested continuously
                          ↓
          ML model learns your normal spending patterns
          (accounts for day-of-week patterns, growth trends, seasonality)
                          ↓
          Anomaly detected when actual cost deviates significantly
                          ↓
          Alert sent via SNS → Email / Slack / PagerDuty / custom webhook
```

---

## Key Features

| Feature | Details |
|---|---|
| **ML-based detection** | No manual threshold configuration needed; adapts to your patterns |
| **Granularity** | Can detect anomalies at account, service, cost category, or cost allocation tag level |
| **Root cause analysis** | Identifies which services/regions/accounts are driving the anomaly |
| **Alert delivery** | Amazon SNS → email, Slack, ticketing system, etc. |
| **Anomaly monitors** | Define what to monitor (individual services, member accounts, cost allocation tags) |
| **Alerting frequency** | Individual alert per anomaly; daily or weekly digest summary |

---

## Anomaly Monitors

You create **monitors** to define the scope of what to watch:

| Monitor Type | Watches |
|---|---|
| **AWS services** | Spending on a specific AWS service (e.g., EC2 only) |
| **Linked accounts** | Spending on a specific member account |
| **Cost allocation tags** | Spending tagged with a specific key:value (e.g., `Environment:Production`) |
| **Cost categories** | Custom cost groupings you define |

---

## Cost Anomaly Detection vs AWS Budgets

| | Cost Anomaly Detection | AWS Budgets |
|---|---|---|
| Detection method | ML-based (no threshold needed) | Manual threshold (you set the $ amount) |
| Best for | Unknown/unexpected spikes | Known budgets with defined limits |
| Alert timing | As anomaly occurs | When budget threshold % is crossed |
| Root cause info | Yes — identifies the source service/account | No — just alerts on the total amount |
| Setup complexity | Low (ML handles it) | Slightly more config (enter budget amounts) |

---

## Key Points / Exam Tips

- Cost Anomaly Detection uses **ML** — the key differentiator vs Budgets (which uses static thresholds).
- It sends alerts through **Amazon SNS** — you connect SNS to email, Slack, etc.
- It provides **root cause analysis** — tells you which service or account caused the spike.
- Best for detecting unexpected usage patterns you didn't anticipate (a developer accidentally left a fleet of GPU instances running).
- Does NOT prevent spending — it only detects and alerts.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Automatically detect unusual cost spikes without setting thresholds" | AWS Cost Anomaly Detection |
| "ML-based alerting on unexpected AWS spending" | AWS Cost Anomaly Detection |
| "Get notified when costs unexpectedly spike" | Cost Anomaly Detection → SNS |
| "Identify which service caused a sudden cost increase" | Cost Anomaly Detection (root cause analysis) |
