# Event Driven Architecture

# What Problem Does Event-Driven Architecture Solve?

In traditional systems, applications are often tightly connected.

Example:

```text
Order Service → Payment Service → Email Service → Inventory Service
```

This works at small scale.

But as systems grow:
- dependencies increase
- failures spread
- scaling becomes difficult
- deployments become risky

This is where Event-Driven Architecture (EDA) becomes useful.

Instead of services directly calling each other,
services communicate using events.

---

# Simple Intuition

Think about YouTube notifications.

When a creator uploads a video:

- subscribers get notifications
- analytics update
- recommendations update
- emails may be sent

The upload service does NOT directly call every system.

Instead:

```text
"Video Uploaded" event is generated
```

Other services react independently.

That is event-driven architecture.

---

# Core Idea

In EDA:

- something happens
- an event is generated
- interested systems react to it

This creates:
- loose coupling
- better scalability
- easier system evolution

---

# What is an Event?

An event is simply:

> A record that something happened.

Examples:

```json
{
  "event": "order_created",
  "order_id": 101
}
```

```json
{
  "event": "payment_completed",
  "user_id": 55
}
```

```json
{
  "event": "file_uploaded"
}
```

---

# Real World Examples

| System | Event |
|---|---|
| Ecommerce | Order placed |
| Banking | Transaction completed |
| Ride sharing | Driver arrived |
| IoT | Sensor threshold exceeded |
| Social media | User posted content |

---

# Traditional vs Event-Driven Systems

# Traditional Architecture

```text
Service A → Service B → Service C
```

Problems:
- tightly coupled
- difficult scaling
- failures cascade
- hard to modify

---

# Event-Driven Architecture

```text
Service A
   ↓
Event Bus
   ↓
Service B
Service C
Service D
```

Advantages:
- services independent
- easier scaling
- flexible integrations
- fault isolation

---

# Core Components of Event-Driven Architecture

# 1. Producer

The producer generates events.

Examples:
- ecommerce application
- mobile app
- IoT device

Producer only emits events.

It usually does NOT know:
- who consumes them
- how many consumers exist

This is extremely important.

---

# 2. Event Bus / Broker

Acts like a middle layer.

Receives events and distributes them.

AWS Services:
- EventBridge
- SNS
- Kinesis
- SQS

---

# 3. Consumer

Consumes and processes events.

Examples:
- Lambda function
- analytics service
- notification system

---

# Basic Event Flow

```text
User Places Order
        ↓
Order Created Event
        ↓
EventBridge / SNS
        ↓
 ┌──────────┬──────────┬──────────┐
Email     Billing    Analytics
Service   Service     Service
```

This is a classic AWS architecture pattern.

---

# Why Event-Driven Systems Are Popular

Modern systems need:
- scalability
- microservices
- asynchronous processing
- real-time reactions

EDA helps achieve all of these.

---

# Key Benefits

# Loose Coupling

Services become independent.

Example:
- adding a new consumer does NOT affect producer

Huge advantage at scale.

---

# Scalability

Consumers can scale independently.

Example:
- notification service scales separately
- analytics scales separately

---

# Fault Isolation

If one consumer fails:
- other consumers continue working

This improves reliability.

---

# Asynchronous Processing

Producer does not wait for consumer completion.

Example:
- order placed instantly
- email sent later

Improves application responsiveness.

---

# Important AWS Services in EDA

# Amazon EventBridge

Event routing service.

Best for:
- event buses
- SaaS integrations
- application integration

Think of it as:
> intelligent event router

---

# Amazon SNS

Pub/Sub messaging service.

One message can go to:
- multiple subscribers

Subscribers may include:
- Lambda
- SQS
- HTTP endpoints
- email

---

# Amazon SQS

Queue service.

Used for:
- buffering
- decoupling
- retry handling

Very common in event-driven systems.

---

# AWS Lambda

Serverless compute service.

Perfect for:
- event-triggered processing

Example:
- file uploaded to S3
- Lambda automatically runs

This is one of the most common AWS patterns.

---

# Kinesis

Used for:
- real-time streaming events

Important difference:

| Service | Purpose |
|---|---|
| SNS | Messaging |
| SQS | Queue buffering |
| EventBridge | Event routing |
| Kinesis | Real-time streaming |

---

# Common AWS Event-Driven Patterns

# 1. S3 Event Processing Pattern

```text
File Uploaded to S3
        ↓
S3 Event Trigger
        ↓
Lambda Function
        ↓
Data Processing
```

Very common exam scenario.

---

# 2. SNS + SQS Fanout Pattern

