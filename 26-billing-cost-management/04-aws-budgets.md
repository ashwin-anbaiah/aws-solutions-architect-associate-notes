# AWS Budgets and Billing Alerts

## AWS Budgets

**AWS Budgets** lets you set custom cost and usage thresholds and receive alerts when actual or forecasted spending approaches or exceeds those thresholds. Unlike Cost Anomaly Detection (ML-based), Budgets requires you to define the limits yourself.

---

## Budget Types

| Type | What It Tracks |
|---|---|
| **Cost Budget** | Total dollar spend — alert when cost exceeds $X |
| **Usage Budget** | Resource consumption — alert when you use >X hours of EC2, X GB of S3, etc. |
| **RI (Reserved Instance) Budget** | RI utilization or coverage thresholds |
| **Savings Plan Budget** | Savings Plan utilization or coverage thresholds |

---

## How to Configure a Budget

1. Go to **Billing and Cost Management → Budgets**
2. Choose a template or custom setup
3. Define the budget amount (e.g., $500/month for EC2)
4. Set alert thresholds (e.g., 80% and 100% of budget)
5. Specify notification channels (email or SNS)

---

## Budget Actions

**Budget Actions** let you automatically respond when a budget threshold is crossed — not just notify, but take action:

| Action Type | Example |
|---|---|
| **Apply IAM policy** | Attach a Deny policy to a role/user to stop them from launching new resources |
| **Apply SCP** | Apply an SCP to an OU/account to restrict spending |
| **Target an EC2 Auto Scaling group** | Stop EC2 instances in a specific ASG |

- Budget Actions require approval (manual review required before action) or can be automatic.
- Useful for enforcing hard spending limits in development accounts.

---

## AWS Billing Alarm (CloudWatch)

In addition to AWS Budgets, you can set a **CloudWatch Billing Alarm**:
- Must be configured in the **us-east-1 (N. Virginia)** Region (billing metrics are only in us-east-1).
- Monitors `EstimatedCharges` metric in CloudWatch.
- Set a threshold (e.g., alert when > $5 estimated charges for the month).
- Triggers SNS notification → email.

**Prerequisites:** Enable "Receive CloudWatch Billing Alerts" in Billing Preferences first.

---

## Budgets vs Cost Anomaly Detection vs Billing Alarm

| Tool | Threshold | Detection | Best For |
|---|---|---|---|
| **AWS Budgets** | Manual (you set $) | Static | Known spending limits, RI/SP coverage |
| **Cost Anomaly Detection** | ML (automatic) | Dynamic/ML | Unknown unexpected spikes |
| **CloudWatch Billing Alarm** | Manual (you set $) | Static | Simple total-cost alerting |

---

## Key Points / Exam Tips

- Budgets supports **four types:** Cost, Usage, RI, and Savings Plan — not just dollar amounts.
- **Budget Actions** = proactive cost control (attach Deny IAM policy or SCP automatically).
- CloudWatch Billing Alarm = simpler but only monitors total estimated charges (not per-service).
- CloudWatch Billing Alarm must be set up in **us-east-1** — billing metrics are only published there.
- Budgets can alert at both actual and **forecasted** spending thresholds.
- You can have **up to 20,000 Budgets** per account (soft limit).

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Alert when monthly spending exceeds $X" | AWS Budgets (Cost budget) |
| "Alert when EC2 usage exceeds X hours" | AWS Budgets (Usage budget) |
| "Alert when RI coverage drops below 80%" | AWS Budgets (RI Coverage budget) |
| "Automatically stop resources when budget exceeded" | AWS Budgets Actions |
| "Simple billing alert when total charges exceed $5" | CloudWatch Billing Alarm (us-east-1) |
