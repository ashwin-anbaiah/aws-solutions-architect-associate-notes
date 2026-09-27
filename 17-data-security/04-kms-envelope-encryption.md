# KMS Envelope Encryption and Multi-Region Keys

## Does KMS Encrypt Your Data Directly?

**No.** KMS does not directly encrypt your S3 objects, EBS volumes, or DynamoDB tables. Instead, AWS services use **Envelope Encryption** — a two-layer approach.

## What is Envelope Encryption?

**Envelope encryption** wraps a **Data Encryption Key (DEK)** with a **KMS Key (CMK)** so that the DEK can be stored safely alongside the encrypted data.

### How it works (Encryption):

1. AWS service (e.g., S3) calls KMS: **"Generate a Data Key for key `arn:aws:kms:...:key/1234`"**
2. KMS returns **two items**:
   - **Plaintext Data Key (DEK)**: used to encrypt the actual data
   - **Encrypted Data Key (eDEK)**: the same DEK encrypted with the CMK
3. The AWS service encrypts your data (e.g., S3 object) locally using the **Plaintext DEK** — this happens inside the service's infrastructure, not inside KMS
4. The **Plaintext DEK is immediately discarded** (never stored)
5. The **Encrypted DEK is stored alongside** the encrypted data (e.g., in S3 object metadata)

### How it works (Decryption):

1. Service retrieves the **Encrypted DEK** stored with the encrypted data
2. Sends the Encrypted DEK to KMS: **"Decrypt this"**
3. KMS decrypts it using the CMK → returns **Plaintext DEK**
4. Service uses Plaintext DEK to **decrypt the data**
5. Plaintext DEK is discarded again

```
Encrypt:
[AWS Service] --GenerateDataKey--> [KMS]
              <--Plaintext DEK + Encrypted DEK--
[AWS Service encrypts data with Plaintext DEK]
[Stores: Encrypted Data + Encrypted DEK]
[Discards Plaintext DEK]

Decrypt:
[AWS Service retrieves Encrypted DEK]
[Sends Encrypted DEK to KMS]
[KMS decrypts with CMK --> returns Plaintext DEK]
[AWS Service decrypts data with Plaintext DEK]
[Discards Plaintext DEK]
```

## Why Envelope Encryption?

- **Performance**: KMS has throughput limits; encrypting large files inside KMS would be slow. Envelope encryption lets services encrypt terabytes locally at full speed.
- **Cost**: Fewer KMS API calls — one call to generate the DEK, then local encryption for all data
- **Security**: CMK never leaves KMS; only the DEK touches your data — the DEK itself is protected by the CMK

## Services That Use Envelope Encryption

Most AWS services that encrypt data use envelope encryption internally:
- **S3** (SSE-KMS)
- **EBS** (encrypted volumes)
- **RDS** (encrypted instances)
- **DynamoDB** (encryption at rest)
- **Secrets Manager** (encrypts secrets)
- **Systems Manager Parameter Store** (SecureString parameters)
- **CloudTrail** (log encryption)

## Multi-Region Keys

When data must be encrypted and decrypted **in different AWS regions**, standard regional KMS keys require re-encryption. **Multi-Region Keys** solve this.

### Key Facts

- **Primary Key**: the source key, created in one region
- **Replica Keys**: copies synchronized from the primary, in other regions
- **Same Key Material**: encrypt in one region, decrypt in another — no re-encryption needed
- **Same Key ID** across all replicas (the `mrk-` prefixed ID is identical)
- **Different ARNs**: only the region segment differs
  - Primary: `arn:aws:kms:us-east-1:111122223333:key/mrk-1234abcd...`
  - Replica: `arn:aws:kms:eu-west-1:111122223333:key/mrk-1234abcd...`
- Metadata, key policy, tags, and rotation state **automatically sync** from primary to replicas
- Deletion must be initiated at the primary and propagated to replicas

### Multi-Region Key Use Cases

| Scenario | Why needed |
|---|---|
| **S3 CRR (Cross-Region Replication) with SSE-KMS** | Objects encrypted in source region must be decryptable in destination region |
| **DynamoDB Global Tables** | Consistent encryption across replicas in multiple regions |
| **Aurora Global Database** | Decrypt data at the disaster recovery region |
| **EBS Snapshot copy across regions** | Snapshot encrypted in source region, usable in destination |
| **AMI copy across regions** | Same as EBS snapshots |

## Cross-Account Key Sharing

When sharing encrypted resources (AMIs, EBS snapshots) with another AWS account:
1. Update the **KMS key policy** to grant the target account's principals access (kms:Decrypt, kms:CreateGrant)
2. The target account must also have **IAM permissions** to use the KMS key

Both conditions must be met: key policy allows + IAM allows.

## Key Points / Exam Tips

- KMS does **not** encrypt your data directly — AWS services use envelope encryption with a DEK
- The **Plaintext DEK is never stored** — only the Encrypted DEK is stored with your data
- Envelope encryption is why KMS can scale to encrypt billions of objects
- **Multi-Region Keys**: same key material, same Key ID, different ARNs — no re-encryption for cross-region
- For cross-account encrypted resource sharing: update **both** the KMS key policy AND IAM policy
- "GenerateDataKey" API = request KMS to create a data key for envelope encryption

## Trigger Words

- "Encrypt large amounts of data efficiently with KMS" → Envelope encryption
- "Data encrypted in us-east-1 needs to be decrypted in eu-west-1" → Multi-Region KMS Key
- "Copy encrypted EBS snapshot to another region" → Multi-Region KMS Key (or re-encryption)
- "Share encrypted AMI with another AWS account" → Update KMS key policy + IAM
- "GenerateDataKey API" → Envelope encryption — service is requesting a DEK
