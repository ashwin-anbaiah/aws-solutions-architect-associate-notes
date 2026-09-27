# IAM Tools (Access Analyzer, Policy Simulator, Policy Generator)

## Overview

AWS provides three built-in tools to help you work with IAM policies safely and efficiently:
1. **IAM Access Analyzer** — understand who can access your resources
2. **IAM Policy Simulator** — test and debug policies before applying them
3. **IAM Policy Generator** — create valid policy JSON without writing it from scratch

---

## 1. IAM Access Analyzer

**IAM Access Analyzer** continuously monitors IAM policies (both identity-based and resource-based) to help you understand actual access permissions and detect risky or unintended access.

### What It Analyzes

**External Access Findings** — resources that can be accessed by identities outside your account or organization:
- S3 buckets with public or cross-account access
- KMS keys accessible by external accounts
- IAM roles with trust policies allowing external principals

**Internal Access Findings** — which users/roles can access a specific internal resource (e.g., "which roles have access to this KMS key?")

**Unused Access Findings** — helps enforce least privilege:
- **Unused roles** — roles with no activity in a configurable time window
- **Unused access keys** — IAM user keys that haven't been used recently
- **Unused permissions** — specific permissions that are granted but never used

For unused permissions, Access Analyzer can even **recommend new policies** that trim the overpermissioned policy down to only what's actually needed (based on CloudTrail activity).

### Zone of Trust

You define a "zone of trust" (your account or your AWS Organization). Access Analyzer looks for resources accessible from outside this zone — anything outside is flagged as a potential finding.

### Key Uses

- Detect accidentally public S3 buckets before they cause a security incident
- Identify overpermissioned roles and users for least-privilege enforcement
- Validate that cross-account access is intentional, not accidental

---

## 2. IAM Policy Simulator

**IAM Policy Simulator** lets you test IAM policies and simulate whether a specific action would be allowed or denied — without actually performing the action.

### Use Cases

- Test a new policy before attaching it to a user/role (avoid unexpected side effects)
- Troubleshoot `AccessDenied` errors — simulate the failing action to identify which policy is blocking it
- Validate that a policy change won't break existing access

### How to Access

- **Console**: https://policysim.aws.amazon.com/
- **CLI**: `aws iam simulate-principal-policy`

### What You Can Simulate

- Inline policies attached to users/roles
- Managed policies (customer or AWS)
- Resource-based policies (S3 bucket policy, SQS policy)
- Combinations of all of the above (to check the full effective permission)

### Simulation Output

For each action you test, the simulator tells you:
- **Allowed** or **Denied**
- Which specific policy statement caused the decision
- If a condition prevented access

---

## 3. IAM Policy Generator

The **IAM Policy Generator** is a tool that helps you build valid JSON policy documents without writing JSON from scratch.

### Two Ways to Generate Policies

**Method 1: Visual/Graphical Generator** (via AWS Console or the standalone tool)
- Navigate the wizard: select Service → Actions → Resources
- The tool generates valid JSON
- Good for building new policies quickly

**Method 2: Generate Based on CloudTrail Activity**
- IAM Access Analyzer analyzes your CloudTrail events for a specified time period (up to 90 days)
- It generates a policy based on what a specific user or role *actually did* — not what they might need
- This produces a tightly scoped, least-privilege policy based on real usage
- Steps: IAM Console → User or Role → Permissions → Generate Policy → Set time range → Review and customize → Attach

This method is powerful for right-sizing permissions on existing roles where you're not sure what's actually needed.

---

## Tool Comparison Summary

| Tool | Primary Purpose | When to Use |
|---|---|---|
| **Access Analyzer** | Audit and monitoring — find unintended access, detect unused permissions | Ongoing governance, security reviews |
| **Policy Simulator** | Testing — validate before applying, debug access denied errors | Before deploying policy changes, troubleshooting |
| **Policy Generator** | Creation — build new policies without writing JSON from scratch | Writing new policies, right-sizing existing ones |

---

## Key Points / Exam Tips

- **Access Analyzer = "who can access my resources?"** — continuous, automated, flags external and unused access
- **Policy Simulator = "will this work before I apply it?"** — test and debug, no real actions are executed
- **Policy Generator = "help me write the JSON"** — both visual wizard and CloudTrail-based generation
- Access Analyzer can generate least-privilege policies based on actual CloudTrail usage — useful for tightening overpermissioned roles
- Policy Simulator can test resource-based policies too — not just identity-based

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Find public or cross-account access" | IAM Access Analyzer |
| "Find unused permissions/roles" | IAM Access Analyzer (Unused Access findings) |
| "Test a policy before applying" | IAM Policy Simulator |
| "Debug AccessDenied errors" | IAM Policy Simulator |
| "Generate least-privilege policy based on actual usage" | IAM Policy Generator via CloudTrail activity |
| "Build a policy without writing JSON" | IAM Policy Generator (visual wizard) |