```text
Application
    ↓
SNS Topic
    ↓
 ┌──────────┬──────────┬──────────┐
SQS Queue  SQS Queue  Lambda
```

Why use this?

Because:
- one event reaches many systems
- queues provide durability
- systems stay decoupled

---

# 3. EventBridge Routing Pattern

```text
Applications
      ↓
EventBridge
      ↓
Different Targets Based on Rules
```

EventBridge can route:
- based on event type
- source
- metadata

Very flexible.

---

# 4. Streaming Event Pattern

```text
Applications
      ↓
Kinesis Streams
      ↓
Analytics / Lambda / Storage
```

Used for:
- real-time analytics
- telemetry
- fraud detection

---

# Synchronous vs Asynchronous Systems

# Synchronous

Producer waits for response.

```text
Service A → Service B
(waiting...)
```

Problems:
- slower
- tightly coupled

---

# Asynchronous

Producer continues immediately.

```text
Service A → Queue/Event
```

Consumer processes later.

This is the heart of event-driven systems.

---

# Event Ordering

Sometimes order matters.

Example:
- banking transactions

Kinesis preserves order:
- within a shard

SQS standard queues:
- no guaranteed ordering

FIFO queues:
- preserve ordering

Very common exam trap.

---

# Idempotency

Event systems sometimes deliver duplicates.

Applications should safely handle repeated events.

Example:
- avoid charging payment twice

This concept appears often in:
- Lambda
- streaming systems
- distributed architectures

---

# Retries and Failures

Failures are normal in distributed systems.

AWS commonly uses:
- retries
- dead-letter queues (DLQ)

Example:

```text
Lambda Failure
      ↓
Message moved to DLQ
```

Usually:
- SQS acts as DLQ

---

# Dead Letter Queues (DLQ)

DLQs store failed messages.

Benefits:
- prevent data loss
- easier debugging
- retry later

Very important exam concept.

---

# Event Replay

Replay means:
- reprocessing past events

Useful for:
- debugging
- analytics correction
- recovery

Kinesis supports replay.

SNS does not.

---

# Eventual Consistency

In distributed event systems:

all systems may NOT update instantly.

Example:
- payment succeeds
- analytics updates few seconds later

This is normal.

---

# Monitoring Event-Driven Systems

Important AWS services:

| Service | Purpose |
|---|---|
| CloudWatch | Metrics and alarms |
| CloudTrail | API auditing |
| X-Ray | Distributed tracing |

---

# Security Best Practices

# IAM Least Privilege

Only allow required permissions.

---

# Encryption

Use:
- KMS
- TLS

to secure events.

---

# Private Communication

Use:
- VPC endpoints
- private networking

for secure architectures.

---

# Cost Optimization

# Use Serverless Services

AWS generally prefers:
- Lambda
- EventBridge
- SNS
- SQS

because:
- no infrastructure management
- auto scaling
- pay per use

---

# Avoid Overengineering

Not every system needs:
- Kafka
- complex streaming

Sometimes:
- SQS + Lambda is enough

Very important real-world lesson.

---

# Common AWS Exam Scenarios

# Scenario 1

Question:
Need loosely coupled microservices.

Best Answer:
- SNS
- SQS
- EventBridge

---

# Scenario 2

Question:
Need reliable buffering between services.

Best Answer:
- SQS

---

# Scenario 3

Question:
Need one event delivered to many systems.

Best Answer:
- SNS fanout

---

# Scenario 4

Question:
Need advanced event routing.

Best Answer:
- EventBridge

---

# Scenario 5

Question:
Need automatic processing after S3 upload.

Best Answer:
- S3 Event + Lambda

---

# Common Exam Traps

# Trap 1

Using SQS when broadcasting to multiple systems is needed.

Wrong because:
- SQS delivers message to one consumer

Better:
- SNS fanout

---

# Trap 2

Assuming standard SQS guarantees ordering.

Wrong.
Only FIFO queues guarantee ordering.

---

# Trap 3

Using Lambda for extremely long-running workloads.

Lambda has:
- timeout limits

Better:
- Step Functions
- ECS
- EMR

depending on workload.

---

# Think Like AWS

AWS usually prefers:

- managed services
- decoupled systems
- asynchronous communication
- serverless architectures

That is why exam answers often prefer:
- SQS over direct service calls
- EventBridge for routing
- Lambda for event processing

---

# Final Revision Notes

- Event-driven systems are loosely coupled
- SNS = pub/sub
- SQS = queue buffering
- EventBridge = event routing
- Lambda works extremely well with events
- DLQs handle failures
- FIFO queues preserve order
- Kinesis supports replay
- Event-driven systems are asynchronous
- AWS strongly prefers decoupled architectures