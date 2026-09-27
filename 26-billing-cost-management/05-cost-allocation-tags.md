# Cost Allocation Tags and Data Export

## What Are Cost Allocation Tags?

**Cost Allocation Tags** are key-value labels you attach to AWS resources. Once activated in the Billing console, they appear as filterable dimensions in Cost Explorer, Cost and Usage Reports (CUR), and Budgets — letting you break down costs by project, team, environment, owner, or any dimension that matters to your business.

---

## Types of Cost Allocation Tags

| Type | Who Creates It | Examples |
|---|---|---|
| **User-Defined (Custom) Tags** | You/your team | `Environment=Production`, `Project=DataLake`, `Owner=alice@example.com` |
| **AWS-Generated Tags** | AWS automatically | `aws:createdBy` (IAM principal that created the resource), `aws:cloudformation:stack-name` |

---

## How Cost Allocation Tags Work

### Step 1: Tag Your Resources
Apply tags when creating resources (EC2, RDS, S3, etc.):
```
Key:   Environment
Value: Production
```

### Step 2: Activate Tags in the Billing Console
- Go to **Billing and Cost Management → Cost Allocation Tags**
- Activate the tags you want to track
- **Tags are NOT available for cost tracking until explicitly activated here** — creating the tag on the resource is not enough
- Activation takes up to 24 hours to reflect in billing data

### Step 3: Use Tags in Cost Tools
- Filter Cost Explorer by tag dimension
- Group Cost and Usage Report data by tag
- Create Budgets scoped to a specific tag value

---

## User-Defined vs AWS-Generated

| | User-Defined Tags | AWS-Generated Tags |
|---|---|---|
| Created by | Your team | AWS automatically |
| Format | `user:<key>` in CUR | `aws:<key>` in CUR |
| Example | `user:Environment=Production` | `aws:createdBy=arn:aws:iam::123:user/alice` |
| When available | After you create + activate | After you activate in Billing console |

---

## Cost Categories

**Cost Categories** is a separate but related feature:
- Define custom grouping rules that categorize costs based on tags, accounts, services, or usage types.
- Example: group all EC2 costs tagged `Project=Analytics` and all RDS costs tagged `Project=Analytics` into a single "Analytics" cost category.
- Appears as a filterable dimension in Cost Explorer and CUR.
- Unlike simple tags, Cost Categories can combine multiple rules and hierarchies.

---

## Tagging Best Practices

- Tag **consistently** across all resources from day one — retroactive tagging is painful.
- Use **Tag Policies** (Organizations feature) to enforce capitalization and allowed values.
- Common dimensions to tag: `Environment`, `Project`, `Owner`, `CostCenter`, `Team`.
- Tag **all major resource types**: EC2, RDS, EBS, S3, Lambda, ELB, VPC.
- Use **Tag Editor** in the console to bulk-apply or audit tags across resources.

---

## Key Points / Exam Tips

- Tags must be **activated in the Billing console** before they appear in billing reports — tagging resources alone is not enough.
- **Two types:** user-defined (you create) and AWS-generated (`aws:createdBy` is the most common AWS-generated tag).
- Tags enable granular cost attribution: by project, team, environment, cost center.
- Tag Policies (Organizations) enforce tag key names and allowed values across all accounts.
- Cost Categories are more powerful than simple tags — they can combine multiple dimensions.
- Tags appear in CUR with `user:` prefix for custom tags and `aws:` prefix for AWS-generated tags.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Break down costs by project, team, or environment" | Cost Allocation Tags |
| "Tag EC2 with Environment=Production, see it in Cost Explorer" | Cost Allocation Tags (must activate first) |
| "Who created this resource?" for cost attribution | aws:createdBy (AWS-generated tag) |
| "Enforce tag standardization across all accounts" | Tag Policies (Organizations) |
| "Group costs from multiple services under one label" | Cost Categories |
