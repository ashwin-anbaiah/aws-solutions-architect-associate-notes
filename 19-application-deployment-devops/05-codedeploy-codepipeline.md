# AWS CodeDeploy and CodePipeline

## AWS CodeDeploy

### What is CodeDeploy?

**AWS CodeDeploy** is a fully managed **deployment service** that automates application deployments to compute targets: EC2 instances, on-premises servers, ECS services, and Lambda functions.

> "Automate code deployments — in-place or blue/green — across any compute."

### Deployment Targets

| Target | Deployment Types Available |
|---|---|
| **EC2 / On-premises** | In-place, Blue/Green |
| **Amazon ECS** | Blue/Green only |
| **AWS Lambda** | Blue/Green (traffic shifting) only |

### Deployment Types

#### In-Place Deployment (EC2 / On-premises only)

- The existing instances are **stopped, updated, and restarted**
- Capacity is **reduced** during deployment
- Rollback = redeploy previous version (slower)
- Good for simple, stateful EC2 deployments

#### Blue/Green Deployment

- A **new set of instances** (green) is provisioned alongside the existing set (blue)
- Traffic is shifted to the green environment after validation
- **Fast rollback** = simply redirect traffic back to blue
- For ECS and Lambda: no new EC2 instances needed — new task set or Lambda version is created

### The AppSpec File

The **`appspec.yml`** (or `appspec.json`) file defines deployment actions and lifecycle hooks.

**For EC2/on-premises:**
```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html
hooks:
  BeforeInstall:
    - location: scripts/stop_server.sh
  AfterInstall:
    - location: scripts/start_server.sh
  ApplicationStart:
    - location: scripts/validate_service.sh
  ValidateService:
    - location: scripts/health_check.sh
```

### Deployment Lifecycle Hooks (EC2)

The deployment lifecycle events in order:

1. `ApplicationStop`
2. `DownloadBundle`
3. `BeforeInstall`
4. **`Install`**
5. `AfterInstall`
6. `ApplicationStart`
7. `ValidateService`

### Key Features

- **CodeDeploy Agent** must be installed on EC2 / on-premises instances
- Supports **deployment groups** — logical groups of instances (by tag, ASG name)
- **Rollback** can be automatic (on failure or alarm) or manual
- **Deployment configurations** control rollout speed (all-at-once, half-at-a-time, one-at-a-time)

---

## AWS CodePipeline

### What is CodePipeline?

**AWS CodePipeline** is a fully managed **Continuous Integration / Continuous Delivery (CI/CD)** orchestration service that automates the steps required to release software changes.

> "Orchestrate your entire CI/CD workflow from source to production."

### Pipeline Structure

A pipeline consists of **stages**, and each stage has one or more **actions**:

```
Source Stage    →   Build Stage     →   Test Stage     →   Deploy Stage
(CodeCommit /       (CodeBuild /        (CodeBuild /        (CodeDeploy /
 S3 / GitHub)        Jenkins)            Lambda)             ECS / Beanstalk)
```

### Supported Integrations

| Stage Type | Service Options |
|---|---|
| **Source** | CodeCommit, S3, GitHub, Bitbucket, ECR |
| **Build** | CodeBuild, Jenkins |
| **Test** | CodeBuild, third-party tools |
| **Deploy** | CodeDeploy, Elastic Beanstalk, ECS, CloudFormation, S3, Lambda |
| **Approval** | Manual approval action (human gate) |

### Key Features

- **Fully managed** — no servers to provision
- **Visual pipeline editor** in the console
- **Automatic triggers** on source changes (commit, new S3 object, ECR image push)
- **Manual approval gates** — add a human approval step before production deployments
- **Parallel actions** within a stage
- **Artifact store** in S3 — each stage passes artifacts to the next
- **EventBridge integration** for pipeline state change notifications
- **Pay per active pipeline** per month

### CodePipeline vs CodeDeploy

| Aspect | CodePipeline | CodeDeploy |
|---|---|---|
| Role | Orchestrates the entire CI/CD workflow | Handles the actual deployment step |
| Scope | Source → Build → Test → Deploy | Deploy only |
| Think of it as | The pipeline (conductor) | One stage in the pipeline (executor) |

## Key Points / Exam Tips

- **CodeDeploy** = automates deployments; requires **CodeDeploy Agent** on EC2
- **appspec.yml** defines the deployment — lifecycle hooks let you run scripts at each phase
- **In-place** = update existing instances (downtime possible); **Blue/Green** = new instances (fast rollback)
- For **ECS and Lambda**, only **Blue/Green** deployments are supported
- **CodePipeline** is the **orchestrator** — it chains source, build, test, and deploy stages
- A **Manual Approval** action in CodePipeline pauses the pipeline until a human approves
- CodePipeline uses **S3 as the artifact store** between stages
- CodePipeline integrates with third-party tools (GitHub, Jenkins, etc.)

## Trigger Words

| Keyword | Think |
|---|---|
| "Automate deployments to EC2/ECS/Lambda" | CodeDeploy |
| "appspec.yml lifecycle hooks" | CodeDeploy |
| "In-place vs Blue/Green deployment" | CodeDeploy |
| "End-to-end CI/CD pipeline" | CodePipeline |
| "Orchestrate source, build, test, deploy" | CodePipeline |
| "Manual approval before production" | CodePipeline Approval action |
| "Blue/Green for Lambda" | CodeDeploy (traffic shifting) |
