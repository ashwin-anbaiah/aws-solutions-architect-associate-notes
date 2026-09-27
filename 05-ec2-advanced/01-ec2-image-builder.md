# EC2 Image Builder

## What Is EC2 Image Builder?

**EC2 Image Builder** is a fully managed AWS service that automates the process of creating, testing, maintaining, and distributing **Amazon Machine Images (AMIs)** and container images.

Without Image Builder, creating a golden AMI involves: launching an instance, manually installing software, manually testing it, manually creating an AMI, and manually copying it to other Regions. This is error-prone and hard to version-control.

Image Builder automates the entire pipeline.

---

## The Problem It Solves

Every organization needs a "golden image" — a standardized base AMI with:
- Security patches applied
- Required agents installed (CloudWatch, SSM, antivirus)
- Hardening configurations applied
- Application runtimes pre-installed

Doing this manually is slow, inconsistent, and doesn't stay current. Image Builder creates a repeatable, automated pipeline.

---

## How EC2 Image Builder Works

```
Base AMI (Amazon Linux 2023)
      ↓
Image Builder Pipeline
      ├── Build Phase: Launch "builder" EC2 → apply recipe (install packages, apply config)
      ├── Test Phase: Launch "test" EC2 from built AMI → run test components (automated validation)
      └── Distribute Phase: AMI is stored and distributed to target Regions and accounts
```

### Key Concepts

| Concept | Description |
|---|---|
| **Image Recipe** | A set of components to apply to a base image (what to install/configure) |
| **Component** | A single unit of work (install Apache, harden SSH, install CW agent) |
| **Pipeline** | The full automated workflow: build → test → distribute |
| **Distribution Settings** | Which Regions and accounts receive the final AMI |

---

## Key Features

- **Fully automated** — no manual SSH required; all steps are defined as code
- **Scheduled builds** — can run on a schedule (weekly, whenever new packages are released)
- **Cross-Region and cross-account** distribution — push the AMI to multiple Regions and share with other accounts automatically
- **Testing built in** — run automated tests against the new AMI before distributing it (CIS benchmarks, OS hardening checks, custom validation scripts)
- **Free service** — you only pay for the EC2 instances and EBS volumes used during the build/test process, not for Image Builder itself
- **Version tracking** — every recipe version is tracked; you can pin to a specific version or always get the latest

---

## Image Builder vs Manual AMI Creation

| | Manual AMI Creation | EC2 Image Builder |
|---|---|---|
| Process | SSH into instance, install software, create AMI manually | Define recipe as code, pipeline runs automatically |
| Consistency | Error-prone, varies between operators | Exactly repeatable every time |
| Testing | Manual or none | Automated test phase built in |
| Multi-Region | Manual copy operation for each Region | Automated distribution to any Regions/accounts |
| Scheduling | Manual trigger | Can be scheduled (cron-based) |
| Auditability | Hard — no trace of what was installed | Full audit trail in CloudTrail |

---

## Integration with Organizations

For enterprises managing many accounts via AWS Organizations:
- Build the AMI once in a central "golden image" account
- Use Image Builder's distribution settings to share the AMI to all member accounts
- Teams in other accounts launch from the approved AMI — they can't accidentally use an outdated or un-hardened image

---

## Key Points / Exam Tips

- **Free service** — pay only for EC2/EBS used during build and test
- **Automates the full AMI lifecycle**: create → test → distribute → keep up-to-date
- **Cross-Region distribution is built in** — no manual AMI copy step
- **Scheduled pipelines** keep images current with OS patches automatically
- Use Image Builder when the question mentions "automate AMI creation," "keep AMIs patched," or "standardized golden images at scale"

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Automate AMI creation with testing" | EC2 Image Builder |
| "Keep golden AMIs up to date with security patches" | EC2 Image Builder (scheduled pipeline) |
| "Distribute AMI to multiple Regions automatically" | EC2 Image Builder distribution settings |
| "Standardized base image for all teams" | EC2 Image Builder + Organizations |
| "Free AMI automation service" | EC2 Image Builder |
