# AWS Pricing Calculator

## What Is the AWS Pricing Calculator?

The **AWS Pricing Calculator** (available at [calculator.aws](https://calculator.aws)) is a free web-based tool that lets you estimate the monthly cost of AWS services **before you deploy anything**. It is used in the planning phase — to build a cost estimate for a proposed architecture, compare service options, or create a business case for cloud adoption.

---

## Key Characteristics

- **Pre-deployment tool** — estimates before you spend anything.
- No AWS account required to use it.
- Organized around "estimates" that you can save, share, and export.
- Supports estimating cost for individual services or full architectures.
- Generates a shareable URL or exportable CSV/PDF for proposals and business cases.

---

## What You Can Do With It

- **Estimate a new architecture:** Add each service (EC2, RDS, S3, CloudFront, etc.), configure the expected usage, and see the monthly estimate.
- **Compare options:** Side-by-side cost comparison of Reserved vs On-Demand vs Spot instances.
- **Build a business case:** Export estimates for cloud migration proposals.
- **Group services:** Organize estimates into groups by application, team, or environment.

---

## Pricing Calculator vs Cost Explorer

| Tool | When to Use | What It Does |
|---|---|---|
| **Pricing Calculator** | BEFORE deploying (planning phase) | Estimates future cost based on configured usage |
| **Cost Explorer** | AFTER deploying (analysis phase) | Analyzes actual historical spending and forecasts |

> **Exam rule:** "Estimate cost before deploying" → Pricing Calculator. "Analyze/visualize current or past spend" → Cost Explorer.

---

## Key Points / Exam Tips

- The Pricing Calculator is the **only correct answer** for "estimate costs BEFORE deployment."
- It does NOT connect to your AWS account — no actual spending data, purely hypothetical.
- Cost Explorer, Bills page, and Cost Anomaly Detection all require an active account with actual usage.
- Exportable for presenting to management or finance teams.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Estimate cost before deploying / before going to production" | AWS Pricing Calculator |
| "Build a cost estimate for a new architecture proposal" | AWS Pricing Calculator |
| "Compare Reserved vs On-Demand cost before committing" | AWS Pricing Calculator |
| "How much will this architecture cost per month?" | AWS Pricing Calculator |
