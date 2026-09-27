# DynamoDB Accelerator (DAX)

## What Is DAX?

- **DAX (DynamoDB Accelerator)** — fully managed, highly available **in-memory cache** purpose-built for Amazon DynamoDB.
- Improves DynamoDB read performance from **single-digit milliseconds to microseconds**.
- **API-compatible with DynamoDB** — only the client endpoint needs to change; no application code rewrite required.
- Provides access to **eventually consistent** data from DynamoDB tables.

## How DAX Works

```
Application
    ↓ (DynamoDB-compatible API calls)
DAX Cluster (in-memory cache)
    ↓ (cache miss only)
DynamoDB Table
```

- On a cache **hit**: DAX returns data from memory — microsecond latency.
- On a cache **miss**: DAX fetches from DynamoDB, caches the result, and returns to the application.
- DAX handles **cache invalidation** automatically when DynamoDB data is updated.

## Key Features

- Runs inside your **VPC** — private, not publicly accessible.
- **Multi-AZ** for high availability.
- Supports both **item cache** (individual item lookups) and **query cache** (query/scan results).
- No need to manage cache invalidation logic — DAX auto-syncs with DynamoDB.

## DAX vs ElastiCache

| Feature | DAX | ElastiCache |
|---|---|---|
| Works with | DynamoDB only | Any data source (RDS, Aurora, custom) |
| API compatibility | Drop-in DynamoDB-compatible | Requires application caching logic |
| Cache invalidation | Automatic (auto-syncs with DynamoDB) | Manual / application-managed |
| Data structures | DynamoDB items and query results | Rich (sorted sets, hashes, pub/sub — Redis) |
| Advanced features | Limited | Rich (TTL per key, leaderboards, streams) |
| Best when | Speed up DynamoDB reads, no code changes | Cache any system, need advanced features |

## When NOT to Use DAX

- **Strongly consistent reads** — DAX provides only eventually consistent data.
- **Write-intensive workloads** — DAX is a read cache; writes always go directly to DynamoDB.
- **Applications without repeated reads** — cache provides no benefit if every request accesses different data.

---

## Key Points / Exam Tips

- **Trigger:** "microsecond latency for DynamoDB," "cache for DynamoDB" → **DAX**
- **Trigger:** "DynamoDB reads too slow, need in-memory acceleration" → **DAX**
- DAX is **API-compatible** with DynamoDB — only endpoint URL changes; no code rewrite
- DAX provides **eventually consistent reads only** — not suitable for strongly consistent read workloads
- DAX is NOT appropriate for write-heavy workloads — it does not accelerate writes
- If both DAX and ElastiCache appear as options and the source is DynamoDB → **DAX**; if source is RDS/Aurora or you need pub/sub/leaderboards → **ElastiCache**
- DAX automatically syncs with DynamoDB — no manual cache invalidation code needed
