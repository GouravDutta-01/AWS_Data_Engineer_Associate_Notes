# Schema Evolution

# Why Schema Evolution Matters

In real-world systems:

Schemas change constantly.

Examples:

- New columns added
- Old columns removed
- Data types changed
- Business requirements updated

If systems cannot handle schema changes:

```text
Pipelines Break
Reports Fail
Applications Crash
```

Schema evolution helps systems adapt safely.

This is an EXTREMELY important AWS DEA topic.

---

# What is a Schema?

A schema defines:

- column names
- data types
- structure of data

Example:

```text
customer_id   INT
name          STRING
age           INT
```

This is a schema.

---

# Example Dataset

Version 1:

```text
customer_id
name
```

Data:

```text
1,John
2,Alice
```

---

# Business Requirement Changes

Company now wants:

```text
email
```

New schema:

```text
customer_id
name
email
```

This is schema evolution.

---

# Definition

Schema Evolution means:

> The ability to safely change data schemas over time while maintaining compatibility.

---

# Why It Is Difficult

Suppose:

Old data:

```text
customer_id
name
```

New application expects:

```text
customer_id
name
email
```

Question:

```text
What happens to old records?
```

This is the core challenge.

---

# Common Schema Changes

Most common:

1. Add Column
2. Remove Column
3. Rename Column
4. Change Datatype

---

# Add Column

Old Schema:

```text
id
name
```

New Schema:

```text
id
name
email
```

Most systems can handle this safely.

---

# Example

Old record:

```json
{
 "id":1,
 "name":"John"
}
```

New schema expects:

```json
{
 "id":1,
 "name":"John",
 "email":null
}
```

Usually safe.

---

# Remove Column

Old Schema:

```text
id
name
email
```

New Schema:

```text
id
name
```

More dangerous.

Older applications may still expect:

```text
email
```

---

# Rename Column

Old:

```text
customer_name
```

New:

```text
full_name
```

Often breaks pipelines.

Many systems treat this as:

```text
Remove + Add
```

---

# Change Datatype

Old:

```text
age INT
```

New:

```text
age STRING
```

Potentially dangerous.

Can cause:

- parsing failures
- query failures
- application errors

---

# Backward Compatibility

MOST IMPORTANT concept.

---

# Definition

New schema can read old data.

---

# Example

Old Data:

```text
id
name
```

New Schema:

```text
id
name
email
```

Old records still work.

This is:

```text
Backward Compatible
```

---

# Forward Compatibility

Less commonly discussed.

---

# Definition

Old application can read new data.

---

# Example

Application understands:

```text
id
name
```

New records contain:

```text
id
name
email
```

Old application ignores:

```text
email
```

Still works.

---

# Full Compatibility

Supports both:

```text
Backward
+
Forward
```

Compatibility.

---

# Why Data Lakes Need Schema Evolution

Data lakes store data for years.

Business changes constantly.

Without schema evolution:

```text
Historical Data
≠
New Data
```

Huge operational problem.

---

# Avro and Schema Evolution

Avro was designed with schema evolution in mind.

One of its biggest advantages.

---

# Example

Schema Version 1:

```text
id
name
```

Version 2:

```text
id
name
email
```

Avro can handle this gracefully.

---

# Default Values

Very important Avro concept.

Example:

```text
email = null
```

for old records.

Allows compatibility.

---

# Parquet and Schema Evolution

Parquet supports schema evolution reasonably well.

Common operations:

- add columns
- optional fields

work well.

---

# Why Parquet Is Popular

Combines:

```text
Columnar Storage
+
Schema Metadata
```

Very useful in data lakes.

---

# JSON and Schema Evolution

JSON is naturally flexible.

Example:

Record 1:

```json
{
 "id":1
}
```

Record 2:

```json
{
 "id":1,
 "email":"abc@xyz.com"
}
```

Works easily.

---

# Problem with JSON

Too much flexibility can cause:

- inconsistent structures
- missing fields
- data quality issues

---

# Glue Data Catalog

AWS Glue stores:

```text
Table Metadata
Schemas
Partitions
```

for datasets.

---

# Why Glue Matters

Athena depends on schema definitions.

If schema changes unexpectedly:

```text
Queries May Fail
```

---

# Schema Evolution in Glue

Example:

```text
New Column Added
```

Need to:

```text
Update Glue Catalog
```

or run crawler.

---

# Glue Crawlers

Can detect:

- new columns
- schema updates

Automatically.

---

# Schema Drift

Very important concept.

---

# Definition

Schema changes unexpectedly.

Example:

Yesterday:

```text
premium_amount
```

Today:

```text
premium_amt
```

Pipeline breaks.

---

# Schema Drift Problems

Can cause:

- ETL failures
- Athena failures
- reporting issues

---

# Schema Enforcement

Opposite of schema drift.

System validates incoming data.

---

# Example

Expected:

```text
age INT
```

Incoming:

```text
age = "abc"
```

Rejected.

---

# Hudi and Schema Evolution

Hudi supports:

- adding columns
- evolving schemas

while maintaining historical data.

Very important in lakehouses.

---

# Iceberg and Schema Evolution

Iceberg provides excellent schema evolution support.

Supports:

- add columns
- rename columns
- reorder columns

more safely than many older systems.

---

# Why Lakehouses Care

Data lives for years.

Schema changes are inevitable.

Therefore:

```text
Schema Evolution
```

becomes a first-class feature.

---

# CDC and Schema Evolution

Very important real-world topic.

Suppose source database adds:

```text
policy_status
```

CDC pipeline starts sending it.

Downstream systems must adapt.

Otherwise:

```text
Pipeline Failures
```

occur.

---

# Example Insurance Pipeline

```text
Source DB
      ↓
CDC
      ↓
Raw S3
      ↓
Hudi
      ↓
Athena
```

If source schema changes:

Every downstream component must understand it.

---

# Common AWS Exam Scenarios

# Scenario 1

Question:

Need to add new columns without breaking historical data.

Best Answer:

```text
Schema Evolution
```

---

# Scenario 2

Question:

Need compatibility across schema versions.

Best Answer:

```text
Avro
```

---

# Scenario 3

Question:

Need lakehouse-friendly schema management.

Best Answer:

```text
Iceberg or Hudi
```

---

# Common Beginner Mistakes

# Mistake 1

Thinking schemas never change.

They always do.

---

# Mistake 2

Renaming columns carelessly.

Often breaks downstream consumers.

---

# Mistake 3

Ignoring schema compatibility.

Causes production incidents.

---

# Think Like AWS

Modern AWS architectures assume:

```text
Schemas Will Change
```

Design systems that can evolve safely.

---

# Beginner → Intermediate → Advanced

# Beginner

Understand:

- schema
- schema evolution
- compatibility

---

# Intermediate

Learn:

- Avro evolution
- Glue Catalog updates
- schema drift

---

# Advanced

Master:

- Hudi evolution
- Iceberg evolution
- CDC schema propagation
- contract-based data engineering

---

# Final Revision Notes

- Schema evolution allows schemas to change safely
- Common changes include add/remove/rename columns
- Backward compatibility is extremely important
- Avro was designed for schema evolution
- Parquet supports schema evolution well
- Glue Catalog stores schema metadata
- Schema drift can break pipelines
- Hudi and Iceberg support modern schema evolution
- CDC pipelines must handle schema changes
- One of the most important lakehouse concepts