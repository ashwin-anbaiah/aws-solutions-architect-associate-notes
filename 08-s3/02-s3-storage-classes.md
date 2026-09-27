# S3 Storage Classes

## Overview

S3 offers multiple storage classes optimized for different access patterns and cost requirements. All classes share the same **11 nines durability** (except One Zone-IA).

Choose a storage class based on: **how often you access the data**, **how quickly you need it**, and **how long you store it**.

---

## Storage Classes Summary

| Storage Class | Access Frequency | Retrieval Latency | Min Storage Duration | Cost vs Standard |
|---|---|---|---|---|
| **S3 Standard** | Frequent | Milliseconds | None | Baseline (highest) |
| **S3 Intelligent-Tiering** | Unknown / variable | Milliseconds | None | Varies; monitoring fee applies |
| **S3 Standard-IA** | Infrequent | Milliseconds | 30 days | Lower storage, retrieval fee |
| **S3 One Zone-IA** | Infrequent | Milliseconds | 30 days | Lowest among IA |
| **S3 Glacier Instant Retrieval** | Rare (quarterly) | Milliseconds | 90 days | Very low storage cost |
| **S3 Glacier Flexible Retrieval** | Rare (yearly) | Minutes to 12 hours | 90 days | Very low |
| **S3 Glacier Deep Archive** | Very rare (<once/year) | 12–48 hours | 180 days | Lowest cost |
| **S3 Express One Zone** | High-performance | Single-digit ms | None | Premium |

---

## Detailed Descriptions

### S3 Standard (General Purpose)
- Highest storage cost; no retrieval fee
- Best for: frequently accessed data, websites, mobile apps, gaming, data lakes
- Multi-AZ durability (redundant across ≥3 AZs)

### S3 Standard-IA (Infrequent Access)
- Lower storage cost than Standard, but a **per-GB retrieval fee** applies
- Minimum storage duration: **30 days** (you pay for 30 days even if deleted sooner)
- Multi-AZ durability
- Best for: disaster recovery backups, on-premises data replicas, data accessed monthly

### S3 One Zone-IA
- Same as Standard-IA but stored in **only ONE AZ**
- **NOT suitable for critical/irreplaceable data** — data is lost if the AZ is destroyed
- Cheapest IA tier
- Best for: secondary backup copies that can be re-created, easily reproducible data

### S3 Intelligent-Tiering
- AWS automatically moves objects between tiers based on actual access patterns (no retrieval fees)
- Small monthly monitoring and automation fee per object
- Tiers:
  - **Frequent Access** (default) — moves to Infrequent Access after 30 days of no access
  - **Infrequent Access** — moves to Archive Instant Access after 60 days
  - **Archive Instant Access** — after 90 days (optional)
  - **Archive Access** and **Deep Archive Access** — after 90 and 180 days (optional, configurable)
- Best for: data with unknown or unpredictable access patterns

### S3 Glacier Instant Retrieval
- Millisecond retrieval (unlike other Glacier classes)
- Minimum storage: **90 days**
- Best for: data accessed quarterly, medical images, news media that need instant access occasionally

### S3 Glacier Flexible Retrieval
- Retrieval options:
  - **Expedited**: 1–5 minutes (highest cost)
  - **Standard**: 3–5 hours
  - **Bulk**: 5–12 hours (lowest cost)
- Minimum storage: **90 days**
- Best for: backup and disaster recovery archives, data accessed 1–2 times/year

### S3 Glacier Deep Archive
- **Cheapest** S3 storage class
- Retrieval:
  - **Standard**: 12 hours
  - **Bulk**: up to 48 hours
- Minimum storage: **180 days**
- Best for: long-term regulatory archives, compliance data retained for 7+ years

### S3 Express One Zone (High Performance)
- Single-digit millisecond access
- Stored in a single AZ (Directory Bucket only)
- Up to 10x faster than S3 Standard, request costs 80% lower
- Best for: ML model training, high-frequency trading, analytics that need lowest possible latency

---

## S3 Intelligent-Tiering vs. Lifecycle Rules

| Feature | S3 Intelligent-Tiering | S3 Lifecycle Rules |
|---|---|---|
| Transitions | Automatic based on access | User-defined (specify days) |
| Direction | Can move both ways | One-way (waterfall model) |
| Expiration | Not supported | Supported |
| Monitoring fee | Yes (per object) | No |
| Best for | Unknown/unpredictable access | Known/predictable access |

---

## Minimum Billing Duration

Objects deleted before the minimum storage duration are still billed for the full minimum period:
- Standard-IA, One Zone-IA: **30 days**
- Glacier Instant Retrieval, Glacier Flexible Retrieval: **90 days**
- Glacier Deep Archive: **180 days**

---

## Key Points / Exam Tips

- **"Immediate access always required"** → rules out all Glacier tiers (Instant Retrieval is ms, but has 90-day minimum)
- **"Data can be re-created / non-critical"** → One Zone-IA is acceptable (otherwise avoid it)
- **"Unpredictable access pattern"** → Intelligent-Tiering
- **"Known access pattern" (e.g., rarely accessed after 30 days)** → Lifecycle Policy to Standard-IA/Glacier (cheaper than Intelligent-Tiering's monitoring fee)
- **"Cheapest long-term storage"** → Glacier Deep Archive
- S3 Standard-IA has a **retrieval fee** — high-frequency access from IA is more expensive than Standard
- Snowball cannot write directly to Glacier — data lands in S3 first, then a lifecycle rule transitions it

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Frequently accessed data" | S3 Standard |
| "Infrequently accessed but needs instant retrieval" | S3 Standard-IA |
| "Reproducible data, single AZ OK, low cost" | S3 One Zone-IA |
| "Unpredictable access pattern, auto-tier" | S3 Intelligent-Tiering |
| "Quarterly access, millisecond retrieval" | S3 Glacier Instant Retrieval |
| "Long-term archive, hours to retrieve, cheapest" | S3 Glacier Deep Archive |
| "ML training, ultra-low latency" | S3 Express One Zone |
