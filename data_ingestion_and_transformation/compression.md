# Compression in Data Engineering

# Why Compression Matters

Compression is one of the MOST important optimization concepts in:
- Athena
- Glue
- Spark
- Redshift
- S3 data lakes

Compression directly affects:
- storage cost
- query performance
- network transfer
- Athena scan cost

VERY important AWS DEA topic.

---

# Simple Intuition

Compression means:

> reducing file size by encoding data efficiently.

Example:

```text
1 GB CSV
    ↓
200 MB compressed file
```

Now:
- less storage used
- less network transfer
- faster reads

---

# Why Compression is Powerful in Cloud

AWS pricing heavily depends on:
- storage
- data scanned
- network movement

Compression reduces all three.

---

# Main Compression Types

| Compression | Common Usage |
|---|---|
| GZIP | CSV/JSON compression |
| Snappy | Parquet/Spark |
| BZIP2 | High compression |
| ZSTD | Modern high-performance |
| LZO | Hadoop ecosystems |

---

# Compression vs File Format

VERY important distinction.

| File Format | Compression |
|---|---|
| CSV | GZIP |
| JSON | GZIP |
| Parquet | Snappy |
| ORC | ZLIB/Snappy |

Format = how data organized  
Compression = how data size reduced

---

# Example

```text
sales.csv
```

can become:

```text
sales.csv.gz
```

Same data,
smaller size.

---

# GZIP

# What is GZIP?

General-purpose compression algorithm.

Very common for:
- CSV
- JSON
- logs

---

# Advantages of GZIP

- high compression ratio
- widely supported
- easy to use

---

# Disadvantages of GZIP

Important big-data limitation:

```text 
GZIP files are NOT splittable.
```

Very important concept.

---

# What Does "Not Splittable" Mean?

Suppose:

```text 
100 GB GZIP file
```

Spark cannot easily process:
- different chunks in parallel.

Usually:
- one executor processes large portions sequentially.

This hurts parallelism.

---

# Snappy

# What is Snappy?

Compression optimized for:
- speed
- distributed systems

Very common with:
- Parquet
- Spark

---

# Snappy Philosophy

```text 
Faster compression/decompression
Less compression ratio
```

Trade storage savings for speed.

---

# Why Snappy is Popular

Big-data systems care heavily about:
- parallel processing
- fast decompression

Snappy is excellent for this.

---

# Snappy + Parquet

One of the MOST common combinations:

```text
Partitioned Parquet + Snappy
```

Extremely important AWS analytics pattern.

---

# BZIP2

Provides:
- higher compression than GZIP

But:
- slower processing

Less common today.

---

# ZSTD (Zstandard)

Modern compression algorithm.

Provides:
- strong compression
- fast decompression

Increasingly popular in modern systems.

---

# Splittable vs Non-Splittable Compression

VERY important distributed systems concept.

---

# Splittable Compression

Allows:
- parallel processing of file chunks

Better for:
- Spark
- Hadoop
- large-scale analytics

---

# Non-Splittable Compression

Requires:
- mostly sequential processing

Hurts:
- parallelism
- scalability

---

# Important Comparison

| Compression | Splittable |
|---|---|
| GZIP | No |
| Snappy | Yes-ish via Parquet blocks |
| BZIP2 | Yes |
| ZSTD | Depends implementation |

---

# Why Parquet + Snappy Works So Well

Parquet already divides data into:
- row groups
- column chunks

Snappy compresses these chunks efficiently.

Result:
- fast analytics
- parallel reads
- reduced storage

---

# Compression and Athena

Athena charges by:
> data scanned

Compression reduces:
- bytes scanned
- query cost

VERY important exam concept.

---

# Example Athena Optimization

## BAD

```text
Uncompressed CSV
```

Large scans.

---

## GOOD

```text 
Partitioned Parquet + Snappy
```

Massive optimization.

---

# Compression and S3

Compression reduces:
- S3 storage cost
- transfer time
- replication cost

Very important at scale.

---

# Compression and Spark

Spark benefits from:
- compressed I/O
- reduced shuffle size

But:
- too much compression can increase CPU overhead.

Tradeoff exists.

---

# CPU vs Storage Tradeoff

Compression saves:
- storage
- network bandwidth

But requires:
- CPU for compression/decompression

Important systems design tradeoff.

---

# Columnar Formats Compress Better

Parquet/ORC compress extremely well because:

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

# Compression in Data Lakes

Modern lakehouses almost always use:

```text
Parquet + Snappy
```

because it balances:
- speed
- compression
- analytics performance

---

# Redshift Compression

Redshift automatically uses:
- column compression encodings

to reduce:
- storage
- query I/O

Very important warehouse optimization concept.

---

# Compression and Streaming

Streaming systems often use:
- Avro + compression

to reduce:
- network transfer
- Kafka/Kinesis bandwidth

---

# Common AWS Exam Scenarios

# Scenario 1

Question:
Need lower Athena query cost.

Best Answer:
- compression
- partitioning
- Parquet

---

# Scenario 2

Question:
Need fast Spark analytics.

Best Answer:
- Parquet + Snappy

---

# Scenario 3

Question:
Need best compression ratio.

Possible Answer:
- BZIP2 or ZSTD

depending on workload.

---

# Common Beginner Mistakes

# Mistake 1

Using CSV without compression.

Very expensive at scale.

---

# Mistake 2

Using GZIP for huge distributed Spark workloads.

May reduce parallelism.

---

# Mistake 3

Thinking maximum compression is always best.

Sometimes:
- decompression CPU becomes bottleneck.

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

This combination minimizes:
- cost
- scan size
- latency

---

# Beginner → Intermediate → Advanced

# Beginner

Understand:
- why compression matters
- GZIP vs Snappy basics

---

# Intermediate

Learn:
- splittability
- Parquet compression
- Athena optimization

---

# Advanced

Master:
- compression tuning
- row group optimization
- CPU vs I/O tradeoffs

---

# Final Revision Notes

- Compression reduces storage and scan cost
- Athena charges by data scanned
- GZIP is common but not splittable
- Snappy is optimized for big-data systems
- Parquet + Snappy is a core AWS analytics pattern
- Columnar formats compress very efficiently
- Compression improves storage and network efficiency
- One of the most important analytics optimizations