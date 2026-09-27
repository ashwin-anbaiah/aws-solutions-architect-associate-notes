# S3 Lifecycle Rules

## What are S3 Lifecycle Rules?

**S3 Lifecycle Rules** automatically transition objects to cheaper storage classes or expire (delete) them after a specified period. They help manage cost across the full lifecycle of your data — from hot storage when data is fresh to archival when it becomes rarely accessed.

Think of it as a "tiered filing system": recent documents on your desk (S3 Standard), older ones in the filing cabinet (Standard-IA), and really old documents in a warehouse (Glacier).

---

## Types of Lifecycle Actions

| Action Type | What It Does | Example |
|---|---|---|
| **Transition** | Move objects to a different (cheaper) storage class | Move to Standard-IA after 30 days |
| **Expiration** | Permanently delete objects or versions | Delete objects after 365 days |

---

## Lifecycle Waterfall Model

S3 Lifecycle rules follow a **one-directional waterfall** — objects can only transition to colder (cheaper) classes, never warmer:

```
S3 Standard
    ↓ (after 30+ days)
S3 Standard-IA / One Zone-IA / Intelligent-Tiering
    ↓ (after 90+ days total)
S3 Glacier Instant Retrieval
    ↓
S3 Glacier Flexible Retrieval
    ↓ (after 180+ days total)
S3 Glacier Deep Archive
    ↓ (or delete)
Expired (deleted)
```

---

## Transition Minimum Age Rules

| Transition To | Minimum Age from Creation |
|---|---|
| S3 Standard-IA or One Zone-IA | **30 days** |
| Glacier Instant Retrieval | **90 days** |
| Glacier Flexible Retrieval | **90 days** |
| Glacier Deep Archive | **180 days** |

**Note:** Objects smaller than **128 KB** will not be transitioned — the overhead cost of managing them in IA/Glacier is not worth it.

---

## Expiration Actions

- Delete current object versions after N days
- Delete **non-current versions** (older versioned objects) after N days — essential for cost control when versioning is enabled
- Delete **incomplete multipart uploads** after N days (very common cost optimization)
- Delete **expired delete markers** (when all versions are gone, clean up the delete marker)

---

## Lifecycle Rule Filters

Lifecycle rules can be scoped to specific objects using:

| Filter Type | Example |
|---|---|
| **Object prefix** | `logs/2024/` — applies only to objects under this prefix |
| **Object tags** | `environment=development` |
| **Object size** | Min size: 1 MB, Max size: 5 GB |
| **No filter** | Applies to all objects in the bucket |

**Important:** Lifecycle rules apply **within a single bucket** — they cannot copy or move objects to a different bucket.

---

## Lifecycle Rules vs. S3 Intelligent-Tiering

| Feature | S3 Lifecycle Rules | S3 Intelligent-Tiering |
|---|---|---|
| Transition logic | User-defined (specify days) | Automatic based on access patterns |
| Direction | One-way (waterfall, colder only) | Bidirectional (can move warmer) |
| Expiration | Supported | Not supported |
| Monitoring fee | No | Yes (small per-object fee) |
| Best for | Known/predictable access patterns | Unknown/unpredictable access patterns |

---

## Real-World Scenario Example

**News articles + summary snippets:**

For full articles:
1. Store in S3 Standard (day 0–30, frequently accessed)
2. Transition to S3 Glacier Deep Archive after 30 days (rarely accessed, OK to wait 12 hrs)
3. Expire/delete after 7 years (compliance retention met)

For summary snippets (re-creatable, non-critical):
1. Store in S3 Standard (day 0–30)
2. Transition to S3 One Zone-IA after 30 days (can be re-created if lost)
3. Expire/delete after 90 days

---

## Key Points / Exam Tips

- Lifecycle rules **cannot move objects between buckets** — only storage classes within the same bucket
- Minimum 30 days in Standard before transitioning to IA tiers
- Objects under 128 KB are not transitioned (minimum size for IA/Glacier cost effectiveness)
- Versioning + Lifecycle: use **non-current version expiration** rules to expire old versions and control costs
- Incomplete multipart upload cleanup via Lifecycle expiration is a key cost optimization practice
- Lifecycle processing is **asynchronous** — transitions do not happen instantly on the day specified
- One Zone-IA is appropriate for re-creatable data (non-critical secondary copies)

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Move logs to cheaper storage after 30 days" | S3 Lifecycle Transition rule |
| "Delete old object versions automatically" | S3 Lifecycle Expiration on non-current versions |
| "Known access pattern" | S3 Lifecycle Rules (vs Intelligent-Tiering) |
| "Clean up incomplete uploads" | S3 Lifecycle Expiration for incomplete multipart uploads |
| "Waterfall model" | S3 Lifecycle (one-directional transition, colder only) |
| "Data compliance for 7 years, then delete" | Lifecycle Transition to Deep Archive + Expiration at 7 years |
| "Secondary backups, re-creatable data, low cost" | Transition to One Zone-IA via Lifecycle |
