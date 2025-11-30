# AWS Redshift

## 1. Overview

* Fully managed **data warehouse** service on AWS
* Optimized for **analytics, reporting, and BI workloads**
* **Columnar storage** + **Massively Parallel Processing (MPP)** → fast for large-scale scans
* Use case: aggregation-heavy queries, large dataset analytics
* **Not suitable for**: transactional OLTP workloads

**Important Tip:** Redshift = Analytics, DynamoDB = OLTP.

---

## 2. Cluster Components

* **Leader Node**

  * Receives queries from clients
  * Parses and coordinates query execution
  * Aggregates results from compute nodes
* **Compute Nodes**

  * Store table data
  * Perform query processing in parallel
* **Node Slices**

  * Each compute node is divided into slices
  * Slices determine how data is distributed and processed in parallel

---

## 3. Table Design

### Columnar Storage

* Stores data column-wise instead of row-wise
* Efficient for **scanning large datasets**
* Reduces I/O for queries selecting a subset of columns

### Distribution Styles

| Style    | Description                        | When to Use                                     |
| -------- | ---------------------------------- | ----------------------------------------------- |
| **KEY**  | Rows distributed based on a column | Large tables with frequent joins on that column |
| **ALL**  | Entire table copied to all nodes   | Small lookup tables                             |
| **EVEN** | Round-robin distribution           | Tables with no obvious key, medium size         |

### Sort Keys

* **COMPOUND:** Prioritizes first columns in sorting, good for range queries
* **INTERLEAVED:** Multiple columns equally prioritized, good for queries filtering on different columns

### Compression Encodings

* Reduces storage space and I/O
* Example: `LZO`, `ZSTD`, `RAW`
* Applied automatically during `COPY` if using `COMPUPDATE ON`

---

## 4. Querying

* Uses **SQL (ANSI standard)**
* Supports **JOINs, aggregates, window functions**
* Optimized for **large scans**, not single-row operations
* **Concurrency Scaling**: adds temporary clusters to handle spikes
* **Workload Management (WLM):** queues queries, set priorities, concurrency limits

---

## 5. Data Loading

* **COPY command:** primary method to load data

  * Sources: **S3, DynamoDB, EMR, remote hosts via SSH**
  * Supports compressed files and columnar formats (**Parquet, ORC**)
  * Load data in **parallel** for large datasets

**Best Practices:**

* Split files into multiple smaller files for parallel loads
* Use proper compression to reduce storage and network I/O
* Load in batch rather than single rows for efficiency

---

## 6. Data Unloading

* **UNLOAD command:** export Redshift data to **S3**
* Supported formats: **Parquet, CSV, JSON**
* Useful for **ETL pipelines** or sharing data with external systems

---

## 7. Performance Optimization

* **Distribution Key:** minimize data movement between nodes
* **Sort Key:** improves query performance on range filters
* **Compression Encodings:** reduce disk I/O
* **Vacuum:** reclaims space after deletes/updates, re-sorts data
* **Analyze:** updates table statistics for the query planner

**Important Tip:** Always run `VACUUM` + `ANALYZE` after heavy deletes/updates.

---

## 8. Security

* **IAM Policies:** control Redshift API actions
* **VPC:** run cluster inside a private network
* **Encryption**

  * At rest: **KMS-managed keys**
  * In transit: **SSL**
* **User Management:** users, groups, and roles with fine-grained permissions

---

## 9. Backup & Recovery

* **Automated Snapshots:** enable point-in-time recovery
* **Manual Snapshots:** retained as needed for longer-term backups
* **Cross-Region Snapshots:** for disaster recovery

**Important Tip:** Redshift automated snapshots are incremental after the first full snapshot.

---

## 10. Integration with AWS Services

* **S3:** main source/target for COPY and UNLOAD
* **Glue Data Catalog:** metadata for external tables
* **Redshift Spectrum:** query S3 data directly without loading
* **Kinesis Firehose:** streaming data ingestion into Redshift
* **Athena:** query S3 data in external tables if needed

---

## 11. Redshift Spectrum

* Query **S3 data without loading into Redshift tables**
* Uses **external schemas** and **external tables**
* Supported formats: **Parquet, ORC, CSV, JSON**
* Useful for **ad-hoc queries** or large-scale analytics on raw S3 data

---

## 12. Monitoring

* **CloudWatch Metrics:** CPU, disk space, query throughput
* **System Tables:** STL (logs) / STV (status)
* **WLM Queues:** manage query priorities, concurrency, and memory

---

## 13. Important Points

* Redshift = **analytics-focused**, not transactional
* Columnar storage + MPP → optimized for **large scans**
* Proper **distribution key** reduces network traffic and improves joins
* Sort keys → improve performance on **range queries**
* **Spectrum** → query S3 without loading
* **COPY** → load data efficiently
* **UNLOAD** → export data to S3
* **Compression + Vacuum + Analyze** → optimize performance
* WLM → handle query concurrency and priority
* Automated snapshots + PITR → backup and recovery

---

