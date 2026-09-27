# AWS Elastic Beanstalk

## What is Elastic Beanstalk?

**AWS Elastic Beanstalk** is a **Platform as a Service (PaaS)**. You upload your application code; Beanstalk automatically provisions and manages the underlying infrastructure (EC2, ALB, Auto Scaling, CloudWatch, RDS connections, etc.).

> "Just upload code — Beanstalk handles the rest."

## What Beanstalk Manages For You

- EC2 instances
- Elastic Load Balancer (ALB/NLB/CLB)
- Auto Scaling Group
- Security Groups
- CloudWatch monitoring and health checks
- Application versioning and rollback

## Supported Platforms

| Application Frameworks | Container Platforms |
|---|---|
| Java SE / Java with Tomcat | Docker (single & multi-container) |
| .NET on Windows Server with IIS | Amazon ECS (multi-container) |
| Node.js, Python, PHP, Ruby, Go | — |

## Environment Types

### Web Server Environment

Handles HTTP/HTTPS request-response traffic.

| Mode | Description |
|---|---|
| **Single-Instance** | One EC2 + Elastic IP; no load balancer; cheapest; for dev/test |
| **Load-Balanced** | ALB + Auto Scaling Group; for production traffic |

### Worker Environment

Handles **background / asynchronous processing**.

- Does **not** receive HTTP traffic from users
- Always paired with an **Amazon SQS queue**
- EC2 instances poll SQS; Beanstalk runs an **SQS daemon (sqsd)** on each worker
- Daemon pulls messages and makes an HTTP POST to `localhost:80` (configurable)
- Auto Scaling based on **SQS queue depth**
- Supports `cron.yaml` for scheduled background jobs
- Failed messages can be retried or sent to a **Dead-Letter Queue (DLQ)**
- Typical use: image processing, email sending, data transformation, batch jobs

## Deployment Methods

| Method | Deploys To | Downtime? | DNS Change? | Rollback |
|---|---|---|---|---|
| **All at Once** | Existing instances | **Yes** | No | Manual redeploy |
| **Rolling** | Existing instances (batches) | No | No | Manual redeploy |
| **Rolling with Additional Batch** | New + existing instances | No | No | Manual redeploy |
| **Immutable** | New instances only | No | No | Terminate new instances |
| **Traffic Splitting** | New instances | No | No | Reroute traffic + terminate |
| **Blue/Green** | New separate environment | No | **Yes (CNAME swap)** | Swap URL back |

### Key Differences

- **All at Once** — fastest but causes downtime; avoid in production
- **Rolling** — no downtime but temporarily reduces capacity during deployment
- **Rolling with Additional Batch** — maintains full capacity throughout (extra cost for batch)
- **Immutable** — safest; new instances are created fresh; easy to terminate on failure
- **Traffic Splitting** — canary-style; gradually shifts traffic percentage to new version
- **Blue/Green** — two completely separate environments; instant rollback by swapping CNAMEs

## Application Versioning

- Each deployment creates an **Application Version**
- Versions can be deployed to different environments (dev, staging, prod)
- Old versions can be re-deployed for rollback

## Key Points / Exam Tips

- Beanstalk is **PaaS** — you manage the **application**, AWS manages the **platform**
- Beanstalk uses **CloudFormation** under the hood to provision resources
- Worker environments always use **SQS** — they never receive direct HTTP traffic
- **Immutable** = safest deployment; no shared infrastructure with old version
- **Blue/Green** = requires **DNS change (CNAME swap)**; fastest rollback
- **All at Once** = only strategy with downtime
- You are still responsible for databases — Beanstalk does not manage RDS internally (use external RDS)
- Beanstalk itself is free; you pay for the underlying AWS resources

## Trigger Words

| Keyword | Think |
|---|---|
| "PaaS, just upload code" | Elastic Beanstalk |
| "Background job processing from SQS" | Elastic Beanstalk Worker Environment |
| "cron.yaml for scheduled tasks" | Elastic Beanstalk Worker |
| "Safest deployment, new instances" | Immutable deployment |
| "Blue/Green with CNAME swap" | Elastic Beanstalk Blue/Green |
| "No downtime, maintain capacity" | Rolling with Additional Batch or Immutable |
