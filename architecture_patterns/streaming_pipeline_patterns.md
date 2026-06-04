# Streaming Pipeline Patterns

# What is Stream Processing?

Stream processing means:

* Data is processed continuously
* Events are handled in real time or near real time
* Data flows continuously through the system

Unlike batch processing:

* streaming does NOT wait for large datasets to accumulate

---

# Real-Life Examples

| Use Case             | Why Streaming?       |
| -------------------- | -------------------- |
| Fraud detection      | Need instant alerts  |
| Stock market systems | Millisecond updates  |
| IoT sensors          | Continuous telemetry |
| Social media feeds   | Live updates         |
| Ride-sharing apps    | Real-time tracking   |
| Gaming leaderboards  | Instant scoring      |

---

# Batch vs Streaming

| Batch Processing        | Streaming Processing     |
| ----------------------- | ------------------------ |
| Scheduled               | Continuous               |
| High latency            | Low latency              |
| Historical data         | Live data                |
| Simpler                 | More complex             |
| Cheaper                 | More expensive           |
| Example: Nightly report | Example: Fraud detection |

---

# Core Characteristics of Streaming Systems

| Feature              | Description                   |
| -------------------- | ----------------------------- |
| Continuous ingestion | Data always arriving          |
| Low latency          | Seconds or milliseconds       |
| Scalability          | Must handle spikes            |
| Fault tolerance      | Failures should not lose data |
| Ordering             | Event sequence matters        |
| Replay capability    | Reprocess old events          |

---

# Common AWS Streaming Services

| Service                | Purpose                       |
| ---------------------- | ----------------------------- |
| Kinesis Data Streams   | Real-time streaming ingestion |
| Kinesis Data Firehose  | Managed streaming delivery    |
| Kinesis Data Analytics | Streaming SQL analytics       |
| Amazon MSK             | Managed Kafka                 |
| Lambda                 | Event processing              |
| SQS                    | Queue buffering               |
| SNS                    | Pub/Sub messaging             |
| EventBridge            | Event routing                 |

---

# Typical Streaming Architecture

```text 
Applications / Devices
        ↓
Kinesis Data Streams
        ↓
Lambda / Analytics
        ↓
S3 / Redshift / DynamoDB
        ↓
Dashboards / Alerts
```

---

# Core Streaming Concepts

# Producer

A producer sends events into the stream.

Examples:

* Mobile app
* Website
* IoT sensor
* Backend service

---

# Consumer

A consumer reads and processes events.

Examples:

* Lambda function
* Analytics engine
* Fraud detector
* Monitoring system

---

# Stream

A continuous flow of events.

Example event:

```json 
{
  "user_id": 101,
  "action": "purchase",
  "amount": 250
}
```

---

# Event

An individual record in the stream.

Examples:

* Click event
* Payment event
* Sensor reading
* Login attempt

---

# Streaming Pipeline Flow

# Step 1 — Event Generation

Applications continuously generate data.

Examples:

* User clicks
* IoT telemetry
* Transactions

---

# Step 2 — Stream Ingestion

Events enter services like:

* Kinesis Data Streams
* MSK (Kafka)

Purpose:

* Buffer data
* Scale ingestion
* Enable multiple consumers

---

# Step 3 — Stream Processing

Processing includes:

* Filtering
* Aggregation
* Transformation
* Enrichment

AWS Services:

* Lambda
* Kinesis Analytics
* Apache Spark on EMR

---

# Step 4 — Data Delivery

Processed data goes to:

| Destination | Use Case            |
| ----------- | ------------------- |
| S3          | Data lake           |
| Redshift    | Analytics           |
| DynamoDB    | Fast lookup         |
| OpenSearch  | Search/log analysis |

---

# Kinesis Data Streams

Core real-time streaming service.

Features:

* Real-time ingestion
* Replay capability
* Multiple consumers
* Manual shard scaling

---

# Kinesis Shards

A shard is a unit of throughput.

Each shard supports:

* 1 MB/sec write
* 2 MB/sec read

More shards = more throughput.

---

# Important Exam Point

Kinesis Data Streams supports:

* replaying old data
* multiple consumers
* custom processing

This is VERY important.

---

# Kinesis Data Firehose

Fully managed delivery service.

Automatically sends streaming data to:

* S3
* Redshift
* OpenSearch

---

# Firehose vs Data Streams

| Firehose         | Data Streams         |
| ---------------- | -------------------- |
| Fully managed    | More control         |
| Auto scaling     | Manual shard scaling |
| No replay        | Replay supported     |
| Delivery focused | Processing focused   |
| Easier           | More flexible        |

---

# Exam Keyword Differences

| Keyword                     | Service              |
| --------------------------- | -------------------- |
| Replay data                 | Kinesis Data Streams |
| Simplest streaming delivery | Firehose             |
| Multiple consumers          | Data Streams         |
| Automatic loading to S3     | Firehose             |

---

# Streaming Processing Patterns

# 1. Real-Time Analytics Pattern

```text 
Applications
     ↓
Kinesis Streams
     ↓
Kinesis Analytics
     ↓
Dashboards
```

Use Cases:

* Live dashboards
* Monitoring
* Metrics

---

# 2. Stream-to-Lake Pattern

```text
Producers
    ↓
Firehose
    ↓
Amazon S3
```

