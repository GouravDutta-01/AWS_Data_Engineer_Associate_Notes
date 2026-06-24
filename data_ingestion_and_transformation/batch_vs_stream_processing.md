# Batch vs Stream Processing

# Why This Topic Matters

One of the MOST important concepts in:
- data engineering
- analytics architectures
- AWS DEA exam
- distributed systems

Almost every modern pipeline is either:
- batch
- streaming
- hybrid

Understanding the difference is critical.

---

# Simple Intuition

# Batch Processing

Process:
> large chunks of accumulated data together.

Example:

```text
Process yesterday's sales every night at 2 AM
```

---

# Stream Processing

Process:
> data continuously as events arrive.

Example:

```text
Process transactions instantly when users pay
```

---

# Batch Processing

# Core Idea

Data collected first,
processed later.

---

# Example Batch Pipeline

```text 
Application Logs
      ↓
S3 Storage
      ↓
Nightly Glue Job
      ↓
Athena Analytics
```

Very common architecture.

---

# Characteristics of Batch Processing

| Feature | Description |
|---|---|
| High throughput | Process huge volumes |
| Higher latency | Delayed results |
| Efficient large scans | Optimized bulk processing |
| Simpler systems | Easier debugging |

---

# Batch Processing Examples

- nightly ETL
- billing reports
- historical analytics
- warehouse loading
- monthly reports

---

# Stream Processing

# Core Idea

Process events continuously in real time.

---

# Example Streaming Pipeline

```text
Applications
      ↓
Kinesis Stream
      ↓
Lambda/Flink
      ↓
Real-Time Dashboard
```

Very common AWS streaming architecture.

---

# Characteristics of Streaming

| Feature | Description |
|---|---|
| Low latency | Near real-time |
| Continuous processing | Event-by-event |
| More complex | Stateful systems |
| Faster insights | Immediate analytics |

---

# Streaming Examples

- fraud detection
- IoT monitoring
- stock trading
- clickstream analytics
- live dashboards

---

# Latency Difference

MOST IMPORTANT comparison.

| Batch | Streaming |
|---|---|
| Minutes/hours | Milliseconds/seconds |

---

# Throughput vs Latency

Batch systems optimize for:
- throughput

Streaming systems optimize for:
- latency

VERY important systems concept.

---

# AWS Batch Services

| Service | Role |
|---|---|
| Glue | Batch ETL |
| EMR | Large batch analytics |
| Athena | Ad hoc batch queries |
| Redshift | Warehouse analytics |

---

# AWS Streaming Services

| Service | Role |
|---|---|
| Kinesis Data Streams | Real-time ingestion |
| Kinesis Firehose | Streaming delivery |
| Lambda | Event processing |
| Managed Flink | Stateful streaming |

---

# Batch Advantages

| Advantage | Why Important |
|---|---|
| Simpler architecture | Easier maintenance |
| Efficient for huge datasets | Bulk optimization |
| Easier replay/recovery | Historical recomputation |
| Lower operational complexity | Stable pipelines |

---

# Batch Disadvantages

| Problem | Why Important |
|---|---|
| High latency | Delayed insights |
| Not real-time | Slow reaction |
| Large processing windows | Delayed analytics |

---

# Streaming Advantages

| Advantage | Why Important |
|---|---|
| Real-time insights | Immediate actions |
| Continuous analytics | Live dashboards |
| Faster decisions | Fraud detection |

---

# Streaming Disadvantages

| Problem | Why Important |
|---|---|
| More complex systems | Stateful processing |
| Ordering issues | Distributed events |
| Duplicate handling | Idempotency needed |
| Harder debugging | Continuous systems |

---

# Stateful vs Stateless Streaming

VERY important streaming concept.

---

# Stateless

Each event processed independently.

Example:

```text
Convert currency format
```

---

# Stateful

Requires previous events/history.

Example:

```text 
Count transactions in last 5 minutes
```

Much more complex.

---

# Event Time vs Processing Time

VERY important streaming topic.

---

# Event Time

When event actually happened.

---

# Processing Time

When system processed event.

Events may arrive late/out-of-order.

Huge distributed systems challenge.

---

# Exactly Once vs At Least Once

Streaming systems must handle:
- duplicates
- retries

Important delivery guarantees:

| Guarantee | Meaning |
|---|---|
| At-most-once | Possible data loss |
| At-least-once | Possible duplicates |
| Exactly-once | Most complex |

VERY important exam concept.

---

# Windowing

Streaming systems process infinite data streams using:
- windows

Examples:
- tumbling windows
- sliding windows
- session windows

Very important modern streaming concept.

---

# Lambda Architecture Connection

Lambda combines:
- batch layer
- streaming layer

to achieve:
- low latency
- eventual correctness

---

# Kappa Architecture Connection

Kappa removes:
- batch layer

Uses:
- streaming only

with replay capability.

---

# Batch vs Streaming Cost

Streaming systems often:
- cost more operationally
- require always-running infrastructure

Batch systems:
- cheaper for periodic workloads

---

# Hybrid Architectures

Most real-world systems use BOTH.

Example:

```text
Streaming → real-time dashboard
Batch → daily reporting
```

Very common enterprise design.

---

# Example Modern AWS Architecture

```text 
Applications
      ↓
Kinesis
      ↓
Real-Time Dashboard

ALSO

S3 Raw Events
      ↓
Glue Batch ETL
      ↓
Athena/Redshift Analytics
```

Very realistic architecture.

---

# Replay and Recovery

Batch systems:
- easier replay

Streaming systems:
- need checkpointing
- offsets
- replay logs

---

# Common AWS Exam Scenarios

# Scenario 1

Question:
Need immediate fraud detection.

Best Answer:
- streaming

---

# Scenario 2

Question:
Need nightly reporting.

Best Answer:
- batch ETL

---

# Scenario 3

Question:
Need both real-time and historical analytics.

Best Answer:
- hybrid architecture

---

# Common Beginner Mistakes

# Mistake 1

Thinking streaming always better.

Streaming adds:
- complexity
- operational challenges

---

# Mistake 2

Ignoring replay/failure handling.

Critical in streaming systems.

---

# Mistake 3

Using batch for ultra-low-latency workloads.

Not suitable.

---

# Think Like AWS

AWS modern analytics heavily combines:

```text 
Streaming ingestion
+
S3 historical storage
+
Batch analytics
```

This hybrid pattern dominates modern architectures.

---

# Beginner → Intermediate → Advanced

# Beginner

Understand:
- batch vs streaming basics

---

# Intermediate

Learn:
- stateful streaming
- windows
- delivery guarantees

---

# Advanced

Master:
- Flink internals
- event-time processing
- checkpointing
- large-scale stream architectures

---

# Final Revision Notes

- Batch processes accumulated data periodically
- Streaming processes events continuously
- Batch optimizes throughput
- Streaming optimizes latency
- Kinesis is core AWS streaming service
- Glue/EMR commonly used for batch
- Streaming systems are more complex
- Hybrid architectures are most common in real world