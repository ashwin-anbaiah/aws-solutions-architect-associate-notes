# IAM Policies and Policy Types

## What Is an IAM Policy?

An **IAM Policy** is a JSON document that defines permissions — what actions are allowed or denied on what resources, and under what conditions. Policies are the core mechanism by which IAM controls access.

---

## Policy Document Structure

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ReadAccess",
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::my-bucket",
        "arn:aws:s3:::my-bucket/*"
      ],
      "Condition": {
        "StringEquals": {"aws:RequestedRegion": "ap-south-1"}
      }
    }
  ]
}
```

### Policy Components

| Element | Required? | Description |
|---|---|---|
| **Version** | Yes | Always `"2012-10-17"` for modern policies |
| **Statement** | Yes | Array of permission rules |
| **Sid** | No | Statement ID — a label for documentation |
| **Effect** | Yes | `"Allow"` or `"Deny"` |
| **Principal** | Resource-based only | Who this applies to (user, role, account, service) |
| **Action** | Yes | AWS API actions (e.g., `s3:GetObject`, `ec2:*`) |
| **Resource** | Yes | The ARN of the resource(s). Use `*` for all. |
| **Condition** | No | Optional fine-grained conditions |

---

## IAM Policy Types

### 1. Identity-Based Policies

Attached to **IAM users, groups, or roles** — defines what that identity can do.

- **No `Principal` element** — the identity is implied by what the policy is attached to
- Two subtypes:
  - **Managed Policies** (reusable, can be attached to multiple identities)
    - **AWS Managed**: created and maintained by AWS (e.g., `AmazonS3ReadOnlyAccess`)
    - **Customer Managed**: created by you, more flexible, version-controlled
  - **Inline Policies** (embedded directly in a single user/role/group — cannot be reused)

### 2. Resource-Based Policies

Attached **directly to a resource** (S3 bucket, SQS queue, KMS key, etc.) — defines who can access the resource.

- **Includes a `Principal` element** — specifies which identity is being granted access
- Key use case: **cross-account access** — a resource-based policy can grant access to principals in other AWS accounts
- Example services that support resource-based policies: S3, KMS, SQS, SNS, Lambda, ECR

```
Identity-Based Policy          Resource-Based Policy
(attached to IAM User/Role)    (attached to S3 bucket)
      |                               |
   "I can access..."            "You can access me..."
```

### 3. Permissions Boundary

(Covered in detail in a separate file) — sets the *maximum* permissions a user or role can have, regardless of what their identity-based policies say.

---

## Policy Evaluation Logic

When a request comes in, AWS evaluates all applicable policies together:

1. **Explicit Deny** — if ANY policy says Deny, the request is denied immediately
2. **Explicit Allow** — if at least one policy says Allow (and no Deny), request is allowed
3. **Implicit Deny** — if nothing says Allow, request is denied by default

```
Request → Check all policies → Any Explicit Deny? YES → DENY
                            → No Deny + Any Allow?   → ALLOW
                            → No Deny + No Allow?     → DENY (implicit)
```

**Critical rule: Explicit Deny always wins over any Allow.**

---

## Cross-Account Access Requires Both Sides

For a user in Account A to access an S3 bucket in Account B:
1. Account A user's identity-based policy must allow the S3 action
2. Account B's S3 bucket policy (resource-based) must explicitly allow Account A

**Just an identity policy in Account A is not enough.** Both sides must agree.

---

## Versioning of Managed Policies

- When you modify a customer-managed policy, AWS creates a new **version**
- You can have up to **5 versions** of a managed policy
- You can set any version as the **default** (active) version
- Roll back to a previous version at any time — useful for recovering from accidental policy changes

---

## Common Policy Examples

```json
// Allow user to only launch EC2 in Mumbai
{
  "Effect": "Allow",
  "Action": "ec2:RunInstances",
  "Resource": "*",
  "Condition": {"StringEquals": {"aws:RequestedRegion": "ap-south-1"}}
}

// Allow user to access only their own S3 folder
{
  "Effect": "Allow",
  "Action": "s3:*",
  "Resource": [
    "arn:aws:s3:::company-data/${aws:username}",
    "arn:aws:s3:::company-data/${aws:username}/*"
  ]
}
```

---

## Key Points / Exam Tips

- **Inline policies** cannot be reused; **managed policies** can be shared across identities
- **Resource-based policies** are the way to enable cross-account access — identity-based policies alone cannot do this
- **Explicit Deny always wins** — even if another policy allows, one deny is enough to block
- **Implicit Deny by default** — a new identity with no policies can do nothing
- **Policy versioning** on managed policies allows up to 5 versions; supports rollback

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Cross-account S3 access" | Bucket policy (resource-based) required on BOTH sides |
| "Deny always wins" | Explicit Deny overrides all Allows |
| "New user can't do anything" | Implicit Deny — no policies attached yet |
| "Reuse a policy across many roles" | Customer managed policy |
| "Policy embedded in one user" | Inline policy |
| "S3 bucket controls who can access it" | Resource-based policy (bucket policy) |
