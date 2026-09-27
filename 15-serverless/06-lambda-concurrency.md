# Lambda Concurrency (Reserved and Provisioned)

## What is Concurrency?

**Concurrency** is the number of Lambda function instances running **simultaneously** at any given moment.

- Each request/event occupies one concurrent execution for its duration
- **Formula**: `Concurrency = (avg requests per second) × (avg execution duration in seconds)`
- Example: 100 req/sec × 0.5 sec avg duration = **50 concurrent executions needed**

## Default Concurrency Limits

- **Account-level limit**: **1,000 concurrent executions** across all functions in a region (default)
- All functions in the account **share this pool** on a first-come, first-served basis
- When the account limit is exhausted, functions experience **throttling** → requests are dropped
- Throttled synchronous invocations return: **HTTP 429 TooManyRequestsException**
- This limit can be increased via AWS Support request

## Reserved Concurrency

**Reserved Concurrency** guarantees a minimum (and caps the maximum) number of concurrent executions for a specific function.

- **Guarantees capacity**: the reserved amount is exclusively dedicated to that function
- **Prevents noisy neighbors**: other functions cannot consume the reserved capacity
- **Caps usage**: the function cannot exceed its reserved limit — acts as both floor and ceiling
- **No additional cost** to configure reserved concurrency
- Setting reserved concurrency = 0 effectively **disables** the function (all invocations throttled)

**When to use:**
- Critical functions that must not be starved by other functions
- Functions that should be rate-limited to protect downstream services (e.g., database)

## Provisioned Concurrency

**Provisioned Concurrency** pre-warms a specified number of execution environments so they are **ready to respond immediately** (no cold start).

- Lambda **pre-initializes** the specified number of environments
- Invocations on these pre-warmed environments have **no cold start latency**
- **You pay for Provisioned Concurrency** even when there are no active invocations
- Can be adjusted dynamically using **Application Auto Scaling** based on schedules or metrics
- Supported for **Lambda function versions** and **aliases** (not $LATEST)

**When to use:**
- Latency-sensitive functions (APIs, real-time processing)
- Functions with large packages or heavy initialization (Java, .NET)
- Predictable traffic patterns where you know when to pre-warm

## Reserved vs Provisioned Comparison

| | Reserved Concurrency | Provisioned Concurrency |
|---|---|---|
| **Solves** | Throttling / starvation | Cold start latency |
| **Pre-warms environments** | No | Yes |
| **Cost** | Free | Paid (per pre-initialized env/hour) |
| **Behavior** | Guarantees and caps capacity | Always-warm for immediate response |
| **Use when** | Function must not compete with others | Function has strict latency requirements |

## Cold Start vs Warm Start

| | Cold Start | Warm Start |
|---|---|---|
| **What happens** | New environment initialized (download code, init runtime, init handler) | Existing environment reused |
| **Latency** | Higher (ms to seconds for heavy runtimes) | Low (just execute handler) |
| **Mitigation** | Provisioned Concurrency or SnapStart | Natural with traffic |

**Runtimes most affected by cold starts**: Java, .NET (large initialization); Python and Node.js have faster cold starts.

## Lambda SnapStart

A complementary feature to reduce cold starts (primarily for **Java 11+, Python 3.12+, .NET 8+**):

- Takes a **Firecracker microVM snapshot** of the initialized execution environment
- Restores snapshot in milliseconds for each invocation instead of re-initializing
- **10x faster startup**, no extra cost, minimal code changes
- **Limitation**: initialization code must be stateless (no database connections, no random UUIDs at init)
- Cheaper and faster than Provisioned Concurrency for supported runtimes with stateless init

## Key Points / Exam Tips

- **Reserved Concurrency** = solves throttling; **Provisioned Concurrency** = solves cold starts — know the difference
- Default account concurrency is **1,000** per region — critical fact for exams
- Reserved concurrency of 0 = function is disabled (all invocations return throttle error)
- Provisioned Concurrency costs money even with zero invocations — it keeps environments initialized
- **SnapStart** is the cheapest way to reduce cold starts for Java/Python/.NET — but requires stateless init
- When a function is throttled (synchronous invocation), caller gets **HTTP 429**

## Trigger Words

- "Lambda function is being throttled / 429 error" → Check concurrency limits; consider Reserved Concurrency
- "Reduce Lambda cold start latency" → Provisioned Concurrency or SnapStart
- "Prevent one Lambda from consuming all account concurrency" → Reserved Concurrency
- "Java Lambda has slow startup" → SnapStart or Provisioned Concurrency
- "Pre-warm Lambda before peak traffic" → Provisioned Concurrency with Auto Scaling schedule
