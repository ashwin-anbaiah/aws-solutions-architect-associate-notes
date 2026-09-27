# S3 Versioning and MFA Delete

## S3 Versioning

**Versioning** preserves every version of an object in a bucket. When enabled, every PUT overwrites create a new version rather than replacing the previous one.

Think of it like Git for your files — every upload is a commit, and you can roll back to any prior state.

---

## How Versioning Works

| Action | With Versioning Disabled | With Versioning Enabled |
|---|---|---|
| Upload file | Overwrites existing object | Creates new version; old version retained |
| Delete without version ID | Permanently deletes object | Adds a **Delete Marker** (hides object, data retained) |
| Delete with version ID | N/A | Permanently deletes that specific version |
| List objects | Shows current objects | Can show all versions (including delete markers) |

- Every version gets a unique **Version ID** (e.g., `3/L4kqtJlcpXroDTDmJ+rmSpXd3dIbrHY`)
- Objects before versioning was enabled get Version ID = `null`
- All versions are stored separately — costs accumulate

---

## Versioning States

| State | Description |
|---|---|
| **Unversioned** (default) | No versioning, no Version IDs |
| **Versioning-enabled** | All new uploads create versions |
| **Versioning-suspended** | New uploads do NOT get versioned; existing versions are preserved |

**Important:** Once versioning is enabled, it can only be **suspended** — not disabled. Suspended state stops creating new versions but keeps all existing ones.

---

## Delete Markers

- A **Delete Marker** is a placeholder version with no data
- When you delete an object (no version ID specified), S3 adds a delete marker as the newest version
- The object appears deleted (GET returns 404), but all prior versions still exist
- **Restoring a deleted object** = delete the delete marker
- Delete markers themselves can have a version ID and can be permanently deleted

---

## Versioning Prerequisites and Use Cases

Versioning must be enabled before using:
- **MFA Delete**
- **S3 Object Lock**
- **S3 Replication**

Use cases:
- Accidental deletion recovery
- Rollback to a previous version of a file
- Audit trail of changes to objects
- Meeting compliance requirements for data retention

---

## MFA Delete

**MFA Delete** adds a second layer of protection to prevent accidental or malicious deletions.

### What Requires MFA?

| Operation | MFA Required? |
|---|---|
| Permanently delete a specific object version | Yes |
| Change the versioning state (enable/suspend) | Yes |
| Regular object upload (new version) | No |
| Add a delete marker (soft delete) | No |

### MFA Delete Configuration Rules

- **Versioning must be enabled** before enabling MFA Delete
- Can only be enabled/disabled by the **root user** — not IAM users or roles
- Must be configured via **AWS CLI, SDK, or API** — not the console
- Applies at the **bucket level** (all objects in the bucket protected)
- Works independently from (and can be combined with) S3 Object Lock

### Enabling MFA Delete (CLI Example)

```bash
aws s3api put-bucket-versioning \
  --bucket my-bucket \
  --versioning-configuration Status=Enabled,MFADelete=Enabled \
  --mfa "arn:aws:iam::123456789:mfa/root-mfa-device 123456"
```

---

## Key Points / Exam Tips

- Versioning can be **suspended but not disabled** once enabled
- A **Delete Marker** is NOT a permanent deletion — data survives; delete the marker to restore
- MFA Delete can only be managed by the **root user** — not any IAM user
- MFA Delete protects against permanent version deletion and versioning state changes
- Objects uploaded before versioning was enabled have `null` as their Version ID
- Versioning stores ALL versions — costs increase; use Lifecycle rules to expire old versions and control costs
- For rollback: simply delete the current version (or the delete marker) to expose the previous version

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Protect S3 from accidental deletion" | Enable versioning + MFA Delete |
| "Roll back to previous version of a file" | S3 Versioning |
| "Object shows 404 but data was not permanently deleted" | Delete Marker exists — versioning in effect |
| "Prevent root user from bypassing S3 deletion protection" | MFA Delete |
| "Only root user can manage this" | MFA Delete configuration |
| "Required for S3 Object Lock and Replication" | S3 Versioning must be enabled first |
| "Versioning cannot be turned off" | It can only be suspended, never disabled |
