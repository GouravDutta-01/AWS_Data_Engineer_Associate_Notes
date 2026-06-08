# Kappa Architecture

# Why Kappa Architecture Exists

Lambda Architecture solved a big problem:

- real-time processing (speed layer)
- accurate batch processing (batch layer)

But it created a new problem:

> You now maintain TWO systems doing the same logic.

Problems:
- duplicate code (batch + stream)
- higher maintenance cost
- debugging complexity
- inconsistent logic between layers

So engineers asked:

 “Can we simplify this architecture?”

Kappa Architecture is the answer.

---

# Core Idea of Kappa Architecture

Instead of two layers (batch + speed):

> Use ONLY streaming for everything.

```text
Data → Stream → Processing → Storage → Analytics
```

No separate batch layer.

---

# Simple Intuition

Think of Kappa as:

> “Everything is a stream”

Even historical data is replayed as a stream.

---

# Key Concept: Replay Instead of Batch Layer

In Lambda:
- batch layer recomputes everything

In Kappa:
- we replay the event stream instead

So instead of:

```text
Recompute entire dataset using batch jobs
```

We do:

```text
Replay events from the beginning in stream system
```

---

# Basic Architecture

```text
          +------------------+
          |   Event Source   |
          +--------+---------+
                   |
                   v
          +------------------+
          |  Streaming Log   |
          | (Kafka / Kinesis)|
          +--------+---------+
                   |
                   v
          +------------------+
          | Stream Processing|
          | (Flink / Spark   |
          |  Streaming)      |
          +--------+---------+
                   |
                   v
          +------------------+
          | Storage / Views  |
          +------------------+
```

---

# Key Component: Append-Only Log

Kappa architecture depends heavily on:

- Kafka
- Kinesis Data Streams

These act as:
> immutable event logs

They store full history for replay.

---

# Why Replay is the Key Idea

Instead of batch recomputation:

```text
Old data is reprocessed by replaying stream
```

So:
- same pipeline
- same logic
- same system

Just different starting point.

---

# Example

Suppose bug found in logic:

Bad logic:
```text
tax = amount * 0.08
```

Correct logic:
```text
tax = amount * 0.18
```

In Kappa:

```text
Replay all events from Kafka/Kinesis
Apply corrected logic
Rebuild output tables
```

---

# Kappa vs Lambda

| Feature | Lambda | Kappa |
|---|---|---|
| Batch layer | Yes | No |
| Speed layer | Yes | Yes |
| Code duplication | Yes | No |
| Complexity | High | Lower |
| Replay | Batch recompute | Stream replay |
| Storage | S3 + streams | Streams + storage |

---

# Key Difference

Lambda:
- two systems (batch + stream)

Kappa:
- one system (streaming only)

---

# Where Does S3 Fit in Kappa?

S3 is still used, but differently:

- stores raw event backups
- stores processed outputs
- supports long-term storage

But NOT as a batch processing engine.

---

# Kappa Architecture in AWS Context

Typical AWS setup:

```text
Applications
    ↓
Kinesis Data Streams
    ↓
Kinesis Analytics / Lambda / Flink
    ↓
S3 / Redshift / DynamoDB
```

Replay = reading Kinesis stream again.

---

# Why Kappa Became Popular

Because it removes:

- duplicate pipelines
- batch complexity
- sync issues between batch and stream

Simpler mental model:
> everything is streaming

---

# But Kappa Has Tradeoffs

Kappa is NOT perfect.

## Problem 1: Replay Cost

Replaying massive streams can be:
- expensive
- time-consuming

---

## Problem 2: Long-Term Storage

Streaming systems like Kafka/Kinesis:
- are NOT meant for infinite storage

So you still need:
- S3 for archival

---

## Problem 3: Complex State Management

Stream systems need:
- windowing
- stateful processing
- checkpointing

---

# Real-World Reality

Most companies today use:

> Hybrid architectures

Not pure Lambda or pure Kappa.

---

# Common Hybrid Setup

```text
Kafka/Kinesis → Stream Processing → Real-time Views
                      ↓
                    S3 (archive)
                      ↓
            Batch reprocessing if needed
```

So:
- streaming is primary
- batch exists for recovery/backfills

---

# When to Use Kappa Architecture

Good for:
- real-time analytics
- event-driven systems
- log processing
- monitoring systems

Not ideal for:
- heavy batch transformations
- very large historical recomputation workloads

---

# AWS Exam Thinking

If question says:

- “simplify architecture”
- “avoid batch + streaming duplication”
- “real-time pipeline only”

Answer is usually Kinesis + streaming (Kappa-style)

---

# Key Takeaway

Kappa Architecture is:

> Lambda Architecture without batch layer

But with strong dependency on:
- streaming logs (Kafka/Kinesis)
- replay capability

---

# Final Revision Notes

- Kappa uses ONLY streaming
- batch layer is removed
- replay replaces batch recomputation
- Kafka/Kinesis act as system of record
- S3 used for long-term storage
- simpler than Lambda but not always cheaper
- widely used in modern event-driven systems