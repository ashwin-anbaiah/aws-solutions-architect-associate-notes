# ELB Advanced Features

## Cross-Zone Load Balancing

Without cross-zone load balancing, each ELB node only sends traffic to targets **in its own AZ**. If AZ-1 has 2 instances and AZ-2 has 8 instances, AZ-1 instances each receive 25% of the total traffic while AZ-2 instances each receive 6.25%.

With cross-zone load balancing, every ELB node distributes requests **evenly across all targets in all AZs** — regardless of which AZ the node is in.

| ELB Type | Cross-Zone Default | Can Change? | Extra Cost? |
|---|---|---|---|
| **ALB** | ON (enabled) | Yes | No extra charge |
| **NLB** | OFF (disabled) | Yes | Inter-AZ data transfer cost if enabled |
| **GWLB** | OFF (disabled) | Yes | Inter-AZ data transfer cost if enabled |
| **CLB** | OFF (disabled) | Yes | No extra charge |

---

## Server Name Indication (SNI)

- Allows a single load balancer to serve **multiple HTTPS domains**, each with a different SSL certificate
- The client specifies the **desired hostname** in the TLS Client Hello during handshake
- The load balancer selects the matching certificate and returns it
- Supported on: **ALB and NLB only** (NOT CLB)

**Use case:** Host `www.app1.com`, `api.app2.com`, and `checkout.app3.com` behind a single ALB, each with its own SSL certificate.

---

## Connection Draining (Deregistration Delay)

- When a target is **deregistered** (removed) or **unhealthy**, ELB stops sending new requests to it
- Existing **in-flight requests** are allowed to complete before the target is fully removed
- Default delay: **300 seconds** (configurable from 0 to 3600 seconds)
- Set to 0 to immediately terminate connections (fast deregistration — use when instances terminate quickly)

---

## Sticky Sessions (Session Affinity)

Sticky sessions ensure a user's requests are always sent to the **same target** within a target group.

| Type | Cookie Name | Who Sets It |
|---|---|---|
| **Duration-based** | `AWSALB` | ALB sets it automatically |
| **Application-based** | `AWSALBAPP` | Your application sets a custom cookie |

- **Works on ALB and CLB** — NLB uses source IP-based stickiness (not cookies)
- Sticky sessions can cause **uneven load distribution** — some targets receive more traffic because users "stick" to them
- Set the expiry duration carefully; longer duration = more imbalance risk

---

## Client IP Preservation

| Load Balancer | How Client IP is Preserved |
|---|---|
| **ALB** | Client IP is in the `X-Forwarded-For` HTTP header (targets see ALB's private IP in TCP) |
| **NLB** | Client IP is preserved directly in the TCP packet (pass-through) |
| **NLB with TLS termination** | Enable **Proxy Protocol v2** to include client IP in a binary header |

**Why it matters:** Web application firewalls (WAF), logging systems, and rate-limiting logic need the real client IP — not the load balancer's IP.

---

## ELB Comparison Table

| Feature | ALB | NLB | GWLB | CLB |
|---|---|---|---|---|
| Layer | 7 | 4 | 3 | 4 / 7 |
| Protocols | HTTP/HTTPS/gRPC | TCP/UDP/TLS | GENEVE | HTTP/TCP |
| Static IP | No | Yes (EIP) | No | No |
| SNI | Yes | Yes | No | No |
| Lambda target | Yes | No | No | No |
| ALB as target | No | Yes | No | No |
| Cross-zone (default) | On | Off | Off | Off |
| Content routing | Yes | No | No | Limited |

---

## Key Points / Exam Tips

- **Cross-zone load balancing is ON by default ONLY for ALB** — always a favorite exam question
- SNI is **not** available on CLB — if multiple certs are needed on one LB, use ALB or NLB
- **Connection draining = deregistration delay** — these are the same thing, just different naming depending on the console/docs version
- If you see "instances receiving uneven traffic despite equal count in each AZ," the fix is **enabling cross-zone load balancing**
- The `X-Forwarded-For` header only exists on ALB (HTTP-based); NLB does not add HTTP headers

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Uneven traffic distribution across AZs" | Enable cross-zone load balancing |
| "Multiple SSL certificates on one LB" | SNI (ALB or NLB) |
| "User always sent to same backend" | Sticky sessions |
| "In-flight requests finish before instance removed" | Connection draining / deregistration delay |
| "Real client IP at the application layer" | X-Forwarded-For (ALB) or Proxy Protocol v2 (NLB+TLS) |
| "Cross-zone enabled by default" | Only ALB — NLB and GWLB are OFF by default |
