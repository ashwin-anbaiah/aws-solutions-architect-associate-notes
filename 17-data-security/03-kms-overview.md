# AWS KMS (Key Management Service)

## What is AWS KMS?

**AWS Key Management Service (KMS)** is a managed service for creating, storing, and controlling **encryption keys** used to protect data across AWS services.

- Supports both **Symmetric** (AES-256) and **Asymmetric** (RSA-2048, ECC NIST P-256) keys
- Keys **never leave KMS** — all cryptographic operations happen inside **AWS-managed HSMs** (FIPS 140-2 validated)
- Every AWS service that encrypts data (S3, EBS, RDS, DynamoDB, etc.) integrates with KMS
- Access controlled via **IAM policies** AND **KMS Key Policies**
- Keys are **regional** by default — a key in us-east-1 cannot be used in eu-west-1 (unless Multi-Region)

## KMS Key Types

| Key Type | Managed By | Key Material | Cost | Use Case |
|---|---|---|---|---|
| **AWS Managed Key** | AWS | AWS | Free | Default encryption for AWS services (e.g., `aws/s3`, `aws/ebs`) |
| **Customer Managed Key (CMK)** | Customer | Customer or AWS | $1/month/key + API costs | Custom key policies, rotation control, cross-account sharing |
| **AWS Owned Key** | AWS | AWS | Free | Used internally by AWS services — not visible to customers |

## Key Policies

**KMS Key Policies** are resource-based policies attached to KMS keys — they control who can use or manage a key.

- Similar to **S3 bucket policies** but for KMS keys
- **Unlike IAM policies**, KMS key policies are REQUIRED — without a key policy, no one can use the key (even account root)
- The default key policy gives the AWS account root full control

**Access levels:**
- **Key Administrator**: Manage the key (create, enable, disable, schedule deletion, rotate, manage policy)
- **Key Users**: Use the key to encrypt/decrypt and generate Data Keys
- **Cross-account access**: Allow another AWS account's principals to use the key (must also have IAM permissions)

**Example key policy statement:**
```json
{
  "Sid": "AllowKeyAdminAccess",
  "Effect": "Allow",
  "Principal": {"AWS": "arn:aws:iam::123456789012:role/AdminRole"},
  "Action": ["kms:DescribeKey", "kms:Create*", "kms:Enable*", "kms:Disable*", "kms:ScheduleKeyDeletion"]
}
```

## Key Rotation

For **Customer Managed Keys (CMKs)**:
- **Automatic rotation**: enabled manually; rotates every **365 days** (1 year) by default
- Rotation creates **new key material** but keeps the **same Key ID and ARN** — no re-encryption of existing data needed
- Old key material is retained — existing encrypted data can still be decrypted
- **AWS Managed Keys** rotate automatically every **1 year** (AWS managed, cannot disable)

## KMS Grants

**Grants** are an alternative to key policies for temporarily delegating key access:
- Allows a principal to use a KMS key for specific operations
- Use case: Give an AWS service (e.g., AWS Secrets Manager) temporary access to a KMS key for a specific operation
- Grants can be revoked without modifying the key policy

## Multi-Region Keys

**Multi-Region KMS Keys** allow encryption/decryption across AWS regions without re-encryption:
- Consists of a **Primary Key** and **Replica Keys** in other regions
- Replica keys share the same **Key ID and key material** as the primary
- Different ARNs (only the region code differs)
- Primary metadata, policy, tags, and rotation state automatically sync to replicas
- **No re-encryption required** — data encrypted in one region can be decrypted in another

**Use cases for Multi-Region Keys:**
- S3 Cross-Region Replication (CRR) with SSE-KMS
- DynamoDB Global Tables
- Aurora Global Database
- EBS Snapshot copy across regions
- AMI copy across regions

**Cross-account sharing note**: When sharing encrypted AMIs or snapshots with another account, update the KMS key policy to allow the target account's principals to use the key.

## Key Points / Exam Tips

- **Keys never leave KMS** — actual data encryption happens in the service (envelope encryption)
- KMS key policies are **required** — IAM alone is not sufficient to use a KMS key
- **CMK rotation** = every year by default; Key ID stays the same; old material retained
- "Access Denied (KMS)" on S3 = user likely missing `kms:Decrypt` or `kms:GenerateDataKey` IAM permission
- KMS keys are **regional** by default — use Multi-Region Keys for cross-region DR/replication scenarios
- **AWS Managed Keys** = free, auto-rotate, limited control; **CMKs** = $1/month, full control

## Trigger Words

- "Encrypt data with customer-managed encryption key" → KMS CMK
- "Rotate encryption keys automatically" → KMS key rotation
- "Control who can use an encryption key" → KMS Key Policy
- "Cross-region encrypted data replication" → KMS Multi-Region Keys
- "Access Denied when reading encrypted S3 object" → Missing KMS key permissions
- "Shared encrypted AMI / snapshot to another account" → Update KMS key policy for target account
