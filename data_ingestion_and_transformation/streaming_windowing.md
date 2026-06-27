# Streaming Windowing

# Why Windowing Exists

Streams are theoretically infinite.

Example:

```text
User clicks
Transactions
IoT events
Logs
```

These never stop.

So the question becomes:

```text
How do we aggregate infinite data?
```

Answer:

> Windows

---

# What is a Window?

A window is:

> a finite chunk of a continuous stream.

Instead of processing:

```text
All events forever
```

we process:

```text
Events in a specific time range
```

---

# Example

Count purchases:

```text
Last 5 minutes
```

instead of:

```text
Since company started
```

---

# Simple Intuition

Windowing for streams is similar to:

```text
GROUP BY time
```

for continuous data.

---

# Most Important Window Types

1. Tumbling Window
2. Sliding Window
3. Session Window

These are the most tested.

---

# Tumbling Window

Fixed size.

No overlap.

---

# Example

Window Size:

```text
5 minutes
```

Windows:

```text
10:00 - 10:05
10:05 - 10:10
10:10 - 10:15
```

---

# Visualization

```text
|----|
     |----|
          |----|
```

No overlap.

---

# Use Cases

- hourly reports
- transaction counts
- metrics aggregation

---

# Tumbling Window Advantages

- simple
- predictable
- efficient

---

# Sliding Window

Fixed size.

Windows overlap.

---

# Example

Window:

```text
5 minutes
```

Slide:

```text
1 minute
```

---

# Windows

```text
10:00-10:05
10:01-10:06
10:02-10:07
```

---

# Visualization

```text
|-----|
 |-----|
  |-----|
```

Overlap exists.

---

# Why Sliding Windows?

Provides smoother analytics.

Example:

```text
Moving average
```

---

# Use Cases

- fraud detection
- trend analysis
- anomaly detection

---

# Session Window

Most important real-world concept.

Groups events based on user activity.

---

# Example

User activity:

```text
10:00 click
10:01 click
10:02 click

(no activity)

10:20 click
10:21 click
```

---

# Session Timeout

Suppose:

```text
10 minutes
```

Then:

Session 1:

```text
10:00 - 10:02
```

Session 2:

```text
10:20 - 10:21
```

---

# Visualization

```text
[click click click]

gap

[click click]
```

---

# Why Session Windows Matter

User behavior is naturally session-based.

Examples:

- websites
- mobile apps
- streaming platforms

---

# Event Time vs Processing Time

VERY important concept.

---

# Event Time

When event occurred.

Example:

```text
10:00 AM
```

---

# Processing Time

When system receives event.

Example:

```text
10:03 AM
```

---

# Late Arriving Events

Real systems often receive events late.

Example:

```text
Network delay
```

Event happened at:

```text
10:00
```

Received at:

```text
10:05
```

---

# Why This Is Hard

Window already closed.

Now system must decide:

```text
Accept late event?
Ignore?
Recompute?
```

---

# Watermarks

Watermarks help manage late events.

Simple intuition:

```text
Wait a little before finalizing windows
```

---

# Example

Window ends:

```text
10:05
```

Watermark:

```text
2 minutes
```

Finalize at:

```text
10:07
```

Allows late arrivals.

---

# AWS Streaming Services

Windowing commonly used with:

- Amazon Managed Service for Apache Flink
- Kinesis Data Analytics
- Spark Structured Streaming

---

# Example Fraud Detection

```text
Count transactions
in last 5 minutes
```

This is a sliding window.

---

# Example IoT Monitoring

```text
Average temperature
every 1 minute
```

This is a tumbling window.

---

# Example User Analytics

```text
Track user sessions
```

This is a session window.

---

# Common AWS Exam Thinking

Question:

Need rolling average.

Best Answer:

```text
Sliding Window
```

---

Question:

Need hourly report.

Best Answer:

```text
Tumbling Window
```

---

Question:

Need user session analytics.

Best Answer:

```text
Session Window
```

---

# Common Beginner Mistakes

# Mistake 1

Confusing tumbling and sliding windows.

---

# Mistake 2

Ignoring late-arriving events.

---

# Mistake 3

Thinking event time equals processing time.

They often differ.

---

# Final Revision Notes

- Streams are infinite
- Windows create finite processing units
- Tumbling windows do not overlap
- Sliding windows overlap
- Session windows are activity-based
- Event time and processing time differ
- Watermarks handle late events
- Windowing is fundamental to streaming analytics