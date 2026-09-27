# AWS CodeArtifact

## What is CodeArtifact?

**AWS CodeArtifact** is a fully managed **software artifact repository** (package manager) that makes it easy to store, publish, and share software packages used in the development process.

> "A managed private repository for your npm, Maven, PyPI, NuGet packages — integrated with AWS."

## Supported Package Formats

| Format | Language / Ecosystem |
|---|---|
| **npm** | JavaScript / Node.js |
| **PyPI** | Python |
| **Maven** | Java |
| **Gradle** | Java / Kotlin |
| **NuGet** | .NET / C# |
| **Swift** | iOS / macOS (Swift Package Manager) |
| **generic** | Any binary artifacts |

## Core Concepts

### Repository

- A **repository** stores versioned software packages in a specific format
- Multiple repositories can exist per domain
- Repositories can be **upstream-linked** to each other

### Domain

- A **domain** is a container for one or more repositories
- All repositories in a domain share a single encrypted storage (KMS)
- Cross-account sharing of packages is done at the domain level

### Upstream Repositories

- A repository can have **upstream repositories** configured
- When a package is not found locally, CodeArtifact **fetches it from the upstream** and caches it
- Can connect to public registries (npmjs.com, PyPI, Maven Central) as upstreams via **external connection**

```
Build Tool (npm/pip/mvn)
        ↓  request package
CodeArtifact Repository (local cache)
        ↓  if not found
Upstream CodeArtifact Repo  OR  Public Registry (npmjs, PyPI, etc.)
```

## How It Integrates with CI/CD

- **CodeBuild** can pull packages from CodeArtifact during builds (use `codeartifact:GetAuthorizationToken`)
- **CodePipeline** stages can publish packages to CodeArtifact after a successful build
- Package consumers (developers, build tools) authenticate using short-lived **authorization tokens**

## Key Benefits

- **Centralized** — single source of truth for all internal and external packages
- **Security** — access controlled via IAM; packages scanned for vulnerabilities
- **Cost-efficient** — cache public packages to avoid repeated external downloads (reduces egress costs, improves build speed)
- **High availability** — fully managed, multi-AZ
- **Audit trail** — package access and publish events logged in CloudTrail

## CodeArtifact vs CodeCommit

| Aspect | CodeArtifact | CodeCommit |
|---|---|---|
| Stores | Software packages (npm, pip, jar, etc.) | Source code (Git repositories) |
| Use case | Dependency management | Version control |
| Format | Package registries | Git |

## Key Points / Exam Tips

- CodeArtifact = **package/artifact repository**, not source code repository
- Use CodeArtifact to **proxy and cache** packages from public registries (npmjs, PyPI, Maven Central) to improve build speed and security
- **Upstream connections** allow fetching from other repos or the internet if a package is missing locally
- Access is controlled by **IAM** + resource-based **domain policies**
- Short-lived **authorization tokens** are used by build tools to authenticate
- CodeArtifact reduces external traffic and enforces use of **approved package versions**

## Trigger Words

| Keyword | Think |
|---|---|
| "Managed private npm / PyPI / Maven repository" | CodeArtifact |
| "Cache public packages internally" | CodeArtifact |
| "Software package management on AWS" | CodeArtifact |
| "Proxy public registry, cache artifacts" | CodeArtifact upstream + external connection |
| "Approved packages for builds" | CodeArtifact |
