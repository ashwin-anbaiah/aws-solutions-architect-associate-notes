# Amazon EC2 Overview

## What Is EC2?

**Amazon EC2 (Elastic Compute Cloud)** is AWS's virtual machine service. You rent a virtual server (an EC2 instance) in the cloud instead of owning physical hardware. AWS handles the physical infrastructure; you handle the OS, applications, and everything above it.

EC2 is the foundation of most AWS architectures — many services run on top of EC2 or alongside it.

---

## Where EC2 Instances Run

```
AWS Account
└── Region (e.g., ap-south-1 — Mumbai)
    └── Availability Zone (e.g., ap-south-1a)
        └── VPC (Virtual Private Cloud)
            └── Subnet
                └── EC2 Instance
                    ├── EBS Volume (root, + optional data volumes)
                    └── Security Group (firewall)
```

EC2 instances are **tied to a specific AZ**. To get high availability, you run instances across multiple AZs.

---

## EC2 Configuration Options

When you launch an EC2 instance, you configure:

| Setting | What It Controls | Configured Via |
|---|---|---|
| **Name** | Instance label (optional) | Tags (key: Name) |
| **Operating System** | Linux, Windows, macOS | AMI (Amazon Machine Image) |
| **CPU + Memory** | How much compute you need | Instance Type |
| **Login Credentials** | How you SSH/RDP into the instance | SSH Key Pair |
| **Network + IP** | Which VPC/subnet, public/private IP | VPC settings |
| **Storage** | Root volume + data volumes | EBS, Instance Store |
| **Firewall** | Inbound/outbound traffic rules | Security Group |
| **Purchasing Option** | On-Demand vs Spot vs Reserved | Pricing model selection |
| **Tenancy** | Shared hardware vs dedicated | VPC/instance tenancy |
| **Startup Scripts** | Auto-configure on first boot | User Data |
| **AWS Permissions** | What AWS services this instance can call | IAM Role |

---

## Ways to Connect to an EC2 Instance

| Method | OS | Requirements |
|---|---|---|
| **SSH Client** (Terminal/PuTTY) | Linux | Port 22 open, .pem key file |
| **RDP Client** | Windows | Port 3389 open, admin password (decrypted via key) |
| **EC2 Instance Connect** (browser) | Linux | Port 22 open, IAM permission `ec2-instance-connect:SendSSHPublicKey` |
| **AWS Session Manager (SSM)** | Linux + Windows | SSM Agent installed, no open ports needed |
| **EC2 Instance Connect Endpoint** | Linux (private subnet) | For instances with no public IP |

**EC2 Instance Connect** uses temporary SSH keys pushed via IAM — no long-term key management. More secure than traditional SSH for console-accessible instances.

**Session Manager** is the most secure option: no inbound ports, no key management, fully audited via CloudTrail.

---

## EC2 States

| State | Description |
|---|---|
| **Pending** | Starting up |
| **Running** | Active, being billed by the second |
| **Stopping** | Shutting down (gracefully) |
| **Stopped** | Off — EBS data persists, no compute charge (still charged for EBS) |
| **Shutting-down** | Terminating |
| **Terminated** | Deleted — cannot be recovered |

**Important**: Stopping then starting an instance will **change its public IP** (unless you use an Elastic IP). The **private IP stays the same**.

---

## EC2 vs Containers vs Serverless

| | EC2 | ECS/EKS (Containers) | Lambda (Serverless) |
|---|---|---|---|
| Model | IaaS — you manage OS upward | PaaS/CaaS — you manage app | FaaS — you manage only code |
| Startup time | Minutes | Seconds | Milliseconds |
| State | Persistent | Ephemeral (configurable) | Ephemeral |
| Max runtime | Unlimited | Unlimited | 15 minutes |
| Best for | Full control, legacy apps, databases | Microservices, containerized workloads | Event-driven, short functions |

---

## Key Points / Exam Tips

- EC2 is **tied to a specific AZ** — for HA, deploy across multiple AZs
- **Stop/Start changes the public IP** — use Elastic IP for a static public IP
- **Terminate = permanent** — instance and root EBS volume deleted (by default)
- **IAM role on EC2** is the right way to give the instance AWS service access — not access keys
- **SSH vs EC2 Instance Connect vs Session Manager**: SSH requires key files + open ports; Instance Connect uses temporary keys; Session Manager needs no open ports

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Virtual machine on AWS" | EC2 |
| "Full OS control, customize everything" | EC2 (IaaS) |
| "Public IP changes after stop/start" | Need Elastic IP for static public address |
| "No inbound ports, no key management, browser SSH" | AWS Session Manager (SSM) |
| "Launch multiple identical servers fast" | Custom AMI → launch from it |
