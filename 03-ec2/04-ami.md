# Amazon Machine Image (AMI)

## What Is an AMI?

An **Amazon Machine Image (AMI)** is a complete snapshot/template that contains everything needed to launch an EC2 instance:
- The operating system
- All installed applications and packages
- Configuration files
- Any attached EBS volumes (captured as EBS snapshots)
- Launch permissions (which accounts can use this AMI)

Think of an AMI as a "golden master image" — once you've set up an instance exactly the way you want it, you capture it as an AMI and use that to launch as many identical instances as you need.

---

## What an AMI Contains

```
AMI
├── Root volume template (EBS snapshot of the OS + software)
├── (Optional) Additional EBS volume snapshots (data volumes)
├── Launch permissions (public / specific accounts / private)
└── Block device mapping (which EBS volumes to attach and how)
```

---

## AMI Sources

| Source | Description |
|---|---|
| **AWS-managed AMIs** | Amazon Linux 2, Amazon Linux 2023, Windows Server — maintained by AWS |
| **AWS Marketplace** | Third-party AMIs (e.g., pre-configured Bitnami WordPress, CIS hardened images) |
| **Community AMIs** | Public AMIs shared by the AWS community — use with caution |
| **Your own custom AMIs** | AMIs you create from your own configured instances |

---

## Creating a Custom AMI — Process

1. **Launch a base EC2 instance** from an existing AMI
2. **Configure the instance** — install applications, set up the OS, run updates
3. **Create an AMI** from the running (or stopped) instance — AWS takes EBS snapshots automatically
4. **The AMI is now available** — launch as many instances from it as needed

```
Base AMI → EC2 Instance → Install/Configure → Create AMI (Custom) → Launch N identical instances
```

This is how you achieve fast, repeatable, consistent deployments — especially in Auto Scaling Groups.

---

## AMIs Are Region-Specific

**Critical exam fact**: AMIs belong to a specific Region. An AMI in ap-south-1 (Mumbai) cannot be used directly in us-east-1 (N. Virginia).

**To use an AMI in another Region**: you must **copy the AMI** to the target Region first.

Use case: Multi-Region DR (disaster recovery) — create your golden AMI in your primary Region, copy it to your DR Region, so you can launch instances there if the primary fails.

```
Mumbai (ap-south-1)         N. Virginia (us-east-1)
  Custom AMI          ──copy──▶   Copied AMI
  EC2 instances                    EC2 instances (for DR)
```

---

## AMI vs Launch Template vs User Data

These are often confused:

| | AMI | Launch Template | User Data |
|---|---|---|---|
| Contains actual data | Yes (OS + apps as EBS snapshots) | No (just configuration pointers) | No (just a script) |
| What it is | Full disk image (EBS snapshots) | Saved EC2 launch configuration | Startup bootstrap script |
| Use case | Pre-baked environment, fast launch | Standardize EC2 launch settings | Automate first-boot setup |
| When to use | Need identical instances fast, large software pre-installed | Consistency in launch settings across team/ASG | Small config changes on first boot |

**AMI = data**. Launch Template = settings (references an AMI but contains no data itself). User Data = script (runs at boot on the instance).

---

## AMI Encryption

- AMIs can be **encrypted** — the underlying EBS snapshots are encrypted
- You can copy an unencrypted AMI → encrypted AMI (encryption enabled during the copy)
- You **cannot** copy an encrypted AMI → unencrypted AMI (security safeguard, one-way only)
- An encrypted AMI can only be used by accounts/roles with access to the KMS key used for encryption

---

## Sharing AMIs

- By default, AMIs are private (only your account)
- You can share an AMI with specific AWS account IDs
- You can make an AMI public (visible to all AWS customers)
- If an AMI uses an encrypted EBS snapshot, you must also share the KMS key with the other account

---

## Key Points / Exam Tips

- **AMIs are Region-specific** — must copy to use in another Region
- **AMI = full disk snapshot** — includes OS, apps, data; much more than just configuration
- **Launch Template ≠ AMI** — template is just a saved set of launch settings (points to an AMI but has no data itself)
- **Fast DR recovery pattern**: Create AMI → Copy to DR Region → Launch from AMI in DR Region when needed
- **Encrypted to unencrypted copy is NOT allowed** — one-way only

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Launch identical EC2 instances, pre-configured" | Custom AMI |
| "Use in a different Region for DR" | Copy AMI to target Region first |
| "Multi-Region failover with fast RTO" | AMI pre-copied to DR Region |
| "AMI contains actual software and data" | Yes — EBS snapshots of OS + applications |
| "Launch Template is an AMI" | No — it's just configuration settings (no data) |
| "Unencrypted AMI → encrypted" | Allowed (copy with encryption enabled) |
| "Encrypted AMI → unencrypted" | NOT allowed |
