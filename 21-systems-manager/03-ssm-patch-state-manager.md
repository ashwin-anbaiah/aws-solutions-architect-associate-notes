# SSM Patch Manager and State Manager

## SSM Patch Manager

### What is Patch Manager?

**SSM Patch Manager** automates the process of **patching managed instances** with security patches and other software updates.

> "Patch Manager keeps your fleet up to date without logging into every server."

### Key Components

#### Patch Baseline

A **Patch Baseline** defines:
- Which patches to **approve** automatically
- Which patches to **reject** or defer
- Approval criteria (e.g., Critical and Important patches auto-approved after 3 days)

| Baseline Type | Description |
|---|---|
| **AWS-managed baselines** | Pre-built by AWS per OS (e.g., `AWS-AmazonLinux2DefaultPatchBaseline`) |
| **Custom baselines** | You define the approval rules and exceptions |

- Default baselines exist for: Amazon Linux, Amazon Linux 2, Ubuntu, RHEL, SUSE, Windows Server, CentOS
- Custom baselines can override auto-approval delays, filter by CVE severity, or exclude specific packages

#### Patch Group

- A **Patch Group** tags instances to associate them with a specific patch baseline
- Use the tag key `Patch Group` on EC2 instances
- Instances without a Patch Group tag use the default baseline for their OS

#### Maintenance Windows

A **Maintenance Window** defines a scheduled time to run patching operations:

- Schedule: cron or rate expression (e.g., every Tuesday at 2:00 AM UTC)
- Duration and cutoff: maximum window duration; cutoff stops new tasks from starting near the end
- Targets: instances (by tag, resource group, or instance ID)
- Tasks: run patch scan or install using an SSM document (e.g., `AWS-RunPatchBaseline`)

### Patch Operations

| Operation | Description |
|---|---|
| **Scan** | Check for missing patches; report compliance — does NOT install |
| **Install** | Install approved missing patches — may reboot instances |

### Patch Compliance

- After patching, Patch Manager reports compliance status per instance
- Compliance data visible in the SSM console and queryable via AWS Config
- Non-compliant instances can trigger automated remediation

---

## SSM State Manager

### What is State Manager?

**SSM State Manager** ensures that your instances are in and **remain in a desired configuration state** over time.

> "State Manager is like a continuous enforcement system — it applies (and re-applies) a configuration to keep instances in spec."

### How It Works

State Manager uses **Associations** to define and enforce state:

- An **Association** links an SSM Document to target instances with a schedule
- The document defines what to apply (e.g., install an agent, configure a setting)
- State Manager runs the association on a schedule and **re-applies** it if the instance drifts

### Common Use Cases

| Use Case | SSM Document |
|---|---|
| Keep CloudWatch Agent installed and running | `AWS-ConfigureAWSPackage` |
| Enforce SSH daemon configuration | Custom document |
| Bootstrap instances with required software | `AWS-RunShellScript` |
| Keep SSM Agent up to date | `AWS-UpdateSSMAgent` |

### Association Schedule

- Associations run on a **cron or rate schedule**
- Example: re-apply every 30 minutes to detect and fix drift
- On-demand execution is also supported

### State Manager vs Patch Manager

| Aspect | State Manager | Patch Manager |
|---|---|---|
| Purpose | Enforce any configuration state | Specifically for OS/software patching |
| What it applies | Any SSM Document | `AWS-RunPatchBaseline` document |
| Scheduling | Associations with schedules | Maintenance Windows |
| Drift correction | Yes (continuous re-apply) | On scan/install schedule |

## Key Points / Exam Tips

- **Patch Baselines** define *what* to patch; **Maintenance Windows** define *when*
- **Patch Group** tag (`Patch Group: Production`) links instances to a specific patch baseline
- **Scan** operation = assessment only, no changes; **Install** = applies patches (may reboot)
- **State Manager** = continuous configuration compliance, not just one-time application
- State Manager associations use SSM Documents and run on a **schedule**
- Both Patch Manager and State Manager are better exam answers than "log in and run yum update"

## Trigger Words

| Keyword | Think |
|---|---|
| "Automate OS patching at scale" | Patch Manager |
| "Schedule patches for Tuesday nights" | Maintenance Windows |
| "Different patches for dev vs prod" | Patch Groups + custom baselines |
| "Ensure CloudWatch agent always installed" | State Manager Association |
| "Continuously enforce configuration" | State Manager |
| "Patch compliance report" | Patch Manager compliance |
