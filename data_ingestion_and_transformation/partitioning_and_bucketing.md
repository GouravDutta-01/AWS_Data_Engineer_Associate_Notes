# Partitioning and Bucketing

# Why Partitioning Matters

Partitioning is one of the MOST important concepts in:
- Athena
- Glue
- Spark
- Hive
- lakehouses

It directly affects:
- query speed
- ETL performance
- Athena cost

This is EXTREMELY important for AWS DEA.

---

# Simple Intuition

Partitioning means:

> physically dividing data into smaller organized folders/groups.

Instead of:

```text
One giant dataset
```

we organize data intelligently.

---

# Example Without Partitioning

```text
s3://sales-data/all_data.parquet
```

Every query scans:
- entire dataset

Very expensive.

---

# Example With Partitioning

```text 
s3://sales/year=2026/month=06/day=08/
```

Now queries scan:
- only required partitions

Huge improvement.

---

# Most Common Partition Keys

| Partition Key | Why Used |
|---|---|
| year | Time filtering |
| month | Time filtering |
| day | Time filtering |
| region | Geographic filtering |
| country | Common query filter |

---

# Partition Pruning

MOST IMPORTANT concept.

Partition pruning means:

> query engine skips irrelevant partitions.

---

# Example

Query:

```sql 
SELECT *
FROM sales
WHERE year='2026';
```

Athena reads ONLY:

```text 
/year=2026/
```

instead of:
- all years

This massively reduces:
- scan cost
- latency

---

# Why Partitioning is Critical in Athena

Athena charges based on:
> data scanned

Partitioning reduces:
- scanned files
- cost

VERY important exam topic.

---

# Common S3 Partition Structure

```text 
s3://logs/year=2026/month=06/day=08/
```

Extremely common design.

---

# Glue and Partitioning

Glue Catalog tracks:
- partition metadata

This allows:
- Athena
- Spark
- Redshift Spectrum

to query partitions efficiently.

---

# Static vs Dynamic Partitioning

# Static Partitioning

Partitions predefined manually.

---

# Dynamic Partitioning

Partitions created automatically during ETL.

Very common in Glue/Spark jobs.

---

# Example Spark Partition Write

```python
df.write.partitionBy("year", "month")
```

Creates organized S3 layout automatically.

---

# Benefits of Partitioning

| Benefit | Why Important |
|---|---|
| Lower Athena cost | Less scanning |
| Faster queries | Less I/O |
| Better Spark performance | Parallel reads |
| Easier data management | Organized layout |

---

# Over-Partitioning Problem

VERY important practical concept.

Too many partitions create:
- metadata overhead
- small file problems
- slower planning

---

# BAD Example

```text
partition by second/user_id/device_id
```

Creates millions of tiny partitions.

Very inefficient.

---

# Good Partitioning Strategy

Choose:
- commonly filtered columns
- moderate cardinality

Good:
- year
- month
- region

Bad:
- unique IDs
- timestamps with extreme granularity

---

# Small File Problem

One of the MOST important Spark/lakehouse problems.

Too many tiny files cause:
- slow metadata operations
- inefficient Spark jobs
- poor query performance

---

# Ideal File Sizes

Common recommendation:

```text
128 MB to 1 GB
```

for analytics files.

---

# Bucketing

Bucketing is DIFFERENT from partitioning.

Partitioning:
- creates separate directories

Bucketing:
- divides data into fixed number of files/buckets

using hashing.

---

# Example Bucketing

```text
bucket(user_id) into 16 buckets
```

Rows distributed across:
- 16 files

---

# Why Bucketing Helps

Useful for:
- joins
- shuffles
- evenly distributing data

Especially in Spark/Hive.

---

# Partitioning vs Bucketing

| Partitioning | Bucketing |
|---|---|
| Directory separation | File grouping |
| Reduces scan scope | Optimizes joins |
| High-level filtering | Hash-based distribution |

---

# Example

## Partition By

```text
year=2026/
```

---

## Bucket By

Inside partition:

```text
bucket-1.parquet
bucket-2.parquet
```

---

# Partitioning in Data Lakes

Data lakes heavily depend on:
- partitioned storage

because S3 itself has:
- no indexes

Partitioning acts like:
> logical pruning mechanism.

---

# Hive-Style Partitioning

Very common structure:

```text 
year=2026/month=06/day=08/
```

Supported heavily by:
- Athena
- Glue
- Spark

---

# Athena Partition Discovery

Athena can discover partitions using:

```sql
MSCK REPAIR TABLE sales;
```

Important operational command.

---

# Partition Projection

Advanced Athena optimization.

Avoids storing huge partition metadata.

Useful for:
- extremely large partition counts

---

# Partition Evolution

Modern lakehouse systems support:
- changing partition strategies over time

using:
- Iceberg
- Delta Lake
- Hudi

Very advanced concept.

---

# Common AWS Exam Scenarios

# Scenario 1

Question:
Need lower Athena query cost.

Best Answer:
- partitioning
- Parquet
- compression

---

# Scenario 2

Question:
Queries mostly filtered by date.

Best Answer:
- partition by date

---

# Scenario 3

Question:
Too many small files causing slow Spark jobs.

Problem:
- over-partitioning

---

# Common Beginner Mistakes

# Mistake 1

Partitioning by highly unique columns.

Bad for performance.

---

# Mistake 2

Thinking partitioning alone solves everything.

Need:
- proper file format
- compression
- file sizing

---

# Mistake 3

Creating too many tiny files.

Huge real-world issue.

---

# Think Like AWS

AWS analytics systems strongly prefer:

```text 
Partitioned
+
Compressed
+
Columnar
```

datasets.

This is foundational modern data lake design.

---

# Beginner → Intermediate → Advanced

# Beginner

Understand:
- what partitioning is
- partition pruning

---

# Intermediate

Learn:
- bucketing
- small file problem
- Glue partition metadata

---

# Advanced

Master:
- partition evolution
- Iceberg optimization
- large-scale lakehouse layouts

---

# Final Revision Notes

- Partitioning reduces data scans
- Athena heavily depends on partition pruning
- Good partition keys have moderate cardinality
- Over-partitioning creates small file problems
- Bucketing optimizes joins/distribution
- Glue Catalog tracks partition metadata
- Partitioned Parquet is a core AWS analytics pattern
- One of the MOST important optimization concepts in AWS DEA