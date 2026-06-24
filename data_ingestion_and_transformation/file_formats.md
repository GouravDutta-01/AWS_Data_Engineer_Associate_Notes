# File Formats in Data Engineering

# Why File Formats Matter

In data engineering:

> file format choice directly affects:
- query performance
- storage cost
- ETL speed
- Athena scan cost
- Spark efficiency

This is an EXTREMELY important AWS Data Engineer topic.

---

# Main File Formats

| Format | Type |
|---|---|
| CSV | Row-based text |
| JSON | Semi-structured text |
| Avro | Row-based binary |
| ORC | Columnar binary |
| Parquet | Columnar binary |

---

# Two Most Important Categories

# Row-Based Formats

Store data row by row.

Examples:
- CSV
- JSON
- Avro

---

# Columnar Formats

Store data column by column.

Examples:
- Parquet
- ORC

This distinction is VERY important.

---

# Simple Intuition

Suppose table:

| id | name | salary |
|---|---|---|
| 1 | A | 100 |
| 2 | B | 200 |

---

# Row Format Storage

```text
1,A,100
2,B,200
```

Rows stored together.

---

# Columnar Format Storage

```text
id:     1,2
name:   A,B
salary: 100,200
```

Columns stored together.

---

# Why Columnar Storage is Powerful

Suppose query:

```sql
SELECT salary FROM employees;
```

Columnar format reads:
- ONLY salary column

Row format reads:
- entire rows

Huge performance difference.

---

# CSV

# What is CSV?

CSV = Comma Separated Values

Example:

```csv
id,name,salary
1,Alice,100
2,Bob,200
```

---

# Advantages of CSV

- simple
- human readable
- universally supported

---

# Disadvantages of CSV

- no schema
- poor compression
- slow analytics
- scans full rows
- large storage size

---

# CSV Use Cases

Good for:
- exports
- simple ingestion
- small datasets

Not ideal for:
- analytics lakes
- Athena-heavy workloads

---

# JSON

# What is JSON?

Semi-structured format.

Example:

```json
{
  "id": 1,
  "name": "Alice",
  "skills": ["Python", "AWS"]
}
```

---

# Advantages of JSON

- flexible schema
- nested structures
- API-friendly

---

# Disadvantages of JSON

- large size
- expensive scans
- slower analytics

---

# JSON Use Cases

Good for:
- APIs
- event payloads
- streaming events

Often converted later into:
- Parquet
- ORC

---

# Avro

# What is Avro?

Binary row-based format with:
- schema support
- compact storage

Very common in:
- Kafka ecosystems
- streaming pipelines

---

# Key Feature: Schema Evolution

Avro supports:
- changing schemas over time

VERY important streaming concept.

---

# Avro Advantages

- compact binary format
- schema evolution
- efficient serialization

---

# Avro Disadvantages

- not ideal for analytics scans
- row-oriented

---

# Avro Common Use Cases

Good for:
- streaming
- Kafka messages
- event pipelines

---

# Parquet

# What is Parquet?

Parquet is:

> columnar binary storage format optimized for analytics.

One of the MOST important formats in AWS data engineering.

---

# Why Parquet is Popular

Benefits:
- columnar storage
- compression
- fast analytics
- reduced Athena cost

---

# Example

Query:

```sql
SELECT amount FROM sales;
```

Parquet scans:
- only amount column

Massive performance improvement.

---

# Parquet Advantages

| Benefit | Why Important |
|---|---|
| Columnar | Read fewer columns |
| Compression | Lower storage cost |
| Faster queries | Less I/O |
| Athena optimization | Lower scan cost |

---

# Parquet Disadvantages

- more complex
- not human readable
- slower row-based writes

---

# Parquet Common Use Cases

Excellent for:
- Athena
- Redshift Spectrum
- Glue ETL
- data lakes
- lakehouses

---

# ORC

ORC = Optimized Row Columnar.

