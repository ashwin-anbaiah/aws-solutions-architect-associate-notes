# Route 53 Hosted Zones

## What is Amazon Route 53?

**Amazon Route 53** is a fully managed, global DNS service and domain registrar by AWS.

- The **only AWS service with a 100% availability SLA**
- Can register new domain names directly through Route 53
- Manages DNS records in **hosted zones**
- Named after port **53**, the standard port for DNS

## Hosted Zone Types

A **hosted zone** is a container for DNS records that define how traffic is routed for a domain.

| | Public Hosted Zone | Private Hosted Zone |
|---|---|---|
| **Purpose** | DNS for public internet traffic | DNS for resources inside a VPC |
| **Resolves from** | Anywhere on the internet | Only from within associated VPCs |
| **Example records** | `www.example.com → 11.22.33.44` | `db.myapp.com → 10.0.0.7` |
| **Use case** | Public-facing websites and APIs | Internal microservices, databases |
| **Cost** | $0.50/hosted zone/month | $0.50/hosted zone/month |

## Public Hosted Zone

- Contains records that resolve publicly over the internet
- Required when you own a domain and want AWS to serve its DNS
- **Route 53 becomes the authoritative name server** for the domain
- Typical records: A records pointing to public IPs, CNAME/Alias pointing to ALB or CloudFront

**Common setup:**
1. Register domain (or transfer to Route 53)
2. Route 53 creates a Public Hosted Zone with 4 Name Server (NS) records
3. Update your domain registrar's NS records to point to Route 53 NS records
4. Add A/AAAA/CNAME/Alias records for your subdomains

## Private Hosted Zone

- Contains records that resolve **only within associated VPCs**
- Allows internal services to use friendly names instead of IP addresses
- Example: EC2 app server resolves `db.myapp.com` to `10.0.0.7` internally
- A private hosted zone must be **associated with one or more VPCs** to function
- Resolves via the **VPC's Route 53 Resolver** (runs at VPC Base + 2 address)

**Use cases:**
- Internal microservices discovery
- Database endpoint aliasing (change IP without updating app configs)
- Multi-tier applications needing internal service names

## Key Points / Exam Tips

- Route 53 is the **only AWS service** guaranteed at 100% uptime SLA — remember this for availability questions
- A **Public Hosted Zone** is needed for any publicly accessible domain; a **Private Hosted Zone** is for VPC-internal DNS
- Private Hosted Zones are **not accessible** from on-premises networks by default — you need Route 53 Resolver endpoints for hybrid DNS
- Both hosted zone types cost **$0.50 per hosted zone per month**
- When you register a domain through Route 53, it automatically creates a Public Hosted Zone
- Zone file format within Route 53: key-value pairs mapping hostnames to target IPs or other domains

## Trigger Words

- "Custom domain name for AWS service" → Public Hosted Zone + Alias record
- "Internal DNS resolution within VPC" → Private Hosted Zone
- "100% availability SLA" → Route 53
- "On-premises servers resolve AWS VPC hostnames" → Private Hosted Zone + Inbound Resolver Endpoint
