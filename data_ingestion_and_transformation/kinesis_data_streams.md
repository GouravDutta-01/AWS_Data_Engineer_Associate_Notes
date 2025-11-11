# Amazon Kinesis Data Streams (KDS)

## What It Is

Kinesis Data Streams is AWS’s main service for **real-time data ingestion**.  
It lets you continuously collect data (like app logs, IoT metrics, clickstreams, etc.) and process it in near real time.  

Basically — if data keeps coming in nonstop, KDS is the pipeline that catches it.

---

## Use Cases

- Collecting application or system logs in real time  
- Streaming IoT sensor data  
- Feeding ML models or dashboards with fresh data  
- Real-time fraud detection  
- Ingest → transform → store (e.g., KDS → Lambda → S3 → Glue → Redshift)

---

## Core Building Blocks

| Term | What It Means |
|------|----------------|
| **Stream** | The main pipeline where data flows. |
| **Shard** | A “lane” in the stream. Each shard handles 1 MB/s write, 2 MB/s read. |
| **Record** | The actual data item (max 1 MB). |
| **Producer** | Who’s sending the data (app, agent, IoT). |
| **Consumer** | Who’s reading/processing the data (Lambda, Glue, Firehose). |
| **Retention** | How long data stays in the stream (24h → up to 7d / 365d). |

Think of a stream like a highway, and shards are the lanes.  
More traffic = add more shards.

---

##  Data Flow (Simple View)

Producer → Kinesis Stream (with shards) → Consumer

Example:
App → Kinesis → Lambda → S3 → Glue → Athena

---

## Producers

Ways to send data into Kinesis:

- **Kinesis Producer Library (KPL):** High-performance batching and compression  
- **Kinesis Agent:** Simple way to stream log files from EC2/Linux  
- **AWS SDK:** Manual push via API calls  
- **Other AWS sources:** CloudWatch Logs, IoT Core, Firehose, etc.

**Tip:** If you’re sending logs from EC2, just install the Kinesis Agent.  
It’ll tail the log file and push automatically.

---

##  Consumers

Consumers read from the stream and process the data.

- **Lambda:** Easy for lightweight transforms and triggers  
- **Kinesis Data Analytics:** Run SQL/Flink jobs on the stream  
- **Firehose:** Pushes the data automatically to S3, Redshift, or OpenSearch  
- **Custom App (KCL):** When you want more control or custom logic

 *Lambda + Kinesis is the most common real-time pattern you’ll see in the exam.*

---

## Shards and Scaling

Each **shard** gives:
- 1 MB/sec write or 1000 records/sec  
- 2 MB/sec read  

You can **split shards** (scale up) or **merge shards** (scale down).

**Enhanced Fan-Out:**  
If multiple consumers need to read the same stream, fan-out gives each consumer its own 2 MB/sec pipe — prevents read throttling.

**Pricing reminder:** ~$0.015 per shard-hour.  
So scaling affects cost directly.

---

## Retention and Replay

- Default: 24 hours  
- Can increase up to 7 days  
- With **extended retention**, up to **365 days**

You can replay data later — that’s a big difference from **Firehose**, which doesn’t support replay.

---

## Security

- IAM for access control (who can read/write)
- Encryption in transit: **TLS**
- Encryption at rest: **KMS**
- Use **VPC endpoints** for private connectivity
- CloudTrail logs for auditing who did what

---

## Monitoring

Use **Amazon CloudWatch** to monitor how your Kinesis stream is performing in real time.  
These are the key metrics you’ll actually use or see in exam questions:

| Metric Name | What It Means | When It Goes Up | What You Should Do |
|--------------|----------------|------------------|--------------------|
| **IncomingBytes** | Total data volume coming into the stream (in bytes/sec). | When producers send more data or larger records. | Keep an eye on this to ensure you’re not close to the shard limit (1 MB/sec per shard). |
| **WriteProvisionedThroughputExceeded** | Number of write requests rejected due to exceeding shard write capacity. | When producers try to write more than 1 MB/sec or 1000 records/sec per shard. | **Producer throttling** → Add more shards (split) or batch writes using KPL. |
| **ReadProvisionedThroughputExceeded** | Number of read requests rejected because shard read limit (2 MB/sec) is hit. | When multiple consumers or slow processing causes reads beyond limit. | **Consumer throttling** → Add shards or use **Enhanced Fan-Out** for separate read pipes. |
| **GetRecords.IteratorAgeMilliseconds** | Age (in ms) of the last record read by consumers. | When consumers lag behind — processing slower than new data arriving. | **Consumer lag** → Scale up consumers, increase concurrency, or optimize processing logic. |

---

### Quick Mental Model

- **IncomingBytes** → “How much is flowing in?”
- **WriteProvisionedThroughputExceeded** → “Am I writing too fast?”
- **ReadProvisionedThroughputExceeded** → “Am I reading too fast?”
- **IteratorAgeMilliseconds** → “Am I lagging behind?”

---
### Quick Fix Reference

| Problem | Symptom Metric | Fix |
|----------|----------------|-----|
| Producer throttling | `WriteProvisionedThroughputExceeded` | Split shards or use KPL for batching |
| Consumer throttling | `ReadProvisionedThroughputExceeded` | Use Enhanced Fan-Out or add shards |
| Consumer lagging | `IteratorAgeMilliseconds` | Increase consumer parallelism |
| Approaching write limit | `IncomingBytes` near 1 MB/sec per shard | Scale up shards proactively |

---

 **Tip:**  
For the exam, if a question mentions “data producers getting throttled,” think → `WriteProvisionedThroughputExceeded`.  
If it says “consumers are falling behind,” think → `IteratorAgeMilliseconds`.  
That’s almost always the right direction.

---

## Integrations You Should Know

| Service | How It Connects |
|----------|----------------|
| **Lambda** | Triggered by Kinesis events |
| **Firehose** | Can take data from KDS and auto-load to S3/Redshift |
| **Glue** | ETL from stream data |
| **Athena** | Queries data once it’s stored in S3 |
| **OpenSearch** | For searching log-type data |
| **QuickSight** | For dashboards |

**Exam Tip:** Kinesis Firehose is for delivery, not replay.  
Kinesis Data Streams is for processing, replay, and custom logic.

---

##  Pricing Quick Recap

- **Shard-hour:** ~$0.015  
- **PUT payload unit (25 KB):** ~$0.014 per million  
- **Enhanced fan-out:** $0.015/hr per consumer per shard  
- **Extended retention:** Extra cost based on data volume  

So if you have 4 shards → you pay roughly $0.06/hour for the stream itself.

---

## Common Exam Reminders

- 1 MB/s write & 2 MB/s read per shard  
- Max record size = 1 MB  
- Retention = 24h → 365d  
- Replay supported  
- Firehose → No replay   
- Enhanced Fan-Out → multiple consumers  
- Scaling → split/merge shards  
- Ordering guaranteed within a shard  
- Best for event-driven streaming or near-real-time analytics

---

##  Small Architecture Example

App / IoT Device
↓
Kinesis Data Stream
↓
Lambda → process & store → S3
↓
Glue → transform → curated S3
↓
Athena / QuickSight → query & visualize

---

**References:**
- AWS Docs: [Kinesis Data Streams Developer Guide](https://docs.aws.amazon.com/streams/latest/dev/introduction.html)

---