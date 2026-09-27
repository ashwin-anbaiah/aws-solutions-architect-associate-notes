# ECS Anywhere and EKS Anywhere

## Overview

Both services extend AWS container orchestration capabilities to **on-premises infrastructure**, enabling a hybrid container strategy managed from the AWS cloud.

---

## Amazon ECS Anywhere

- Run **Amazon ECS** on your own on-premises servers or virtual machines.
- The **ECS control plane** remains in AWS; an **ECS agent** and **SSM agent** are installed on your on-premises servers.
- On-premises servers register as **external instances** in your ECS cluster.
- AWS **Systems Manager (SSM)** handles secure communication between the on-prem instance and the AWS control plane.

### How It Works
1. Install the ECS agent and SSM agent on the on-premises host.
2. Register the host as an external instance in the ECS cluster.
3. Deploy ECS tasks to external instances using standard ECS APIs and CLI.

### Use Case
- Extend existing ECS workloads to on-premises data centers without refactoring.
- Comply with data residency requirements while using AWS management tooling.

---

## Amazon EKS Anywhere

- Run **Kubernetes clusters** on your own on-premises infrastructure using open-source Kubernetes packaged and validated by AWS.
- AWS provides **EKS Anywhere tooling** to create, manage, and upgrade Kubernetes clusters on-premises.
- Clusters can connect to AWS services:
  - **Amazon ECR** — for container image storage and pulls
  - **Amazon CloudWatch** — for logs and metrics
  - **AWS IAM** — for access control (via IAM Roles for Service Accounts)

### How It Works
1. Use the `eksctl anywhere` CLI to provision a cluster on-premises.
2. AWS-packaged Kubernetes runs on your hardware.
3. Optionally connect to AWS services using Connector agent.

### Use Case
- Organizations with strict data sovereignty needs that must run workloads on-premises.
- Teams wanting consistent Kubernetes tooling and lifecycle management across cloud and on-premises.

---

## ECS Anywhere vs EKS Anywhere

| Dimension | ECS Anywhere | EKS Anywhere |
|---|---|---|
| Orchestrator | AWS ECS (proprietary) | Kubernetes (open-source) |
| Control plane location | AWS cloud | Your on-premises infrastructure |
| Agent required | ECS Agent + SSM Agent | EKS Anywhere tooling |
| AWS integration | Direct (SSM, CloudWatch) | Optional connector to AWS services |

---

## Key Points / Exam Tips

- **Trigger:** "run ECS/EKS workloads on-premises with AWS management" → **ECS Anywhere / EKS Anywhere**
- **Trigger:** "data residency," "on-premises compliance," "hybrid containers" → consider Anywhere options
- ECS Anywhere: control plane is **in AWS**; compute is **on-premises**
- EKS Anywhere: both control plane and compute are **on-premises**; AWS provides tooling and support
- Both options allow use of **Amazon ECR** for image storage
- ECS Anywhere uses **AWS Systems Manager** as the secure communication channel to on-premises nodes
