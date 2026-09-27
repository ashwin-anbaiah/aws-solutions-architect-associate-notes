# S3 Security (Block Public Access, Bucket Policy, ACL)

## S3 Security Layers

S3 access control uses multiple layers:

1. **Block Public Access** — account or bucket-level kill switch for public access
2. **Access Control Lists (ACLs)** — legacy, object/bucket-level grants
3. **Bucket Policy** — resource-based JSON policy attached to the bucket
4. **IAM Policies** — identity-based policies attached to users, roles, or groups

---

## Block Public Access

The **S3 Block Public Access** setting prevents data from being accidentally exposed to the internet.

| Setting | Effect |
|---|---|
| Block all public access | Overrides all bucket policies and ACLs that would allow public access |
| Can be set at | Account level (applies to all buckets) or bucket level |
| Default | ON (enabled) for new buckets |

**Best practice:** Leave Block Public Access ON unless the bucket intentionally needs to serve public content (e.g., a static website).

---

## Access Control Lists (ACLs)

- Legacy mechanism; **disabled by default** for new buckets (AWS recommends keeping them disabled)
- Grants access at the **bucket level** or **object level** to specific AWS accounts or predefined groups
- Modern use cases no longer require ACLs — use bucket policies or IAM policies instead
- **Exception:** Cross-account uploads where the bucket owner needs full control — use `bucket-owner-full-control` ACL or enable **S3 Object Ownership = Bucket Owner Enforced** (disables ACLs, all objects owned by bucket owner)

---

## Bucket Policy

A **Bucket Policy** is a JSON-based resource policy attached directly to an S3 bucket. It controls access to the bucket and its objects.

### Bucket Policy Elements

| Element | Purpose |
|---|---|
| `Version` | Policy language version (always use `"2012-10-17"`) |
| `Effect` | Allow or Deny |
| `Principal` | Who the policy applies to (IAM user, role, account, `*` for everyone) |
| `Action` | S3 API operations (e.g., `s3:GetObject`, `s3:PutObject`) |
| `Resource` | Bucket or object ARN |
| `Condition` | Optional conditions (IP address, VPC, MFA, etc.) |

### Common Bucket Policy Use Cases

| Use Case | Policy Approach |
|---|---|
| **Public read access** (static website) | `"Principal": "*"`, `Action: s3:GetObject` |
| **Cross-account access** | `"Principal": "arn:aws:iam::OTHER_ACCOUNT:root"` |
| **Restrict to specific VPC** | Condition: `"aws:sourceVpc": "vpc-xxx"` |
| **Restrict to specific VPC Endpoint** | Condition: `"aws:sourceVpce": "vpce-xxx"` |
| **Restrict to specific IP range** | Condition: `"aws:SourceIp": "x.x.x.x/24"` |
| **Enforce HTTPS only** | Condition: Deny `"aws:SecureTransport": "false"` |
| **Enforce encryption at upload** | Condition: Deny without `s3:x-amz-server-side-encryption` header |

### Making an S3 Bucket Public (Both Steps Required)
1. Disable Block Public Access for the bucket
2. Add a bucket policy with `"Principal": "*"` and `"Action": "s3:GetObject"`

---

## IAM Policies vs. Bucket Policies

| Feature | IAM Policy | Bucket Policy |
|---|---|---|
| Attached to | IAM user, role, or group | S3 bucket |
| Controls | What the identity can do | Who can access the resource |
| Cross-account | Cannot grant cross-account by itself | Can grant cross-account access |
| Anonymous (public) access | Cannot grant | Can grant (with `"Principal": "*"`) |

**Rule:** For cross-account S3 access, **both** the IAM policy in the caller's account AND the bucket policy in the resource account must allow the access.

---

## IAM Access Analyzer for S3

- Analyzes bucket ACLs and bucket policies
- Identifies **publicly accessible buckets** and **buckets shared with other AWS accounts**
- Helps you audit and remediate unintended public or cross-account access

---

## Key Points / Exam Tips

- **Block Public Access overrides** bucket policies — even if a bucket policy allows public access, Block Public Access will prevent it if enabled
- Bucket policies support **Deny** rules — unlike IAM policies where explicit Deny also wins
- **S3 has NO Security Groups** — all access control is through IAM, bucket policies, ACLs, and Block Public Access
- `"Principal": "*"` means everyone — including anonymous internet users; only safe when combined with Block Public Access being disabled
- For cross-account access: both the IAM policy (caller's account) AND the bucket policy (owner's account) must allow it
- ACLs are disabled by default on new buckets — AWS recommends not enabling them for new workloads

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Prevent public exposure of S3 data organization-wide" | Block Public Access at account level |
| "Cross-account S3 access" | Bucket policy (resource-based policy) + IAM policy on caller |
| "Allow only HTTPS access to S3" | Bucket policy with Deny on `aws:SecureTransport: false` |
| "Restrict S3 access to a specific VPC" | Bucket policy with `aws:sourceVpc` condition |
| "S3 security group" | Invalid — S3 has NO security groups |
| "Public read access for a static website" | Disable Block Public Access + add `"Principal": "*"` bucket policy |
