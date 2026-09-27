# AWS CloudFormation Overview

## What is CloudFormation?

**AWS CloudFormation** is AWS's native **Infrastructure as Code (IaC)** service. You describe your desired infrastructure in a **JSON or YAML template**, and CloudFormation provisions, manages, and tears it all down as a single unit called a **stack**.

> "You say what you want, CloudFormation figures out how to build it — in the right order, possibly in parallel."

## Why Use CloudFormation?

- **No manual errors** — resources are created and deleted automatically from a template
- **Group lifecycle** — resources live and die together as a stack; no orphaned "ghost" resources
- **Parallel provisioning** — independent resources are created simultaneously (faster deployments)
- **Reusable & portable** — same template deploys across multiple regions/accounts with minimal changes
- **Version-controlled** — templates stored in Git for rollback and history
- **Visual authoring** — **Infrastructure Composer** lets you drag-and-drop to build templates graphically

## How CloudFormation Works

```
Template (JSON/YAML)  -->  CloudFormation  -->  Stack (AWS Resources)
```

1. Author a template (locally or using Infrastructure Composer)
2. Upload template to S3 (or directly via console/CLI)
3. CloudFormation creates a **stack** from the template
4. Resources are created in dependency order; independent ones in parallel
5. On failure, automatic rollback to last known stable state

## Stacks

- A **stack** is a collection of AWS resources managed as a single unit
- Create, update, or delete all resources together
- Stack updates are applied by submitting a new or modified template

## Key Points / Exam Tips

- CloudFormation is **declarative** — you define the *desired state*, not the steps
- **Resources** section is the only **mandatory** section in a template
- CloudFormation uses the **IAM permissions of the user/role** that launches the stack, OR you can assign it a **CloudFormation Service Role** via `iam:PassRole`
- The `iam:PassRole` permission must be granted to the IAM principal passing a service role to CloudFormation
- If CloudFormation needs to create resources with IAM roles (e.g., EC2 with instance profile), the service role must also include `iam:PassRole`
- CloudFormation is free; you pay only for the AWS resources it creates

## Trigger Words

| Keyword | Think |
|---|---|
| "Infrastructure as Code" | CloudFormation |
| "Declarative provisioning" | CloudFormation |
| "Template / Stack" | CloudFormation |
| "JSON or YAML template" | CloudFormation |
| "Provision resources consistently across accounts" | CloudFormation / StackSets |
| "Infrastructure Composer" | Visual CloudFormation template builder |
