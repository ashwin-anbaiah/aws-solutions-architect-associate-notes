# Amazon QuickSight

## What Is QuickSight?

- **Amazon QuickSight** — serverless, machine learning-powered **Business Intelligence (BI) service** for creating interactive dashboards and visualizations.
- Fast, automatically scalable, embeddable, with **per-session pricing**.
- No servers to manage — fully serverless.

## Data Sources

QuickSight connects to a wide range of data sources:
- **AWS services:** Amazon RDS, Aurora, Athena, S3, Redshift, OpenSearch
- **File uploads:** CSV, XLS, JSON (direct upload)
- **Third-party:** Salesforce, Jira, Teradata, and more

## SPICE (Super-fast Parallel In-memory Calculation Engine)

- **SPICE** — QuickSight's in-memory data engine that accelerates dashboard performance.
- Data stored in SPICE is a **snapshot** of the source data at a point in time.
- Refresh SPICE data on a **schedule** (daily, hourly) or via event-driven approach to get latest data.
- No need to provision or manage SPICE infrastructure.

## Users, Groups, and Sharing

- QuickSight has its own **users and groups** — separate from AWS IAM users.
- **Enterprise edition** supports User Groups.
- Share **analyses** or **dashboards** with individual users or groups.
- Shared dashboard users can **view and interact** but cannot edit the underlying analysis.
- Users see visualized data — no direct access to raw underlying data sources.

## Row-Level Security (RLS)

- Supported in **Enterprise edition**.
- Define rules that restrict which rows of data each user or group can see.
- Example: `Alice (EMEA) → sees only EMEA revenue data`; `Bob (APAC) → sees only APAC revenue data`.

## Common Use Cases

- Executive dashboards for business KPIs
- Sales and revenue reporting
- Operational metrics visualization
- Ad-hoc data exploration from S3/Athena

---

## Key Points / Exam Tips

- **Trigger:** "BI dashboards, visualizations, business intelligence" → **Amazon QuickSight**
- **Trigger:** "visualize Athena query results" → **QuickSight + Athena**
- **Trigger:** "per-user access control on dashboard data" → **QuickSight Row-Level Security (RLS)**
- QuickSight is **serverless** — no server setup required
- SPICE stores a **snapshot** — not live data; must refresh to get latest values
- QuickSight users/groups are **independent of IAM** — managed within QuickSight
- Shared dashboard viewers **cannot edit** the analysis — view-only access
