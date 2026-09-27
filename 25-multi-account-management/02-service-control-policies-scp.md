# Service Control Policies (SCP)

## What Are SCPs?

**Service Control Policies (SCPs)** are a type of AWS Organizations policy that set the **maximum permission boundary** for IAM users and roles in member accounts. SCPs do not grant permissions — they only restrict what is possible.

> Think of SCPs as a ceiling: an IAM policy can only grant what the SCP allows. Even if an IAM policy says "Allow all actions," the SCP determines the actual upper limit.

---

## How SCPs Work

- Applied at the **Root, OU, or individual account** level.
- **Effective permissions = IAM policy ∩ SCP** — both must allow the action.
- An **explicit Deny in any SCP always overrides** any Allow — Deny wins everywhere.
- If an SCP contains only Allow statements, any action **not explicitly allowed is implicitly denied**.
- **SCPs do NOT affect the management account** — only member accounts.
- **SCPs affect ALL IAM principals in member accounts, including the root user** of that account.
- **SCPs do NOT affect service-linked roles** (exempted so that core AWS service automation isn't broken).

---

## Default Behavior

- By default, AWS attaches **FullAWSAccess** at the Root level — this allows all actions unless restricted.
- SCPs do NOT add permissions; they only restrict.

```json
{
  "Effect": "Allow",
  "Action": "*",
  "Resource": "*"
}
```

---

## Whitelist vs Blacklist Approaches

### Blacklist (Deny-based) — More Common
- Keep the default **FullAWSAccess** at root.
- Add **Deny** SCPs at the OU or account level to block specific services or actions.
- Everything not explicitly denied is allowed (from the SCP perspective — IAM still restricts).

```json
{
  "Effect": "Deny",
  "Action": "ec2:RunInstances",
  "Resource": "arn:aws:ec2:*:*:instance/*",
  "Condition": {
    "StringNotEquals": {
      "ec2:InstanceType": "t2.micro"
    }
  }
}
```
This denies launching any EC2 instance that is NOT a t2.micro — effectively restricting all member accounts in this OU to only t2.micro.

### Whitelist (Allow-based) — Less Common
- Remove **FullAWSAccess** from root.
- Add explicit **Allow** SCPs for only the services/actions you want to permit.
- Stricter but more operationally intensive to maintain.

---

## SCP Inheritance

- SCPs applied at a parent OU are **inherited by all child OUs and accounts**.
- An account's effective SCP = intersection of all SCPs in the chain from Root → OU → account.
- If no SCP is attached at a level, permissions are inherited from the parent.

---

## Common SCP Use Cases

| Use Case | SCP Example |
|---|---|
| Restrict to approved Regions | Deny all actions unless `aws:RequestedRegion` equals approved regions |
| Prevent unencrypted S3 uploads | Deny `s3:PutObject` unless `s3:x-amz-server-side-encryption` is set |
| Restrict instance types in dev | Deny `ec2:RunInstances` for anything not t2.micro or t3.micro |
| Block disabling CloudTrail | Deny `cloudtrail:StopLogging`, `cloudtrail:DeleteTrail` |
| Prevent IAM user creation | Deny `iam:CreateUser` — force IAM Identity Center usage |
| Restrict to specific services | Allow only S3, EC2, RDS (whitelist approach) |

---

## SCP vs IAM Policy vs Permission Boundary

| Tool | Scope | Who It Affects | Can Override IAM? |
|---|---|---|---|
| **SCP** | Account-level maximum | All IAM users, roles, AND root user in member accounts | Yes — sets ceiling regardless of IAM policies |
| **IAM Policy** | User/role-level permissions | Specific user or role | No — subject to SCP ceiling |
| **Permission Boundary** | User/role-level maximum | Specific IAM user or role | No — sets ceiling for that user/role, not org-wide |

- SCP + IAM policy: **both must allow** for the action to succeed.
- Permission Boundary + IAM policy: **both must allow** for the action to succeed.
- SCP cannot be bypassed from inside the account — even by root.

---

## Key Points / Exam Tips

- SCPs do **not grant** permissions — they only restrict (set the ceiling).
- Management account is **SCP-exempt** — never apply SCPs expecting them to restrict the management account.
- SCPs affect **root user of member accounts** — this is a powerful security control.
- **Service-linked roles are exempt** from SCPs — deliberate design to prevent breaking core AWS automation.
- **Explicit Deny wins** in SCP evaluation — same as IAM policy evaluation.
- SCPs require AWS Organizations to exist — if the question doesn't mention Organizations, SCPs aren't applicable.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Prevent even root from changing X in member accounts" | SCP |
| "Restrict all accounts to specific AWS regions" | SCP Deny with aws:RequestedRegion condition |
| "Enforce instance type restrictions in dev OU" | SCP Deny RunInstances |
| "Guardrail for all accounts in an OU" | SCP applied at OU level |
| "Cap maximum permissions, even if IAM allows more" | SCP (at org level) or Permission Boundary (at user/role level) |
