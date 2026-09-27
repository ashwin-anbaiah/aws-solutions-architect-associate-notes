# IAM Best Practices

## The Mindset

IAM security is about two principles: **least privilege** (grant only what's needed) and **defense in depth** (multiple layers of protection so a single mistake doesn't lead to a full compromise).

---

## IAM Best Practices Checklist

### 1. Never Use the Root User for Day-to-Day Operations

- Create an **IAM admin user** with `AdministratorAccess` for admin tasks instead
- Store root credentials securely; only use root for tasks that explicitly require it
- Enable MFA on the root user — immediately, before anything else

### 2. Apply Strong Password Policies

- Require minimum length (8+ characters)
- Require mixed character types
- Enable password expiration and prevent reuse

### 3. Enable MFA for Privileged Users

- Enable MFA for root and all admin-level IAM users
- Consider requiring MFA for sensitive operations (via policy conditions with `aws:MultiFactorAuthPresent`)

### 4. Use IAM Roles for Services — Never Embed Credentials

- EC2, Lambda, ECS tasks should use **IAM roles** — credentials are auto-rotated via STS
- Never store access keys in application code, instance user data, or environment variables in plaintext
- Store any necessary secrets in **AWS Secrets Manager** or **Parameter Store (SecureString)**, not in code

### 5. Follow Least Privilege

- Start with no permissions and add only what's required for the job
- Use **IAM Access Analyzer** to identify unused permissions and right-size policies
- Use **Service Last Accessed data** to see which permissions are actually being used
- Avoid using wildcard (`*`) in `Action` or `Resource` unless truly necessary

### 6. Use Permissions Boundaries for Delegated Administration

- If you let teams manage their own IAM roles, use permissions boundaries to cap what they can create
- Prevents privilege escalation — a junior developer can't create an admin role and assume it

### 7. Use Conditions to Restrict Access Further

- Restrict API calls to specific Regions: `aws:RequestedRegion`
- Restrict to specific source IPs: `aws:SourceIp`
- Require MFA for sensitive actions: `aws:MultiFactorAuthPresent`
- Tag-based access: allow access only to resources with matching environment tags

### 8. Rotate Access Keys Regularly

- Set a rotation policy (90 days is a common standard)
- Use the Credentials Report to identify keys that haven't been rotated
- Two-key maximum per user enables zero-downtime rotation: create new key → update apps → deactivate old → delete old

### 9. Audit Regularly

- **Credentials Report**: MFA status, access key age, last login for all users
- **Access Analyzer**: detect unintended external access and unused permissions
- **CloudTrail**: review API activity for suspicious patterns
- **Remove unused users, roles, keys, and permissions** — stale credentials are an attack surface

### 10. Use IAM Roles for Cross-Account Access

- Never share IAM user credentials between accounts
- Use `sts:AssumeRole` cross-account — scoped, auditable, temporary credentials

---

## Quick Reference Summary Table

| Practice | Why |
|---|---|
| Don't use root for daily tasks | Root can't be restricted; a compromise is catastrophic |
| MFA everywhere | Second factor stops credential theft from being enough |
| Roles over access keys for services | Short-lived, auto-rotating, no storage required |
| Least privilege | Limits blast radius of any single compromised identity |
| Permissions boundaries for delegation | Prevents privilege escalation |
| Conditions on sensitive policies | Adds extra context-aware restrictions |
| Rotate access keys | Reduces window of exposure for leaked keys |
| Regular audits | Catches drift before it becomes a security incident |

---

## Key Points / Exam Tips

- "Root for day-to-day" = bad practice; IAM user or role for everything else
- "EC2 app needs AWS access" = IAM role, not embedded access keys
- "Least privilege" = only grant what's needed, and verify via Access Analyzer
- "Permissions boundary" = cap on max permissions for a single user/role (not groups)
- "Cross-account access" = always via `sts:AssumeRole`, never shared credentials
- "Access key rotation" = two-key model enables no-downtime rotation

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Most secure way for EC2 to call S3" | IAM role with S3 permissions |
| "Prevent root account compromise from being catastrophic" | Don't use root; enable MFA; use IAM admin user |
| "Enforce minimum permissions on self-service teams" | Permissions boundary |
| "How to prove all users have MFA" | IAM Credentials Report |
| "Who is accessing what in my account" | Access Analyzer + CloudTrail |
| "Key hasn't been rotated in 90 days" | Credentials Report; rotate via two-key model |
