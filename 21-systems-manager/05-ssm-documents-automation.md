# SSM Documents and Automation

## SSM Documents

### What is an SSM Document?

An **SSM Document** (also called a **runbook**) is a JSON or YAML file that defines a series of **actions** for AWS Systems Manager to perform on managed instances or AWS resources.

> "SSM Documents are the instruction sets for SSM — they define what to do, how to do it, and in what order."

### Document Types

| Type | Used By | Purpose |
|---|---|---|
| **Command** | Run Command | Execute shell/PowerShell scripts on instances |
| **Automation** | Automation | Multi-step workflows on AWS resources (not just instances) |
| **Session** | Session Manager | Define session preferences |
| **Package** | Distributor | Install/uninstall software packages |
| **Policy** | State Manager | Define configuration state associations |

### AWS-Managed Documents (Examples)

| Document Name | Description |
|---|---|
| `AWS-RunShellScript` | Run arbitrary shell commands on Linux |
| `AWS-RunPowerShellScript` | Run PowerShell commands on Windows |
| `AWS-UpdateSSMAgent` | Update the SSM Agent version |
| `AWS-ConfigureAWSPackage` | Install/uninstall a package (e.g., CloudWatch Agent) |
| `AWSEC2-PatchLoadBalancerInstance` | Deregister → patch → reboot → re-register from LB |
| `AWS-RunPatchBaseline` | Apply the instance's patch baseline |

### Creating Custom Documents

- Written in **JSON or YAML**
- **Versioned** — each update creates a new version; you can default to any version
- Can be shared across accounts (public or by account ID)
- Reusable across Run Command, Automation, State Manager, Patch Manager

---

## SSM Automation

### What is SSM Automation?

**SSM Automation** runs **multi-step operational workflows** (using Automation-type SSM Documents) against AWS resources — not just commands on instances.

> "Automation documents are runbooks for operational tasks: patch an instance, create an AMI, remediate a Config finding."

### Automation vs Run Command

| Feature | Run Command | Automation |
|---|---|---|
| Target | **Managed instances** | **AWS resources + instances** |
| Use case | Execute scripts | Multi-step workflows |
| Supports branching | No | Yes |
| Supports approvals | No | Yes |
| Supports waiting | No | Yes |
| Example | Run `yum update` on 100 servers | Deregister LB → patch → reboot → register |

### Automation Example: Patching a Load-Balanced Instance

`AWSEC2-PatchLoadBalancerInstance` Automation steps:
1. Deregister the EC2 instance from the load balancer
2. Apply OS patches (`AWS-RunPatchBaseline`)
3. Reboot the instance if required
4. Re-register the instance back to the load balancer

This ensures **zero downtime patching** for individual instances in a load-balanced fleet.

### Automation Triggers

Automation can be invoked via:

| Trigger | Description |
|---|---|
| **Manual** | Initiated by a user from the console or CLI |
| **Maintenance Windows** | Scheduled via SSM Maintenance Windows |
| **State Manager** | Triggered on a schedule via Association |
| **EventBridge** | Triggered by AWS events (e.g., EC2 state change) |
| **AWS Config Remediation** | Auto-remediate non-compliant resources |

### IAM Requirements

- Automation documents run with an **assumed IAM role** (not the calling user's role)
- The IAM role must have permissions for all actions the automation performs
- Called the **Automation service role** or **assume role**

## Key Points / Exam Tips

- **Command documents** = run scripts on instances; **Automation documents** = multi-step workflows on AWS resources
- Automation supports **branching, waiting, approvals, and rollback** — not available in Run Command
- `AWS-RunPatchBaseline` is a Command document; `AWSEC2-PatchLoadBalancerInstance` is an Automation document
- Documents are **versioned** and can be shared across accounts
- Automation can be triggered by **AWS Config Remediation** — the most common exam integration
- Automation needs an IAM role with appropriate permissions — it doesn't inherit calling user permissions

## Trigger Words

| Keyword | Think |
|---|---|
| "Run shell commands on fleet of EC2 instances" | Run Command + `AWS-RunShellScript` |
| "Multi-step workflow with approval gate" | SSM Automation |
| "Auto-remediate Config non-compliant resource" | Config Remediation → SSM Automation |
| "Patch without removing from load balancer" | `AWSEC2-PatchLoadBalancerInstance` |
| "JSON/YAML operational runbook" | SSM Document |
