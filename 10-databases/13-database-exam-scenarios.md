# Database Exam Scenarios

## Quick-Reference: Trigger Words → Service

| Trigger Words | Answer |
|---|---|
| "active-active writes across multiple regions, low latency" | **DynamoDB Global Tables** |
| "read replicas across regions (read only)" | Aurora Global Database or RDS Read Replicas |
| "cache for DynamoDB, microsecond latency, no code change" | **DAX** |
| "cache for RDS/Aurora, or need pub/sub/leaderboards" | **ElastiCache (Redis)** |
| "graph database, social network, fraud, recommendations" | **Amazon Neptune** |
| "IoT metrics, timestamped data, time-series" | **Amazon Timestream** |
| "immutable audit log, cryptographically verifiable, tamper-proof" | **Amazon QLDB** |
| "MongoDB-compatible, JSON documents" | **Amazon DocumentDB** |
| "Apache Cassandra, CQL, migrate Cassandra" | **Amazon Keyspaces** |
| "petabyte-scale analytics, data warehouse, SQL, BI" | **Amazon Redshift** |
| "serverless NoSQL, millisecond latency, any scale" | **Amazon DynamoDB** |
| "managed relational DB, MySQL/PostgreSQL/Oracle/SQL Server" | **Amazon RDS** |
| "MySQL/PostgreSQL-compatible, 5x faster than RDS" | **Amazon Aurora** |
| "Lambda connecting to RDS, connection pooling, failover time reduction" | **RDS Proxy** |
| "encrypt existing unencrypted RDS DB" | Snapshot → copy as encrypted → restore new DB |
| "create full copy of production Aurora DB in minutes" | **Aurora Cloning** |
| "relational DB with no capacity planning, auto-scales compute" | **Aurora Serverless** |

---

## Scenario 1 — Active-Active Global Orders

**Scenario:** A global e-commerce company wants an active-active database across multiple regions so customers can place orders from anywhere with low latency.

**Key words:** active-active, multiple regions, write from anywhere, low latency

**Answer:** **DynamoDB Global Tables**

**Why not Aurora Global Database?** Placing orders = writes. Aurora Global Database supports read replicas in secondary regions — secondary regions are read-only in normal operation.

---

## Scenario 2 — Cache for DynamoDB vs RDS

**Scenario:** A ride-sharing app needs to cache frequently accessed data (driver locations) to reduce database load and achieve microsecond latency.

**Key words:** cache, microsecond latency

**Answer:** **DAX** (if source is DynamoDB) or **ElastiCache** (if source is RDS/Aurora or if the question mentions advanced features)

**Tiebreaker rule:** If both appear as options, look for additional context:
- Source database is DynamoDB → **DAX** (purpose-built, API-compatible, no code changes)
- Source is RDS/Aurora, OR need pub/sub / leaderboards / TTL per key / real-time messaging → **ElastiCache**

---

## Scenario 3 — Occasional Analytics on Large Volume Data

**Scenario:** A streaming platform wants to store large volumes of user activity logs but only needs them for occasional analytics queries.

**Key words:** occasional, analytics queries, reduce cost

**Answer:** **DynamoDB Standard-IA Table class** (if saving storage cost is the goal) OR **DynamoDB Export to S3 + Athena** (if the goal is running SQL analytics with tools like Athena)

---

## Scenario 4 — Graph Traversal / Shortest Path

**Scenario:** A ride-sharing platform wants to quickly determine the shortest path between drivers and riders based on a complex graph of roads.

**Key words:** shortest path, graph, connections, network

**Answer:** **Amazon Neptune**

---

## Scenario 5 — Database Copy in Minutes for Load Testing

**Scenario:** A team needs a full copy of their production Aurora database to run performance tests without affecting live systems. The copy must be created within minutes.

**Key words:** full copy of production, within minutes, no impact on live system

**Answer:** **Aurora Cloning**

**Why not Read Replicas?** Scenario doesn't say read-only testing; Aurora's copy-on-write cloning is faster than snapshot + restore.

---

## Scenario 6 — Multi-AZ RDS Replication Type

**Key fact:** RDS Multi-AZ standby uses **synchronous** replication and is **NOT readable**. RDS Read Replicas use **asynchronous** replication and ARE readable.

**Trap:** An answer claiming "synchronous read replicas" is wrong — RDS read replicas are always async.

---

## DAX vs ElastiCache — Decision Tree

```
Is the source database DynamoDB?
  ├── YES → DAX (purpose-built, API-compatible, auto cache sync)
  └── NO (RDS, Aurora, custom)
        OR
      Do you need pub/sub, leaderboards, TTL per key, multiple source systems?
            → ElastiCache (Redis)
```
