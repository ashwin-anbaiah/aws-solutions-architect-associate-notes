# AWS Secrets Manager vs Parameter Store

## AWS Secrets Manager

**AWS Secrets Manager** is a fully managed service for securely **storing, retrieving, and automatically rotating** credentials and secrets.

### Core Capabilities

- Store secrets: **passwords, API keys, OAuth tokens, database credentials, certificates, private keys**
- **Automatic rotation**: Built-in rotation for supported databases (RDS MySQL, PostgreSQL, Aurora, Redshift, DocumentDB); custom rotation via Lambda for everything else
- **KMS encryption**: All secrets encrypted at rest using KMS (AWS-managed or customer-managed key)
- **IAM access control**: Access to secrets controlled via IAM policies
- **No hardcoded credentials**: Applications retrieve secrets at runtime via API/SDK

### How Applications Access Secrets

```
EC2/Lambda App
    |
    +-- API call: GetSecretValue(SecretId="prod/db/password")
    |
    +-- Secrets Manager
            |-- Checks IAM permissions
            |-- Decrypts secret value (KMS)
            |-- Returns plaintext secret value
    |
    +-- App uses credential to connect to DB
```

### Automatic Rotation

- Secrets Manager rotates credentials on a **configurable schedule** (e.g., every 30 days)
- For supported databases (RDS, Aurora): Secrets Manager generates a new password, updates the database, and stores the new secret — **zero-downtime rotation**
- For custom services: Invoke a **Lambda function** that handles the rotation logic
- After rotation, old secret versions are retained for **7 days** (configurable) before deletion

### Multi-Region Secrets

- **Replicate secrets** across multiple AWS regions for low latency and redundancy
- Secret rotation happens in the **primary region only** — replicas automatically receive updated values
- You can **promote a replica to primary** during failover
- Use cases: Multi-region Lambda/API workloads, EKS/ECS clusters in multiple regions

### Pricing
- ~$0.40 per secret per month
- ~$0.05 per 10,000 API calls

---

## AWS Systems Manager Parameter Store

**AWS Systems Manager Parameter Store** is a secure, hierarchical storage service for **configuration data** and secrets (though primarily designed for configuration).

### Core Capabilities

- Store **strings, StringLists, and SecureStrings** (encrypted with KMS)
- Hierarchical key structure: `/myapp/prod/db-url`, `/myapp/dev/feature-flag`
- **No automatic rotation** (must implement manually)
- **Free tier available** (Standard parameters are free; Advanced parameters cost ~$0.05/10k API calls)
- Integrates with EC2, Lambda, ECS, CodePipeline, and other AWS services

### Parameter Types

| Type | Description |
|---|---|
| **String** | Plain text value |
| **StringList** | Comma-separated list |
| **SecureString** | Encrypted value using KMS |

### Tiers

| | Standard Tier | Advanced Tier |
|---|---|---|
| **Max value size** | 4 KB | 8 KB |
| **Parameter policies** | No | Yes (expiration, notification) |
| **Cost** | Free | $0.05/10k API calls |
| **Throughput** | 40 requests/sec | 1,000 requests/sec |

---

## Secrets Manager vs Parameter Store Comparison

| Feature | Secrets Manager | Parameter Store |
|---|---|---|
| **Primary purpose** | Secrets (passwords, keys, tokens) | Configuration (settings, flags, paths) |
| **Automatic rotation** | Yes (built-in + custom Lambda) | No |
| **Encryption** | Always encrypted (KMS) | Optional (SecureString type) |
| **Multi-region** | Yes (secret replication) | No |
| **Cost** | ~$0.40/secret/month | Free (standard) |
| **Versioning** | Yes | Yes |
| **Hierarchical** | No | Yes (path-based: `/app/env/key`) |
| **Integration** | RDS, Redshift, DocumentDB | EC2, Lambda, ECS, CodePipeline |
| **Best for** | DB passwords, API keys, OAuth tokens | App config, feature flags, non-sensitive settings |

---

## When to Use Which

**Use Secrets Manager when:**
- Storing database passwords or API keys that **must rotate automatically**
- Need **cross-region secret replication**
- Security compliance requires automatic credential rotation
- Storing sensitive credentials for RDS, Aurora, Redshift, DocumentDB

**Use Parameter Store when:**
- Storing **non-sensitive configuration** (feature flags, app version, endpoint URLs)
- Storing **SecureStrings** (encrypted config) at no cost
- Need a **hierarchical config store** organized by path
- Budget-constrained (Parameter Store is free for standard parameters)

---

## Key Points / Exam Tips

- **Secrets Manager = secrets + auto rotation + higher cost**
- **Parameter Store = configuration + cheaper + no auto rotation**
- Secrets Manager encrypts by default; Parameter Store encryption is **optional** (only SecureString type)
- Automatic rotation is the **key differentiator** — if the question mentions automatic credential rotation, the answer is Secrets Manager
- RDS Proxy can retrieve credentials from **Secrets Manager** directly — recommended for Lambda + RDS architectures
- Parameter Store supports **hierarchical organization** with path-based naming

## Trigger Words

- "Automatically rotate RDS password every 30 days" → Secrets Manager
- "Store database credentials securely without hardcoding" → Secrets Manager
- "Store application configuration and feature flags" → Parameter Store
- "Encrypted configuration parameter" → Parameter Store SecureString
- "Reduce database connections from Lambda" → RDS Proxy (uses Secrets Manager for credentials)
- "Multi-region secret replication" → Secrets Manager multi-region secrets
