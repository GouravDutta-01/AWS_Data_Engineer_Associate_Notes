# Change Data Capture (CDC) Patterns

# What is CDC?

CDC stands for:

> Change Data Capture

It means:
instead of copying an entire database repeatedly,
we only capture the changes.

These changes can be:

* INSERT
* UPDATE
* DELETE

This is one of the most important concepts in modern data engineering.

---

# Why CDC Exists

Imagine a production database with:

* millions of rows
* continuous updates
* large traffic

Copying the full database every few minutes would be:

* slow
* expensive
* inefficient

Instead:
we only capture what changed.

That is CDC.

---

# Simple Intuition

Without CDC:

```text
Every Hour:
Copy Entire Database
```

With CDC:

```text
Every Hour:
Copy Only Changed Records
```

Huge difference at scale.

---

# Real World Example

Imagine an ecommerce database.

Customer updates:

* address
* phone number
* order status

Instead of re-copying every customer row,
CDC captures only modified rows.

This reduces:

* network usage
* processing time
* storage cost

---

# Why CDC is Extremely Important

Modern companies need:

* near real-time analytics
* streaming pipelines
* low-latency dashboards
* database replication

CDC enables all of these.

---

# Common CDC Use Cases

| Use Case             | Why CDC Helps                   |
| -------------------- | ------------------------------- |
| Real-time analytics  | Fast updates                    |
| Data warehouses      | Incremental loading             |
| Database replication | Sync systems                    |
| Event-driven systems | Generate events from DB changes |
| Auditing             | Track modifications             |

---

# Traditional ETL vs CDC

| Traditional ETL  | CDC                 |
| ---------------- | ------------------- |
| Full reloads     | Incremental changes |
| Expensive        | Efficient           |
| Higher latency   | Lower latency       |
| More compute     | Less compute        |
| Slower pipelines | Faster pipelines    |

---

# Basic CDC Architecture

```text
Application Database
         ↓
     CDC Capture
         ↓
Streaming / Queue
         ↓
S3 / Redshift / Analytics
```

Very common modern architecture.

---

# How CDC Works Internally

CDC systems usually monitor:

* transaction logs
* database logs
* binlogs
* WAL logs

instead of scanning tables repeatedly.

This is much more efficient.

---

# Example: MySQL Binlog

MySQL stores changes in:

* binary logs (binlogs)

CDC tools read these logs and capture:

* inserts
* updates
* deletes

without querying full tables.

---

# Example: PostgreSQL WAL

PostgreSQL uses:

* Write Ahead Logs (WAL)

CDC tools read WAL entries to track changes.

---

# Common CDC Approaches

# 1. Timestamp-Based CDC

Tracks rows using:

* updated_at column

Example:

```sql
SELECT *
FROM orders
WHERE updated_at > last_run_time;
```

Simple but limited.

---

# Problems with Timestamp CDC

Can miss:

* deletes
* clock issues
* improperly updated rows

Works only for simpler systems.

---

# 2. Log-Based CDC

Most important modern approach.

Reads:

* database transaction logs

Advantages:

* efficient
* accurate
* low latency
* captures deletes

Widely used in production systems.

---

# 3. Trigger-Based CDC

Database triggers generate change records.

Example:

* insert trigger writes to audit table

Problems:

* performance overhead
* complexity

Less preferred today.

---

# AWS Services Used in CDC

| Service  | Purpose           |
| -------- | ----------------- |
| AWS DMS  | CDC replication   |
| Kinesis  | Streaming changes |
| S3       | Storage           |
| Glue     | ETL               |
| Redshift | Analytics         |
| Lambda   | Event processing  |

---

# AWS DMS (Database Migration Service)

Very important AWS DEA service.

Supports:

* database migration
* continuous replication
* CDC pipelines

Extremely common in exam questions.

---

# Basic AWS CDC Pipeline

```text
RDS / MySQL
      ↓
AWS DMS
      ↓
Kinesis / S3
      ↓
Redshift / Athena
```

This is a HIGHLY important architecture.

---

