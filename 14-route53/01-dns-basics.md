# DNS Basics

## What is DNS?

**DNS (Domain Name System)** translates human-friendly hostnames into machine IP addresses.

- Example: `www.google.com` → `142.250.205.46`
- DNS is the backbone of the Internet; without it, you'd need to memorize IP addresses

## Key DNS Terminology

| Term | Definition |
|---|---|
| **Domain Registrar** | Company authorized to register domain names (Route 53, GoDaddy, Namecheap) |
| **Top Level Domain (TLD)** | Rightmost part of domain: `.com`, `.org`, `.gov`, `.io` |
| **Zone Apex** | The root domain itself: `amazon.com`, `example.com` |
| **Subdomain (SLD)** | Left-of-apex label: `api.amazon.com`, `www.example.com` |
| **FQDN** | Fully Qualified Domain Name — uniquely identifies one host: `webserver.example.com.` |
| **Name Server** | Answers DNS queries by looking up records in the zone file |
| **Zone File** | Text file containing DNS records (A, AAAA, CNAME, etc.) |

## How DNS Resolution Works (Step by Step)

1. Client browser checks **local cache** → found? Done.
2. Client asks **Local DNS Resolver** (assigned by ISP or company network)
3. Local DNS Resolver checks its cache → found? Returns result.
4. If not cached, Resolver asks **Root DNS Server** (managed by ICANN) → gets `.com` Name Server
5. Resolver asks **.com DNS Server** (managed by IANA) → gets `example.com` Name Server
6. Resolver asks **example.com Name Server** (managed by domain registrar / Route 53) → gets IP
7. Resolver **caches the result** (for TTL duration) and returns IP to client
8. Client connects to the IP address

## DNS Record Types

| Record | Purpose | Example |
|---|---|---|
| **A** | Domain → IPv4 address | `example.com → 11.22.33.44` |
| **AAAA** | Domain → IPv6 address | `example.com → 2001:db8::1` |
| **CNAME** | Hostname → another hostname | `www.example.com → example.com` |
| **MX** | Mail exchange servers | `example.com → mail.example.com` |
| **TXT** | Text data (SPF, DKIM, verification) | `example.com → "v=spf1 ..."` |
| **NS** | Delegates zone to name servers | `example.com → ns1.awsdns-12.com` |
| **SOA** | Start of Authority — zone metadata | Primary NS, admin contact, serial |
| **PTR** | Reverse DNS (IP → domain) | `44.33.22.11.in-addr.arpa → example.com` |
| **CAA** | Which CAs can issue certificates | `example.com → issue "letsencrypt.org"` |
| **SRV** | Service location (protocol, port) | `_chat._tcp.example.com → chatserver.example.com:5222` |

## DNS TTL (Time To Live)

- **TTL** tells resolvers and clients how long to **cache** a DNS record before requesting a fresh copy
- **High TTL** (e.g., 86400 sec / 24 hours): fewer DNS queries, lower cost, but slow propagation of changes
- **Low TTL** (e.g., 300 sec): fast propagation, more DNS queries and slightly higher cost
- **Best practice**: lower TTL before planned migrations, raise it after stabilization

## Key Points / Exam Tips

- The DNS hierarchy goes: Root → TLD → Domain (Zone) → Subdomain
- **ICANN** manages root; **IANA** manages TLDs; **registrars** (like Route 53) manage individual domains
- Route 53 uses port **53** — that's where the name "Route 53" comes from
- DNS caching happens at: browser, OS, local DNS resolver — TTL controls duration at each level
- **FQDN** ends with a trailing dot (`.`) representing the root zone — e.g., `www.example.com.`

## Trigger Words

- "Domain name to IP resolution" → DNS
- "Name server" → holds DNS records for a zone
- "TTL" → controls DNS cache duration at resolvers
