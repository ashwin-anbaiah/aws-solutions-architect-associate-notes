# Amazon ECR (Elastic Container Registry)

## What Is ECR?

- **Amazon ECR** — fully managed container image registry for storing, managing, and deploying Docker container images.
- Tightly integrated with **Amazon ECS** and **Amazon EKS** for seamless image pulls during deployment.

## Key Features

- **Private repositories** by default; also supports **public repositories** (ECR Public Gallery).
- **Image versioning** using tags (e.g., `latest`, `v1.2.3`) and immutable digests (SHA256 hash).
- **IAM-based access control** — push/pull access governed by IAM policies and repository policies.
- **Encryption** — images encrypted at rest (AES-256 via KMS) and in transit (TLS) automatically.
- **Vulnerability scanning** — integrates with **Amazon Inspector** to scan images for known CVEs (Common Vulnerabilities and Exposures).
- **Lifecycle policies** — automatically delete old or unused image tags to control storage cost.
- **Cross-region and cross-account replication** — replicate images to other regions or accounts for DR and global deployments.

## How ECR Fits in the Container Workflow

```
Developer → docker build → docker push → ECR Repository
                                              ↓
                              ECS Task Definition / EKS Pod spec
                                    references ECR image URI
                                              ↓
                              ECS/EKS pulls image → runs container
```

## ECR Authentication

- Use `aws ecr get-login-password` to get a temporary auth token, then `docker login` with it.
- ECS and EKS automatically handle ECR authentication using the associated IAM role — no manual login needed in production.

---

## Key Points / Exam Tips

- **Trigger:** "private container image registry on AWS" → **Amazon ECR**
- **Trigger:** "scan container images for vulnerabilities" → **ECR + Amazon Inspector**
- ECR is analogous to Docker Hub but **private, AWS-managed, and IAM-governed**
- Lifecycle policies help reduce storage costs by cleaning up old/untagged images
- For ECS, the **EC2 Instance Profile** role must have `ecr:GetAuthorizationToken` and `ecr:BatchGetImage` permissions to pull images
- ECR supports both **mutable** (default — tags can be overwritten) and **immutable** (tags are locked — recommended for prod) tag settings
- Cross-region replication: configure ECR replication rules to keep images in sync across regions for low-latency pulls
