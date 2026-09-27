# AWS Lake Formation

## What Is Lake Formation?

- **AWS Lake Formation** — service that makes it easy to **set up, secure, and manage a data lake** in days rather than months.
- Provides **centralized, fine-grained data access control** at the table, column, and row level.
- Builds on the **AWS Glue Data Catalog** to store metadata and enforce permissions.

## The Problem Lake Formation Solves

### Without Lake Formation:
- Users require individual **IAM permissions** to directly access S3 data, Glue catalog, and Athena — multiple permission sets to manage.
- Permissions are at the **S3 object level** (coarse-grained).
- No easy column-level or row-level filtering.

### With Lake Formation:
- Users only need **Lake Formation permissions** — direct S3 access is not required for querying.
- Permissions are at the **database, table, column, and row level** (fine-grained).
- Lake Formation issues **temporary credentials** to analytics services (Athena, EMR) to access data on behalf of the user.

## Access Control Flow

```
Data User → Athena query
    → Lake Formation checks permissions
    → If allowed: issues temp credentials + metadata from Glue Catalog
    → Athena accesses S3 data using those credentials
```

## Key Features

- **Fine-grained access control:** table, column, and row-level permissions.
- **ABAC (Attribute-Based Access Control):** using LF-tags (key-value labels like `department=finance`).
- **Resource-based permissions:** grant SELECT on specific databases or tables.
- **Blueprints and Workflows:** automate data ingestion from RDS, CloudTrail logs, NoSQL databases, S3.
  - A blueprint defines the steps to ingest data (crawl, create catalog, register S3 location).
  - Supports recurring schedules for continuous ingestion.

## Integration with Other Services

- **AWS Glue** — underlying metadata catalog; Glue crawlers populate the catalog.
- **Amazon Athena** — query engine that respects Lake Formation permissions.
- **Amazon Redshift Spectrum** — query S3 via Redshift using Lake Formation access control.
- **Amazon EMR** — big data processing respecting Lake Formation table permissions.

---

## Key Points / Exam Tips

- **Trigger:** "fine-grained access control on data lake," "column-level or row-level security on S3 data," "centralized data governance" → **AWS Lake Formation**
- **Trigger:** "automate data lake setup, blueprints, ingestion workflows" → **Lake Formation**
- Lake Formation is **not a storage service** — data stays in **S3**; Lake Formation is a governance layer
- Lake Formation issues **temporary credentials** — users never directly access S3 (security advantage)
- Lake Formation works on top of the **Glue Data Catalog** — you still need Glue crawlers to populate metadata
- Without Lake Formation: manage separate IAM, Glue, S3 permissions for each user; With Lake Formation: single place to manage all data access
