# EBS Encryption

## What Is EBS Encryption?

**EBS Encryption** protects your data at rest on EBS volumes using **AWS KMS (Key Management Service)**. When you enable encryption on an EBS volume, everything is encrypted:

- Data at rest inside the volume
- All data moving between the volume and the EC2 instance (in-transit encryption over the network path)
- All snapshots created from that volume
- All volumes created from those encrypted snapshots

**No performance impact**: encryption/decryption happens at the hypervisor level — the instance and application see no difference in performance.

---

## How to Enable Encryption

### Option 1: Enable Encryption by Default (Account-Level Setting)

In each Region, you can enable **Encryption by Default**:
- All new EBS volumes created in that Region are encrypted automatically
- Uses the AWS-managed default KMS key (`aws/ebs`)
- No per-volume configuration needed

### Option 2: Manually Enable During Volume Creation

When creating a new EBS volume, you can choose to encrypt it and select which KMS key to use:
- **AWS Managed Key** (`aws/ebs`): automatically created by AWS, managed by AWS
- **Customer Managed Key (CMK)**: a KMS key you create and manage yourself, giving you more control (key rotation, key policies, cross-account usage)

---

## What Gets Encrypted

```
EBS Volume (encrypted)
    ↓ Data at rest: encrypted ✓
    ↓ Snapshot: encrypted ✓
    ↓ New volume from snapshot: encrypted ✓
    ↓ In-transit between volume and EC2: encrypted ✓
```

Everything in the encrypted chain stays encrypted.

---

## Encryption Scenarios

### Encrypting a NEW Volume

Easy — just enable encryption when creating the volume. Done.

### Encrypting an EXISTING Unencrypted Volume

**You cannot directly encrypt an existing unencrypted EBS volume.** There is no "enable encryption" button for a live volume.

The only path:
1. Create a **snapshot** of the unencrypted volume
2. **Copy** the snapshot with **encryption enabled** (you choose the KMS key during copy)
3. **Create a new EBS volume** from the encrypted snapshot
4. Attach the new encrypted volume to the instance (replace the old unencrypted one)
5. Delete the old unencrypted volume

```
Unencrypted Volume
      ↓ create snapshot
Unencrypted Snapshot
      ↓ copy with encryption enabled
Encrypted Snapshot
      ↓ create volume
Encrypted Volume ← use this going forward
```

### Cross-Account Encrypted AMI/Snapshot Sharing

If you share an encrypted snapshot or AMI with another account, you must also **share the KMS key** with that account. Without access to the KMS key, the other account cannot decrypt the snapshot.

---

## CMK Disabled or Deleted — What Happens?

| KMS Key State | Effect on EBS Volume |
|---|---|
| **CMK Disabled** | Volume becomes **inaccessible** until the key is re-enabled |
| **CMK Deleted** | **Permanent data loss** — cannot decrypt the volume data, ever |

This is why key management governance is critical. Always have proper KMS key policies and deletion protection in place.

---

## Encryption and Snapshots

| Scenario | Result |
|---|---|
| Snapshot from encrypted volume | Snapshot is **automatically encrypted** (same key) |
| Snapshot from unencrypted volume | Snapshot is unencrypted |
| Volume from encrypted snapshot | Volume is **automatically encrypted** (same key) |
| Copy unencrypted snapshot with encryption enabled | Encrypted snapshot (your choice of key) |
| Copy encrypted snapshot with a different key | Re-encrypts with the new key |
| Copy encrypted snapshot without encryption | **NOT ALLOWED** — one-way only |

**The one-way rule**: You can always encrypt (unencrypted → encrypted), but you can never decrypt (encrypted → unencrypted). This is a hard AWS security safeguard.

---

## Key Points / Exam Tips

- **No performance overhead** — encryption happens at the hypervisor, transparent to the instance and app
- **Encrypting an existing volume requires**: snapshot → copy with encryption → create new volume from encrypted snapshot
- **Encrypted to unencrypted is NOT allowed** — one-way only
- **All snapshots from encrypted volumes are automatically encrypted**
- **CMK deletion = permanent data loss** — protect your KMS keys
- **"Encryption by default"** setting encrypts all new volumes in the Region automatically

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Encrypt an existing EBS volume" | Cannot do directly — snapshot → encrypted copy → new volume |
| "Snapshot of encrypted volume" | Automatically encrypted (same KMS key) |
| "Encrypted → unencrypted copy" | NOT allowed — security safeguard |
| "CMK deleted, what happens to the volume?" | Permanent data loss — volume cannot be decrypted |
| "All new volumes in the account should be encrypted" | Enable "Encryption by Default" at Region level |
| "No performance impact from EBS encryption" | Correct — hypervisor-level, transparent |
