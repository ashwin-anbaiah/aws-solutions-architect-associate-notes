# S3 Logging and Reporting (Server Access Logs, Inventory, Storage Lens)

## Overview

S3 offers several tools for visibility into access patterns, storage usage, and security posture:

1. **Server Access Logs** — request-level audit trail
2. **S3 Inventory** — periodic metadata report across all objects
3. **IAM Access Analyzer for S3** — identifies publicly accessible or cross-account shared buckets
4. **S3 Storage Lens** — organization-wide storage analytics

---

## S3 Server Access Logs

**Server Access Logs** record detailed information about every HTTP request made to a bucket.

| Property | Detail |
|---|---|
| **What is logged** | Requester, bucket name, object key, operation, response status, error code, bytes transferred, time |
| **Delivery target** | A separate S3 bucket (MUST be a different bucket from the source) |
| **Timing** | NOT real-time — delivery may take minutes to hours |
| **Cost** | No extra charge to enable; pay only for storage of the logs |
| **Log volume** | Can become very large; use Lifecycle rules to move/expire old logs |

**Warning:** Never set the logging bucket to be the same as the source bucket — this creates an infinite logging loop, generating massive costs.

### Use Cases
- Security and audit: track who accessed which objects
- Access pattern analysis: identify most/least accessed objects
- Cost optimization: find high-egress objects to cache with CloudFront

---

## S3 Inventory

**S3 Inventory** provides a scheduled report listing all objects and their metadata in a bucket.

| Property | Detail |
|---|---|
| **Schedule** | Daily or weekly |
| **Output format** | CSV, ORC, or Parquet — stored in a separate S3 bucket |
| **Included metadata** | Object size, storage class, encryption status, replication status, ETag, version ID, Object Lock info, last modified date |
| **Scope filters** | By prefix, current version only, or all versions |
| **Analysis tool** | Pair with Amazon Athena for SQL-based queries at scale |

### Why Inventory Instead of List API?

- `ListObjects` scans the bucket in real-time — slow and expensive for millions of objects
- S3 Inventory is **cost-efficient** and **scheduled** — designed for large-scale auditing and compliance reporting

### Use Cases
- Encryption compliance: identify unencrypted objects
- Replication compliance: identify objects not yet replicated
- Cost optimization: analyze storage class distribution
- Audit: generate complete object lists for compliance reports

---

## IAM Access Analyzer for S3

**IAM Access Analyzer for S3** analyzes bucket policies and ACLs to detect unintended public or cross-account access.

| Identifies | Description |
|---|---|
| **Publicly accessible buckets** | Buckets accessible by anonymous (`*`) internet users |
| **Cross-account shared buckets** | Buckets accessible by other AWS accounts or external IAM principals |

- Available in the S3 Console (Permissions tab) and IAM Access Analyzer service
- Helps remediate unintended access exposures
- Complements Block Public Access but provides deeper policy analysis

---

## S3 Storage Lens

**S3 Storage Lens** is a visualization and analytics tool for organization-wide S3 usage and activity.

| Property | Detail |
|---|---|
| **Scope** | All buckets, all regions, all accounts in an AWS Organization |
| **Metrics** | 100+ metrics: storage usage, object counts, versions, encryption status, activity trends |
| **Dashboard** | Pre-built interactive dashboard in S3 Console |
| **Recommendations** | Cost optimization suggestions (unused buckets, incomplete multipart uploads, non-IA-eligible data) |
| **Advanced tier (paid)** | Per-prefix insights, activity metrics (PUT/GET/DELETE rates), security findings |
| **Data export** | Export to S3 in CSV or Parquet for further analysis |

### Use Cases
- Identify unused or under-utilized buckets
- Find objects not transitioning to cheaper storage classes
- Detect incomplete multipart uploads accumulating cost
- Track organization-wide encryption adoption
- Monitor request patterns across all accounts

---

## Tools Comparison

| Tool | Granularity | Frequency | Purpose |
|---|---|---|---|
| Server Access Logs | Per-request | Near-real-time (minutes) | Detailed request audit trail |
| S3 Inventory | Per-object metadata | Daily or weekly | Bulk object metadata reporting |
| IAM Access Analyzer | Per-bucket policy/ACL | On-demand / continuous | Security: public or cross-account access |
| S3 Storage Lens | Org-wide aggregation | Daily | Cost optimization, usage trends |

---

## Key Points / Exam Tips

- Server Access Logs must go to a **different bucket** — never the same source bucket (infinite loop)
- S3 Inventory is the right tool for large-scale object auditing — do NOT use `ListObjects` at scale
- IAM Access Analyzer for S3 is the tool to identify publicly exposed or cross-account buckets
- S3 Storage Lens = organization-wide (multi-account, multi-region) analytics — not per-bucket
- S3 Inventory pairs with **Amazon Athena** for SQL analysis of object metadata

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Audit every S3 request (who accessed what)" | S3 Server Access Logs |
| "Periodic report of all objects and their encryption status" | S3 Inventory |
| "Identify publicly accessible S3 buckets" | IAM Access Analyzer for S3 |
| "Organization-wide S3 usage analytics across accounts" | S3 Storage Lens |
| "Scalable alternative to ListObjects for auditing millions of objects" | S3 Inventory |
| "Find S3 cost optimization opportunities across all accounts" | S3 Storage Lens |
