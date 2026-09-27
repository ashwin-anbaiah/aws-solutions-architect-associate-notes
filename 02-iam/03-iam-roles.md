# IAM Roles

## What Is an IAM Role?

An **IAM Role** is an AWS identity that can be *assumed temporarily* by users, applications, or AWS services. Instead of carrying long-term credentials (like access keys), a role provides **short-lived, automatically-rotating temporary credentials** via AWS Security Token Service (STS).

Think of it like a badge-access pass: you check one out when you need it, use it for a limited time, and return it. The pass is tied to specific permissions, and it expires automatically.

---

## Why Roles Instead of Users for Services?

When an EC2 instance needs to access S3, the naive approach is to put access keys on the instance. This is a security anti-pattern because:
- Keys are long-term and don't expire
- Keys can be accidentally exposed (pushed to GitHub, leaked in logs)
- Rotating keys on instances is operationally painful

With a **role attached to the EC2 instance**:
- Credentials are short-lived (auto-rotated every ~1 hour by default)
- The instance gets credentials from the metadata service automatically
- No keys are stored on disk — nothing to leak

```
EC2 instance → assumes IAM Role → gets temporary creds from STS
             → uses creds to call S3 → creds expire → auto-renewed
```

---

## Who Can Assume an IAM Role?

| Assumed By | Use Case |
|---|---|
| **AWS service** (EC2, Lambda, ECS) | Service accessing other AWS resources (most common) |
| **IAM user in the same account** | Temporarily elevating permissions |
| **IAM user in a different account** | Cross-account access |
| **External identity (Google, Facebook, SAML IdP)** | Federated access / SSO |

---

## Temporary Credentials from STS

When a role is assumed, AWS STS issues three pieces:
1. **Access Key ID** — like a username
2. **Secret Access Key** — like a password
3. **Session Token** — proves the credentials are temporary and not long-term keys

All three expire together. The application must refresh them before expiry (AWS SDKs handle this automatically when using instance profiles/roles).

---

## Role Types You Need to Know

| Type | Description |
|---|---|
| **Service Role** | A role for an AWS service (EC2, Lambda, RDS) to call other services |
| **Service-Linked Role** | A pre-built role created by AWS for a specific service (e.g., Auto Scaling); permissions are predefined, not editable |
| **Cross-Account Role** | Allows an identity in Account A to access resources in Account B via `sts:AssumeRole` |
| **Federated Role** | For external users authenticated by an identity provider (Google, Okta, AD) |

---

## How Roles Work — The Trust Policy

Every IAM role has two parts:
1. **Trust Policy** (who can assume the role) — specifies the `Principal`
2. **Permission Policy** (what the role can do) — specifies the allowed actions

```json
// Trust Policy — allows EC2 to assume this role
{
  "Statement": [{
    "Effect": "Allow",
    "Principal": {"Service": "ec2.amazonaws.com"},
    "Action": "sts:AssumeRole"
  }]
}
```

```json
// Permission Policy — what the role can do
{
  "Statement": [{
    "Effect": "Allow",
    "Action": ["s3:GetObject", "s3:PutObject"],
    "Resource": "arn:aws:s3:::my-bucket/*"
  }]
}
```

---

## Cross-Account Access Pattern

```
Account A (Developer)                    Account B (Production)
├── IAM User (Alice)         ─────────▶  ├── IAM Role (ProdReadOnly)
│   └── Policy: Allow                    │   ├── Trust Policy: Allow Account A
│         sts:AssumeRole                 │   └── Permission: s3:GetObject on prod-bucket
│         for ProdReadOnly role
```

Alice in Account A assumes the role in Account B, gets temporary credentials scoped to `s3:GetObject` on prod-bucket — without any long-term credentials being shared across accounts.

---

## Key Points / Exam Tips

- **Roles provide short-lived credentials** — auto-rotated, not stored on disk
- **Best practice for EC2/Lambda/ECS**: always use IAM roles, never embed access keys in instances or code
- **Service-linked roles**: predefined by AWS, permissions are not editable
- **Cross-account access**: always done via `sts:AssumeRole`, never by sharing IAM user credentials
- **Trust policy**: the "who can assume this role" half of the role — must allow the right principal
- **IAM role credentials = Access Key + Secret Key + Session Token** (all three are required)

## Trigger Words

| Exam phrase | Think |
|---|---|
| "EC2 accessing S3 securely" | IAM Role for EC2 (not access keys on the instance) |
| "Lambda needs DynamoDB access" | IAM Role for Lambda |
| "Cross-account access" | IAM Role + sts:AssumeRole |
| "Short-lived credentials" | STS + IAM Role |
| "External users logging in via Google/AD" | Federated Role |
| "Auto Scaling needs to launch instances" | Service-linked role (pre-built by AWS) |
| "Static long-term credentials on an instance" | Security anti-pattern — use IAM roles instead |
