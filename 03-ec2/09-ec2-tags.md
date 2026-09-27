# EC2 Tags

## What Are Tags?

**Tags** are key-value pairs that you can attach to AWS resources — EC2 instances, EBS volumes, S3 buckets, RDS databases, and most other AWS resources. Tags are metadata that help you organize, manage, and report on your resources.

```
Key: Name          Value: WebServer-Prod-1
Key: Owner         Value: TeamAlpha
Key: Environment   Value: Production
Key: Project       Value: SpaceMission
Key: CostCenter    Value: CC-1042
```

---

## Why Tags Matter

Tags have practical uses across several dimensions:

### 1. Organization and Filtering

Find all resources belonging to a specific project or team without manually clicking through the console. In the EC2 console, you can filter instances by tag values.

### 2. Cost Allocation

Enable **Cost Allocation Tags** in the billing console to break down AWS costs by tag values:
- See how much each project is spending
- See how much each environment (dev vs prod) costs
- Attribute costs to specific teams or cost centers

This is how finance teams track cloud spend per business unit.

### 3. IAM Policy-Based Access Control

Use tags in IAM policy conditions to restrict access:
- Developers can only start/stop EC2 instances tagged `Environment=Development`
- Prevents accidental modification of production resources

```json
{
  "Effect": "Allow",
  "Action": ["ec2:StartInstances", "ec2:StopInstances"],
  "Resource": "*",
  "Condition": {
    "StringEquals": {"ec2:ResourceTag/Environment": "Development"}
  }
}
```

### 4. Automation and Deployment Tools

AWS services like CodeDeploy, CodePipeline, and Systems Manager Patch Manager use tags to target specific instances:
- "Apply this patch to all instances tagged `PatchGroup=WebServers`"
- "Deploy to all instances tagged `App=OrderService` and `Environment=Staging`"

### 5. Resource Groups

AWS Resource Groups lets you create logical groups of resources based on tag criteria — useful for monitoring, automation, and access control across multiple resource types at once.

---

## Tag Limits and Rules

| Limit | Value |
|---|---|
| Maximum tags per resource | 50 |
| Key maximum length | 128 characters |
| Value maximum length | 256 characters |
| Key prefix restriction | Cannot start with `aws:` (reserved for AWS) |
| Case sensitivity | Keys and values ARE case-sensitive |

---

## The "Name" Tag

The `Name` tag is special: AWS uses it as the display name in the console. Without a `Name` tag, your instances show as their instance ID (like `i-0abc123def456789`) — with a Name tag, they show the value (like "WebServer-1").

The Name tag has no functional significance — it's purely cosmetic for the console.

---

## Tagging Best Practices

1. **Define a tagging standard** early — agree on tag names, values, and requirements across the team
2. **Enforce tagging** using AWS Config rules or Service Control Policies (prevent launching resources without required tags)
3. **Include at minimum**: Name, Environment, Owner/Team, Project, CostCenter
4. **Be consistent with casing** — `Environment` and `environment` are different keys

---

## Key Points / Exam Tips

- Tags are **case-sensitive** — `Environment` and `environment` are different tags
- **Up to 50 tags per resource**
- Tags are the primary mechanism for **cost allocation** and **attribute-based access control** in IAM
- Tags do NOT affect performance, availability, or routing — they are purely metadata
- **Cost Allocation Tags** must be activated in the billing console to appear in cost reports

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Break down costs by project/team" | Cost Allocation Tags + AWS Cost Explorer |
| "Allow developers to only touch dev instances" | IAM policy condition on ec2:ResourceTag |
| "Target specific instances for patching" | Tags + Systems Manager Patch Manager |
| "Organize resources into logical groups" | Tags + AWS Resource Groups |
| "Instance shows with friendly name in console" | Name tag |
