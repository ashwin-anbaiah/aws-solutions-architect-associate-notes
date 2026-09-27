# S3 Basics: Buckets and Objects

## What is Amazon S3?

**Amazon Simple Storage Service (S3)** is an object storage service that provides virtually unlimited storage capacity. Launched in 2006, it is one of AWS's oldest and most widely used services.

S3 is designed for **99.999999999% (11 nines) durability** — data is automatically replicated across multiple AZs within a region.

---

## Common Use Cases

- Enterprise application file storage
- Data lakes and big data analytics
- Media storage and distribution
- Backup and archival
- Static website hosting
- ML/AI training datasets

---

## Buckets

A **bucket** is the fundamental container for storing objects in S3.

| Property | Detail |
|---|---|
| **Scope** | Regional — all data is stored in the AWS region where the bucket was created |
| **Naming** | Globally unique across all AWS customers and accounts |
| **Default access** | All buckets and objects are **private** by default |
| **Types** | General Purpose (most common), Directory, Table, Vector |

### Bucket Types

| Type | Purpose |
|---|---|
| **General Purpose** | Standard; supports all storage classes; most common |
| **Directory Bucket** | High-performance workloads using S3 Express One Zone |
| **Table Bucket** | Tabular datasets, optimized for Apache Iceberg format |
| **Vector Bucket** | Storing vector embeddings for ML and LLM models |

---

## Objects

An **object** is a file stored in S3, consisting of the data itself plus metadata.

| Component | Description |
|---|---|
| **Key** | Unique identifier (the "file path") within the bucket — e.g., `section5/s3-overview.pdf` |
| **Value** | The actual data (bytes) |
| **Version ID** | Unique identifier for a specific version (when versioning is enabled) |
| **Metadata** | System-assigned (size, creation time, storage class) and user-defined (key-value pairs) |
| **Tags** | User-defined key-value pairs for access control, lifecycle, cost allocation |
| **ETag** | System-generated hash (usually MD5) for integrity verification |

**Object URL example:** `https://bucket-name.s3.ap-south-1.amazonaws.com/section5/s3-overview.pdf`

---

## Metadata vs. Tags

| Feature | User-Defined Metadata | Object Tags |
|---|---|---|
| Format | HTTP headers (`x-amz-meta-*`) | Key-value pairs |
| When set | Upload time only (cannot modify later without re-upload) | Any time (can add/update/remove after upload) |
| API access | Returned in every GET response | Separate API call required |
| Use for | Application-specific info (e.g., `source=camera-1`) | Lifecycle rules, access control, cost allocation, replication |

---

## Key S3 Limits

| Limit | Value |
|---|---|
| Maximum object size | 5 TB |
| Maximum single PUT upload | 5 GB |
| Objects > 100 MB | Use Multipart Upload (recommended) |
| Objects > 5 GB | Multipart Upload **required** |
| Bucket count per account | Default: 100 (can be increased) |

---

## Accessing S3

| Access Method | How |
|---|---|
| **AWS Console** | Browser-based UI |
| **AWS CLI** | `aws s3 cp`, `aws s3 ls`, etc. |
| **AWS SDKs** | Python (boto3), Java, Node.js, etc. |
| **REST API** | Direct HTTPS calls to S3 endpoints |
| **Pre-signed URLs** | Temporary access without AWS credentials |

---

## Key Points / Exam Tips

- S3 is **object storage** — not block storage (EBS) or file storage (EFS); objects are accessed via API, not mounted as a filesystem
- Bucket names must be **globally unique** — across all AWS accounts, not just yours
- Data is stored in the specified region; it does NOT automatically replicate across regions (use Cross-Region Replication for that)
- Objects are **private by default** — no public access without explicit configuration
- S3 key names are the "path" to the object; the `/` in the key creates the visual appearance of folders (there are no real folders in S3)
- Maximum single object size is **5 TB**

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Globally unique name required" | S3 bucket naming requirement |
| "Object storage" | S3 (not EBS or EFS) |
| "Virtually unlimited storage" | S3 |
| "11 nines durability" | S3 (99.999999999%) |
| "Private by default" | S3 buckets and objects |
| "File larger than 5 GB" | Must use S3 Multipart Upload |
