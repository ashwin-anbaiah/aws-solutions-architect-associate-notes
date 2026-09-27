# AWS Config

## What is AWS Config?

**AWS Config** tracks and records the **configuration state** of AWS resources over time, evaluating them against desired settings using **Config Rules**.

> "CloudTrail records *who did* what; AWS Config records *what the resource looks like* now — and how it's changed over time."

## What AWS Config Answers

- Are all security groups locked down (no port 22 open to 0.0.0.0/0)?
- Are there any S3 buckets with public access enabled?
- Is backups enabled on all RDS instances?
- Is CloudTrail enabled in all regions?
- Are there any IAM access keys older than 90 days?
- Are there any untagged resources?
- Is autoscaling configured with multi-AZ?

## Core Concepts

### Configuration Recorder

- Must be **enabled** to start capturing resource configurations and changes
- Records the current configuration of supported AWS resources
- Creates a **configuration item** every time a resource is created, modified, or deleted

### Configuration Items

- A snapshot of a resource's configuration at a point in time
- Contains: resource type, ID, ARN, configuration details, related resources, relationships
- Stored in **S3** (via delivery channel)

### Config Rules

**Config Rules** evaluate whether resources comply with desired configuration conditions.

| Rule Type | Description |
|---|---|
| **AWS Managed Rules** | Pre-built rules maintained by AWS (200+) |
| **Custom Rules** | You define the logic using an AWS Lambda function |

**Example Managed Rules:**
- `restricted-ssh` — flags any SG with port 22 open to 0.0.0.0/0
- `s3-bucket-public-read-prohibited` — flags S3 buckets with public read access
- `rds-multi-az-support` — flags RDS instances not configured with Multi-AZ
- `cloud-trail-enabled` — flags if CloudTrail is not enabled
- `iam-password-policy` — verifies IAM password policy meets requirements
- `required-tags` — flags resources missing required tags

### Compliance States

Each resource evaluated by a rule is marked as:

| State | Meaning |
|---|---|
| **COMPLIANT** | Resource meets the rule's requirements |
| **NON_COMPLIANT** | Resource violates the rule |
| **NOT_APPLICABLE** | Rule doesn't apply to this resource type |
| **ERROR** | Rule evaluation failed |

## AWS Config Features

### Notifications

- SNS notifications when **compliance state changes** (resource becomes non-compliant or returns to compliant)
- Useful for alerting security/ops teams in real time

### Remediation

- Automatically or manually **remediate non-compliant resources**
- Remediation actions use **SSM Automation Documents**
- Can retry automatically on failure (configurable max retries and interval)
- The SSM execution role needs appropriate permissions

```
Config Rule: restricted-ssh  →  NON_COMPLIANT
        ↓
Remediation: Run SSM Automation → Remove inbound rule → Notify
```

### Conformance Packs

- A **Conformance Pack** is a collection of Config Rules and remediation actions packaged together
- Deployed as a single entity to an account/region or across an AWS Organization
- Pre-built packs available: CIS AWS Foundations, NIST 800-53, PCI DSS, HIPAA, etc.

### Aggregators

- **Configuration Aggregators** collect compliance data across **multiple accounts and multiple regions**
- Provides a centralized, **read-only** compliance dashboard
- Works with AWS Organizations (automatic authorization) or individual account authorization
- Aggregators do **not** evaluate resources or trigger remediation — they only **aggregate**

### Advanced Queries

- Query the **current configuration state** using a **SQL-like syntax**
- Can query across multiple accounts and regions (via aggregator)

```sql
SELECT *
WHERE resourceType = 'AWS::EC2::SecurityGroup'
  AND configuration.ipPermissions.ipRanges CONTAINS '0.0.0.0/0'
```

## AWS Config vs CloudTrail

| Aspect | AWS Config | CloudTrail |
|---|---|---|
| What it tracks | **Resource configuration state** (what the resource looks like) | **API activity** (who did what) |
| Question answered | "Is this S3 bucket public?" | "Who made the S3 bucket public?" |
| Historical tracking | Yes (configuration history) | Yes (event history) |
| Compliance | Yes (Config Rules) | No |
| Remediation | Yes (via SSM Automation) | No |

## Key Points / Exam Tips

- AWS Config = **compliance and configuration tracking** — not API audit (that's CloudTrail)
- Config Rules evaluate resources as **COMPLIANT** or **NON_COMPLIANT**
- Must **enable the Configuration Recorder** first — Config doesn't work without it
- Remediation uses **SSM Automation Documents** — know this integration
- **Aggregators** = centralized multi-account compliance view; they are **read-only** (no evaluation)
- **Conformance Packs** = bundle of rules + remediations; used for regulatory compliance frameworks
- Config is a **per-region** service — aggregators bring together cross-region/account data
- Security Hub integrates with Config — non-compliant findings appear in Security Hub CSPM findings

## Trigger Words

| Keyword | Think |
|---|---|
| "Continuously track resource configurations" | AWS Config |
| "Check if S3 bucket is public" | AWS Config Rule |
| "Auto-remediate non-compliant security group" | AWS Config Remediation + SSM Automation |
| "Compliance across multiple accounts" | Config Aggregators |
| "Regulatory compliance pack (CIS/NIST/PCI)" | Conformance Packs |
| "Resource configuration history" | AWS Config |
