# ASG Scaling Policies

## Overview

ASG supports multiple scaling policy types. Choosing the right one depends on whether demand is predictable, and how quickly you need to react.

---

## Policy Types

### 1. Manual Scaling
- Manually set the **Desired Capacity** in the ASG configuration
- No automation — you directly control the instance count
- Useful for planned events where you know the exact capacity needed

### 2. Dynamic Scaling

#### Simple Scaling
- Triggered by a **CloudWatch Alarm**
- When alarm fires, add or remove a fixed number of instances
- After scaling action, ASG waits for the **cooldown period** before scaling again (default: 300 seconds)
- **Limitation**: must wait out the cooldown — can be too slow for sudden spikes

Example:
```
Alarm: avg CPU > 50% → add 1 instance
Alarm: avg CPU > 75% → add 2 instances
Alarm: avg CPU < 40% → remove 1 instance
```

#### Step Scaling
- Similar to simple scaling but uses **step adjustments** — different scaling amounts based on how far the metric is from the threshold
- Does **NOT wait for cooldown** before reacting to new alarms
- More responsive to sudden demand changes than simple scaling

Example:
```
CPU 50–70%: add 1 instance
CPU 70–90%: add 2 instances
CPU > 90%: add 3 instances
```

#### Target Tracking Scaling
- You define a **target metric value** and ASG automatically adjusts capacity to maintain it
- No alarms to configure — ASG manages CloudWatch alarms internally
- Most commonly used and recommended for most workloads

Example targets:
- Keep average CPU utilization at 40%
- Keep ALB request count per target at 1000
- Keep SQS ApproximateNumberOfMessagesVisible / in-service instance count at a set value (**backlog-per-instance** metric)

### 3. Scheduled Scaling
- Define scaling actions for **known, recurring time windows**
- ASG changes min/max/desired at the scheduled time regardless of current metrics

Example:
```
Every Saturday 6pm: set minimum=10 (expect high traffic)
Every Saturday 11pm: set minimum=2 (traffic drops)
```

### 4. Predictive Scaling
- Uses **Machine Learning** to analyze historical traffic patterns and pre-emptively scale
- Scales out BEFORE demand hits (proactive, not reactive)
- Useful when workloads have recurring daily/weekly patterns

---

## Cooldown Period

- After a **simple scaling** action, ASG will not respond to new alarms until the cooldown expires (default 300 seconds)
- This prevents "thrashing" — constantly adding and removing instances
- **Target tracking and step scaling** have their own stabilization windows and do not use the same cooldown logic

---

## Scaling Policy Comparison

| Policy | Trigger | Reaction Speed | Best For |
|---|---|---|---|
| **Manual** | Manual input | N/A | Planned events, testing |
| **Simple** | CloudWatch alarm | Slow (waits for cooldown) | Stable, predictable loads |
| **Step** | CloudWatch alarm | Faster (no cooldown wait) | Variable but known thresholds |
| **Target Tracking** | Continuous metric target | Automatic, continuous | Most workloads, default recommendation |
| **Scheduled** | Time/calendar | Predetermined | Predictable recurring patterns |
| **Predictive** | ML forecasts | Pre-emptive | Workloads with historical patterns |

---

## SQS-Based Scaling (Target Tracking)

For worker fleets processing SQS queues, use Target Tracking with a custom metric:

```
backlog-per-instance = ApproximateNumberOfMessages / in-service instance count
Target: acceptable backlog-per-instance value (e.g., 100 messages per worker)
```

This is more accurate than simply tracking queue depth alone because it accounts for current instance count.

---

## Key Points / Exam Tips

- **Target Tracking** is the recommended policy for most scenarios — simplest to configure and most accurate
- **Simple Scaling** is the weakest for sudden spikes — must wait for cooldown before reacting again
- For SQS-backed worker fleets: use **Target Tracking with backlog-per-instance** (not raw queue depth)
- **Scheduled Scaling** is for predictable patterns only — NOT for unpredictable/sudden demand
- **Predictive Scaling** requires at least 2 weeks of historical data to make useful predictions
- Multiple scaling policies can run simultaneously; ASG uses the one that results in the **largest capacity change**

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Scale based on CPU / request count target" | Target Tracking scaling |
| "Scale every Wednesday night for batch jobs" | Scheduled scaling |
| "Sudden unpredictable spikes" | Target Tracking or Step scaling |
| "Scale based on SQS queue depth" | Target Tracking with backlog-per-instance metric |
| "Pre-scale before peak using historical data" | Predictive scaling |
| "Cooldown period" | Simple Scaling (cooldown between actions) |
| "Known recurring traffic pattern" | Scheduled Scaling |
