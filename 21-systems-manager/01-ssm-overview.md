# AWS Systems Manager (SSM) Overview

## What is AWS Systems Manager?

**AWS Systems Manager (SSM)** is an operations management service that provides a unified interface for managing AWS resources and **hybrid infrastructure** (EC2 instances, on-premises servers, VMs).

> "SSM is the control plane for managing your fleet — patch, connect, configure, and automate — without SSH or RDP."

## Supported Operating Systems

- Amazon Linux / Amazon Linux 2 / Amazon Linux 2023
- Windows Server
- macOS
- Ubuntu, RHEL, SUSE, CentOS, and other Linux distributions
- Raspberry Pi OS

## The SSM Agent

**SSM Agent** is the software installed on managed nodes that:
- Communicates with the SSM service over **HTTPS (port 443)**
- Executes instructions sent by SSM
- Reports status back to SSM

**Pre-installed on:**
- Amazon Linux 2, Amazon Linux 2023
- Windows Server AMIs (AWS-provided)
- Ubuntu 16.04+ (AWS-provided)

**For custom AMIs / on-premises**: install the SSM Agent manually.

**Managed Node**: any machine with the SSM Agent installed and proper IAM permissions is called a **managed node**.

## IAM Requirements

For an EC2 instance to appear as a managed node in SSM:

1. Install SSM Agent (pre-installed on Amazon Linux)
2. Attach an IAM role with the `AmazonEC2RoleforSSM` (or `AmazonSSMManagedInstanceCore`) policy
3. The EC2 security group must allow **outbound HTTPS (443)** to SSM endpoints

## SSM Endpoints (Outbound HTTPS Required)

- `ssm.region.amazonaws.com`
- `ssmmessages.region.amazonaws.com`
- `ec2messages.region.amazonaws.com`

(If the instance has no internet access, create **VPC Endpoints** for these services.)

## SSM Feature Summary

| Feature | Purpose |
|---|---|
| **Session Manager** | Secure browser/CLI shell access — no SSH, no bastion |
| **Run Command** | Execute commands across fleet using SSM Documents |
| **Patch Manager** | Automate OS and software patching |
| **State Manager** | Enforce desired configuration state on instances |
| **Parameter Store** | Centralized, secure storage for configuration values and secrets |
| **Documents (Runbooks)** | Define operational actions in JSON/YAML |
| **Automation** | Multi-step operational workflows |
| **Fleet Manager** | Operational overview of your managed instance fleet |
| **Inventory** | Collect software/hardware inventory from managed nodes |

## Key Points / Exam Tips

- SSM works for both **AWS** (EC2) and **on-premises** servers — it's a **hybrid** management solution
- The **SSM Agent** + **IAM role** (not SSH keys or security group inbound rules) enables management
- **No inbound port** needs to be open — only **outbound HTTPS (443)** is required
- For private instances with no internet access: use **VPC Endpoints** for SSM
- `AmazonEC2RoleforSSM` or `AmazonSSMManagedInstanceCore` = standard IAM policy for managed nodes
- SSM is the go-to exam answer for "manage EC2 without SSH" scenarios

## Trigger Words

| Keyword | Think |
|---|---|
| "Manage EC2 without SSH" | AWS Systems Manager |
| "On-premises server management from AWS" | SSM (hybrid) |
| "Patch, connect, configure at scale" | SSM |
| "SSM Agent" | Prerequisite for all SSM features |
| "No bastion host needed" | SSM Session Manager |
