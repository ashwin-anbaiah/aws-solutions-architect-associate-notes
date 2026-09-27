# CloudFormation Features: Nested Stacks, StackSets, Change Sets, Drift Detection

## Nested Stacks

A **parent (root) stack** creates and manages one or more **child (nested) stacks** as resources.

- Use the `AWS::CloudFormation::Stack` resource type to embed a child stack
- Useful for **modularity and reuse** — split large templates into smaller, purpose-built templates
- Common pattern: separate stacks for Networking, Compute, Database layers
- Child stacks are created and updated by the parent; deletion of the parent deletes children
- **Template size limit** workaround — break one big template into several nested templates

**Key distinction:**
- **Nested Stacks** = modular templates within **one account/region**
- **StackSets** = same template across **many accounts/regions**

## StackSets

**StackSets** let you deploy the same CloudFormation stack to **multiple AWS accounts and multiple Regions** from a single administrator account.

- Commonly used with **AWS Organizations** for centralized governance
- Creates **stack instances** in each target account/region combination
- Updates to a StackSet automatically roll out to all stack instances
- Supports **drift detection** across all member accounts
- Provides **failure tolerance** and **deployment order** controls
- Use cases: IAM roles, AWS Config rules, security baselines, CloudTrail trails

```
Administrator Account (StackSet)
    ├── Account A / Region 1  →  Stack
    ├── Account B / Region 1  →  Stack
    ├── Account C / Region 2  →  Stack
    └── Account D / Region 2  →  Stack
```

## Change Sets

**Change sets** let you preview the impact of a stack update **before executing it**.

- Show which resources will be **Added**, **Modified**, or **Deleted**
- Help avoid unintended resource replacement or data loss
- Changes are **not applied** until you explicitly execute the change set

**Workflow:**
```
Current Template  -->  Submit Updated Template  -->  Review Change Set  -->  Execute
```

- A single stack can have multiple change sets; only one can be executed
- If a resource replacement would occur (e.g., renaming an RDS instance), the change set will flag it clearly

## Drift Detection

**Drift detection** identifies manual changes made to resources **outside of CloudFormation**.

- Compares **actual resource configuration** with what the CloudFormation template specifies
- Each resource is marked **IN_SYNC** (no drift) or **MODIFIED** (drifted)
- Helps enforce IaC discipline and detect unauthorized/manual changes

| Feature | Purpose |
|---|---|
| **Change Sets** | Review impact *before* applying stack updates |
| **Drift Detection** | Identify resources modified *outside* CloudFormation |

## Automatic Rollback and Deletion Policy

### Rollback

- Automatically triggered if stack **creation or update fails**
- CloudFormation attempts to return the stack to its **last known stable state**
- On creation failure: all successfully created resources are **deleted** (by default)
- You can **disable rollback** during stack creation (useful for troubleshooting failures)

### DeletionPolicy

Controls what happens to a resource when the stack is deleted or a resource is removed:

| Policy | Behavior |
|---|---|
| `Delete` | Default — resource is deleted with the stack |
| `Retain` | Resource is kept after stack deletion (prevent data loss) |
| `Snapshot` | Creates a snapshot first (supported: RDS, EBS, ElastiCache, Redshift) |

```yaml
Resources:
  MyDB:
    Type: AWS::RDS::DBInstance
    DeletionPolicy: Retain
```

## CloudFormation IAM Service Role

- By default, CloudFormation uses the permissions of the **IAM user/role** that launches the stack
- You can assign CloudFormation a dedicated **IAM Service Role** for least-privilege deployments
- The user launching the stack needs **`iam:PassRole`** to hand the service role to CloudFormation
- If CloudFormation must create resources with embedded IAM roles, the service role also needs `iam:PassRole`

## Key Points / Exam Tips

- Nested stacks = **modular** (same account/region); StackSets = **multi-account/region**
- StackSets require an **administrator account** and integrate with **AWS Organizations**
- Change sets are **read-only previews** — they do nothing until you execute them
- Drift detection is **manual** (you trigger it) or can be scheduled
- `DeletionPolicy: Retain` is the exam answer when you need to preserve data on stack deletion
- `DeletionPolicy: Snapshot` is for databases (RDS, Redshift) when you want a backup before deletion
- Rollback can be **disabled** during creation for debugging — not recommended for production

## Trigger Words

| Keyword | Think |
|---|---|
| "Same template across multiple accounts" | StackSets |
| "Reusable modular templates" | Nested Stacks |
| "Preview before updating stack" | Change Sets |
| "Manual changes detected outside IaC" | Drift Detection |
| "Preserve RDS on stack delete" | `DeletionPolicy: Snapshot` or `Retain` |
| "Stack creation failed, keep resources for debugging" | Disable Rollback |
