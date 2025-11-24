# Amazon Kinesis Data Firehose

## Overview
Amazon Kinesis Data Firehose is a **fully managed** service for real-time or near–real-time data ingestion into AWS analytics and storage systems.  
It is designed for **zero-administration**, **automatic scaling**, and **simple data delivery**.

Firehose delivers streaming data to:
- Amazon S3  
- Amazon Redshift  
- Amazon OpenSearch Service  
- Splunk  
- Generic HTTP endpoints  
- SaaS partners (Datadog, New Relic, Dynatrace)

---

## Key Features

### 1. Fully Managed & Auto-Scaling
- No clusters, shards, or capacity units to manage.
- Firehose automatically handles throughput.

### 2. Near Real-Time Delivery
- Data is buffered, then delivered when:
  - **Buffer size** is reached (1–128 MB)
  - **Buffer interval** is reached (60–900s)
- Lower buffer = lower latency, higher cost.

### 3. Transformations
- **AWS Lambda** can preprocess/transform data before delivery.
- Record-by-record transformation.
- If transformation fails → retry → backup to S3.

### 4. Format Conversion
Built-in conversion to:
- Parquet  
- ORC  
Works best with **Glue Schema Registry** (optional).

### 5. Backup Options
- Backup **all data** or **failed-only** to S3.
- Essential for debugging and audit requirements.

### 6. Security
- IAM for access control.
- Encryption: SSE-S3 or SSE-KMS.
- Can run inside VPCs for private delivery (VPC destination).

### 7. Compression
Supports:
- GZIP  
- ZIP  
- Snappy  
- Hadoop-compatible compression for columnar formats.

---

## Dynamic Partitioning

- Allows Firehose to write data into **dynamic S3 folder structures** based on record fields (e.g., timestamp, region, userType).
- Useful for analytics-ready data lakes (Athena, Glue, Redshift Spectrum).
- Requires:
  - Input records in **JSON format**
  - **Record metadata extraction** enabled
  - Optional AWS Lambda for custom partition keys
- Each partition has its **own buffering**, so low-volume partitions may introduce delivery delays.
- Extra Firehose processing charges apply.
- Example output path: **s3://bucket/logs/year=2025/month=11/day=24/region=us-east-1/**


## How Firehose Delivers to Major Services

### Delivery to Amazon S3
Primary and simplest destination.

### Delivery to Amazon Redshift
Firehose → **staging S3 bucket** → Redshift **COPY** command.

### Delivery to Amazon OpenSearch
Firehose can add documents automatically to indexes.

### Delivery to Splunk
Uses Splunk HTTP Event Collector (HEC).

---

## Comparisons

### Kinesis Firehose vs Kinesis Data Streams

| Feature | Firehose | Data Streams |
|--------|----------|--------------|
| Admin | Fully managed | Manual / On-demand |
| Latency | Near-real-time | Real-time (<10 ms) |
| Transform | Lambda | Kinesis Analytics / Consumers |
| Retention | None | 24h–1yr |
| Scaling | Auto | Shards (manual or on-demand) |
| Use Case | Delivery pipelines | Custom stream processing |
| Consumers | 1 destination | Multiple (fan-out) |
| Ordering | Best effort | Strong per shard |

---

### Kinesis Firehose vs AWS Glue Streaming

| Feature | Firehose | Glue Streaming |
|---------|-----------|----------------|
| Processing | Light transforms only | Complex ETL, ML, joins |
| Destination | S3/Redshift/OpenSearch/Splunk | Flexible |
| Admin | Zero-admin | Needs Glue jobs |
| Use Case | Ingestion & delivery | ETL on streaming data |

---

### Kinesis Firehose vs DMS

| Feature | Firehose | DMS |
|---------|----------|-----|
| Type | Streaming ingestion | Database migration/CDC |
| Destinations | S3, Redshift, Splunk | RDS, DynamoDB, S3 |
| Transforms | Lambda | Limited |
| Best For | Real-time log/metric ingestion | Database replication |

---

## Common Scenarios

### Real-time application logs → S3 (low ops)
Use **Firehose → S3**.

### Real-time logs → Redshift analytics
Use **Firehose with S3 staging & COPY**.

### Need <1 sec latency
Use **Kinesis Data Streams**, not Firehose.

### Convert JSON → Parquet automatically
Use **Firehose format conversion**.

### Send logs to Splunk
Firehose supports **Splunk HEC** directly.

---

## Important Notes

- Firehose is the simplest and most managed streaming option.
No shards, no scaling decisions. You only configure a delivery stream.

- Firehose buffers data before delivery.
    You control:
    - Buffer size (1–128 MB)
    - Buffer interval (60–900 sec)

    Delivery triggers when **either** is reached.

- Firehose cannot retain or replay data.
If you need retention → **use Kinesis Data Streams**.

- For Redshift delivery, Firehose always writes to S3 first.
Then runs a Redshift **COPY** command behind the scenes.

- Firehose transforms data using Lambda only.
Light transformations, record-by-record.  
For complex transformations → Glue Streaming / Kinesis Analytics.

- Firehose supports automatic Parquet/ORC conversion.
Very commonly tested!

- Firehose can compress data before delivery.
Saves cost when writing to S3.

- Backup to S3 is essential.
You can back up **all data** or **only failed data**.

- Cheapest + easiest pipeline for streaming logs → S3.
    Default choice unless you need:
    - real-time (<1s)  
    - complex processing  
    - multiple consumers  

- Firehose supports integrations with SaaS partners.
E.g., Splunk, Datadog, NewRelic.

- Firehose guarantees *at-least-once* delivery.
But order is **not guaranteed**.

---

