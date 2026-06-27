# Retry and Dead Letter Queue (DLQ) Patterns

# Why This Topic Matters

Failures are normal in distributed systems.

Examples:

- Network failures
- Database outages
- API timeouts
- Service throttling
- Temporary infrastructure issues

A good data pipeline must handle failures gracefully.

This is a VERY important AWS DEA topic because many AWS services support:
- retries
- DLQs
- failure handling

---

# Simple Intuition

Imagine:

```text
Order Event
      ↓
Lambda
      ↓
Database
```

What if database is temporarily unavailable?

Without retries:

```text
Event Lost
```

Bad.

With retries:

```text
Try Again
```

Much better.

---

# What is a Retry?

A retry means:

> Re-attempting an operation after failure.

---

# Example

```text
API Call
   ↓
Failure
   ↓
Retry
   ↓
Success
```

Very common pattern.

---

# Why Retries Work

Many failures are temporary.

Examples:

- transient network issue
- temporary throttling
- short service outage

Waiting and retrying often succeeds.

---

# Retry Example

Attempt 1:

```text
Database Timeout
```

Attempt 2:

```text
Database Timeout
```

Attempt 3:

```text
Success
```

No data loss.

---

# Retry Strategies

Most systems do not retry immediately.

Instead they wait.

---

# Fixed Retry

Example:

```text
Retry every 5 seconds
```

---

# Flow

```text
Fail
 ↓
5 sec
 ↓
Retry
```

Simple but not ideal.

---

# Exponential Backoff

Most important retry strategy.

---

# Example

```text
Retry 1 → 1 sec
Retry 2 → 2 sec
Retry 3 → 4 sec
Retry 4 → 8 sec
```

Wait time increases.

---

# Why Exponential Backoff?

Suppose service is overloaded.

Immediate retries create:

```text
More Load
```

making problem worse.

Backoff reduces pressure.

---

# Visualization

```text
1 sec
2 sec
4 sec
8 sec
16 sec
```

Very common AWS behavior.

---

# Jitter

Advanced concept.

Instead of:

```text
All clients retry at 8 sec
```

add randomness:

```text
7 sec
9 sec
10 sec
```

This avoids retry storms.

---

# Retry Storm

Imagine:

```text
100,000 Lambdas
```

all retry simultaneously.

Service becomes overloaded again.

Called:

```text
Retry Storm
```

Jitter helps prevent this.

---

# Retry Limits

Retries must be bounded.

Bad:

```text
Retry forever
```

Good:

```text
Retry 3 times
Retry 5 times
```

After limit reached:

```text
Move to DLQ
```

---

# What is a DLQ?

DLQ = Dead Letter Queue

A special queue for failed messages.

---

# Simple Intuition

Normal queue:

```text
Message
   ↓
Consumer
```

If processing repeatedly fails:

```text
Message
   ↓
DLQ
```

instead of being lost.

---

# Why DLQ Exists

Without DLQ:

```text
Message Lost
```

With DLQ:

```text
Message Saved
```

for investigation.

---

# DLQ Flow

```text
Main Queue
      ↓
Consumer
      ↓
Fail
      ↓
Retry
      ↓
Retry
      ↓
Retry
      ↓
DLQ
```

Very common architecture.

---

# Example

Message:

```json
{
  "customerId": null
}
```

Application expects:

```text
customerId must exist
```

Processing fails every time.

Retries won't help.

Move to DLQ.

---

# Transient vs Permanent Failures

VERY IMPORTANT concept.

---

# Transient Failure

Temporary.

Examples:

- network issue
- database restart
- throttling

Retries often succeed.

---

# Permanent Failure

Data itself is bad.

Examples:

```json
{
  "customerId": null
}
```

Retries won't help.

Needs investigation.

DLQ is useful here.

---

# AWS SQS and DLQ

Most common AWS DLQ pattern.

---

# Architecture

```text
SQS Queue
    ↓
Lambda
    ↓
Failure
    ↓
DLQ
```

---

# Max Receive Count

SQS configuration:

```text
maxReceiveCount = 5
```

Meaning:

```text
Fail 5 times
↓
Move to DLQ
```

---

# Lambda Retries

Lambda retry behavior depends on trigger.

---

# Synchronous Invocation

Example:

```text
API Gateway
 ↓
Lambda
```

Caller handles retries.

---

# Asynchronous Invocation

Example:

```text
EventBridge
 ↓
Lambda
```

Lambda retries automatically.

---

# SQS + Lambda

Flow:

```text
SQS
 ↓
Lambda
```

Failed messages become visible again.

Eventually:

```text
DLQ
```

after retry limit.

---

# EventBridge DLQ

EventBridge can send failed events to:

```text
SQS DLQ
```

Very common design.

---

# SNS DLQ

SNS supports:

```text
DLQ targets
```

for undeliverable messages.

---

# Kinesis Failure Handling

Kinesis does not use DLQs directly.

Common approach:

```text
Failed Records
      ↓
S3 Error Bucket
```

for replay.

---

# Kafka Equivalent

Kafka often uses:

```text
Dead Letter Topics
```

instead of DLQs.

Same concept.

---

# Retry vs DLQ

| Retry | DLQ |
|---------|---------|
| Temporary recovery | Permanent failure handling |
| Automatic | Manual investigation |
| First response | Last resort |

---

# Idempotency

VERY IMPORTANT.

Retries may process same event multiple times.

---

# Example Problem

```text
Charge Credit Card
```

Retry occurs.

Customer charged twice.

Bad.

---

# Idempotent Design

Operation can run multiple times safely.

Example:

```text
Order ID 123 already processed
```

Ignore duplicate.

---

# Exactly Once vs At Least Once

Retries create duplicates.

Therefore many systems provide:

```text
At-Least-Once Delivery
```

Meaning:

```text
Duplicates Possible
```

Consumers must handle them.

---

# Monitoring DLQs

DLQs should never be ignored.

Monitor:

- DLQ size
- failure rate
- retry counts

Growing DLQ often indicates production issue.

---

# Common AWS Exam Scenarios

# Scenario 1

Question:

Temporary API failure.

Best Answer:

```text
Retry with exponential backoff
```

---

# Scenario 2

Question:

Poison message repeatedly failing.

Best Answer:

```text
Move to DLQ
```

---

# Scenario 3

Question:

Need failed messages preserved.

Best Answer:

```text
Dead Letter Queue
```

---

# Common Beginner Mistakes

# Mistake 1

Retrying forever.

Can overload systems.

---

# Mistake 2

No DLQ configured.

Leads to message loss.

---

# Mistake 3

Ignoring idempotency.

Retries create duplicates.

---

# Think Like AWS

A resilient AWS architecture often looks like:

```text
SQS
 ↓
Lambda
 ↓
Retry
 ↓
DLQ
```

This pattern appears everywhere.

---

# Final Revision Notes

- Retries handle temporary failures
- DLQs handle permanent failures
- Exponential backoff is preferred
- Jitter prevents retry storms
- SQS supports DLQs natively
- Lambda integrates with retries and DLQs
- DLQs preserve failed messages
- Idempotency is critical with retries
- One of the most important reliability patterns