# AWS CodeCommit and CodeBuild

## AWS CodeCommit

### What is CodeCommit?

**AWS CodeCommit** is a fully managed, **private Git-compatible source control** service hosted on AWS.

> "GitHub/GitLab — but managed by AWS and integrated with IAM."

### Key Features

- **Fully managed** — no servers to provision or maintain
- **Git-compatible** — works with all standard Git tools (`git clone`, `git push`, etc.)
- **Highly available** — data automatically replicated across multiple AZs
- **Secure** — repositories are encrypted at rest (KMS) and in transit (HTTPS/SSH)
- **IAM-integrated** — access controlled via IAM users, roles, and policies (not just SSH keys)
- **Unlimited private repositories** — no per-repo pricing
- **Triggers and notifications** — integrate with SNS/Lambda on code events
- **Pull request reviews** — built-in code review workflows

### CodeCommit vs GitHub/GitLab

| Aspect | CodeCommit | GitHub / GitLab |
|---|---|---|
| Hosting | AWS managed | Cloud (GitHub.com) or self-hosted |
| Auth | IAM (no username/password by default) | Username/password, OAuth, SSO |
| Integration | Deep AWS service integration | Broad ecosystem |
| Pricing | Included in AWS (first 5 users free) | Free tier + paid plans |

### Key Points — CodeCommit

- Access is controlled via **IAM policies** — attach `AWSCodeCommitFullAccess` or granular policies
- Supports **HTTPS** (using Git credentials or AWS CLI credential helper) and **SSH** authentication
- Can trigger **EventBridge events** or **SNS notifications** on push, pull request, branch actions
- CodeCommit repositories are **private by default** — no public repositories

---

## AWS CodeBuild

### What is CodeBuild?

**AWS CodeBuild** is a fully managed **continuous integration (CI)** service that compiles source code, runs unit tests, and produces deployable artifacts — with no servers to manage.

> "A managed build server: compile, test, package."

### How CodeBuild Works

```
Source (CodeCommit / S3 / GitHub)
        ↓
buildspec.yml (build instructions)
        ↓
CodeBuild (managed build environment)
        ↓
Artifacts (S3 bucket, ECR image, etc.)
```

### buildspec.yml

The `buildspec.yml` file in the root of your repository defines the build steps:

```yaml
version: 0.2
phases:
  install:
    commands:
      - npm install
  build:
    commands:
      - npm run build
      - npm test
artifacts:
  files:
    - '**/*'
  base-directory: dist
```

### Build Phases

| Phase | Purpose |
|---|---|
| `install` | Install dependencies, runtime versions |
| `pre_build` | Pre-build steps (login to ECR, set env vars) |
| `build` | Main build commands (compile, test) |
| `post_build` | Post-build steps (push Docker image, notify) |

### Key Features

- **Fully managed** — no build servers to provision or maintain
- **Pay per build minute** — only charged while a build is running
- **Scales automatically** — runs multiple builds concurrently
- **Pre-built environments** — Amazon Linux, Ubuntu with common runtimes (Node.js, Python, Java, Go, Docker, etc.)
- **Custom environments** — use your own Docker image for the build environment
- **VPC support** — builds can run inside a VPC to access private resources
- **Caching** — cache dependencies in S3 to speed up subsequent builds
- **Security** — IAM role assigned to the build project; secrets via Parameter Store or Secrets Manager

### Key Points — CodeBuild

- Build instructions are defined in **`buildspec.yml`** in the project root
- Build artifacts (compiled output) are stored in **S3**
- Supports building and pushing **Docker images to ECR**
- CodeBuild can be used **standalone** or as a stage within **CodePipeline**
- Environment variables can reference **SSM Parameter Store** or **Secrets Manager** for secrets

## Trigger Words

| Keyword | Think |
|---|---|
| "Managed private Git repository on AWS" | CodeCommit |
| "IAM-controlled source control" | CodeCommit |
| "Compile and test code without managing servers" | CodeBuild |
| "buildspec.yml" | CodeBuild |
| "Managed CI service" | CodeBuild |
| "Build Docker image and push to ECR" | CodeBuild |
