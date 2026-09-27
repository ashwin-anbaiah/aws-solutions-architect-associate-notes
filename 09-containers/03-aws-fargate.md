# AWS Fargate

## What Is Fargate?

- **AWS Fargate** is a serverless compute engine for running containers on AWS — you define what to run, AWS manages the underlying infrastructure.
- Works with both **Amazon ECS** (tasks) and **Amazon EKS** (pods).
- No EC2 instances to provision, patch, or manage.

## How It Works

1. Define the container image, CPU, and memory requirements in the task/pod definition.
2. Fargate provisions the right amount of compute, runs the containers, and tears it down when done.
3. **Pay-as-you-go** — billed per vCPU and memory used per second.

## Key Characteristics

- **Serverless** — zero infrastructure management; AWS handles patching, scaling, and availability.
- **Networking** — Fargate always uses **`awsvpc` network mode**: each task gets its own Elastic Network Interface (ENI) and security group, making it a first-class VPC citizen.
- **Monitoring** — use **CloudWatch Container Insights** for CPU, memory, network, and disk metrics at the task/container level.
- **Storage** — ephemeral storage up to 200 GB per task; for persistence use **Amazon EFS**.
- **Security** — each task is isolated with its own kernel, CPU, and memory; no cross-task resource sharing.

## Fargate vs EC2 Launch Type

| Dimension | Fargate | EC2 Launch Type |
|---|---|---|
| Infrastructure management | None (AWS managed) | Customer manages EC2 instances |
| Cost model | Per vCPU/memory second | EC2 instance pricing |
| Spot support | Fargate Spot available | EC2 Spot instances via ASG |
| Customization | Limited (no host-level access) | Full OS and host access |
| Startup time | Slightly slower than EC2 | Faster after warm instances |
| Best for | Variable/intermittent workloads, serverless-first | Consistent high-throughput, specific instance types |

---

## Key Points / Exam Tips

- **Trigger:** "run containers without managing servers/EC2" → **AWS Fargate**
- **Trigger:** "fully managed container infrastructure, serverless" → **Fargate**
- Fargate works with **ECS tasks** AND **EKS pods** — it is the data plane, not the control plane
- Fargate always uses **`awsvpc` network mode** — this means security groups apply at the task level
- For persistent/shared data with Fargate: use **Amazon EFS** (not EBS — EBS is EC2-only)
- Monitoring Fargate containers: **CloudWatch Container Insights**
- **Fargate Spot** provides discounted capacity for interruption-tolerant container workloads
