# AWS Shield and DDoS Protection

## What is a DDoS Attack?

A **Distributed Denial-of-Service (DDoS)** attack floods your application with malicious traffic to exhaust resources and make it unavailable to legitimate users.

### Common DDoS Attack Types

| Attack | Layer | Description |
|---|---|---|
| **SYN Flood** | L4 (Transport) | Too many half-open TCP connections |
| **UDP Flood** | L4 (Transport) | Overwhelm with UDP packets |
| **UDP Reflection** | L3/L4 | Spoof victim's IP as source; victim gets flood of unexpected responses |
| **DNS Flood** | L3/L4 | Overwhelm DNS so legitimate users can't resolve the domain |
| **Slow Loris** | L7 (Application) | Open many HTTP connections and keep them alive slowly |
| **Cache Busting** | L7 (Application) | Force CDN cache misses to overload origin |

---

## AWS Shield

**AWS Shield** provides **managed DDoS protection** at **Layer 3 (Network) and Layer 4 (Transport)**.

### Shield Standard

| Attribute | Details |
|---|---|
| Cost | **Free** — automatically applied to every AWS customer |
| Scope | All AWS resources |
| Protection | Layer 3/4 DDoS attacks (SYN floods, UDP floods, reflection attacks) |
| Configuration | **None required** — always on |

### Shield Advanced

| Attribute | Details |
|---|---|
| Cost | **$3,000/month per organization** |
| Coverage | EC2, ELB, CloudFront, Global Accelerator, Route 53 |
| Protection | More sophisticated and large-scale Layer 3/4 attacks + support |
| DRT Access | **24/7 AWS DDoS Response Team (DRT)** — manual response during attacks |
| Cost protection | Reimbursement for **AWS usage cost spikes** caused by DDoS attacks |
| Attack visibility | Near real-time attack metrics and notifications |
| WAF integration | Automatic WAF rules deployed during an attack |

### Shield Standard vs Shield Advanced

| Feature | Shield Standard | Shield Advanced |
|---|---|---|
| Cost | Free | $3,000/month/org |
| Always-on | Yes | Yes |
| Layer 3/4 protection | Basic | Enhanced (higher capacity) |
| Layer 7 protection | No | Via integrated WAF |
| DDoS Response Team | No | Yes (24/7 DRT) |
| Attack notifications | No | Yes (near real-time) |
| Cost protection | No | Yes (bill credits for spikes) |
| Supported resources | All | EC2, ELB, CloudFront, Route 53, Global Accelerator |

## DDoS Protection Architecture

Best practice reference architecture (defense in depth):

```
Users → Route 53 (Shield Standard)
           ↓
       CloudFront (Shield Standard/Advanced + WAF)
           ↓
       ALB in public subnet (Shield Advanced)
           ↓
       EC2 Auto Scaling Group in private subnet (SG controls)
```

- **Route 53** — protected by Shield Standard (DNS-layer protection)
- **CloudFront** — first line of defense; absorbs volumetric attacks at edge; WAF applied
- **ALB** — Shield Advanced + WAF for regional protection
- **EC2** — security groups restrict direct traffic; no direct internet exposure

## Shield vs WAF

| Aspect | AWS Shield | AWS WAF |
|---|---|---|
| Layer | L3 / L4 (network/transport) | L7 (application) |
| Attack type | Volumetric DDoS (floods) | Web exploits (SQLi, XSS, bots) |
| Automatic | Shield Standard: always on | Must configure Web ACL rules |
| Use together? | **Yes** — complementary |

## Key Points / Exam Tips

- **Shield Standard** = free, always on, basic L3/L4 protection — no action needed
- **Shield Advanced** = $3,000/month, enhanced protection, **DRT access**, **cost protection**
- Shield protects **Layer 3 and Layer 4** — WAF handles **Layer 7**
- For "sophisticated DDoS protection with 24/7 support" → Shield Advanced
- Shield Advanced integrates with WAF to automatically create rules during an attack
- The **DRT (DDoS Response Team)** is available only with Shield Advanced
- Use **CloudFront + WAF + Shield** together for multi-layer defense

## Trigger Words

| Keyword | Think |
|---|---|
| "DDoS protection Layer 3/4" | AWS Shield |
| "Always-on free DDoS protection" | Shield Standard |
| "24/7 DDoS Response Team" | Shield Advanced |
| "Bill credit for DDoS-caused cost spike" | Shield Advanced cost protection |
| "Sophisticated / large-scale DDoS" | Shield Advanced |
| "CloudFront protection from DDoS" | Shield Standard (built-in) + WAF |
