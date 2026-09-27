# S3 Performance (Multipart Upload, Byte Range Fetch, Transfer Acceleration)

## Overview

S3 is designed to scale automatically, but you can unlock significantly higher throughput and lower latency by using the right performance optimization techniques:

1. **Prefix partitioning** — spread requests across multiple prefixes for higher request throughput
2. **Multipart Upload** — split large uploads for faster, parallel transfers
3. **Byte-Range Fetch** — download specific byte ranges in parallel for faster reads
4. **S3 Transfer Acceleration** — use AWS edge network for fast long-distance uploads/downloads
5. **Amazon CloudFront** — cache frequently accessed S3 content at edge locations

---

## Prefix Partitioning

S3 partitions data internally based on object key prefixes to scale request rates.

| Request Type | Rate per Prefix |
|---|---|
| **PUT / COPY / POST / DELETE** | 3,500 requests/second |
| **GET / HEAD** | 5,500 requests/second |

**Key insight:** These limits are **per prefix**, not per bucket. Use multiple prefixes to multiply throughput.

### Good vs. Bad Prefix Design

| Anti-Pattern | Better Pattern | Why |
|---|---|---|
| `images/img001.jpg` `images/img002.jpg` | `images/hash-01/img001.jpg` `images/hash-02/img002.jpg` | Single hot prefix vs. distributed across prefixes |
| `logs/app.log` | `logs/2024/01/05/app.log` | Date-based prefix creates natural partitioning |
| `iot/data.json` | `iot/device-01/data.json` `iot/device-02/data.json` | Device-per-prefix distributes load |

**Note:** SSE-KMS has an additional limit — KMS API calls (GenerateDataKey, Decrypt) have their own regional quota. Use **S3 Bucket Keys** to dramatically reduce KMS API calls per object.

---

## Multipart Upload

**Multipart Upload** splits a large object into parts that are uploaded in parallel.

| Threshold | Recommendation |
|---|---|
| Objects > 100 MB | **Recommended** to use Multipart Upload |
| Objects > 5 GB | **Required** — single PUT is not allowed |
| Objects > 5 TB | Not possible — 5 TB is the S3 maximum object size |

### Benefits
- Parallel uploads maximize available bandwidth
- If a part fails, only that part needs to be retried (not the entire object)
- Better resilience for large file uploads over unreliable networks
- Upload parts can begin before the full file is ready

### Cost Tip
- Incomplete multipart uploads accumulate storage costs — configure an **S3 Lifecycle rule** to abort incomplete multipart uploads after N days

---

## Byte-Range Fetch

**Byte-Range Fetch** allows downloading only a specific portion of an object using HTTP range requests.

```bash
# AWS CLI example — download bytes 0 through 500
aws s3api get-object \
  --bucket my-bucket \
  --key folder/my_data \
  --range bytes=0-500 \
  my_data_range.output
```

### Use Cases

| Use Case | How Byte-Range Helps |
|---|---|
| **Parallel downloads** | Split large file into ranges, download in parallel, reassemble |
| **Media streaming** | Fetch only the currently needed video segment |
| **Partial reads (ETL)** | Read only headers or first N bytes of large files |
| **Resumable downloads** | Track last byte fetched; resume from that offset |
| **Read file headers** | Fetch just the first few bytes to check file format/metadata |

---

## S3 Transfer Acceleration (S3TA)

**S3 Transfer Acceleration** routes data through globally distributed **AWS Edge Locations** and over the **AWS backbone network**, significantly reducing latency for long-distance transfers.

| Property | Detail |
|---|---|
| **Speed improvement** | 50–500% faster for long-distance transfers of large objects |
| **How it works** | Data enters nearest AWS edge location, travels AWS backbone (not public internet) to S3 |
| **New endpoint** | `bucket-name.s3-accelerate.amazonaws.com` |
| **Enable/Disable** | Per-bucket setting |
| **Cost** | Additional per-GB charge on top of standard data transfer fees |
| **Best for** | Users geographically far from the S3 bucket region |

**Without S3TA:** User in Japan → multiple internet hops → S3 bucket in N. Virginia
**With S3TA:** User in Japan → nearest edge location → AWS backbone → S3 bucket in N. Virginia

---

## Amazon CloudFront for S3 Caching

**Amazon CloudFront** is a CDN that caches S3 static content at edge locations close to users.

| Property | Detail |
|---|---|
| **Cache location** | 400+ edge locations worldwide |
| **Best for** | Frequently read static content (images, videos, HTML/CSS/JS) |
| **Cost benefit** | Reduced data transfer out cost vs. direct S3; 1 TB/month free DTO via CloudFront |
| **Private bucket access** | Use **CloudFront Origin Access Control (OAC)** — newer, uses SigV4 signing |
| **Legacy private access** | CloudFront Origin Access Identity (OAI) — being replaced by OAC |

### S3TA vs CloudFront

| Feature | S3 Transfer Acceleration | Amazon CloudFront |
|---|---|---|
| Direction | Upload + Download | Primarily download (cache) |
| Caching | No | Yes |
| Best for | Large file upload from distant users | Frequently accessed static content |
| Access control | IAM/bucket policy | OAC or OAI for private S3 origin |

---

## Key Points / Exam Tips

- 3,500 PUT / 5,500 GET requests/second **per prefix** — spread objects across prefixes for higher throughput
- Multipart required for objects > **5 GB**; recommended for > **100 MB**
- Byte-Range Fetch is ideal for **parallel downloads** and **partial reads**
- S3 Transfer Acceleration uses edge locations for **long-distance** transfers — NOT for users near the bucket
- CloudFront caches content at edge; S3TA does not cache — they serve different performance goals
- CloudFront + OAC is the current best practice for private S3 origins (OAI is legacy)
- Use S3 Lifecycle to auto-delete **incomplete multipart uploads** — a common cost leak

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Increase S3 throughput beyond single prefix limits" | Prefix partitioning (multiple prefixes) |
| "Upload large file faster with failure recovery" | Multipart Upload |
| "File over 5 GB to S3" | Multipart Upload required |
| "Download specific sections of a large file in parallel" | Byte-Range Fetch |
| "Users in Europe uploading large files to S3 in US" | S3 Transfer Acceleration |
| "Cache S3 static assets globally" | Amazon CloudFront |
| "Secure private S3 bucket accessible only via CloudFront" | CloudFront OAC (Origin Access Control) |
| "Reduce KMS API calls for SSE-KMS S3 objects" | S3 Bucket Keys |
