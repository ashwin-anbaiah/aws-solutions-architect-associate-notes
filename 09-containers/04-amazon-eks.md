# Amazon EKS (Elastic Kubernetes Service)

## What Is EKS?

- **Amazon EKS** — fully managed Kubernetes control plane on AWS.
- Runs **vanilla, upstream-certified conformant Kubernetes** — compatible with any standard Kubernetes tool.
- AWS manages the control plane (API server, etcd, scheduler); you manage or optionally delegate the data plane.

## Compute Options (Data Plane)

| Option | Description |
|---|---|
| **EC2 Managed Node Groups** | EKS provisions and manages EC2 instances in a node group; supports Spot instances |
| **EC2 Self-Managed Nodes** | You fully control EC2 instances and join them to the cluster |
| **AWS Fargate** | Serverless; AWS manages the compute; no EC2 nodes to manage |

## Key Concepts

- **Pod** — smallest deployable unit in Kubernetes; contains one or more containers.
- **Node** — EC2 instance (or Fargate profile) that runs Pods.
- **Cluster** — the full Kubernetes environment: control plane + data plane.
- **kubelet** — the agent on each node that communicates with the EKS control plane.
- EKS supports **4 minor versions** of Kubernetes at any time.

## EKS vs ECS

| Dimension | Amazon EKS | Amazon ECS |
|---|---|---|
| Orchestrator | Kubernetes (open-source) | AWS-proprietary |
| Portability | High — works across clouds/on-prem | AWS-specific |
| Complexity | Higher — requires Kubernetes knowledge | Lower — simpler AWS-native setup |
| Best for | Teams already using Kubernetes at scale | Teams wanting simplest AWS container service |
| Data plane | EC2 Managed Nodes or Fargate | EC2 or Fargate |

## EKS Architecture

1. Developer builds image and pushes to **Amazon ECR**.
2. Admin deploys Pods via `kubectl` to **Amazon EKS** control plane.
3. EKS schedules Pods onto EC2 nodes (or Fargate).
4. **kubelet** on each node pulls the image and runs containers using Docker runtime.
5. **Elastic Load Balancer** fronts the service for users.

---

## Key Points / Exam Tips

- **Trigger:** "already running Kubernetes," "Kubernetes-compatible," "multi-cloud portability" → **EKS**
- **Trigger:** "want managed Kubernetes without self-managing the control plane" → **EKS**
- EKS runs **vanilla Kubernetes** — no custom AWS-only orchestration layer
- EKS supports **Spot instances** as worker nodes in managed or self-managed node groups
- Fargate with EKS: no node groups to manage — define a **Fargate Profile** matching Pod selectors
- EKS Anywhere = run EKS control tooling and open-source Kubernetes on **your own on-premises infrastructure**
- Both ECS and EKS use **Amazon ECR** for container image storage
