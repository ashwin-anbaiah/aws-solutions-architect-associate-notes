# Containers and Docker Overview

## What Are Containers?

- **Containers** are lightweight virtualization units that package application code, libraries, and dependencies together and run them in isolated processes.
- Key benefits:
  - **Isolated** — own filesystem, process space, and network interface
  - **Lightweight** — no full OS overhead; shares the host kernel
  - **Consistent** — same behavior in dev, staging, and production
  - **Portable** — run on any platform: local machine, physical server, or cloud

## How Containers Work Internally

| Mechanism | Purpose |
|---|---|
| **Namespaces** | Isolate what each container can see and access |
| **Control Groups (cgroups)** | Limit and monitor CPU, memory, and I/O per container |
| **Union Filesystem** | Layer filesystem so containers share common files efficiently |

## Docker

- **Docker** is an open platform for developing, shipping, and running applications as containers.
- **Workflow:**
  1. Write a `Dockerfile` describing the image
  2. `docker build -t myapp .` — builds a container image
  3. `docker run myapp` — runs a container from the image
- **Docker Image** — immutable, portable, packed with app code and dependencies; pushed to a registry
- **Container Registry** — stores and distributes container images; AWS native registry is **Amazon ECR**

## VMs vs Containers vs Serverless

| Dimension | Virtual Machines | Containers | Serverless (Lambda) |
|---|---|---|---|
| OS overhead | Full guest OS per VM | Shared host kernel | None |
| Startup time | Minutes | Seconds | Milliseconds |
| Isolation level | Strong (hypervisor) | Process-level | Function-level |
| Flexibility | Maximum | High | Limited |
| AWS service | EC2 | ECS / EKS + Fargate | Lambda |

## Container Orchestration

When running many containers across many hosts, you need an **orchestrator** to handle:
- Scheduling and placement of containers
- Resource allocation (CPU, memory)
- Health checks and automatic restarts
- Service discovery and networking
- Autoscaling
- Logging and monitoring

**Popular open-source orchestrators:** Kubernetes, Docker Swarm, Apache Mesos, OpenShift

**AWS container orchestration options:**

| Service | What it is |
|---|---|
| **Amazon ECS** | AWS-native container orchestration; simpler, deeply integrated with AWS |
| **Amazon EKS** | Managed Kubernetes; ideal if you already run Kubernetes in production |
| **AWS Fargate** | Serverless compute data plane for ECS or EKS (no EC2 instances to manage) |

---

## Key Points / Exam Tips

- **Trigger:** "package and run app with its dependencies consistently everywhere" → **Container / Docker**
- **Trigger:** "no servers/EC2 to manage for running containers" → **AWS Fargate**
- **Trigger:** "already using Kubernetes at scale" → **EKS**; "want simpler AWS-native setup" → **ECS**
- Containers **share the host OS kernel** — they are NOT full virtual machines
- A **Docker image** is immutable; a **container** is the running instance of that image
- Container images are stored in **registries** — AWS uses **Amazon ECR**
- ECS and EKS both support two compute (data plane) options: **EC2** (you manage) or **Fargate** (AWS manages)
