# Amazon ECS (Elastic Container Service)

## What Is ECS?

- **Amazon ECS** — fully managed container orchestration service to run and manage Docker containers at scale.
- Supports two compute (data plane) options:
  - **EC2 launch type** — you manage the EC2 instances; supports Spot instances for cost savings
  - **Fargate launch type** — AWS manages the infrastructure; serverless
- Integrates natively with ALB/NLB, API Gateway, EventBridge, CloudWatch, and Auto Scaling.

## Core Concepts

### ECS Task
- A single running instance of a **task definition**.
- **Task definition** — a JSON blueprint specifying container image(s), CPU, memory, ports, environment variables, and IAM role.
- Key task definition fields: `family`, `networkMode`, `requiresCompatibilities`, `cpu`, `memory`, `taskRoleArn`, `containerDefinitions`.

### ECS Service
- Runs and maintains a **specified number of tasks** continuously.
- Automatically replaces failed tasks to maintain desired count.
- Integrates with **Application Load Balancer** or **Network Load Balancer** for traffic distribution.

## IAM Roles for ECS

ECS uses two distinct IAM role types:

| Role | Used By | Purpose |
|---|---|---|
| **EC2 Instance Profile** | ECS Agent (EC2 launch type only) | Pull images from ECR, send logs to CloudWatch, access Secrets Manager / SSM |
| **ECS Task Role** (`taskRoleArn`) | Individual ECS tasks | Grant tasks access to AWS services (e.g., S3, DynamoDB) — defined per task definition |

- Always prefer **Task Roles** over embedding credentials in environment variables.

## Persistent Storage for ECS Tasks

- **Amazon EFS** is the recommended persistent shared storage for ECS.
- Mounted via the task definition; accessible from both EC2 and Fargate launch types.
- **Multi-AZ**: EFS supports access from multiple AZs simultaneously.
- Use cases: shared config files, user-uploaded files, stateful Fargate workloads.

## ECS Service Auto Scaling

- Uses **AWS Application Auto Scaling** — scales tasks (not instances).
- ECS publishes CloudWatch metrics (CPU %, memory %) that drive scaling.
- Scaling policies:

| Policy Type | How it works |
|---|---|
| **Target Tracking** | Keep a metric (e.g., CPU at 50%) at a target; like a thermostat |
| **Step Scaling** | Scale in predefined steps based on CloudWatch alarm thresholds |
| **Scheduled Scaling** | Scale at specific times (e.g., more capacity every morning) |
| **Predictive Scaling** | Uses ML forecasting to scale ahead of expected traffic spikes |

- **EC2 launch type:** pair Service Auto Scaling with an **EC2 Capacity Provider** backed by an ASG to scale EC2 instances automatically.
- **Fargate launch type:** AWS scales underlying compute automatically.

## Common ECS Architecture Patterns

- **Backend services behind ALB** — ECS Service A and B each with Tasks, fronted by an Elastic Load Balancer
- **API backend via API Gateway** — API Gateway routes to ECS tasks
- **Event-driven processing** — EventBridge triggers an ECS task when a file lands in S3
- **Batch scaling from SQS** — ECS tasks poll an SQS queue; CloudWatch `ApproximateNumberOfMessagesVisible` metric drives auto scaling

---

## Key Points / Exam Tips

- **Trigger:** "run containers on AWS without managing Kubernetes" → **ECS**
- **Trigger:** "run ECS without managing EC2 instances" → **ECS + Fargate**
- **Trigger:** "shared persistent storage for ECS containers" → **Amazon EFS**
- **Trigger:** "scale ECS based on SQS queue depth" → ECS Auto Scaling + CloudWatch `ApproximateNumberOfMessagesVisible`
- EC2 Instance Profile → used by the ECS **agent**; Task Role → used by the **application code inside the container**
- Dynamic port mapping is supported with **ALB** — multiple tasks can run on the same EC2 instance on different host ports
- ECS on Fargate always uses **`awsvpc` network mode** — each task gets its own ENI and security group
