# IAM Users and Groups

## IAM Users

An **IAM User** is a permanent identity within your AWS account representing a specific person or application. Unlike the root user, IAM users have restricted access defined entirely by the IAM policies attached to them.

### Two Types of Credentials

| Credential Type | Used For | Via |
|---|---|---|
| **Username + Password** | AWS Management Console (browser) | Console sign-in URL |
| **Access Key ID + Secret Access Key** | CLI, SDK, programmatic access | `aws configure` or environment variables |

One user can have both types simultaneously. A user can have **maximum 2 active access keys** at any time — useful for key rotation without downtime.

### Access Key Facts

- Access keys consist of: `Access Key ID` (like a username) + `Secret Access Key` (like a password)
- The Secret Access Key is shown only once at creation — store it securely immediately
- Access keys **cannot be re-generated** — if you lose the secret, delete and create a new pair
- Best practice: rotate access keys regularly; immediately disable/delete leaked keys

---

## Multi-Factor Authentication (MFA)

**MFA** adds a second layer of security on top of username/password. Even if a password is compromised, an attacker still needs the MFA device.

```
Login = Email/Username + Password + MFA Token → Successful Console Access
```

### MFA Device Types

| Type | Examples |
|---|---|
| **Virtual MFA app** | Google Authenticator, Microsoft Authenticator |
| **FIDO security keys** | YubiKey, Titan Security Key |
| **Hardware TOTP tokens** | Thales, Hypersecu (physical devices) |

**Best practice**: Enable MFA for the root user and all privileged IAM users.

---

## IAM Password Policy

You can configure a custom password policy for IAM user console passwords:
- Minimum length (AWS default: 8 characters)
- Required character types (uppercase, lowercase, numbers, special characters)
- Password expiration (1–1095 days)
- Allow/deny users to change their own password
- Prevent reuse of previous passwords (up to 24 previous)

---

## IAM Groups

An **IAM Group** is a collection of IAM users. Instead of attaching policies to each user individually, you attach policies to the group — all users in the group inherit those permissions.

```
DevOps Group
├── Policy: AmazonEC2FullAccess
├── Policy: AmazonCloudWatchFullAccess
│
├── Alice (IAM User)
├── Bob (IAM User)
└── Charlie (IAM User)
```

### Key Group Facts

- A group can contain multiple users; a user can belong to **multiple groups**
- Groups can have multiple policies attached
- **Groups cannot contain other groups** — groups are flat, not nested
- **Groups cannot be used as a principal in IAM policies** — you can't grant a group access to a resource directly via a resource-based policy; only users/roles/accounts/services can be principals

### Practical Usage Pattern

| Group | Policies Attached |
|---|---|
| Developers | EC2 read/write, S3 access, CloudWatch |
| DBAs | RDS full access, EC2 read-only |
| QA Engineers | EC2 start/stop, S3 read-only |
| SRE/DevOps | EC2 full, CloudFormation, Systems Manager |

---

## Users vs Groups vs Roles — Quick Distinction

| Identity | Best For | Credentials |
|---|---|---|
| **IAM User** | Human individual or long-term service account | Long-term (password + access keys) |
| **IAM Group** | Managing permissions for multiple users at once | N/A (no credentials; inherits user credentials) |
| **IAM Role** | AWS services, cross-account access, temporary access | Short-term (temporary credentials via STS) |

---

## Key Points / Exam Tips

- **Every person should have their own IAM user** — never share credentials
- **Groups simplify permission management** — attach policies to groups, not individual users
- **Groups cannot be nested** — a group cannot contain another group
- **Groups cannot be principals** in resource-based policies (like S3 bucket policies)
- **Maximum 2 access keys per user** at any time
- **MFA should be enabled** for root user and privileged IAM users
- **Secret access key is shown only once** — must be saved at creation time

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Manage permissions for a team" | IAM Group |
| "Individual developer needs CLI access" | IAM User + access keys |
| "Second layer of security beyond password" | MFA |
| "Rotate access keys without downtime" | 2-key maximum allows rotation: create new → update apps → deactivate old → delete old |
| "Groups cannot contain other groups" | Flat group structure — not hierarchical |
