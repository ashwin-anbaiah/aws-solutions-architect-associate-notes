# Security Groups vs Network ACLs

## Security Groups

A **Security Group** acts as a virtual firewall at the **instance (ENI) level**. It controls inbound and outbound traffic for the resources it is associated with.

### Security Group Characteristics

| Property | Detail |
|---|---|
| **Level** | Instance / Elastic Network Interface (ENI) |
| **Rule types** | **Allow only** — no Deny rules |
| **Stateful** | Return traffic is **automatically allowed** — no need to add outbound rule for responses |
| **Rule evaluation** | All rules are evaluated; most permissive match wins |
| **Source/Destination** | IP ranges (CIDR), other Security Groups, prefix lists |
| **Default behavior** | Default SG: allow all inbound from same SG, allow all outbound |

**Stateful** means: if you allow inbound port 80, the response traffic on an ephemeral port is automatically allowed — you do NOT need a separate outbound rule for it.

### Security Group Use Cases

- Allow HTTP (80) and HTTPS (443) from `0.0.0.0/0` to a web server
- Allow MySQL (3306) from the application tier's Security Group to a database tier
- Allow SSH (22) only from a specific IP address for administration

### Security Group — Valid Rule Sources

Valid: IP address, CIDR range, another Security Group, prefix list
Invalid (not supported): Internet Gateway ID, Subnet ID, Route Table ID

---

## Network Access Control Lists (NACLs)

A **Network ACL** is an optional firewall layer at the **subnet boundary**. It applies to all traffic entering or leaving the subnet, regardless of which instance it is going to.

### NACL Characteristics

| Property | Detail |
|---|---|
| **Level** | Subnet |
| **Rule types** | Both **Allow AND Deny** rules |
| **Stateless** | Return traffic must be **explicitly allowed** with a separate outbound rule |
| **Rule evaluation** | Rules evaluated in **numeric order** (lowest number first); first match wins |
| **Rule numbers** | 1 to 32766; rule `*` is the implicit deny at the end |
| **Default NACL** | Allows ALL inbound and outbound traffic |

**Stateless** means: if you allow inbound port 80, you must ALSO explicitly allow the response on ephemeral ports (1024-65535) in the outbound rules.

### Ephemeral Ports (NACL Outbound Rules)

When a client makes a request, the response uses **ephemeral (temporary) ports**:
- Linux: 32768–60999
- Windows: 49152–65535
- Safe range to allow: **1024–65535**

For NACLs, outbound rules must allow traffic on these ephemeral ports to permit response traffic.

---

## Comparison Table

| Feature | Security Group | Network ACL |
|---|---|---|
| Applied at | Instance (ENI) | Subnet |
| Rule types | Allow only | Allow and Deny |
| Stateful / Stateless | **Stateful** | **Stateless** |
| Rule evaluation | All rules evaluated | First matching rule wins (numbered order) |
| Default behavior | Deny all inbound; Allow all outbound | Allow all inbound and outbound |
| Applied to | Only resources explicitly attached | ALL resources in the subnet automatically |

---

## When to Use Each

| Use Case | Use |
|---|---|
| Control access to a specific EC2 instance | Security Group |
| Block a specific IP address | NACL (only tool with Deny rules) |
| Subnet-level network boundary | NACL |
| Allow ports for a specific application tier | Security Group |
| Layer of defense in addition to Security Groups | NACL |

**Important:** S3 has **no Security Groups** — S3 access is controlled via IAM policies, bucket policies, and ACLs. Never answer "Security Group on S3" on the exam.

---

## Defense-in-Depth Pattern

```
Internet
    |
    v
NACL (subnet boundary) — can Deny entire IP ranges
    |
    v
Security Group (instance level) — fine-grained allow rules
    |
    v
EC2 instance
```

---

## Key Points / Exam Tips

- **Security Group = stateful** (return traffic auto-allowed); **NACL = stateless** (return traffic needs explicit rule)
- Only **NACLs can block (Deny) a specific IP** — Security Groups cannot have Deny rules
- NACLs are evaluated **in order** — lower number rules take priority
- A NACL rule `*` (asterisk) is the implicit Deny All that sits at the end of every NACL rule list
- When a new NACL is created (custom NACL), it **denies all traffic by default** — unlike the Default NACL which allows all
- Security Groups are **instance-specific** — you must attach them to each instance; NACLs apply to **all instances** in the subnet automatically
- For return traffic in NACLs: allow outbound on ephemeral ports (1024-65535)

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Block a specific IP address" | NACL (Deny rule) — Security Groups cannot Deny |
| "Return traffic automatically allowed" | Security Group (stateful) |
| "Must explicitly allow response traffic" | NACL (stateless) |
| "Applies to all instances in a subnet" | NACL |
| "S3 security group" | Invalid — S3 has no security groups |
| "First matching rule wins" | NACL rule evaluation |
