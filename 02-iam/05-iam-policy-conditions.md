# IAM Policy Conditions

## What Are Conditions?

**Conditions** are the "when" clause in IAM policies. They let you fine-tune access control beyond just *who* and *what* — you can say "allow this action, but only when these specific circumstances are true."

Without conditions, a policy either always allows or always denies. With conditions, you can write nuanced rules like:
- Allow only if the request comes from the corporate office IP range
- Allow only if the user has MFA enabled
- Allow only in a specific Region
- Allow only during business hours

---

## Condition Structure

```json
"Condition": {
  "ConditionOperator": {
    "ConditionKey": "ConditionValue"
  }
}
```

Multiple conditions in the same statement use **AND** logic (all must be true).
Multiple values for the same key use **OR** logic (any one can match).

---

## Condition Operators

| Operator | Use For | Example |
|---|---|---|
| `StringEquals` / `StringNotEquals` | Exact string match / exclusion | Region name, username |
| `StringLike` / `StringNotLike` | Wildcard string match | ARN prefix patterns |
| `Bool` | True/false conditions | MFA present: true |
| `IpAddress` / `NotIpAddress` | IP address or CIDR range | Source IP |
| `DateGreaterThan` / `DateLessThan` | Time-based | Business hours |
| `ArnEquals` / `ArnLike` | ARN matching | Specific resource ARNs |
| `NumericEquals` / `NumericLessThan` | Numeric comparison | Instance count |

---

## Common Condition Keys

| Condition Key | What It Checks | Example Use |
|---|---|---|
| `aws:RequestedRegion` | The AWS Region the API call targets | Restrict actions to a specific Region |
| `aws:SourceIp` | The IP address of the caller making the API request | Restrict to corporate network |
| `aws:MultiFactorAuthPresent` | Whether the caller authenticated with MFA | Require MFA for sensitive actions |
| `aws:username` | The IAM username | Give each user access to their own S3 folder |
| `aws:CurrentTime` | The current UTC time | Allow only during business hours |
| `aws:PrincipalTag` | Tags on the IAM identity | Attribute-based access control |
| `ec2:ResourceTag` / `s3:prefix` | Service-specific resource attributes | Target specific tagged resources |

**Important note about `aws:SourceIp`**: This is the IP of the *caller making the API request* — NOT the IP of any resource being created or managed. Common trap: people assume it restricts the EC2 instance's IP when actually it restricts where the API call comes from.

---

## Practical Examples

### 1. Allow EC2 Launch Only in Mumbai Region

```json
{
  "Effect": "Allow",
  "Action": "ec2:RunInstances",
  "Resource": "*",
  "Condition": {
    "StringEquals": {"aws:RequestedRegion": "ap-south-1"}
  }
}
```

### 2. Require MFA for Sensitive S3 Operations

```json
{
  "Effect": "Allow",
  "Action": ["s3:DeleteObject", "s3:PutBucketPolicy"],
  "Resource": "arn:aws:s3:::sensitive-bucket/*",
  "Condition": {
    "Bool": {"aws:MultiFactorAuthPresent": "true"}
  }
}
```

### 3. Deny All Actions from Outside Corporate Network

```json
{
  "Effect": "Deny",
  "Action": "*",
  "Resource": "*",
  "Condition": {
    "NotIpAddress": {
      "aws:SourceIp": ["203.55.22.0/24", "10.0.0.0/8"]
    }
  }
}
```

### 4. Allow User to Access Only Their Own S3 Folder

```json
{
  "Effect": "Allow",
  "Action": "s3:*",
  "Resource": [
    "arn:aws:s3:::company-data/${aws:username}",
    "arn:aws:s3:::company-data/${aws:username}/*"
  ]
}
```

This uses a **policy variable** (`${aws:username}`) — AWS dynamically substitutes the actual username at evaluation time.

### 5. Allow EC2 Actions Only During Business Hours

```json
{
  "Effect": "Allow",
  "Action": ["ec2:StartInstances", "ec2:StopInstances"],
  "Resource": "*",
  "Condition": {
    "DateGreaterThan": {"aws:CurrentTime": "2025-01-01T09:00:00Z"},
    "DateLessThan": {"aws:CurrentTime": "2025-12-31T18:00:00Z"}
  }
}
```

---

## CIDR Notation Quick Reference (for IP conditions)

| CIDR | Meaning |
|---|---|
| `/32` | Exactly one IP address |
| `/24` | 256 IP addresses (e.g., `192.168.1.0/24`) |
| `/16` | 65,536 IP addresses |
| `/0` | All IP addresses (entire internet) |

Smaller number = bigger range. `/0` = everything; `/32` = just one.

---

## Key Points / Exam Tips

- `aws:SourceIp` = IP of the *requester* (who's calling the API), not the resource's IP
- Multiple conditions = AND (all must be true)
- Multiple values in one condition key = OR (any one can match)
- Policy variables like `${aws:username}` let you write self-referential, per-user policies
- **`StringEquals` vs `StringLike`**: `StringEquals` is exact match; `StringLike` supports `*` and `?` wildcards

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Allow only from corporate office" | `aws:SourceIp` + `IpAddress` condition |
| "Require MFA for sensitive actions" | `aws:MultiFactorAuthPresent` = `true` |
| "Restrict to a specific Region" | `aws:RequestedRegion` + `StringEquals` |
| "Each user accesses only their own data" | `${aws:username}` policy variable |
| "Allow only during business hours" | `aws:CurrentTime` with `DateGreaterThan/DateLessThan` |
| "SourceIP is about the caller, not the resource" | `aws:SourceIp` = caller's IP only |
