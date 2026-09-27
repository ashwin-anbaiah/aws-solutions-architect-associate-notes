# S3 Object Lock (WORM)

## What is S3 Object Lock?

**S3 Object Lock** prevents objects from being deleted or overwritten for a defined retention period or indefinitely. It implements **WORM (Write Once, Read Many)** storage, which is required by regulations in finance, healthcare, insurance, and legal industries.

Think of it as "locking a document in a tamper-proof vault" — even the bucket owner or root user cannot change a locked object in Compliance mode until the lock expires.

**Prerequisite:** S3 bucket **versioning must be enabled** before enabling Object Lock. Object Lock is enabled at bucket creation time and cannot be disabled afterward.

---

## Object Lock Modes

### Retention Modes

| Mode | Protection Level | Can Root User Override? | Use Case |
|---|---|---|---|
| **Compliance Mode** | Strictest — no one (including root) can delete or alter until retention expires | No | SEC, FINRA, HIPAA compliance — non-negotiable retention |
| **Governance Mode** | Protected unless granted special override permission (`s3:BypassGovernanceRetention`) | Yes (with permission) | Internal policy enforcement with occasional admin override |

### Retention Period

- A **fixed period** during which the object version remains locked
- Can be set at the **bucket level** (default for all objects) or per individual object version
- Retention period can be **extended** but never shortened

### Legal Hold

- A **separate, independent lock** with **no expiration date**
- Remains until explicitly removed using the `s3:PutObjectLegalHold` permission
- Does not require a retention period — can be applied independently
- Use case: litigation holds, regulatory investigations, evidence preservation

---

## Retention Mode Comparison

| Feature | Compliance Mode | Governance Mode | Legal Hold |
|---|---|---|---|
| Root user can delete? | No | Yes (with `s3:BypassGovernanceRetention`) | No |
| Expiration | Fixed retention period | Fixed retention period | No expiration |
| Can shorten retention? | No | No (can extend only) | N/A |
| Remove without waiting? | Never | Yes (with override permission) | Yes (with `s3:PutObjectLegalHold`) |

---

## Object Lock on Existing Buckets

- Object Lock must be enabled at **bucket creation** — it cannot be enabled on an existing bucket later
- Once enabled, it cannot be disabled

---

## Common Object Lock Use Cases

| Regulation / Requirement | Lock Configuration |
|---|---|
| SEC Rule 17a-4 (finance) | Compliance Mode, 7-year retention |
| HIPAA records | Compliance Mode, 6-year retention |
| Internal audit logs | Governance Mode (admin can adjust) |
| Active litigation | Legal Hold (indefinite) |
| Video archives (long-term) | Governance Mode + retention period |

---

## Object Lock vs MFA Delete

| Feature | Object Lock | MFA Delete |
|---|---|---|
| Prevents deletion by root? | Yes (Compliance mode) | No |
| Requires versioning | Yes | Yes |
| Protects against legal destruction | Yes | No |
| Managed by root only | No (any authorized IAM user) | Yes |
| Works without expiry | Yes (Legal Hold) | N/A |

They can be used together for maximum protection.

---

## Key Points / Exam Tips

- Object Lock = WORM — objects cannot be modified or deleted during the retention period
- **Compliance Mode** is absolute — even root cannot delete; needed for strict regulatory compliance
- **Governance Mode** allows admin override with `s3:BypassGovernanceRetention` — good for internal policies
- **Legal Hold** has no expiration — applies independently of retention periods
- Versioning must be enabled; Object Lock is set at bucket creation (cannot add later)
- Retention period can be **extended, never shortened**
- Object Lock protects **object versions** — you can still create new versions; the locked version stays

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "WORM storage for regulatory compliance" | S3 Object Lock (Compliance Mode) |
| "Nobody — not even root — can delete objects" | S3 Object Lock Compliance Mode |
| "Admins need occasional override for compliance data" | S3 Object Lock Governance Mode |
| "Indefinite hold for active litigation" | S3 Object Lock Legal Hold |
| "SEC Rule 17a-4 / FINRA / HIPAA retention" | S3 Object Lock Compliance Mode |
| "Compliance mode vs governance mode" | Compliance = no one can delete; Governance = admin can override |
| "Extend but not shorten retention period" | S3 Object Lock behavior |
