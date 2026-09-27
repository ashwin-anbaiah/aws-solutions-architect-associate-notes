# AWS Amplify

## What is AWS Amplify?

**AWS Amplify** is a fully managed **frontend web and mobile application** development platform. It provides tools and services to build, ship, and host full-stack applications with built-in CI/CD pipelines and AWS backend integrations.

> "Build and deploy full-stack web/mobile apps without managing infrastructure."

## Core Components

### Amplify Hosting

- Fully managed **static web hosting** and **server-side rendering (SSR)** hosting
- Automatic CI/CD: connect to a Git repository (GitHub, GitLab, Bitbucket, CodeCommit) and every push triggers a build and deploy
- Supports custom domains with free SSL/TLS certificates
- Feature branch deployments and pull-request previews
- Global CDN via CloudFront

### Amplify Studio

- Visual development environment for building app UIs
- Connect UI components to Amplify backend data models
- Generates React code from designs

### Amplify Libraries (Client-side)

- JavaScript, React, React Native, iOS, Android, Flutter SDKs
- Pre-built UI components for authentication, storage, API calls
- Simplify integration with AWS backend services

## Backend Services Amplify Integrates With

| Category | AWS Service |
|---|---|
| Authentication | Amazon Cognito |
| APIs (REST) | Amazon API Gateway + Lambda |
| APIs (GraphQL) | AWS AppSync |
| Storage | Amazon S3 |
| Database | Amazon DynamoDB |
| Functions | AWS Lambda |
| Notifications | Amazon Pinpoint |

## Amplify vs Elastic Beanstalk vs Lightsail

| Aspect | Amplify | Elastic Beanstalk | Lightsail |
|---|---|---|---|
| Target | Frontend / full-stack web & mobile | Backend web apps | Simple VPS |
| CI/CD | Built-in (Git integration) | Manual or CodePipeline | Not built-in |
| Use case | React, Next.js, Gatsby, Vue apps | Java, Python, Node.js APIs | WordPress, simple sites |
| Pricing model | Pay per build minute + hosting | Resources-based | Fixed monthly bundle |

## Key Points / Exam Tips

- Amplify = **frontend + full-stack** platform with **built-in CI/CD**
- Connects to **Git** (GitHub, GitLab, Bitbucket) for automatic deploys on push
- Uses **Cognito** for authentication, **AppSync** for GraphQL, **S3** for file storage
- Hosting is global via **CloudFront**
- Good for **React, Next.js, Gatsby, Angular, Vue** applications
- For the exam: "frontend web app with CI/CD and Cognito auth" = Amplify

## Trigger Words

| Keyword | Think |
|---|---|
| "Frontend CI/CD with Git integration" | AWS Amplify |
| "Host React/Next.js app on AWS" | AWS Amplify |
| "Full-stack mobile/web app with Cognito" | AWS Amplify |
| "Automatic deploy on Git push" | AWS Amplify Hosting |
| "Managed frontend platform" | AWS Amplify |
