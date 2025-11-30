# AWS DynamoDB 


## 1. Overview

* **Type:** Fully managed NoSQL database (Key-Value + Document)
* **Use Case:** High-performance OLTP applications
* **Not Ideal For:** Analytics on large datasets → use S3 + Athena/Glue

---

## 2. Primary Key Design

| Term        | Meaning                                                            |
| ----------- | ------------------------------------------------------------------ |
| Primary Key | Entire key structure: PK alone (simple) or PK + SK (composite)     |
| PK          | Partition Key (determines partition location, affects performance) |
| SK          | Sort Key (enables range queries and multiple items per PK)         |

**Notes:**

- Avoid hot partitions by proper PK design.  
  **Hot Partition :** A partition that receives a disproportionately high number of read or write requests, causing throttling, high latency, and uneven utilization of provisioned throughput. It is usually caused by poor partition key design.

- Max 10 GB per PK for a single partition.

---

## 3. Capacity Modes

* **Provisioned Capacity:**

  * Set **Read Capacity Units (RCU)** and **Write Capacity Units (WCU)**.
  * Best for **predictable workloads**.
  * Can enable **auto scaling** to adjust RCUs/WCUs automatically.

* **On-Demand Capacity:**

  * **Auto-scales** to handle any workload.
  * Best for **unpredictable or spiky workloads**.
  * You pay per request; no need to manually manage RCUs/WCUs.

**RCU/WCU Calculations:**

* **RCU:** 1 strongly consistent read per **4 KB per second**

  * Eventually consistent reads consume **0.5 RCU per 4 KB per second**
* **WCU:** 1 write per **1 KB per second**

**Adaptive Capacity:**

* Dynamically redistributes throughput across partitions
* Prevents **hot partitions** from being throttled during uneven traffic

---

## 4. Consistency

* **Strongly Consistent Reads:**

  * Always return the **latest data**
  * Consumes **1 RCU per 4 KB**

* **Eventually Consistent Reads:**

  * May return **stale data**
  * Consumes **0.5 RCU per 4 KB**

* **Global Tables:**

  * Multi-region replication of the table
  * Writes are replicated **asynchronously**
  * Reads in a region are **eventually consistent**
  * Helps build **multi-region high availability**

---

**Important Tips:**

* Provisioned = cheaper for predictable traffic; On-Demand = flexible for unpredictable traffic
* Strong consistency **costs double** RCU vs eventually consistent reads
* Adaptive capacity automatically handles hot partition issues
* Global tables are **eventually consistent** across regions


## 5. Indexes

| Index                            | Description           | Notes                                                                 |
| -------------------------------- | --------------------- | --------------------------------------------------------------------- |
| **LSI (Local Secondary Index)**  | Same PK, different SK | Must be created at table creation; shares RCU/WCU; 10GB per PK limit  |
| **GSI (Global Secondary Index)** | Different PK/SK       | Can be added anytime; has its own RCU/WCU; eventually consistent only |

**When to use:**

* LSI: alternate sorting for same PK
* GSI: query on different PK/SK, alternative access patterns

---

## 6. Query vs Scan

| Operation | Notes                                                                              |
| --------- | ---------------------------------------------------------------------------------- |
| Query     | Uses PK (required) + optional SK conditions; fast and efficient                    |
| Scan      | Reads entire table; costly, slow, consumes RCUs; **not recommended for analytics** |

**Important Tip:**

* For large tables, always export to **S3 + Athena** for analytics instead of scanning

---

## 7. DynamoDB Streams

* Captures **item-level changes**: INSERT, MODIFY, REMOVE
* Can trigger **Lambda, Kinesis, or ETL pipelines**
* TTL deletions appear as REMOVE events in Streams

---

## 8. Time To Live (TTL)

* **Purpose:** Auto-delete items after a specified timestamp
* **Attribute:** You choose any name (e.g., `ttl`, `expiresAt`)
* **Value:** Unix epoch timestamp (seconds)
* **AWS does NOT auto-create this attribute**; must be set by app/ETL
* **Deletes appear in Streams** (important for audit/replication)

---

## 9. Transactions

* `TransactWriteItems`, `TransactGetItems` for **ACID across multiple items**
* 4 MB per request limit
* Higher latency and double WCU/RCU cost

---

## 10. Global Tables

* Multi-region **active/active replication**
* Conflict resolution: last-writer-wins (timestamp)
* Use case: globally distributed apps

---

## 11. Backup & Restore

* **On-Demand Backup:** Full table backup anytime
* **Point-in-Time Recovery (PITR):** Continuous backup with 35-day retention
* Restores create a **new table**, no impact on live table

---

## 12. DynamoDB Accelerator (DAX)

* In-memory cache for **microsecond latency reads**
* Read-through + write-through
* Region-specific, not multi-region
* Does **not** cache writes

---

## 13. Export to S3 (for Analytics)

**Purpose:** DynamoDB not suitable for large-scale analytics; export needed

### Methods:

1. **Point-in-Time Export to S3**

   * One-click in console
   * No RCU/WCU consumption
   * Output: Parquet
2. **Streams → Kinesis → S3**

   * Real-time replication
   * For Change Data Capture
3. **AWS Glue**

   * Scheduled ETL jobs
   * Transform/convert schema (JSON → Parquet)

**After Export:** Query using Athena or EMR for analytics

---

## 14. Important Points

* Query requires PK; SK optional
* Use GSI for queries on non-key attributes
* Hot partitions → fix PK design, not capacity
* Use DAX for read-heavy workloads
* TTL requires **your timestamp attribute**
* Scan **never recommended** for analytics; export to S3
* Global Tables = multi-region writes, eventual consistency
* Transactions = ACID across multiple items

---

