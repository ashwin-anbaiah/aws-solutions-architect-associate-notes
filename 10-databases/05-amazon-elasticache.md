# Amazon ElastiCache (Redis / Memcached)

## What Is ElastiCache?

- **Amazon ElastiCache** — fully managed in-memory database / caching service with extremely high performance and low latency.
- Reduces load on backend databases for **read-intensive workloads**.
- Runs inside a **VPC**; security groups, NACLs, and subnet route table rules apply.

## Supported Engines

| Engine | Description |
|---|---|
| **Redis OSS** | Rich data structures (sorted sets, hashes, lists, streams), pub/sub, geospatial, persistence, replication |
| **Memcached** | Simple key-value store; multi-threaded; horizontal scaling; no persistence |
| **Valkey** | Open-source Redis-compatible engine; community-driven; avoids Redis licensing restrictions |

## Redis vs Memcached Comparison

| Feature | Redis | Memcached |
|---|---|---|
| Authentication | AUTH (data-plane) + IAM for API actions | No built-in AUTH; SASL (advanced) |
| Encryption at rest | KMS (supported) | Not supported |
| Encryption in transit | TLS/SSL | Limited |
| High Availability | Multi-AZ with automatic failover | No Multi-AZ failover |
| Replication | Yes — replication groups + read replicas | No replication |
| Backup / Snapshots | Automatic backups and snapshots | No persistence, no backups |
| Global Datastore | Yes (global reads + DR) | Not supported |
| Data structures | Rich (lists, sets, sorted sets, streams, pub/sub) | Simple key-value only |
| Scaling | Vertical + horizontal (cluster mode) | Horizontal only (sharding) |
| Best for | Session store, leaderboards, complex object caching, pub/sub | Simple key-value caching, very high throughput multi-threaded |

## Caching Strategies

| Strategy | Description | Trade-off |
|---|---|---|
| **Lazy Loading** | Load data into cache only on cache miss | Stale data possible; initial reads are slow |
| **Write-Through** | Update cache whenever DB is written | No stale data; wasted cache on rarely-read items |
| **TTL (Time To Live)** | Expire cache entries after a defined time | Prevents stale data; occasional cache miss |

## Common Use Cases

- **Session store** — store user session tokens; fast access, TTL-based expiry
- **Gaming leaderboards** — sorted sets in Redis for real-time ranking
- **Online shopping cart** — temporary cart data with fast read/write
- **Database query caching** — cache results of expensive SQL queries
- **Real-time analytics** — counters, rate limiting, sliding windows

---

## Key Points / Exam Tips

- **Trigger:** "in-memory cache," "reduce database load," "sub-millisecond latency" → **ElastiCache**
- **Trigger:** "session store, leaderboard, pub/sub, persistence, HA" → **Redis**
- **Trigger:** "simple caching, multi-threaded, no persistence needed" → **Memcached**
- **Trigger:** "cache for DynamoDB specifically" → **DAX** (not ElastiCache — see DAX notes)
- Redis supports **Multi-AZ with auto-failover**; Memcached does **not**
- Only Redis supports **backup and restore** — Memcached has no persistence
- ElastiCache **cannot** handle complex SQL joins — for that, use RDS/Aurora
- Exam trap: "ElastiCache for DynamoDB" — if source is DynamoDB, the purpose-built answer is **DAX**; if source is RDS/Aurora or you need advanced features, it's **ElastiCache**
