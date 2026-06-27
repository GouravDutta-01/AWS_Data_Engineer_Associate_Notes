# ETL vs ELT

# Why This Topic Matters

ETL and ELT are among the most important data engineering concepts.

Many AWS services are designed around one of these patterns.

Understanding:
- when transformations happen
- where transformations happen
- why modern systems prefer ELT

is very important for AWS DEA.

---

# What is ETL?

ETL stands for:

```text
Extract
Transform
Load
```

Data is transformed BEFORE loading into the target system.

---

# ETL Flow

```text
Source Systems
      ↓
Extract
      ↓
Transform
      ↓
Load
      ↓
Warehouse
```

---

# Example

Raw CSV:

```text
John,25
Alice,30
```

Transform:

```text
name,age
John,25
Alice,30
```

Then load into warehouse.

---

# ETL Intuition

Think:

```text
Clean first
Store later
```

---

# Traditional ETL Architecture

```text
Database
    ↓
ETL Tool
    ↓
Transformed Data
    ↓
Warehouse
```

Very common before cloud data lakes became popular.

---

# ETL Advantages

| Advantage | Why Important |
|------------|---------------|
| Cleaner warehouse | Data already processed |
| Better governance | Standardized data |
| Less warehouse compute | Transform done earlier |

---

# ETL Disadvantages

| Problem | Why Important |
|----------|---------------|
| Slower ingestion | Must transform first |
| Less flexibility | Raw data lost sometimes |
| Harder reprocessing | Transform logic fixed |

---

# What is ELT?

ELT stands for:

```text
Extract
Load
Transform
```

Data is loaded FIRST.

Transformations happen later.

---

# ELT Flow

```text
Source Systems
      ↓
Extract
      ↓
Load Raw Data
      ↓
Transform
      ↓
Analytics Layer
```

---

# ELT Intuition

Think:

```text
Store first
Transform later
```

---

# Modern Cloud Architecture

```text
Applications
      ↓
S3 Data Lake
      ↓
Raw Data Stored
      ↓
Athena / Glue / Spark
      ↓
Transformations
```

This is ELT.

---

# Why ELT Became Popular

Cloud storage became:

- cheap
- scalable
- durable

So storing raw data became easy.

---

# S3 Changed Everything

Previously:

```text
Storage expensive
```

Now:

```text
Store everything in S3
Transform later
```

This is why ELT dominates modern architectures.

---

# ETL Example

```text
Database
    ↓
Glue Job
    ↓
Transform
    ↓
Redshift
```

Transform BEFORE loading.

---

# ELT Example

```text
Database
    ↓
Raw S3
    ↓
Athena/Glue SQL
    ↓
Gold Dataset
```

Transform AFTER loading.

---

# ETL vs ELT Comparison

| ETL | ELT |
|------|------|
| Transform first | Load first |
| Traditional approach | Modern approach |
| Less storage needed | More storage needed |
| Less flexible | More flexible |
| Harder replay | Easier replay |

---

# Why ELT Helps Reprocessing

Suppose business asks:

```text
Use a new transformation logic
```

With ELT:

```text
Raw data still exists
```

Simply reprocess.

---

# Raw Data Preservation

One of the biggest ELT advantages.

```text
Raw Data
    ↓
Bronze Layer
```

Never lost.

Can always recompute.

---

# Medallion Architecture Connection

ELT is heavily used in:

```text
Bronze
  ↓
Silver
  ↓
Gold
```

Raw data remains available.

---

# AWS Services in ETL

Common ETL services:

- Glue
- EMR
- Data Pipeline

---

# AWS Services in ELT

Common ELT services:

- S3
- Athena
- Glue
- Redshift Spectrum
- Iceberg

---

# Common AWS Exam Thinking

Question:

Need ability to reprocess historical data.

Best Answer:

```text
Store raw data in S3
Use ELT
```

---

# Common Beginner Mistakes

# Mistake 1

Thinking ELT replaced ETL.

Reality:

Both still exist.

---

# Mistake 2

Thinking ELT means no transformations.

Transformations still happen.

Just later.

---

# Final Revision Notes

- ETL = Extract → Transform → Load
- ELT = Extract → Load → Transform
- ETL transforms before storage
- ELT stores raw data first
- ELT dominates modern cloud architectures
- S3 enables cheap raw-data storage
- ELT supports easier replay and recomputation
- Medallion architecture is heavily ELT-oriented