# CDC + Streaming

CDC is heavily connected to streaming systems.

Database changes become:

* event streams

Example:

```text
Database Change
      ↓
Kinesis Stream
      ↓
Real-Time Analytics
```

Very common in modern architectures.

---

# CDC + Event-Driven Systems

Modern systems often convert:
database changes → events

Example:

```text
Order Inserted
      ↓
CDC Event
      ↓
SNS / EventBridge
      ↓
Consumers React
```

This is a powerful architecture pattern.

---

# CDC + Lakehouse

Modern lakehouses often use CDC for:

* incremental updates
* upserts
* near real-time analytics

Especially with:

* Hudi
* Iceberg
* Delta Lake

---

# Upserts

Very important concept.

Upsert means:

* INSERT if row doesn't exist
* UPDATE if row exists

CDC pipelines commonly require this.

---

# Deletes in CDC

Handling deletes is tricky.

Example:

* row removed from source DB

Pipeline must also:

* remove or mark deleted records

This is where CDC becomes more complex.

---

# Soft Deletes

Instead of deleting rows:

```text
is_deleted = true
```

Benefits:

* easier recovery
* easier auditing
* safer analytics

Very common enterprise pattern.

---

# Schema Evolution in CDC

Real systems constantly change schemas.

Example:

* new columns added

CDC systems must handle:

* evolving schemas
* backward compatibility

Very important advanced topic.

---

# Ordering in CDC

Change order matters.

Example:

```text
UPDATE balance
DELETE account
```

Wrong ordering can corrupt data.

Streaming systems like Kinesis help preserve ordering.

---

# Exactly Once Processing

CDC pipelines try to avoid:

* duplicate updates
* inconsistent states

This is difficult in distributed systems.

Very advanced but important concept.

---

# Common File Formats

CDC pipelines commonly use:

* Parquet
* Avro
* JSON

Parquet is usually preferred for analytics.

---

# CDC + Data Warehouses

Instead of full reloads:

CDC continuously updates:

* Redshift
* Snowflake
* BigQuery

This enables:

* near real-time dashboards

---

# Common AWS Exam Scenarios

# Scenario 1

Question:
Need continuous replication from MySQL to Redshift.

Best Answer:

* AWS DMS with CDC

---

# Scenario 2

Question:
Need near real-time analytics from OLTP database.

Best Answer:

* CDC + Kinesis + Redshift/Athena

---

# Scenario 3

Question:
Need minimal load on source database.

Best Answer:

* Log-based CDC

---

# Scenario 4

Question:
Need ongoing database synchronization.

Best Answer:

* AWS DMS continuous replication

---

# Common Beginner Mistakes

# Mistake 1

Reloading full tables repeatedly.

This becomes:

* expensive
* slow
* unscalable

CDC solves this.

---

# Mistake 2

Ignoring deletes.

Deletes are often the hardest part of CDC systems.

---

# Mistake 3

Thinking CDC is only for migrations.

Actually CDC is heavily used in:

* analytics
* streaming
* event-driven systems

---

# Think Like a Data Engineer

At small scale:

* full reloads work

At large scale:

* incremental pipelines become necessary

This is where CDC becomes critical.

---

# Think Like AWS

AWS strongly prefers:

* managed replication
* streaming architectures
* incremental processing

That is why:

* DMS
* Kinesis
* S3
* Glue

often appear together in CDC pipelines.

---

# Beginner → Intermediate → Advanced

# Beginner

Understand:

* what CDC is
* why full reloads are inefficient

---

# Intermediate

Learn:

* DMS
* log-based CDC
* incremental pipelines

---

# Advanced

Master:

* streaming CDC
* upserts
* ordering guarantees
* schema evolution

---

# Final Revision Notes

* CDC captures only changed data
* More efficient than full reloads
* Log-based CDC is most important
* AWS DMS heavily used for CDC
* CDC enables near real-time analytics
* CDC works well with streaming systems
* Upserts are common in CDC pipelines
* Deletes are difficult in CDC systems
* CDC is heavily used in modern lakehouses
