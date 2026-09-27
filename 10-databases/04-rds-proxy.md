# RDS Proxy

## What Is RDS Proxy?

- **RDS Proxy** — fully managed database proxy that sits between your application and an RDS or Aurora database.
- Manages and **pools database connections** to reduce the overhead of many short-lived connections on the database.
- Particularly valuable for **serverless** and **modern microservices** architectures where many Lambda functions or containers open and close connections rapidly.

## Why Use a Database Proxy?

- Serverless apps (Lambda, ECS) create many short-lived connections — each connection requires DB CPU/memory overhead.
- Without a proxy: thousands of concurrent Lambda invocations can **exhaust database connection limits**, causing errors.
- With RDS Proxy: connections are pooled and reused — the DB sees a small number of long-lived connections from the proxy.

## Key Features

- **Connection pooling** — multiple application connections share a smaller pool of long-lived DB connections.
- **Reduced failover time** — reduces RDS and Aurora failover time by up to **66%** (proxy maintains connections during failover, reconnects transparently).
- **Zero application code changes** — most apps can use RDS Proxy with just an endpoint swap.
- **Security** — stores database credentials in **AWS Secrets Manager**; supports optional IAM token authentication.
- **VPC only** — RDS Proxy is **never publicly accessible**; always lives inside a VPC.
- **Multi-AZ** — highly available; automatically routes connections to the standby if primary fails.

## Architecture

```
Application (Lambda / ECS Tasks)
        ↓  (many short-lived connections)
    RDS Proxy  ← credentials in Secrets Manager
        ↓  (small pool of long-lived connections)
    RDS / Aurora DB
```

## Supported Engines

- MySQL, PostgreSQL, MariaDB (via RDS and Aurora)
- **Not supported** for Oracle or SQL Server RDS Proxy natively.

---

## Key Points / Exam Tips

- **Trigger:** "Lambda connecting to RDS," "many short-lived DB connections," "connection exhaustion" → **RDS Proxy**
- **Trigger:** "reduce failover time for RDS" → **RDS Proxy** (up to 66% faster failover)
- **Trigger:** "serverless app with RDS database" → always consider RDS Proxy
- RDS Proxy is **not publicly accessible** — it lives inside the VPC only
- Credentials are stored in **Secrets Manager** — not hardcoded or in environment variables
- RDS Proxy supports **IAM authentication** for connecting applications (using signed IAM token)
- Primarily benefits **Lambda** and **ECS/container-based** workloads where connection counts are unpredictable
