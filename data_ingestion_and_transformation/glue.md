# AWS Glue

# What is AWS Glue?

AWS Glue is a:

> serverless data integration and ETL service.

It is one of the MOST important services in AWS Data Engineering.

Glue helps:

* discover data
* catalog metadata
* transform data
* run ETL pipelines
* orchestrate data processing

without managing infrastructure.

---

# Simple Intuition

Think of Glue as:

```text
Metadata Catalog
+
Serverless Spark ETL
+
Data Discovery
```

combined into one service.

---

# Why Glue Exists

Before Glue:

data engineers had to manually:

* manage Spark clusters
* maintain metadata
* track schemas
* write custom ETL infrastructure

Glue simplifies all of this.

---

# Core Glue Components

| Component         | Purpose                |
| ----------------- | ---------------------- |
| Glue Data Catalog | Metadata repository    |
| Glue Crawlers     | Discover schemas       |
| Glue Jobs         | Run ETL                |
| Glue Studio       | Visual ETL UI          |
| Glue Workflows    | Pipeline orchestration |
| Glue Triggers     | Schedule jobs          |

---

# Most Important Concept

The MOST important Glue concept is:

```text
Glue Data Catalog
```

Almost everything connects to it.

---

# Glue Data Catalog

The Data Catalog stores:

* table metadata
* schemas
* partitions
* S3 locations

Think of it like:

> a centralized metadata repository for analytics services.

---

# Why Data Catalog Matters

Services like:

* Athena
* EMR
* Redshift Spectrum
* Glue Jobs

need metadata to understand:

* table structure
* file formats
* partitions

Glue Catalog provides this centrally.

---

# Example

Suppose S3 contains:

```text
s3://sales-data/year=2026/month=06/
```

Glue Catalog tracks:

* columns
* schema
* partition structure
* file format

Now Athena can query it like SQL table.

---

# Glue Crawlers

Crawlers automatically:

* scan data sources
* infer schema
* create/update tables in Catalog

Supported sources:

* S3
* JDBC databases
* DynamoDB

---

# Example Crawler Flow

```text
S3 Files
   ↓
Glue Crawler
   ↓
Glue Catalog Table Created
   ↓
Athena Queries Table
```

Very common AWS architecture.

---

# Schema Inference

Glue Crawlers detect:

* column names
* datatypes
* partitions

Automatically.

Example:

```json
{
  "user_id": 101,
  "amount": 250
}
```

Crawler infers:

* user_id → integer
* amount → integer

---

# Glue Jobs

Glue Jobs perform:

* ETL transformations
* data cleaning
* joins
* aggregations

Glue internally uses:

* Apache Spark

Very important.

---

# Serverless Spark

One huge Glue advantage:

```text
No Spark cluster management.
```

AWS manages:

* infrastructure
* scaling
* provisioning

This is VERY important exam thinking.

---

# Glue ETL Flow

```text
S3 Raw Data
      ↓
Glue Job
      ↓
Transformed Parquet Data
      ↓
S3 / Redshift
```

Extremely common architecture.

---

# Glue Supports Python and Scala

Glue jobs usually use:

* PySpark
* Scala Spark

Most commonly:

* PySpark

---

# DynamicFrames vs DataFrames

VERY important exam topic.

---

# DataFrame

Standard Spark structure.

Fast and optimized.

---

# DynamicFrame

Glue-specific abstraction.

Provides:

* schema flexibility
* easier semi-structured handling

Useful when schemas are inconsistent.

---

# Key Difference

| DataFrame     | DynamicFrame    |
| ------------- | --------------- |
| Spark-native  | Glue-native     |
| Faster        | More flexible   |
| Strict schema | Flexible schema |

---

# Important Exam Thinking

If question mentions:

* inconsistent schema
* evolving JSON
* messy semi-structured data

DynamicFrames may help.

---

# Glue Job Bookmarks

VERY important feature.

Bookmarks track:

* previously processed data

This prevents:

* duplicate ETL processing

---

# Example

Without bookmarks:

```text
Every run processes ALL files again.
```

With bookmarks:

```text
Process only NEW files.
```

