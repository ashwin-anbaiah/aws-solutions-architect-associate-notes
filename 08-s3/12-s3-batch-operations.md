# S3 Batch Operations

## What is S3 Batch Operations?

**S3 Batch Operations** allows you to perform bulk operations on millions or billions of S3 objects with a single managed job. Instead of writing a custom script to loop through objects, you submit a job and S3 handles all the orchestration, retries, and reporting.

---

## How It Works

```
Step 1: Select Objects (create manifest)
    ↓
Step 2: Choose an Operation
    ↓
Step 3: Submit / Start the Job
    ↓
Step 4: Monitor Progress
    ↓
Step 5: Review Completion Report (output to S3)
```

---

## Manifest File

The manifest tells S3 Batch Operations which objects to process.

| Manifest Source | Description |
|---|---|
| **S3 Inventory report** | Auto-generated periodic report of all objects — easiest for full-bucket operations |
| **Custom CSV file** | You specify exact bucket + key combinations |
| **Generated object list** | New option: specify source + filters directly in the console (no manual CSV) |

---

## Supported Operations

| Operation | Description |
|---|---|
| **Copy objects** | Copy across buckets or within the same bucket (can also re-encrypt during copy) |
| **Replace object tags** | Bulk update or delete tags on millions of objects |
| **Modify ACLs** | Bulk ACL changes |
| **Restore Glacier objects** | Trigger Glacier restoration at scale |
| **Initiate Object Lock retention changes** | Bulk update Object Lock settings |
| **Invoke Lambda function** | Run custom processing on each object (most flexible option) |

---

## Key Features

- **Fully managed** — S3 handles retry logic, failure recovery, and progress tracking
- **Detailed completion report** — output stored in S3; includes per-object success/failure status
- **IAM role required** — Batch Operations uses an IAM role with permissions to read source objects and execute the operation
- **Progress tracking** — real-time job progress in the console

---

## Common Use Cases

| Use Case | Operation |
|---|---|
| Bulk re-encrypt objects with a new KMS key | Copy (with new SSE-KMS key) |
| Add compliance tags to all existing objects | Replace object tags |
| Restore archived Glacier objects for migration | Restore Glacier objects |
| Apply object lock to all existing objects | Initiate Object Lock changes |
| Custom metadata cleanup or transformation | Invoke Lambda per object |
| Large-scale migration between buckets or accounts | Copy objects |

---

## Key Points / Exam Tips

- S3 Batch Operations = bulk processing for **millions/billions** of objects — not for real-time per-event processing
- The **manifest** is the list of target objects — use S3 Inventory for full-bucket operations
- Use **Invoke Lambda** for custom logic that S3 natively doesn't support
- Jobs include **automatic retry** on failures — no custom error handling needed
- Output report goes to a separate S3 bucket with per-object success/failure details
- To re-encrypt objects with a new KMS key, use Batch Operations **Copy** (there is no in-place re-encrypt operation)
- Batch Operations is the right approach for one-time large-scale changes (vs. Lifecycle for ongoing transitions)

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Bulk operation on millions of existing S3 objects" | S3 Batch Operations |
| "Re-encrypt all objects with a new KMS key" | S3 Batch Operations (Copy operation with new SSE-KMS) |
| "Replicate pre-existing objects that weren't covered by replication rule" | S3 Batch Replication |
| "Run custom Lambda on every object in a bucket" | S3 Batch Operations → Invoke Lambda |
| "Bulk restore archived objects from Glacier" | S3 Batch Operations → Restore Glacier objects |
| "Add retention lock to all existing objects" | S3 Batch Operations → Object Lock changes |
