# AWS Health Dashboard

## What is AWS Health?

**AWS Health** provides visibility into the health and availability of AWS services — both globally and specifically for your account. It proactively notifies you about events that may impact your resources.

## AWS Health Dashboard Views

### Service Health (Global)

- Shows **current and historical AWS service disruptions** globally
- Anyone can view this (no login required) at status.aws.amazon.com
- Useful for confirming whether an outage is AWS-wide vs account-specific

### Your Account Health (Personal Health Dashboard)

- Shows events **specifically relevant to your account and resources**
- Two types of events:
  - **Issue events** — active problems affecting your resources (e.g., underlying host degraded)
  - **Scheduled change events** — upcoming maintenance or retirement (e.g., EC2 instance retirement, RDS engine upgrade)
  - **Account notifications** — security, compliance, billing alerts
- Provides **remediation guidance** and links to affected resources
- Accessible from the AWS Console health menu (top right bell icon)

### Organizational View

- Aggregates health events across **all accounts in an AWS Organization**
- Requires enabling organizational view in a delegated administrator account
- Allows centralized visibility for operations / security teams

## AWS Health Integration with EventBridge

**AWS Health** automatically publishes events to **Amazon EventBridge**.

EventBridge rules can filter by:
- Service name (e.g., `EC2`, `RDS`)
- Region
- Event type category (issue, scheduledChange, accountNotification)

**Actions you can trigger:**

```
AWS Health Event
        ↓
Amazon EventBridge Rule (filter by service/region/type)
        ↓
Actions:
  - SNS → Email / SMS notification
  - Lambda → Auto-remediation (e.g., replace affected instance)
  - SSM Automation → Run a runbook
  - SQS → Queue for processing
  - Third-party ticketing (JIRA, ServiceNow via Lambda)
```

This enables **proactive automated responses** to AWS maintenance or outages.

## Key Points / Exam Tips

- **Service Health** = global view (all customers); **Account Health** = your account's resources only
- AWS Health events include: issues, scheduled maintenance, and account notifications
- **Organizational view** aggregates health across all accounts in an AWS Organization
- **EventBridge integration** enables automated responses to health events
- Health events include **remediation steps** — not just alerts
- Common exam scenario: "Notify operations team when an EC2 instance is scheduled for retirement" → AWS Health + EventBridge + SNS

## Trigger Words

| Keyword | Think |
|---|---|
| "AWS service outage affecting my account" | AWS Health Dashboard (Your Account) |
| "EC2 instance scheduled for retirement" | AWS Health scheduled change event |
| "Automate response to AWS maintenance" | AWS Health + EventBridge |
| "Centralized health view across org" | AWS Health Organizational View |
| "Personal Health Dashboard" | AWS Health — Your Account tab |
