# AWS Cost Explorer and Bills

## AWS Bills Page

The **Billing Dashboard → Bills** page shows the detailed breakdown of your current and past monthly charges:
- Per service cost breakdown
- Per region cost breakdown
- Total charged to the paying account (for consolidated billing orgs)
- Line-item charges for each service

This is the factual record of what you were charged — not a forecasting or analysis tool.

---

## AWS Cost Explorer

**AWS Cost Explorer** is an interactive visualization and analysis tool for your actual AWS spending. It lets you explore historical costs, identify trends, forecast future spending, and receive savings recommendations.

---

## Cost Explorer Key Features

| Feature | Description |
|---|---|
| **Cost and usage graphs** | Visualize spending over time by service, region, account, tag, or linked account |
| **Filtering and grouping** | Drill down by any dimension (service, region, instance type, usage type, etc.) |
| **Forecasting** | Projects future spending based on historical usage trends (12-month forecast) |
| **Savings Plan recommendations** | Suggests Compute Savings Plans based on your on-demand usage history |
| **Reserved Instance utilization/coverage** | Shows how well you're using existing RIs and coverage gaps |
| **Hourly/resource-level granularity** | Optional; requires enabling in billing preferences |

---

## Cost Explorer vs Billing Dashboard

| | AWS Bills Page | AWS Cost Explorer |
|---|---|---|
| Purpose | Factual record of charges | Analysis, trends, forecasting, recommendations |
| Historical data | All past months | Up to 12 months of history |
| Granularity | Monthly per service | Daily, monthly; hourly with opt-in |
| Recommendations | None | Savings Plans, RI purchase recommendations |
| Forecasting | None | Yes (based on historical trends) |

---

## Cost and Usage Report (CUR) / Data Export

- **Legacy Cost and Usage Report (CUR):** Most granular billing data available — every API call, resource-level charges, usage types, blended/unblended costs.
- Data exported to an **S3 bucket** (specified by you).
- Can be queried with **Amazon Athena**, loaded into **Amazon Redshift**, or visualized in **Amazon QuickSight**.
- **CUR 2.0** (Data Exports) is the modern replacement — same concept, enhanced format.

---

## Free Tier Usage Dashboard

- Available in Billing and Cost Management console → Cost Analysis → Free Tier.
- Shows current month's free tier consumption vs limits for each service.
- Helps avoid unexpected charges when learning or prototyping.

---

## Key Points / Exam Tips

- **Cost Explorer** = analyze and visualize what you've already spent + savings recommendations.
- **Pricing Calculator** = estimate before you deploy (planning phase).
- Cost Explorer's **Savings Plan recommendations** are based on actual on-demand usage history — the more you've used, the better the recommendation.
- **RI utilization/coverage reports** in Cost Explorer show whether purchased RIs are being fully used.
- CUR/Data Exports → S3 → Athena/Redshift = the pattern for custom BI analysis and detailed billing reports.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Visualize and analyze current/past AWS spending" | AWS Cost Explorer |
| "See monthly bill breakdown per service and region" | AWS Bills page |
| "Get Savings Plan purchase recommendations based on usage" | AWS Cost Explorer |
| "Most granular billing data for custom BI analysis" | Cost and Usage Report (CUR) → S3 + Athena |
| "Check if Reserved Instances are being fully utilized" | Cost Explorer RI utilization report |
| "Forecast future spending based on trends" | Cost Explorer (12-month forecast) |
