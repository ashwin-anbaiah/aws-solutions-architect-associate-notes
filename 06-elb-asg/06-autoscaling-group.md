# Auto Scaling Group (ASG)

## What is an ASG?

An **Auto Scaling Group** automatically manages a fleet of EC2 instances, scaling capacity up (scale-out) or down (scale-in) based on demand. It ensures the desired number of healthy instances is always running.

**Analogy:** ASG is like a staffing agency that automatically hires more workers during busy periods and lets them go when business slows down.

---

## Core Concepts

| Concept | Description |
|---|---|
| **Minimum capacity** | Lowest number of instances — never goes below this |
| **Maximum capacity** | Highest number of instances — never goes above this |
| **Desired capacity** | Target number of instances currently running |
| **Launch Template** | Blueprint defining AMI, instance type, key pair, security groups, user data |

---

## ASG and Launch Templates

A **Launch Template** must be attached to an ASG. It defines:
- **Amazon Machine Image (AMI) ID**
- **Instance type** (e.g., t3.medium)
- **Key pair**, security groups
- **User data** (startup scripts)
- IAM instance profile, network settings

Launch Templates support **versioning** — you can update the template and run an Instance Refresh to roll out the new version gradually.

---

## ASG and ELB Integration

- ASG automatically **registers** new instances with the load balancer's Target Group
- ASG automatically **deregisters** terminated instances
- ASG can use **ELB health checks** in addition to EC2 health checks:
  - **EC2 health check** (default): checks if the instance is running (status check)
  - **ELB health check**: checks if the application is responding (HTTP response)
  - If only EC2 health check is used, ASG may keep an instance that the ALB has marked unhealthy (app is broken, OS is fine)

---

## Health Checks and Replacement

- If an instance fails health checks, ASG **terminates** it and launches a replacement
- Health Check Grace Period: ASG waits this long before starting health checks on newly launched instances (default: 300 seconds). This prevents premature termination during bootstrap.

---

## Default Termination Policy

When scaling in, ASG uses this priority order to choose which instance to terminate:
1. Balance AZs — terminate from the AZ with the most instances
2. Within that AZ, use the **oldest launch template** (or launch configuration)
3. Tiebreaker: closest to the next billing hour

---

## On-Demand + Spot Instance Mix

ASG supports a mix of pricing models:
- **Base Capacity**: a fixed minimum of On-Demand instances (always on, predictable cost)
- **On-Demand Percentage Above Base**: additional On-Demand instances above the base
- **Spot Pools**: multiple instance types eligible for Spot bidding — diversify to reduce interruption risk

ASG automatically replaces interrupted Spot instances and uses Lifecycle Hooks to handle graceful shutdowns.

---

## Common Architecture Patterns

**ELB + ASG with Simple Scaling:**
```
CloudWatch Alarm: avg CPU > 80% → Launch EC2
CloudWatch Alarm: avg CPU < 30% → Terminate EC2
ELB distributes traffic across all instances in the ASG
```

**ELB + ASG with Target Tracking:**
```
Target Tracking: keep avg CPU at ~40%
ASG continuously adjusts instance count to meet the target
```

**SQS-based scaling:**
```
Application uploads jobs to SQS
ASG scales based on SQS queue depth (ApproximateNumberOfMessages)
Workers in the ASG poll and process jobs
Spot instances reduce cost for batch workloads
```

---

## Key Points / Exam Tips

- **Configure ASG to use ELB health checks** — otherwise ASG ignores ALB-reported unhealthy instances (EC2 might look fine but app is broken)
- ASG minimum capacity of 2 across 2 AZs = minimum viable HA setup
- **Reserved Instances for ASG baseline**: since minimum capacity instances always run, they are good candidates for RI pricing
- For single-instance apps needing AZ failover: ASG with min=1, max=1, desired=1 + Elastic IP re-attachment script in user data
- ASG does NOT automatically replace instances just because a new launch template version is created — you must trigger an **Instance Refresh**

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Automatically replace failed instances" | ASG with health checks |
| "Scale in/out based on load" | ASG with scaling policies |
| "Always have X instances running" | ASG minimum capacity |
| "ELB ignores instance but ASG keeps it running" | Configure ASG to use ELB health checks |
| "Mix On-Demand and Spot instances" | ASG with mixed instances policy |
| "Rolling update of EC2 instances" | ASG Instance Refresh |
| "Auto-register instances with load balancer" | ASG + Target Group integration |
