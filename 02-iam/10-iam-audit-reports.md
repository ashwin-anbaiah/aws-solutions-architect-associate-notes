# IAM Audit and Reports

## Why Audit IAM?

IAM is the security backbone of your AWS account. Over time, accounts accumulate unused users, stale access keys, roles with excessive permissions, and users without MFA. Regular auditing lets you maintain a tight security posture and satisfy compliance requirements.

---

## IAM Credentials Report

The **IAM Credentials Report** is a CSV file you can download from the IAM console that lists **all IAM users** in your account along with the status of their credentials.

### What It Contains (per user)

| Field | Description |
|---|---|
| User name | IAM username |
| ARN | User's ARN |
| User creation time | When the user was created |
| Password enabled | Whether console password login is active |
| Password last used | Last time the user logged into the console |
| Password last changed | When password was last changed |
| Password next rotation | When password expires (if rotation is configured) |
| MFA active | Whether MFA is enabled (true/false) |
| Access key 1 active | Whether access key 1 exists and is active |
| Access key 1 last rotated | When key 1 was last rotated |
| Access key 1 last used | When key 1 was last used |
| (same for access key 2) | |

### Use Cases

- **Compliance audits** — demonstrate that all users have MFA enabled, no unused credentials
- **Security reviews** — find users whose access keys haven't been rotated in 90+ days
- **Least privilege** — identify users who haven't logged in for months (might need deactivation)
- **SOC 2 / ISO 27001** — generate evidence that credential hygiene policies are followed

### How to Generate

- Console: IAM → Credential reports → Download Report
- CLI: `aws iam generate-credential-report` then `aws iam get-credential-report`

---

## IAM Access Analyzer Reports

(Covered in detail in the IAM Tools file.) In brief:

- **Unused access findings** — roles, users, and permissions that haven't been used recently
- **External access findings** — resources accessible from outside your zone of trust
- These can be exported/downloaded for compliance documentation

---

## CloudTrail for IAM Auditing

**AWS CloudTrail** logs every API call made in your account — including all IAM actions. This is separate from IAM's own reports but essential for security auditing.

Key IAM-related events logged by CloudTrail:
- `CreateUser`, `DeleteUser`, `AttachUserPolicy`, `DetachUserPolicy`
- `AssumeRole`, `CreateRole`, `DeleteRole`
- `ConsoleLogin` (including failed logins)
- `PutBucketPolicy` and other resource policy changes

CloudTrail gives you a **who did what and when** audit trail for every IAM action.

---

## Service Last Accessed Data

In the IAM console, for any user, group, role, or policy, you can view **Service Last Accessed** data:
- Shows which AWS services the identity has **permission to access** and when they **last accessed** each service
- Useful for identifying permissions that are granted but never used (right-sizing for least privilege)

This is different from the Credentials Report — it's per-identity and per-service rather than per-credential-type.

---

## Key Compliance Checkpoints

| Check | Tool |
|---|---|
| All users have MFA enabled | Credentials Report |
| No access keys older than 90 days | Credentials Report |
| No users inactive for 90+ days | Credentials Report (password last used + key last used) |
| No public S3 buckets or cross-account access | IAM Access Analyzer |
| No unused permissions | IAM Access Analyzer (Unused Access) / Service Last Accessed |
| Audit trail of all API actions | AWS CloudTrail |

---

## Key Points / Exam Tips

- **Credentials Report = account-wide snapshot** of all users and their credential status — good for compliance evidence
- **Service Last Accessed = per-identity, per-service** usage history — good for right-sizing permissions
- **CloudTrail = API audit log** for who did what and when — not an IAM-specific report, but essential for security forensics
- IAM Access Analyzer's unused access findings complement the Credentials Report for a full least-privilege audit

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Which users don't have MFA?" | IAM Credentials Report |
| "Find stale access keys across all users" | IAM Credentials Report |
| "Audit trail of all IAM actions" | AWS CloudTrail |
| "Which services did this role actually use?" | IAM Service Last Accessed data |
| "Compliance evidence for credential hygiene" | IAM Credentials Report |
