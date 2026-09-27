# IAM Overview

## What Is IAM?

**AWS Identity and Access Management (IAM)** is AWS's centralized authentication and authorization system. It controls *who* can access AWS (authentication) and *what* they can do (authorization).

IAM is a **global service** — it doesn't belong to any specific Region. Users, roles, and policies you create are available across all Regions.

IAM is **free** — there's no additional charge for creating users, roles, or policies.

---

## The Core IAM Concepts

```
IAM
├── Identities (who can act)
│   ├── Root User       — account owner, unrestricted access
│   ├── IAM Users       — individual people or service accounts
│   ├── IAM Groups      — collections of users sharing the same permissions
│   └── IAM Roles       — temporary identities assumed by services, users, or apps
│
└── Policies (what they can do)
    ├── Identity-based  — attached to users, groups, or roles
    ├── Resource-based  — attached to resources like S3 buckets
    └── Permissions Boundaries — sets a cap on what an identity can ever do
```

---

## How AWS Authorization Works

Every API call to AWS goes through IAM. The evaluation order:

1. **Explicit Deny** — if any policy explicitly denies the action, access is denied immediately (overrides everything)
2. **Explicit Allow** — if a policy explicitly allows the action and no deny exists, access is granted
3. **Implicit Deny** — if no policy says anything about this action, access is denied by default

**The golden rule: Deny always wins over Allow.**

---

## Three Ways to Access AWS

| Method | Used For | Authentication |
|---|---|---|
| **AWS Management Console** | Human users, browser-based | Username + Password (+ MFA) |
| **AWS CLI** | Developers, scripts, automation | Access Key ID + Secret Access Key |
| **AWS SDK** | Applications, programmatic access | Access Key ID + Secret Access Key (or IAM Role) |

All three methods go through the same IAM authorization system — the policy attached to the identity is what determines what they can and can't do.

---

## What IAM Is NOT

- IAM does not manage **application-level authentication** (e.g., your app's user login system — use Cognito for that)
- IAM does not manage **network-level access control** (that's Security Groups and NACLs)
- IAM policies do **not restrict the root user** — the root user bypasses all IAM policies

---

## IAM vs AWS Organizations

| | IAM | AWS Organizations |
|---|---|---|
| Scope | Single account | Multiple accounts |
| Tool | Policies on users/roles | Service Control Policies (SCPs) |
| Can restrict root user? | No | Yes (SCPs apply to member account root users) |

---

## Key Points / Exam Tips

- IAM is **global** — not tied to any Region
- IAM is **free**
- **Explicit Deny always wins** — if any policy denies an action, it's denied regardless of any allows
- **Implicit Deny by default** — without an explicit Allow, everything is denied
- IAM controls access to **AWS resources and APIs**, not application-level user authentication
- **Root user is exempt from IAM policies** — the only way to restrict a root user is via AWS Organizations SCPs

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Control who can access AWS services" | IAM |
| "Deny always wins" | IAM policy evaluation logic |
| "Global identity service" | IAM (no Region selection needed) |
| "Restrict root user of a member account" | AWS Organizations SCP |
| "Application-level user login (Google, Facebook)" | Amazon Cognito (not IAM) |
