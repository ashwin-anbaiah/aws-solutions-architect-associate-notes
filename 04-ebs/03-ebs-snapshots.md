# EBS Snapshots

## What Is an EBS Snapshot?

An **EBS Snapshot** is a point-in-time backup of an EBS volume. Snapshots are stored in **Amazon S3** (AWS manages this — you don't see or manage the S3 bucket directly) and are **highly durable and Region-wide** (replicated across AZs within the Region automatically).

You can think of a snapshot as a save point for your disk. If something goes wrong, you restore from the snapshot.

---

## How Snapshots Work

### Incremental Storage

The first snapshot of a volume is a **full copy** of all used blocks. Each subsequent snapshot is **incremental** — only the changed blocks since the last snapshot are stored.

```
Day 1: Full snapshot (100 GB used → snapshot is 100 GB)
Day 2: 10 GB changed → snapshot stores only 10 GB of changes
Day 3: 5 GB changed  → snapshot stores only 5 GB of changes
```

Even though each snapshot is incremental, **each snapshot is independently usable** — you don't need all previous snapshots to restore from day 3. AWS handles the reconstruction automatically.

### Cost Implication

You pay for the **total unique changed data across all snapshots**, not the full volume size per snapshot. Incremental snapshots keep costs manageable.

---

## Creating Snapshots

- **No need to stop the instance** — you can take a live snapshot of a running instance's volume
- For consistency (especially for databases): **pause writes or use the application's quiesce mechanism** before snapshotting, or use filesystem-consistent snapshots (via the volume's filesystem freeze capability)
- Snapshots of encrypted volumes are automatically encrypted

---

## What You Can Do With Snapshots

| Action | Use Case |
|---|---|
| **Create new EBS volume** from snapshot | Restore from backup, clone a volume, move to a different AZ |
| **Copy snapshot to another Region** | DR (disaster recovery), multi-Region deployments |
| **Share snapshot** with other AWS accounts | Provide a disk image to partners/collaborators |
| **Create AMI** from snapshot | Build a machine image from the snapshot of a root volume |
| **Restore to Recycle Bin** | Accidentally deleted snapshot can be recovered if you configured Recycle Bin retention |

---

## Moving EBS Volumes Across AZs

Since EBS volumes are AZ-scoped, to "move" one to a different AZ:
1. Create a snapshot of the volume (snapshot is Region-wide)
2. Create a new EBS volume from the snapshot in the target AZ

```
AZ1: Volume (100 GB, gp3)
         ↓ snapshot
Snapshot (Region-wide, in S3)
         ↓ create new volume
AZ2: New Volume (identical data, any AZ in the Region)
```

---

## Moving EBS Volumes Across Regions

1. Create a snapshot
2. **Copy the snapshot to the target Region** (cross-Region copy)
3. Create a volume from the copied snapshot in the target Region

Snapshots are Region-level objects — the copy action moves them across Regions.

---

## EBS Fast Snapshot Restore (FSR)

**Problem**: Volumes created from snapshots have a "cold start" — the first time each block is accessed, AWS fetches it from S3. This causes high latency on first access for blocks not yet initialized.

**Fast Snapshot Restore (FSR)** solves this: volumes created from FSR-enabled snapshots are **fully initialized at creation time** — all blocks are immediately available at full provisioned performance.

**FSR cost**: Charged per snapshot per AZ enabled in (not per volume created). Use FSR when you need volumes that perform immediately at full speed — e.g., spinning up new instances quickly in an Auto Scaling event.

---

## Amazon Data Lifecycle Manager (DLM)

**Data Lifecycle Manager** automates EBS snapshot creation and retention:
- Define policies: "take a snapshot of all volumes tagged `Backup=daily` every 24 hours"
- Set retention: "keep 7 daily snapshots, then delete older ones"
- Works across instance-level snapshots (snapshot all volumes on an instance at once)

This eliminates manual snapshot management for backup compliance.

---

## Recycle Bin for Snapshots

You can configure a **Recycle Bin** retention rule so that accidentally deleted snapshots are held in the Recycle Bin for a defined period (1 day to 1 year) before permanent deletion. Recovery is one click.

---

## Key Points / Exam Tips

- **Snapshots are incremental** — only changed blocks stored after first full backup
- **Snapshots are Region-wide** (stored in S3 across AZs) — but to use in another Region, you must **copy the snapshot**
- **Snapshots of encrypted volumes are encrypted** — always
- **Use FSR** for volumes that need to perform immediately at full speed after creation
- **DLM** for automated backup policies — no manual snapshot scripting needed
- **To move a volume to another AZ** = snapshot → create new volume in target AZ (there's no direct cross-AZ copy)

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Backup an EBS volume" | EBS Snapshot |
| "Copy data to another AZ" | Snapshot → create new volume in target AZ |
| "Copy data to another Region for DR" | Copy snapshot to target Region |
| "Automate daily backups with retention policy" | Amazon Data Lifecycle Manager (DLM) |
| "Volume created from snapshot, slow first access" | Enable Fast Snapshot Restore (FSR) |
| "Accidentally deleted snapshot, can I recover?" | Yes, if Recycle Bin was configured |
