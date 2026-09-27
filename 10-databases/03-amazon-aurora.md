# Amazon Aurora

## What Is Aurora?

- **Amazon Aurora** — AWS-native relational database engine fully compatible with **MySQL** and **PostgreSQL**.
- Also supports **Aurora DSQL** (new, distributed SQL).
- **5x throughput** of MySQL and **3x throughput** of PostgreSQL at **1/10th the cost** of commercial databases (Oracle, SQL Server).
- Decoupled compute and storage architecture — shared distributed storage volume across 3 AZs.

## Aurora Architecture

- **DB Cluster** = Primary (writer) instance + up to 15 read replicas (readers).
- **Shared Storage Volume** — 6 copies of data across 3 AZs; auto-scales in 10 GB increments up to 128 TB.
- Automatic failover using replicas in under **30 seconds** (vs 60–120 sec for standard RDS).
- Reader endpoint load-balances read traffic across all available replicas.

## Aurora vs RDS Comparison

| Feature | Amazon RDS | Amazon Aurora |
|---|---|---|
| DB Engine | MySQL, PostgreSQL, MariaDB, SQL Server, Oracle, DB2 | MySQL, PostgreSQL, DSQL |
| Architecture | Coupled compute + storage | Decoupled compute and storage |
| Read Replicas | Up to 15 (5 for Oracle) | Up to 15 |
| Max Storage | 64 TB | 128 TB (auto-scaled) |
| HA Failover Time | 60–120 seconds | < 30 seconds |
| Cost | Lower | Higher (better performance) |
| Extra Features | Automated backup, Multi-AZ, Read Replicas | + Global Database, Serverless, Cloning, ML integration |

## Aurora Global Database

- Spans **multiple AWS regions**.
- Data written to Primary region; replicated to secondary regions within **1 second**.
- Use cases:
  - Low-latency reads for a globally distributed application
  - Fast disaster recovery: promote a secondary region's replica to primary

## Aurora Serverless

- No need to provision database capacity for peak loads.
- Specify a **capacity range**; Aurora automatically starts, stops, and scales compute.
- **Pay per second** for capacity used.
- Supports all Aurora features (Global Database, Multi-AZ, Read Replicas).
- Use cases: development/test environments, websites with intermittent/unpredictable traffic, business-critical apps needing scale.

## Aurora Cloning

- Creates a full copy of a production database **in minutes** without copying all data initially.
- Uses **copy-on-write** technology — only blocks that change are duplicated.
- Faster and more cost-efficient than snapshot + restore.
- Can create up to **15 clones** from the same source cluster.
- Ideal for creating test/dev databases from production.

## Aurora Machine Learning

- Call AWS ML services **directly from SQL queries** inside Aurora — no separate application-level ML code.
- Integrates with:
  - **Amazon SageMaker AI** — custom ML model inference
  - **Amazon Comprehend** — NLP and sentiment analysis
  - **Amazon Bedrock** — Generative AI platform
- Data stays in Aurora; only the necessary fields are sent to ML services.
- Use cases: fraud detection, sentiment analysis, churn prediction, personalized recommendations.

---

## Key Points / Exam Tips

- **Trigger:** "MySQL/PostgreSQL-compatible, better performance than RDS" → **Aurora**
- **Trigger:** "active-active across regions, write to any region" → **DynamoDB Global Tables** (NOT Aurora Global DB — Aurora only has read replicas in secondary regions)
- **Trigger:** "low-latency reads in multiple regions, fast DR" → **Aurora Global Database**
- **Trigger:** "create full copy of production DB in minutes" → **Aurora Cloning** (copy-on-write)
- **Trigger:** "relational DB with no capacity planning, auto scales" → **Aurora Serverless**
- Aurora replicas share the **same storage volume** — no separate copy to sync, giving near-zero replication lag
- Aurora Multi-AZ uses **Aurora Replicas** (readable); standard RDS Multi-AZ uses a **passive standby** (not readable)
- Aurora Global Database secondary regions have **read-only** replicas — not writable in normal operation
