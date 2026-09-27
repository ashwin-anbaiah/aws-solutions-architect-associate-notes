# Amazon Macie

## What is Amazon Macie?

**Amazon Macie** is a fully managed data security service that uses **machine learning and pattern matching** to automatically discover, classify, and protect sensitive data in Amazon S3.

Think of Macie as an automated security analyst that scans your S3 buckets and raises an alarm whenever it finds personal information like social security numbers, credit card numbers, or health records.

---

## Core Capability

Macie discovers **Personally Identifiable Information (PII)** and other sensitive data types by analyzing S3 object contents:

| Data Type | Examples |
|---|---|
| **PII** | Names, email addresses, phone numbers, SSNs, passport numbers |
| **Financial data** | Credit card numbers, bank account numbers |
| **Health data** | Medical record numbers, health conditions (PHI) |
| **Credentials** | AWS secret keys, private keys |
| **Custom patterns** | User-defined regex patterns for proprietary data types |

---

## How Macie Works

```
S3 Bucket
    ↓ (Macie analyzes object contents)
Amazon Macie
    ↓ (generates findings)
Amazon EventBridge
    ↓
Lambda / SNS / SQS / Security Hub
```

1. Macie continuously monitors configured S3 buckets
2. ML models and pattern matching scan object contents
3. When sensitive data is found, Macie generates a **finding** (classification result)
4. Findings are automatically sent to **Amazon EventBridge** for downstream processing
5. EventBridge can trigger Lambda, SNS notifications, or integrate with AWS Security Hub

---

## Key Features

| Feature | Detail |
|---|---|
| **Automated discovery** | Scans S3 buckets automatically on schedule or on-demand |
| **ML + pattern matching** | Combines statistical models with known PII patterns for accurate detection |
| **Finding types** | Policy findings (e.g., bucket is public) + Sensitive data findings (e.g., SSN found) |
| **EventBridge integration** | All findings sent to EventBridge for automated response |
| **Multi-account** | Works with AWS Organizations to scan all accounts centrally |
| **Managed data identifiers** | AWS-managed patterns for 100+ sensitive data types |
| **Custom identifiers** | User-defined regex patterns for proprietary/business-specific data |

---

## Common Use Cases

| Use Case | How Macie Helps |
|---|---|
| **GDPR / HIPAA compliance** | Identify PII/PHI stored in S3 that shouldn't be there |
| **Data breach prevention** | Detect accidental uploads of sensitive data |
| **Security posture monitoring** | Alert when public S3 buckets contain sensitive data |
| **Data governance** | Classify all S3 objects and map sensitive data locations |
| **Incident response** | Quickly determine what sensitive data may have been exposed |

---

## Macie vs. IAM Access Analyzer for S3

| Feature | Amazon Macie | IAM Access Analyzer for S3 |
|---|---|---|
| **What it analyzes** | Object content (what data is inside) | Bucket policies and ACLs (who has access) |
| **Detects** | Sensitive data (PII, PHI, credentials) | Public or cross-account access |
| **Continuous monitoring** | Yes (scheduled + real-time) | Yes |
| **Use together?** | Yes — complementary tools | Yes |

Use both: Access Analyzer tells you **who can access** your bucket; Macie tells you **what sensitive data** is in it.

---

## Key Points / Exam Tips

- Macie analyzes **object content** — not just metadata
- Findings are published to **Amazon EventBridge** — not directly to CloudWatch alarms
- Macie uses both **AWS managed identifiers** (100+ PII/PHI/credentials types) and **custom identifiers** (your regex patterns)
- Macie supports **multi-account** through AWS Organizations — one central administrator account
- Macie is scoped to **S3 only** — it does not scan EBS, EFS, or databases
- "Detect PII in S3" = Macie; "Detect public S3 bucket" = IAM Access Analyzer

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Discover PII data in S3 buckets" | Amazon Macie |
| "Automatically detect sensitive data stored in S3" | Amazon Macie |
| "GDPR compliance — find personal data in S3" | Amazon Macie |
| "Alert when SSN or credit card numbers found in S3" | Amazon Macie |
| "ML-based data classification for S3" | Amazon Macie |
| "Who has access to my S3 bucket?" | IAM Access Analyzer for S3 |
| "What sensitive data is inside my S3 objects?" | Amazon Macie |