Used for:

* Log ingestion
* Centralized storage
* Analytics pipelines

---

# 3. Event Processing Pattern

```text
Events
   ↓
Kinesis
   ↓
Lambda
   ↓
DynamoDB
```

Use Cases:

* Notifications
* Fraud detection
* Real-time enrichment

---

# 4. Multi-Consumer Pattern

```text
Kinesis Stream
    ↓
 ┌──────────┬──────────┬──────────┐
Analytics  Storage   Monitoring
```

Important advantage:

* One stream supports many consumers.

---

# Windowing in Streaming

Streams are infinite.

Windowing groups events into chunks.

Examples:

* Last 5 minutes
* Last 1 hour

Used for:

* Aggregation
* Trend analysis

---

# Types of Windows

| Window Type     | Description                   |
| --------------- | ----------------------------- |
| Tumbling Window | Fixed non-overlapping windows |
| Sliding Window  | Overlapping windows           |
| Session Window  | Based on activity gaps        |

---

# Exactly Once vs At Least Once

# At Least Once

Events may be duplicated.

Safer because:

* No data loss

Most common in distributed systems.

---

# Exactly Once

Events processed exactly one time.

Harder to implement.

More expensive and complex.

---

# Idempotency

A processing operation that produces the same result even if repeated.

Critical for streaming systems.

Example:

* Prevent duplicate payment processing

---

# Ordering in Streams

Sometimes event order matters.

Example:

* Banking transactions

Kinesis preserves order:

* Within a shard

Very important exam point.

---

# Replay Capability

Replay means:

* Re-reading old stream data

Useful for:

* Debugging
* Reprocessing
* Recovery

Kinesis Data Streams supports replay.

Firehose does NOT.

---

# Checkpointing

Checkpointing tracks:

* Last processed event

Helps:

* Recovery after failure

---

# Dead Letter Queues (DLQ)

Failed events are sent to DLQs.

Usually implemented using:

* Amazon SQS

Benefits:

* Prevents pipeline failures
* Allows later debugging

---

# Monitoring Streaming Pipelines

Use:

| Service    | Purpose             |
| ---------- | ------------------- |
| CloudWatch | Metrics and alarms  |
| X-Ray      | Distributed tracing |
| CloudTrail | API auditing        |

Important metrics:

* Iterator age
* Failed records
* Processing latency

---

# Security Best Practices

| Practice        | Purpose             |
| --------------- | ------------------- |
| IAM permissions | Access control      |
| KMS encryption  | Data protection     |
| VPC endpoints   | Private traffic     |
| TLS encryption  | Secure transmission |

---

# Cost Optimization

# Use Firehose for Simpler Pipelines

Why?

* Fully managed
* Lower operational overhead

---

# Proper Shard Sizing

Too many shards:

* Expensive

Too few shards:

* Throttling

---

# Use Compression

Reduce:

* S3 storage cost
* Network transfer cost

---

# Streaming Exam Scenarios

# Scenario 1

Question:

* Need real-time fraud detection with replay support

Best Answer:

* Kinesis Data Streams

---

# Scenario 2

Question:

* Need easiest way to deliver logs to S3

Best Answer:

* Kinesis Data Firehose

---

# Scenario 3

Question:

* Need multiple applications consuming same stream

Best Answer:

* Kinesis Data Streams

---

# Scenario 4

Question:

* Need fully managed Kafka

Best Answer:

* Amazon MSK

---

# Scenario 5

Question:

* Need event-driven serverless processing

Best Answer:

* Kinesis + Lambda

---

# Important Streaming Exam Keywords

| Keyword                     | Likely Service    |
| --------------------------- | ----------------- |
| Replay events               | Data Streams      |
| Managed delivery            | Firehose          |
| Kafka compatibility         | MSK               |
| Real-time analytics         | Kinesis Analytics |
| Serverless event processing | Lambda            |

---

# Common Exam Traps

# Trap 1

Choosing Firehose when replay is required.

Wrong because:

* Firehose cannot replay events.

---

# Trap 2

Using Lambda for extremely high-throughput analytics.

Better:

* Kinesis Analytics
* EMR Spark Streaming

---

# Trap 3

Assuming ordering across all shards.

Wrong because:

* Ordering is guaranteed only within a shard.

---

# Beginner to Advanced Learning Path

# Beginner

Understand:

* Streams
* Events
* Producers/Consumers

---

# Intermediate

Learn:

* Kinesis Streams
* Firehose
* Lambda processing
* Windowing

---

# Advanced

Master:

* Checkpointing
* Exactly-once semantics
* Replay strategies
* Multi-consumer architectures

---

# Batch + Streaming Together

Modern systems often combine both:

| Real-Time        | Batch                |
| ---------------- | -------------------- |
| Immediate alerts | Historical analytics |
| Fraud detection  | Business reporting   |
| Monitoring       | BI dashboards        |

This leads to:

* Lambda Architecture
* Kappa Architecture

These are advanced patterns covered later.

---

# Final Revision Notes

* Streaming = continuous processing
* Kinesis Streams supports replay
* Firehose is fully managed delivery
* Ordering guaranteed only within shard
* Lambda commonly processes stream events
* Windowing groups infinite streams
* DLQ helps handle failures
* MSK = managed Kafka
* Real-time systems are more complex than batch
