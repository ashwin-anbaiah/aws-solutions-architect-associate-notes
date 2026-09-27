# Amazon API Gateway Overview

## What is API Gateway?

**Amazon API Gateway** is a fully managed service to create, deploy, and manage APIs at any scale. It sits between clients and your backend services.

- Supports **REST APIs**, **HTTP APIs**, and **WebSocket APIs**
- Handles authentication, authorization, throttling, caching, and monitoring out of the box
- Integrates with Lambda, EC2, ECS, EKS, other AWS services, and any HTTP endpoint
- All API Gateway endpoints enforce **HTTPS** — plain HTTP is not supported

## API Types Comparison

| | REST API | HTTP API | WebSocket API |
|---|---|---|---|
| **Protocol** | RESTful HTTP/S | RESTful HTTP/S | WebSocket (persistent) |
| **Features** | Full-featured | Minimal (cheaper) | Bidirectional real-time |
| **Cost** | Higher | ~70% cheaper | — |
| **API Keys** | Yes | No | No |
| **Request validation** | Yes | No | — |
| **AWS WAF integration** | Yes | No | No |
| **Private API endpoint** | Yes | No | — |
| **Usage plans** | Yes | No | — |
| **Use case** | Enterprise, complex APIs | Microservices, low-cost Lambda | Chat, live dashboard, gaming |

**Rule of thumb**: Use REST APIs when you need API keys, per-client throttling, request validation, WAF integration, or private endpoints. Use HTTP APIs for simple, low-cost Lambda or HTTP proxy integrations.

## Endpoint Types

| Endpoint Type | Description | Best For |
|---|---|---|
| **Edge-Optimized** | Routes through CloudFront PoPs | Global clients needing low latency |
| **Regional** | Deployed in a specific AWS region | Clients mainly in one region; use with custom CDN |
| **Private** | Accessible only via VPC Interface Endpoint (PrivateLink) | Secure service-to-service communication inside VPC |

## Backend Integrations

API Gateway supports multiple integration types to connect to backends:

| Integration | Description |
|---|---|
| **Lambda** | Invoke Lambda functions (most common serverless pattern) |
| **HTTP / HTTP Proxy** | Forward to public HTTP endpoints; proxy passes entire request as-is |
| **AWS Service** | Directly invoke AWS services (SQS, SNS, DynamoDB, Step Functions, Kinesis, EventBridge) without writing backend code |
| **Mock** | Return predefined responses — useful for testing and demos |
| **VPC Link** | Connect privately to services inside VPC via ALB/NLB |

**Commonly used AWS service integrations:**
- SQS — send messages
- SNS — publish notifications
- DynamoDB — CRUD operations
- Step Functions — start workflows
- Kinesis — put records
- EventBridge — put events

## WebSocket APIs

- Uses **persistent WebSocket connections** instead of request/response
- Supports **real-time, bidirectional communication** between client and server
- Ideal for: chat applications, live dashboards, notifications, multiplayer games, IoT real-time updates
- Backend can push messages to connected clients without client polling

## Custom Domain Names

- Default API endpoint: `https://{api-id}.execute-api.{region}.amazonaws.com/{stage}`
- You can map a **custom domain** (e.g., `api.myapp.com`) using Route 53 + ACM
- Certificate requirements:
  - **Edge-optimized REST API**: ACM certificate must be in **us-east-1**
  - **Regional REST API or HTTP API**: ACM certificate must be in the **same region** as the API

## Key Points / Exam Tips

- API Gateway is a **public service** — it sits outside the VPC; use VPC Link to reach private resources
- **REST API** = choose this when you need API keys, WAF, private endpoints, or request validation
- **HTTP API** = cheaper, simpler, no WAF or API keys
- **Edge-optimized custom domain** requires ACM cert in **us-east-1** — classic exam trap
- **VPC Link V2** connects to ALB inside VPC; **VPC Link V1** (legacy) connects to NLB
- API Gateway automatically creates **CloudWatch Logs** for access and execution logging
- AWS WAF can be attached to **regional REST APIs and HTTP APIs** (not edge-optimized)

## Trigger Words

- "Fully managed API service" → API Gateway
- "Real-time bidirectional chat / live dashboard" → WebSocket API
- "Expose Lambda as a REST API" → API Gateway + Lambda integration
- "API with rate limiting per client" → REST API + API Keys + Usage Plans
- "Secure internal API, not publicly accessible" → Private Endpoint + VPC Interface Endpoint
- "Call DynamoDB directly from API Gateway without Lambda" → AWS Service Integration
