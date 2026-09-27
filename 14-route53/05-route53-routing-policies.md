# Route 53 Routing Policies

## Overview

A **routing policy** defines how Route 53 responds to DNS queries when there are multiple records with the same name and type. Route 53 supports 8 routing policies.

## 1. Simple Routing

- Routes traffic to a **single resource**
- Can return multiple IP values (client picks one randomly)
- **Health checks not supported** — all values returned regardless of health
- Use when: one backend, no failover needed

## 2. Failover Routing

- Implements **active-passive failover**
- You define a **Primary** record and a **Secondary** (standby) record
- Primary is returned only when its **health check passes**
- If Primary is unhealthy, Route 53 automatically returns the Secondary
- Secondary is typically a backup system, DR site, or static error page
- **Health checks required** on the Primary record

**Exam scenario**: Primary ALB in us-east-1 fails → Route 53 switches to secondary ALB in eu-west-1

## 3. Weighted Routing

- Splits traffic across multiple resources based on **assigned weights**
- Example: 70% to v1, 30% to v2 (weight 70 and weight 30)
- Useful for: load balancing, canary/A-B testing, gradual traffic migration
- Set weight to **0** to stop sending traffic to a specific endpoint
- **Supports health checks** — unhealthy records are removed from the distribution

## 4. Latency Routing

- Routes users to the AWS region that provides the **lowest network latency**
- You create records with the same name but in **different AWS regions**
- Route 53 uses AWS-measured latency between regions and the user's resolver
- **Supports health checks** — unhealthy regions are excluded
- Ideal for latency-sensitive global applications

## 5. Geolocation Routing

- Routes users based on their **physical geographic location** (country, continent, or US state)
- Strict mapping: user from India → always routed to the India endpoint
- Requires a **default record** for locations that don't match any configured mapping
- **Does not guarantee lowest latency** — purely based on location
- Use cases: content localization, regional compliance, data residency requirements

## 6. Geoproximity Routing

- Routes based on **geographic distance** between users and your AWS or on-premises endpoints
- Supports a **bias** value to shift traffic boundaries:
  - **Positive bias (+1 to +99)**: expands the routing region (attracts more traffic)
  - **Negative bias (-1 to -99)**: shrinks the routing region (pushes traffic away)
- More flexible than Geolocation — traffic shifts dynamically with bias adjustments
- **Requires Route 53 Traffic Flow** (policy-based routing) — cannot use standard record sets
- Use cases: shift traffic toward a new region for gradual migration

## 7. IP-Based Routing

- Routes traffic based on the **client's source IP address (CIDR block)**
- You create mappings of CIDR blocks to specific endpoints
- More precise than Geolocation (works at ISP/network level, not just country level)
- Use cases: route corporate network traffic to specific backend, route ISP traffic differently

**Example:**

| Client CIDR | Endpoint |
|---|---|
| `54.11.5.0/24` | `11.22.33.44` (Region A) |
| `70.20.9.0/24` | `12.23.34.45` (Region B) |
| `205.20.7.0/24` | `13.24.35.46` (Region C) |

## 8. Multi-Value Answer Routing

- Returns **up to 8 healthy records** randomly in each DNS response
- Client chooses one of those IPs to connect to
- **Supports health checks** — unhealthy endpoints are automatically excluded
- Provides basic **DNS-level load balancing** (not a replacement for ELB)
- Use when: distributing traffic across multiple servers, no need for sophisticated LB

## Policy Comparison Table

| Policy | Health Checks | Key Feature | Use Case |
|---|---|---|---|
| **Simple** | No | Single resource | One backend |
| **Failover** | Yes (required) | Active-passive | DR failover |
| **Weighted** | Yes | % traffic split | A/B testing, gradual migration |
| **Latency** | Yes | Lowest latency | Global performance |
| **Geolocation** | Yes | By country/continent | Compliance, localization |
| **Geoproximity** | Yes | Distance + bias | Fine-grained regional traffic shift |
| **IP-based** | Yes | Source CIDR | ISP/network-level routing |
| **Multi-value** | Yes | Up to 8 healthy IPs | Basic DNS load balancing |

## Key Points / Exam Tips

- **Simple routing**: no health checks — all values returned
- **Failover routing**: requires health check on Primary; classic active-passive DR pattern
- **Weighted routing**: weight 0 = excluded from routing (not deleted)
- **Latency vs Geolocation**: Latency = fastest connection; Geolocation = closest country match
- **Geoproximity** requires **Route 53 Traffic Flow** — it cannot be configured with regular records
- **Multi-value** is NOT the same as ELB — it's DNS-level only; ELB provides true load balancing

## Trigger Words

- "Route traffic based on user location (country/continent)" → Geolocation
- "Route traffic to lowest latency region" → Latency routing
- "Active-passive failover with automatic DNS switching" → Failover routing
- "Split 90% / 10% traffic between two endpoints" → Weighted routing
- "Shift traffic toward new region gradually" → Geoproximity with positive bias
- "Route specific corporate IP ranges to specific backend" → IP-based routing
- "Return multiple healthy IPs to client" → Multi-value routing
