# EC2 Instance Types

## What Is an Instance Type?

An **EC2 instance type** defines the hardware profile of your virtual machine — how many vCPUs, how much RAM, what kind of networking, and optionally what local storage is included.

Choosing the right instance type is a core architecture decision. Too small → bottlenecks; too large → wasted money.

---

## Instance Type Naming Convention

`c7gn.xlarge`

| Part | Meaning | Example |
|---|---|---|
| **Instance family** | What workload it's optimized for | `c` = Compute, `m` = General, `r` = Memory |
| **Generation** | Hardware generation number | `7` = 7th generation |
| **Processor** | CPU architecture | `g` = Graviton (AWS ARM), `a` = AMD, `i` = Intel |
| **Additional capability** | Special feature | `n` = Network + EBS optimized |
| **Size** | How big the instance is | `xlarge`, `2xlarge`, `4xlarge` |

**Size progression** (generally doubles with each step):
`nano → micro → small → medium → large → xlarge → 2xlarge → 4xlarge → ... → 96xlarge`

---

## Instance Families — When to Use What

| Family | Optimized For | Example Instances | Use Case |
|---|---|---|---|
| **General Purpose** (M, T) | Balanced CPU/memory/networking | m7g, m6i, t3, t2 | Web servers, dev/test, small databases, microservices |
| **Compute Optimized** (C) | High CPU-to-memory ratio | c7g, c6i, c5 | HPC, batch processing, gaming servers, ML inference |
| **Memory Optimized** (R, X, U) | High memory-to-CPU ratio | r8g, r6a, x2gd, u7i | In-memory databases, SAP HANA, Redis, large caches |
| **Storage Optimized** (I, D, H) | High local storage IOPS or throughput | i4i, d3, h1 | NoSQL databases, data warehouses, distributed file systems |
| **Accelerated Computing** (P, G, Trn, Inf, F) | GPU/FPGA | p5, g6, trn1 | ML training, GPU rendering, deep learning, video transcoding |
| **HPC Optimized** (Hpc) | High-performance computing network | hpc7g, hpc6a | Tightly coupled distributed simulations |

### T-type Instances — Burstable Performance

T-series instances (`t2`, `t3`, `t4g`) work differently from others:
- They have a **baseline CPU performance** (e.g., 20% for t2.micro)
- When the CPU is below baseline, they **accumulate CPU credits**
- When they need to burst above baseline, they **spend CPU credits**
- If credits run out, the instance is throttled back to baseline

Good for: workloads with occasional spikes but low average CPU usage (dev environments, low-traffic websites).

---

## Graviton Instances (AWS-designed ARM processors)

AWS Graviton (`g` suffix) instances use custom ARM-based processors designed by Amazon:
- **Up to 40% better price-performance** than equivalent x86 instances
- Available for most families: m7g, c7g, r7g, etc.
- Require applications compiled for ARM64 — not always a drop-in replacement

---

## Key Size Reference (m-family as an example)

| Size | vCPU | Memory |
|---|---|---|
| medium | 1 | 2 GiB |
| large | 2 | 4 GiB |
| xlarge | 4 | 8 GiB |
| 2xlarge | 8 | 16 GiB |
| 4xlarge | 16 | 32 GiB |
| 16xlarge | 64 | 128 GiB |

Each step up doubles both vCPU and memory (approximately).

---

## Choosing the Right Instance Type

1. **Identify workload type** — is it CPU-heavy, memory-heavy, I/O-heavy, or balanced?
2. **Estimate resource requirements** — vCPUs, RAM, network, storage IOPS
3. **Start small, scale out** — prefer horizontal scaling (more instances) over one giant instance
4. **Use AWS Pricing Calculator** — compare costs before committing
5. **Consider Graviton** — if your app supports ARM64, the savings are real

---

## Key Points / Exam Tips

- **T-type = burstable** — accumulate CPU credits when idle, spend them to burst; throttled if credits run out
- **C = compute, M = general, R = memory, I/D = storage, P/G = GPU** — memorize the families
- **Graviton (g) instances** = AWS-designed ARM; best price-performance for compatible workloads
- **Instance type determines max IOPS/throughput** for the instance — even if EBS can do more, the instance type caps it
- **EFA (Elastic Fabric Adapter)** for HPC instances — ultra-low-latency inter-instance communication for tightly-coupled workloads

## Trigger Words

| Exam phrase | Think |
|---|---|
| "HPC, tightly coupled, low-latency inter-node" | C-family (Compute) + Cluster Placement Group + EFA |
| "In-memory database, SAP HANA" | R-family (Memory Optimized) |
| "NoSQL database needing high local disk IOPS" | I-family (Storage Optimized) |
| "GPU-based ML training" | P or G-family (Accelerated Computing) |
| "Balanced web/app server, variable traffic" | M-family or T-family (General Purpose) |
| "Best price/performance, ARM workloads" | Graviton instances (g suffix) |
