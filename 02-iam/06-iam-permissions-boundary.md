# IAM Permissions Boundary

## What Is a Permissions Boundary?

A **Permissions Boundary** is a managed IAM policy that sets the *maximum permissions* an IAM user or role can ever have — regardless of what other identity-based policies are attached to them.

Think of it as a ceiling or guardrail: even if someone attaches an `AdministratorAccess` policy to a user, if their permissions boundary only allows S3 and EC2, they still can only use S3 and EC2.

**Key insight**: A permissions boundary does NOT grant permissions by itself. It only limits what identity-based policies can grant. The effective permission is always the *intersection* of:
- What the identity-based policy allows
- What the permissions boundary allows

```
Identity-Based Policy:  S3 full, EC2 full, IAM full, RDS full
Permissions Boundary:   S3 full, EC2 full
─────────────────────────────────────────────────
Effective Permissions:  S3 full, EC2 full  ← intersection only
```

---

## Who Can Have a Permissions Boundary?

**Only IAM users and IAM roles.** Permissions boundaries **cannot be attached to IAM groups.**

This is a common exam trap — groups do not support permissions boundaries.

---

## Why Use Permissions Boundaries?

### Use Case 1: Developer Self-Service with Guardrails

You want to let developers create their own IAM roles (for their Lambda functions, EC2 instances, etc.) without being able to escalate their own privileges.

Solution:
1. Attach a permissions boundary to the developers' IAM users
2. Also restrict their `iam:CreateRole` permission to only create roles that also have a specific permissions boundary attached

Now developers can self-manage roles, but every role they create is capped at what the boundary allows — they can't create an admin role and then assume it.

### Use Case 2: Delegating Limited IAM Administration

A senior developer manages a team and needs to create IAM users for new hires. You don't want to give them full IAM admin access. Use a permissions boundary to cap what they can do: they can create users, but only users with the same (or narrower) boundary.

---

## Permissions Boundary vs SCP vs Regular IAM Policy

| | Scope | What It Does | Restricts Root? |
|---|---|---|---|
| **Regular IAM Policy** | Single identity | Grants specific permissions | No |
| **Permissions Boundary** | Single user or role | Caps the maximum permissions | No |
| **Service Control Policy (SCP)** | All identities in an AWS account (via Organizations) | Caps the maximum permissions for the entire account | Yes (member account root) |

**Decision rule**:
- Restrict every identity in an account, including root → **SCP** (requires AWS Organizations)
- Restrict one specific IAM user or role, even if they self-attach new policies → **Permissions Boundary**
- Just grant someone specific permissions → **Regular IAM Policy**

---

## Real-World Example

```json
// Permissions Boundary Policy
// This user can never do more than EC2 actions, no matter what policies are attached
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": ["ec2:*"],
    "Resource": "*"
  }]
}
```

Even if this user's identity-based policies include `s3:*`, `rds:*`, and `iam:*`, the boundary limits them to only `ec2:*` actions.

---

## Key Points / Exam Tips

- Permissions boundaries **cap the maximum** — they don't grant anything by themselves
- Effective permissions = intersection of identity-based policy AND boundary
- Boundaries can only be attached to **users and roles, not groups**
- If either the policy or boundary denies something, it's denied — both must allow for access to be granted
- Boundaries are a safeguard against privilege escalation when delegating IAM administration

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Cap what a single IAM user/role can do" | Permissions Boundary |
| "Prevent privilege escalation by developers" | Permissions Boundary |
| "Restrict every account in the organization, including root" | SCP (Organizations) |
| "Boundary doesn't grant permissions" | Correct — it only limits, never adds |
| "Developer can create IAM roles but not escalate" | Permissions boundary on their user + restrict CreateRole to only boundary-attached roles |
| "Can I attach a boundary to a group?" | No — only users and roles |
