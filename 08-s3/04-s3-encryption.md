# S3 Encryption (SSE-S3, SSE-KMS, SSE-C, Client-Side)

## Overview

S3 supports **server-side encryption (SSE)** and **client-side encryption**. Server-side encryption means S3 encrypts data as it writes it to disk and decrypts it when you read it — all over HTTPS.

Think of server-side encryption as a safe deposit box managed by the bank: they lock and unlock it for you using their key (SSE-S3), your key stored at their key desk (SSE-KMS), or your own key you hand them each time (SSE-C).

---

## Encryption Types Compared

| Type | Key Managed By | HTTPS Required? | Request Header | Audit via CloudTrail? |
|---|---|---|---|---|
| **SSE-S3** | AWS S3 (default) | No (but recommended) | `x-amz-server-side-encryption: AES256` | No |
| **SSE-KMS** | AWS KMS | No (but recommended) | `x-amz-server-side-encryption: aws:kms` | Yes |
| **SSE-C** | Customer (outside AWS) | **Yes (required)** | `x-amz-server-side-encryption-customer-algorithm: AES256` + key in header | No |
| **Client-Side** | Customer (encrypt before upload) | Recommended | N/A (data arrives already encrypted) | No |

---

## SSE-S3 (S3-Managed Keys)

- **Enabled by default** on all new S3 buckets and objects
- AWS manages and rotates the encryption keys — you have no control over key management
- Algorithm: **AES-256**
- No additional cost beyond storage
- Best for: general purpose encryption, compliance requirements that just need "data encrypted at rest"

---

## SSE-KMS (KMS-Managed Keys)

- Uses **AWS Key Management Service (KMS)** to store and manage encryption keys
- You choose which KMS key to use (AWS-managed key `aws/s3`, or your own Customer Managed Key)
- Provides **audit trail** via CloudTrail — every encryption/decryption operation is logged
- Envelope encryption: KMS generates a **data key** per object, the object is encrypted with that data key, and the data key itself is encrypted with your KMS CMK

### IAM Permissions Required

| Operation | Required KMS Permission |
|---|---|
| `PutObject` (upload) | `kms:GenerateDataKey` |
| `GetObject` (download) | `kms:Decrypt` |

**Common exam trap:** If a user can call `s3:GetObject` but still gets "Access Denied," the cause is usually a missing `kms:Decrypt` permission.

### KMS Key Rotation

- AWS recommends enabling **automatic key rotation** (default: every year)
- Rotation creates new key material but the **Key ID and ARN stay the same** — no application changes needed
- Old key material is retained, so previously encrypted objects remain decryptable
- New uploads automatically use the latest key version

### S3 Bucket Keys (Cost Optimization)

- By default, every SSE-KMS object encryption/decryption calls KMS individually — high API cost at scale
- Enable **S3 Bucket Keys** to generate a short-lived data key at the bucket level, dramatically reducing KMS API calls (and cost)

---

## SSE-C (Customer-Provided Keys)

- You provide your own encryption key in **every request** — S3 uses it to encrypt/decrypt, then discards it
- S3 never stores your key
- **HTTPS is mandatory** — the key travels in the request header
- Request must include: `x-amz-server-side-encryption-customer-key` (Base64-encoded 256-bit key)
- Requires you to track and manage your own keys completely
- AWS Console does NOT support SSE-C — must use CLI or SDK

---

## Client-Side Encryption

- You **encrypt the object before uploading** to S3
- S3 receives and stores an already-encrypted blob — it cannot decrypt it
- Decryption also happens on the client after download
- Use the **Amazon S3 Encryption Client** (available in SDKs) for easier implementation
- Best for: proprietary encryption algorithms, strict data sovereignty requirements, or when you cannot trust the cloud provider's key management

---

## Enforcing Encryption via Bucket Policy

Use Deny rules to reject uploads that don't use the correct encryption type:

```json
// Enforce SSE-S3
"Condition": {
  "StringNotEquals": {
    "s3:x-amz-server-side-encryption": "AES256"
  }
}

// Enforce SSE-KMS
"Condition": {
  "StringNotEquals": {
    "s3:x-amz-server-side-encryption": "aws:kms"
  }
}

// Enforce a specific KMS Key
"Condition": {
  "ArnNotEqualsIfExists": {
    "s3:x-amz-server-side-encryption-aws-kms-key-id": "arn:aws:kms:us-east-1:111122223333:key/..."
  }
}
```

---

## Key Points / Exam Tips

- SSE-S3 is the **default** and requires no configuration — it's always on for new buckets
- SSE-KMS provides **audit trail** via CloudTrail — choose it when compliance requires proof of who decrypted what
- SSE-C requires **HTTPS** and key in every request — AWS never stores your key
- Client-side encryption: **S3 cannot decrypt** — you are responsible for the entire lifecycle
- "Access Denied on S3 object (KMS)" → check `kms:Decrypt` or `kms:GenerateDataKey` permissions in KMS key policy
- S3 Bucket Keys reduce KMS API call costs for SSE-KMS at scale
- All four types use **envelope encryption** internally

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Audit who decrypted each S3 object" | SSE-KMS with CloudTrail |
| "Customer provides their own key per request" | SSE-C (HTTPS required) |
| "Reduce KMS API costs for S3" | S3 Bucket Keys |
| "Encrypt before upload, S3 cannot decrypt" | Client-side encryption |
| "Enforce SSE-KMS via policy" | Bucket policy Deny on `s3:x-amz-server-side-encryption != aws:kms` |
| "Access Denied on S3 PutObject (KMS error)" | Missing `kms:GenerateDataKey` permission |
| "Default S3 encryption" | SSE-S3 (AES-256, enabled by default) |
