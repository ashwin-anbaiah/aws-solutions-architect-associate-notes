# SSM Parameter Store

## What is SSM Parameter Store?

**AWS Systems Manager Parameter Store** is a centralized, secure service for storing **configuration data** and **secrets** as key-value pairs that applications and services can retrieve at runtime.

> "Parameter Store is a configuration database — store your app settings, DB endpoints, and secrets in one place, protected by IAM."

## Parameter Types

| Type | Description | Encryption |
|---|---|---|
| **String** | Plain text value | None |
| **StringList** | Comma-separated list of plain text values | None |
| **SecureString** | Encrypted value using AWS KMS | Yes (KMS key) |

- **SecureString** is the type to use for passwords, API keys, connection strings
- Uses either the **AWS-managed key** (`aws/ssm`) or a **customer-managed KMS key**

## Parameter Tiers

| Tier | Standard | Advanced |
|---|---|---|
| Max size | 4 KB | 8 KB |
| Parameter policies | No | Yes (expiration, notification) |
| Cost | Free | $0.05/parameter/month |
| Throughput | Standard | Higher |

**Parameter Policies** (Advanced tier only):
- **Expiration** — auto-delete parameter after a date
- **ExpirationNotification** — send EventBridge event before parameter expires
- **NoChangeNotification** — notify if parameter hasn't changed in N days

## Hierarchical Storage

Parameters are stored in a **hierarchical path** structure (like a file system):

```
/AppA/dev/database/password
/AppA/dev/database/host
/AppA/prod/database/password
/AppA/prod/database/host
/shared/api-key
```

**Benefits of hierarchy:**
- Retrieve all parameters for an environment in one API call: `GetParametersByPath /AppA/dev/`
- Apply IAM policies at path level (e.g., allow Lambda only access to `/AppA/dev/`)
- Clear logical separation of environments and applications

## Versioning

- Every update to a parameter creates a new **version**
- You can retrieve the **current version** or any **historical version**
- Supports rollback by referencing a specific version number

## Accessing Parameters

**AWS CLI:**
```bash
# Get a single parameter
aws ssm get-parameter --name "/AppA/dev/db-password" --with-decryption

# Get all parameters under a path
aws ssm get-parameters-by-path --path "/AppA/dev/" --with-decryption
```

**From Lambda (Python):**
```python
import boto3
ssm = boto3.client('ssm')
response = ssm.get_parameter(Name='/AppA/dev/db-password', WithDecryption=True)
value = response['Parameter']['Value']
```

**From EC2 / ECS / CloudFormation** — all natively supported.

## Parameter Store vs Secrets Manager

| Feature | Parameter Store | Secrets Manager |
|---|---|---|
| Cost | Free (standard tier) | $0.40/secret/month |
| Encryption | Optional (SecureString) | Always encrypted |
| Auto-rotation | No (manual or Lambda) | Yes (built-in rotation) |
| Max size | 4 KB (8 KB advanced) | 64 KB |
| Cross-region replication | No | Yes (multi-region secrets) |
| Best for | Config values, non-secret params | Database passwords, API keys needing rotation |
| Integration | Direct SSM integration | Works with RDS, Redshift, DocumentDB out of box |

> Rule of thumb: Use **Secrets Manager** for secrets that need **auto-rotation**. Use **Parameter Store** for general configuration.

## Key Points / Exam Tips

- Parameter Store is **free** for standard parameters — cost-effective for configuration
- Use **SecureString** for sensitive values (passwords, tokens) — encrypted with KMS
- **Hierarchical paths** allow grouping and IAM-level access control
- **Versioning** is automatic — every update is tracked
- **Advanced tier** adds parameter policies (expiration, notifications) and larger size
- IAM controls access — not just anyone with AWS access can read parameters
- Common pattern: Lambda reads DB password from Parameter Store at startup using `get-parameter`

## Trigger Words

| Keyword | Think |
|---|---|
| "Store app config values securely" | Parameter Store |
| "Encrypt database password with KMS" | Parameter Store SecureString |
| "Retrieve all dev environment configs" | Parameter Store hierarchy + `GetParametersByPath` |
| "Config without auto-rotation" | Parameter Store (vs Secrets Manager) |
| "Parameter expiration / TTL" | Advanced tier Parameter Policies |