Similar to Parquet.

Originally optimized heavily for:
- Hive ecosystem

---

# ORC Advantages

- excellent compression
- fast analytics
- column pruning

---

# ORC vs Parquet

Both are:
- columnar
- analytics optimized

Parquet became more popular across ecosystems.

---

# Row vs Columnar

VERY IMPORTANT comparison.

| Row-Based | Columnar |
|---|---|
| Good for writes | Good for analytics |
| Read full rows | Read selected columns |
| OLTP-friendly | OLAP-friendly |
| CSV/JSON/Avro | Parquet/ORC |

---

# OLTP vs OLAP Connection

# OLTP Systems

Need:
- fast row operations

Prefer:
- row-based formats

---

# OLAP Systems

Need:
- analytical scans

Prefer:
- columnar formats

This is a VERY important concept.

---

# Compression

Columnar formats compress MUCH better because:

```text
Similar values stored together
```

Example:

```text
country:
India
India
India
India
```

Highly compressible.

---

# Athena and File Formats

Athena strongly prefers:
- Parquet
- ORC

because Athena charges by:
> data scanned

Columnar + compressed formats reduce scans massively.

---

# Example Athena Optimization

## BAD

```text
CSV + no partitioning
```

Expensive scans.

---

## GOOD

```text
Partitioned Parquet + compression
```

Massively cheaper.

---

# Glue and File Formats

Glue commonly:
- converts CSV/JSON → Parquet

during ETL.

Very common pipeline.

---

# Example ETL Flow

```text
Raw JSON in S3
      ↓
Glue ETL
      ↓
Partitioned Parquet
      ↓
Athena Queries
```

One of the MOST common AWS architectures.

---

# Streaming vs Analytics Formats

| Streaming | Analytics |
|---|---|
| Avro | Parquet |
| JSON | ORC |

---

# Schema Evolution

Important modern concept.

Formats supporting schema evolution well:
- Avro
- Parquet

Useful when:
- columns added
- schemas change

---

# Splittability

Very important big-data concept.

Some formats allow:
- parallel processing of chunks

Parquet/ORC are highly splittable.

This improves:
- Spark parallelism
- query speed

---

# File Size Best Practices

Avoid:
- too many tiny files

Problem:
- metadata overhead
- slow Spark jobs

Preferred:
- moderately large files

Common target:
- 128 MB to 1 GB

Very important practical concept.

---

# Common AWS Exam Thinking

# Scenario 1

Question:
Need cheapest Athena queries.

Best Answer:
- Parquet
- partitioning
- compression

---

# Scenario 2

Question:
Need schema evolution in streaming.

Best Answer:
- Avro

---

# Scenario 3

Question:
Need analytics optimization.

Best Answer:
- columnar formats

---

# Common Beginner Mistakes

# Mistake 1

Using CSV everywhere.

Very inefficient for analytics.

---

# Mistake 2

Ignoring partitioning.

Format alone is NOT enough.

---

# Mistake 3

Thinking JSON is good for analytics.

JSON is flexible but expensive to scan.

---

# Think Like AWS

AWS analytics philosophy strongly prefers:

```text
S3
+
Partitioned Parquet
+
Glue Catalog
+
Athena
```

This is one of the MOST important modern serverless analytics patterns.

---

# Beginner → Intermediate → Advanced

# Beginner

Understand:
- row vs columnar
- Parquet advantages

---

# Intermediate

Learn:
- schema evolution
- compression
- Athena optimization

---

# Advanced

Master:
- Iceberg/Delta formats
- compaction strategies
- file layout optimization

---

# Final Revision Notes

- CSV and JSON are row-based text formats
- Parquet and ORC are columnar formats
- Athena prefers Parquet/ORC
- Columnar formats reduce scan cost
- Avro supports schema evolution well
- Compression is critical for analytics optimization
- File format choice directly affects performance and cost
- Parquet is one of the most important AWS analytics formats