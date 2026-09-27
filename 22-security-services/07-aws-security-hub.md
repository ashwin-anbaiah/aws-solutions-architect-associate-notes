# AWS Security Hub

## What is AWS Security Hub?

**AWS Security Hub** is a **centralized security findings aggregation and Cloud Security Posture Management (CSPM)** service that collects security data from multiple AWS services and third-party tools, then helps you analyze and prioritize security issues.

> "Security Hub = single pane of glass for all AWS security alerts and compliance checks."

## What Security Hub Does

1. **Aggregates findings** from multiple AWS security services and partners
2. **Runs automated security checks** against CIS/PCI/NIST/AWS best practices (CSPM)
3. **Prioritizes findings** by severity for investigation
4. **Routes findings** to EventBridge for automated response or to Detective for investigation

## Data Sources (Finding Providers)

Security Hub automatically aggregates findings from:

| Service | What It Contributes |
|---|---|
| **Amazon GuardDuty** | Threat detection findings |
| **Amazon Inspector** | Vulnerability scan findings |
| **Amazon Macie** | Sensitive data findings (S3) |
| **IAM Access Analyzer** | IAM policy exposure findings |
| **AWS Systems Manager** | Patch compliance findings |
| **AWS Firewall Manager** | Firewall compliance findings |
| **AWS Config** | Resource compliance findings (CSPM) |
| **Third-party partners** | Palo Alto, CrowdStrike, etc. (via AWS Marketplace) |

> Security Hub requires **AWS Config to be enabled** — it uses Config to evaluate resource compliance.

## Automated Security Standards (CSPM)

Security Hub evaluates your environment against industry security frameworks:

| Standard | Description |
|---|---|
| **CIS AWS Foundations Benchmark** | CIS best practices for AWS |
| **AWS Foundational Security Best Practices** | AWS-specific security controls |
| **PCI DSS** | Payment Card Industry standard |
| **NIST SP 800-53** | US federal security framework |
| **HIPAA** | Healthcare data security |

Each standard contains **controls** — automated checks that mark your account as **PASSED** or **FAILED**.

## Multi-Account Aggregation

- Security Hub can aggregate findings from **multiple accounts and regions**
- A designated **administrator account** (via AWS Organizations) sees all findings centrally
- Cross-region aggregation collects findings from multiple regions into one view

```
Account A GuardDuty findings  ↘
Account B Inspector findings  →  Security Hub (Admin Account) → Centralized Dashboard
Account C Macie findings      ↗
```

## Finding Routing

Security Hub findings can be:
- Viewed in the **Security Hub console** (centralized findings list)
- Sent to **Amazon EventBridge** → trigger Lambda, SSM Automation, SNS for remediation
- Sent to **Amazon Detective** for deeper investigation

## Security Hub Finding Format (ASFF)

All findings use the **Amazon Security Finding Format (ASFF)** — a standardized JSON format for security findings. This normalizes findings from all different sources into a common schema.

## Key Points / Exam Tips

- Security Hub = **aggregator** for security findings — it does not generate its own threat detections
- Must enable **AWS Config** for Security Hub CSPM checks to work
- Security Hub consolidates: **GuardDuty + Inspector + Macie + IAM Analyzer + Firewall Manager**
- Findings flow: Security Services → Security Hub → EventBridge → Remediation OR Detective → Investigation
- **ASFF** is the standardized format used by all finding providers
- Multi-account: designated administrator sees all member account findings
- Cross-region aggregation is supported

## Trigger Words

| Keyword | Think |
|---|---|
| "Centralized security findings dashboard" | AWS Security Hub |
| "CSPM / compliance checks vs CIS/PCI/NIST" | Security Hub standards |
| "Aggregate GuardDuty + Inspector findings" | AWS Security Hub |
| "Single pane for all security alerts" | AWS Security Hub |
| "Automated remediation from security findings" | Security Hub → EventBridge → Lambda/SSM |
