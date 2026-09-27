# ASG Advanced Features

## Termination Policy

When ASG scales in, it decides which instance to remove first using the **Termination Policy**. Default policy priority:
1. Find AZ with the most instances (balance AZs)
2. Terminate the instance using the **oldest launch template** (or launch configuration, which is older than templates)
3. Tiebreaker: instance closest to the next billing hour

You can customize with policies like: `OldestInstance`, `NewestInstance`, `ClosestToNextInstanceHour`, `AllocationStrategy`.

---

## Cooldown Period

- Prevents over-scaling by pausing new scaling actions for a set duration after a scale-out or scale-in action
- Default: **300 seconds**
- Applies to **Simple Scaling** policies only
- During cooldown, CloudWatch alarms that would trigger scaling are ignored

---

## Instance Refresh

- Allows you to **safely roll out a new Launch Template** version across all instances in the ASG
- ASG replaces instances incrementally — maintains a minimum healthy percentage during the update
- Configurable: minimum healthy percentage, instance warmup time
- Replaces manual "terminate old instances one by one" approach

Example use case: Update AMI (security patch) → create new Launch Template version → trigger Instance Refresh → ASG replaces all instances with the new AMI while keeping the app running.

---

## Lifecycle Hooks

Lifecycle Hooks let you **pause an instance** at specific ASG lifecycle transitions to run custom actions before the instance fully launches or terminates.

| Hook Type | When It Fires | Common Use Cases |
|---|---|---|
| **Launch (Pending:Wait)** | Instance just launched, before entering InService | Install software, configure monitoring agents, register with config management |
| **Terminate (Terminating:Wait)** | Instance is about to be terminated | Drain connections, upload logs, de-register from service discovery |

- The hook keeps the instance in a **Wait** state for up to 2 hours (configurable)
- You can complete the hook early by calling `complete-lifecycle-action`
- Common integration: SNS or SQS notification → Lambda function performs the action → completes the hook

---

## Warm Pools

- A **Warm Pool** is a group of pre-initialized, stopped EC2 instances that are ready to quickly join the ASG when scaling out
- Normal scale-out: launch instance → install software → warm up → InService (takes minutes)
- With Warm Pool: the instance is already initialized — just start it and it enters service faster

Use cases:
- Applications with long bootstrap times
- Workloads needing fast scale-out response
- Reduces scale-out latency significantly

---

## ASG + Spot Instances

| Setting | Description |
|---|---|
| **Base Capacity** | Minimum number of On-Demand instances always running |
| **On-Demand Percentage Above Base** | % of additional instances that should be On-Demand |
| **Spot Pools** | Multiple instance types/sizes eligible as Spot targets |

Best practices:
- Use multiple Spot pools (diversify instance types) to reduce interruption risk
- Enable Lifecycle Hooks to gracefully handle Spot interruptions (2-minute warning)
- Spot instances can reduce cost by up to 90% for flexible workloads

---

## Instance Refresh vs Lifecycle Hooks vs Warm Pools

| Feature | Purpose |
|---|---|
| **Instance Refresh** | Rolling replacement of all instances with a new Launch Template version |
| **Lifecycle Hooks** | Run custom logic when an individual instance launches or terminates |
| **Warm Pools** | Pre-warm instances to reduce scale-out latency |

---

## Key Points / Exam Tips

- **Instance Refresh** is the correct way to apply a new launch template to existing instances — not manual termination
- **Lifecycle Hooks** are for custom scripts/logic, not for health checks or scaling decisions
- Lifecycle Hooks default timeout is 1 hour; max is 48 hours (configurable)
- **Warm Pools** are for reducing launch latency — the exam distinguishes this from just having more instances (warm pool instances are stopped, not running)
- ASG does NOT replace instances just because the launch template changed — **Instance Refresh must be triggered explicitly**
- Spot interruptions give a **2-minute warning** — design Lifecycle Hooks accordingly

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Run a script when instance launches before it serves traffic" | Lifecycle Hook (Pending:Wait) |
| "Upload logs / drain connections before termination" | Lifecycle Hook (Terminating:Wait) |
| "Roll out new AMI/config to all instances without downtime" | Instance Refresh |
| "Reduce time to scale out for app with long bootstrap" | Warm Pools |
| "Cheapest way to run batch workloads in ASG" | Spot instances with diversified pools |
| "Instances replaced oldest first during scale-in" | Default Termination Policy |
