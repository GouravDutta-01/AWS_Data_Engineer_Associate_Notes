# Amazon Athena

# What is Athena?

Amazon Athena is a:

> serverless interactive query service for data stored in S3.

Athena allows you to run:

* SQL queries directly on S3 data

without:

* managing servers
* loading data into databases

This is one of the MOST important AWS analytics services.

---

# Simple Intuition

Think of Athena as:

```text
SQL Engine for S3
```

You keep files in S3,
Athena queries them directly.

---

# Why Athena Became Popular

Traditional analytics systems required:

* loading data into warehouse
* managing infrastructure
* expensive clusters

Athena simplified this massively.

---

# Core Athena Idea

```text
S3 Storage
+
Glue Metadata
+
SQL Queries
=
Athena Analytics
```

---

# Basic Athena Architecture

```text
S3 Files
    ↓
Glue Catalog
    ↓
Athena SQL Queries
    ↓
Results
```

Very important architecture.

---

# Athena is Serverless

You do NOT manage:

* servers
* clusters
* scaling

AWS handles everything.

This is a major AWS exam theme.

---

# Supported File Formats

Athena supports:

* CSV
* JSON
* Avro
* ORC
* Parquet

Most preferred:

* Parquet
* ORC

because they are columnar.

---

# Why Columnar Formats Matter

Athena charges based on:

> data scanned

Columnar formats scan:

* only required columns

This dramatically reduces:

* query cost
* query time

VERY important exam topic.

---

# Example

Suppose table has 100 columns.

Query:

```sql
SELECT customer_id
FROM sales;
```

Parquet scans:

* only customer_id column

CSV scans:

* entire file

Huge difference.

---

# Athena Pricing Model

Athena charges mainly based on:

```text
Amount of data scanned
```

---

# How to Reduce Athena Cost

Main optimization techniques:

| Technique        | Why Helps          |
| ---------------- | ------------------ |
| Partitioning     | Scan less data     |
| Parquet/ORC      | Scan fewer columns |
| Compression      | Less data scanned  |
| Smaller datasets | Faster queries     |

---

# Glue Catalog Dependency

Athena heavily relies on:

* Glue Data Catalog

Catalog provides:

* table metadata
* schema info
* partition info

Without metadata:
Athena cannot query properly.

---

# Example Table

Glue Catalog may define:

```sql
CREATE EXTERNAL TABLE sales (
  customer_id INT,
  amount DOUBLE
)
PARTITIONED BY (year STRING)
```

Athena uses this metadata to query S3 files.

---

# Athena Uses External Tables

Important concept.

Athena does NOT store data itself.

Data remains in:

* S3

Athena only queries it.

---

# Common Athena Architecture

```text
Applications
      ↓
S3 Raw/Processed Data
      ↓
Glue Catalog
      ↓
Athena Queries
      ↓
QuickSight Dashboards
```

Very common serverless analytics stack.

---

# Athena + Parquet

This is one of the BEST AWS analytics combinations.

Benefits:

* lower scan cost
* faster performance
* compressed storage

AWS exams LOVE this pattern.

---

# Partitioning in Athena

VERY important topic.

Example S3 layout:

```text
s3://sales/year=2026/month=06/
```

Athena can prune partitions:

```sql
SELECT *
FROM sales
WHERE year='2026';
```

Only relevant partition scanned.

Huge cost reduction.

---

# Partition Pruning

One of the MOST important Athena concepts.

Without partitioning:

* Athena scans everything

With partitioning:

* scans only relevant partitions

---

# Compression in Athena

Compressed files:

* reduce scan size
* reduce cost

Common compression:

* Snappy
* GZIP

---

# Athena Query Results

Athena stores query results in:

* S3

You must configure:

* output bucket

Very common beginner mistake.

---

# CTAS (Create Table As Select)

Athena supports:

```sql
CREATE TABLE new_table AS
SELECT ...
```

Useful for:

* transformations
* converting CSV → Parquet
* optimizing datasets

VERY useful feature.

---

# Athena + Lakehouse

Athena now supports:

* Iceberg
* Hudi
* Delta Lake

This is very important modern architecture direction.

---

# Athena + Redshift Spectrum

Both query S3,
but differently.

| Athena           | Redshift Spectrum                |
| ---------------- | -------------------------------- |
| Fully serverless | Uses Redshift cluster            |
| Ad hoc queries   | Warehouse extension              |
| Simpler          | Enterprise warehouse integration |

---

# Athena vs Redshift

VERY important comparison.

| Athena            | Redshift                     |
| ----------------- | ---------------------------- |
| Query S3 directly | Data warehouse               |
| Serverless        | Cluster/serverless warehouse |
| Pay per query     | Dedicated analytics system   |
| Great for ad hoc  | Great for heavy BI           |

---

# Athena Best Use Cases

Good for:

* ad hoc analytics
* log analysis
* serverless SQL
* querying data lakes

Not ideal for:

* extremely high concurrency
* complex warehouse workloads

---

# Athena + BI Tools

Athena integrates with:

* QuickSight
* Tableau
* Power BI

Very common analytics pattern.

---

# Athena Security

Security commonly uses:

* IAM
* Lake Formation
* S3 permissions
* encryption

---

# Common AWS Exam Scenarios

# Scenario 1

Question:
Need SQL queries directly on S3.

Best Answer:

* Athena

---

# Scenario 2

Question:
Need cheapest ad hoc analytics on S3.

Best Answer:

* Athena + Parquet + Partitioning

---

# Scenario 3

Question:
Need serverless analytics.

Best Answer:

* Athena

---

# Scenario 4

Question:
Need reduced Athena query cost.

Best Answer:

* partitioning
* Parquet
* compression

---

# Common Beginner Mistakes

# Mistake 1

Using CSV for analytics-heavy workloads.

Very expensive in Athena.

---

# Mistake 2

Ignoring partitioning.

This massively increases:

* scan cost
* latency

---

# Mistake 3

Thinking Athena stores data.

Athena only queries:

* S3 data

---

# Think Like AWS

AWS strongly prefers:

* serverless analytics
* decoupled storage and compute
* S3-centered architectures

Athena perfectly matches this philosophy.

---

# Beginner → Intermediate → Advanced

# Beginner

Understand:

* Athena queries S3 directly
* Glue Catalog dependency

---

# Intermediate

Learn:

* partitioning
* Parquet optimization
* CTAS

---

# Advanced

Master:

* Iceberg integration
* query optimization
* Lake Formation governance

---

# Final Revision Notes

* Athena is serverless SQL on S3
* Glue Catalog stores metadata
* Athena charges by data scanned
* Partitioning reduces query cost
* Parquet/ORC are preferred formats
* Athena stores query results in S3
* CTAS useful for transformations
* Athena heavily used in lakehouse architectures
