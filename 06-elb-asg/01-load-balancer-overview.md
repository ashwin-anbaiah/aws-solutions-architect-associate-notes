# Load Balancer Overview

## What is an Elastic Load Balancer (ELB)?

An **Elastic Load Balancer** distributes incoming application traffic across multiple targets (EC2 instances, containers, IP addresses, Lambda functions). It is a **regional, managed service** that provides high availability across Availability Zones.

**Analogy:** Think of a hotel front desk that assigns guests to available rooms — the ELB is the front desk, and the rooms are your backend servers.

---

## Core Capabilities

- Exposes a **single DNS endpoint** to clients (e.g., `xxx.elb.amazonaws.com`)
- Performs **health checks** and stops sending traffic to unhealthy targets automatically
- Provides **SSL/TLS termination** — decrypts HTTPS at the LB so backends only handle HTTP
- Supports **Dual-stack mode** (IPv4 + IPv6 simultaneously)
- Supports protocols: **HTTP, HTTPS, HTTP/2, TCP, UDP, gRPC**
- Integrates with: Auto Scaling Groups, Route 53, ACM, ECS, CloudWatch, WAF, Global Accelerator

---

## ELB Components

| Component | Description |
|---|---|
| **Listener** | Checks for connection requests on a specific protocol and port (e.g., HTTPS:443) |
| **Target Group** | A logical group of targets (EC2, IP, container, Lambda) that receive traffic |
| **Target** | An individual resource receiving traffic |
| **Rules** | Define how requests are routed from a listener to target groups |

---

## Types of Elastic Load Balancers

| Type | OSI Layer | Protocols | Best For |
|---|---|---|---|
| **Application Load Balancer (ALB)** | Layer 7 | HTTP, HTTPS, WebSocket, HTTP/2, gRPC | Web apps, content-based routing |
| **Network Load Balancer (NLB)** | Layer 4 | TCP, UDP, TLS | Ultra-low latency, static IPs, IoT, gaming |
| **Gateway Load Balancer (GWLB)** | Layer 3 | GENEVE (port 6081) | Third-party virtual appliances (firewalls, IDS/IPS) |
| **Classic Load Balancer (CLB)** | Layer 4 and 7 | HTTP, HTTPS, TCP, SSL | Legacy only — do not recommend for new architectures |

---

## Security Group Best Practice

- **Load Balancer SG**: allow HTTPS/HTTP from `0.0.0.0/0` (internet)
- **EC2 / Application SG**: allow traffic **only from the LB's Security Group** (restrict direct access, not from the internet)

---

## External vs. Internal Load Balancers

| Type | Subnet | Traffic Source |
|---|---|---|
| **External (Internet-facing)** | Public subnet | Internet traffic |
| **Internal (Private)** | Private subnet | Traffic from within the VPC or peered VPCs only |

---

## Key Points / Exam Tips

- ELB is a **regional service** — it spans multiple AZs within one region automatically
- Health checks happen at the **Target Group** level; interval and thresholds are configurable
- **Classic Load Balancer** is legacy — the exam tests it for comparison only; never recommend it for new designs
- Listeners can be on different ports on the same LB (e.g., port 80 AND port 443)
- The LB **terminates** the client connection; it opens a new, separate connection to the backend target
- ELB does not have a fixed IP (except NLB) — it uses a DNS hostname that resolves to IPs managed by AWS

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Distribute traffic across EC2 / containers / Lambda" | ELB |
| "Single DNS endpoint for the application" | ELB |
| "SSL/TLS termination" | ELB (ALB or NLB with TLS listener) |
| "Health checks to route away from unhealthy instances" | ELB Target Group health checks |
| "Layer 7 / content-based routing" | ALB |
| "Static IP / UDP / ultra-low latency / gaming / IoT" | NLB |
| "Firewall inspection / third-party virtual appliance inline" | GWLB |
| "Internal load balancer / private subnet" | Internal ELB (not internet-facing) |