Huge efficiency improvement.

---

# Glue Workflow

Glue Workflows orchestrate:

* multiple jobs
* crawlers
* triggers

Useful for complex ETL pipelines.

---

# Glue Triggers

Triggers start jobs:

* on schedule
* on event
* after another job

Example:

```text
Crawler finishes
      ↓
ETL Job starts
```

---

# Glue Studio

Visual UI for creating ETL jobs.

Good for:

* beginners
* low-code ETL

Less important than core Glue concepts.

---

# Glue + S3

This is one of the MOST common AWS combinations.

S3 stores:

* raw data
* processed data

Glue:

* catalogs
* transforms
* orchestrates

---

# Glue + Athena

Very important integration.

Flow:

```text
S3 Data
   ↓
Glue Catalog
   ↓
Athena SQL Queries
```

Athena depends heavily on Glue Catalog metadata.

---

# Glue + Redshift

Glue can:

* load Redshift
* transform data before loading
* catalog Redshift datasets

---

# Glue + Lake Formation

Lake Formation provides:

* centralized governance
* permissions

on Glue Catalog resources.

Very important enterprise concept.

---

# Glue Job Types

| Type          | Purpose                      |
| ------------- | ---------------------------- |
| Spark ETL     | Main ETL jobs                |
| Python Shell  | Lightweight scripts          |
| Streaming ETL | Real-time pipelines          |
| Ray Jobs      | Distributed Python workloads |

---

# Streaming ETL in Glue

Glue supports:

* streaming transformations

using:

* Kinesis
* Kafka

Example:

```text
Kinesis Stream
      ↓
Glue Streaming Job
      ↓
S3 Parquet
```

---

# Partitioning in Glue

Glue heavily benefits from partitioned S3 data.

Example:

```text
/year=2026/month=06/day=05/
```

Benefits:

* faster queries
* lower Athena cost
* efficient ETL

VERY important concept.

---

# Glue Pricing

Glue charges for:

* ETL compute
* crawler runtime
* Data Catalog storage

Athena charges separately.

---

# Common AWS Exam Scenarios

# Scenario 1

Question:
Need serverless ETL for S3 data.

Best Answer:

* AWS Glue

---

# Scenario 2

Question:
Need metadata repository for Athena.

Best Answer:

* Glue Data Catalog

---

# Scenario 3

Question:
Need automatic schema discovery.

Best Answer:

* Glue Crawlers

---

# Scenario 4

Question:
Need incremental ETL processing.

Best Answer:

* Glue Job Bookmarks

---

# Common Beginner Mistakes

# Mistake 1

Thinking Glue is only ETL.

Actually Glue is heavily about:

* metadata management.

---

# Mistake 2

Ignoring partitioning.

Poor partitioning causes:

* expensive Athena scans
* slower ETL jobs

---

# Mistake 3

Using CSV heavily in analytics pipelines.

Glue works MUCH better with:

* Parquet
* ORC

---

# Glue vs EMR

VERY important comparison.

| Glue              | EMR                         |
| ----------------- | --------------------------- |
| Serverless        | Cluster-based               |
| Easier management | More control                |
| Simpler ETL       | Advanced big data workloads |
| Auto scaling      | Manual/managed clusters     |

---

# Think Like AWS

AWS strongly prefers:

* serverless analytics
* managed ETL
* metadata-driven systems

That is why Glue is heavily recommended in:

* modern data lakes
* lakehouses
* serverless analytics architectures

---

# Beginner → Intermediate → Advanced

# Beginner

Understand:

* Glue Catalog
* Crawlers
* ETL jobs

---

# Intermediate

Learn:

* bookmarks
* partitioning
* PySpark ETL

---

# Advanced

Master:

* streaming ETL
* schema evolution
* Glue optimization
* Lake Formation integration

---

# Final Revision Notes

* Glue is serverless ETL + metadata platform
* Glue Catalog is central metadata repository
* Crawlers infer schemas automatically
* Glue jobs run Spark ETL
* Job bookmarks support incremental processing
* Glue integrates heavily with Athena
* DynamicFrames handle flexible schemas
* Partitioning is critical for performance
