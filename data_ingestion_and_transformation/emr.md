# AWS EMR (Elastic MapReduce)

## 1. Overview
AWS EMR is a **fully managed distributed processing platform** used for big-data analytics using open-source frameworks.

- Runs frameworks like: **Spark, Hadoop, Hive, HBase, Presto, Flink, Trino**
- Supports **batch + streaming**, ETL, ML data prep, and large-scale log analytics
- Can run on **EC2, EKS, or Serverless**
- Scales horizontally across multiple worker nodes

---

## 2. Key Concepts

- **Cluster**: Group of nodes running EMR software.
- **Master Node**: Coordinates job execution (YARN ResourceManager + HDFS NameNode).
- **Core Nodes**: Execute jobs and store HDFS data.
- **Task Nodes**: Execute jobs only (no HDFS storage).
- **EMRFS**: Connector enabling Spark/Hadoop to read/write S3.
- **Steps**: Spark/Hive/JAR scripts submitted to the cluster.
- **Instance Fleets**: Mix of Spot and On-Demand instances for optimized cost.

---

## 3. How EMR Works

1. User submits a Spark/Hive/MapReduce job.
2. EMR provisions workers and configures distributed storage/computation.
3. Data is read from **S3 (via EMRFS)** or **HDFS**.
4. Workload is executed distributively using **YARN + chosen framework**.
5. Output is written back to S3, HDFS, or a database.
6. Cluster may remain active or **auto-terminate after completion (step mode)**.

---

## 4. Storage in EMR

| Storage Type | Persistent Beyond Cluster? | Notes |
|--------------|---------------------------|-------|
| **S3 (EMRFS)** | Yes | Primary persistent storage; data survives cluster termination |
| **HDFS** | No | Exists only on core nodes; removed when the cluster terminates |
| **EBS (attached volumes)** | No (default behavior) | Used for shuffle and intermediate storage; deleted when EC2 instances terminate *(unless reattachment is explicitly configured)* |
| **Instance Store (Local NVMe/SSD)** | No | Very fast temporary scratch storage; lost on stop, restart, or termination |

---

**Best Practice:** Use **S3/EMRFS** for all long-term and production data.  
Local storage (HDFS, EBS, Instance Store) is best used only for **temporary compute processing**.

---

## 5. Cluster Modes

- **Cluster Mode (Long-Running)**  
  For notebooks, interactive SQL engines, and persistent workloads.

- **Step Mode (Transient)**  
  Executes defined steps and auto-terminates. Best for scheduled ETL.

- **EMR Serverless**  
  No EC2 provisioning; Spark/Hive workloads automatically scale and run on demand.

---

## 6. Node Types & Pricing Strategy

| Node Type | Stores HDFS? | Recommended Pricing | Notes |
|-----------|--------------|--------------------|-------|
| **Master** | No | On-Demand | Critical node; never Spot |
| **Core** | Yes | On-Demand | Stores state; loss = corruption |
| **Task** | No | Spot | Safe for interruption |

**Use Spot for Task nodes** for cost savings.

---

## 7. Scaling

- Supports **manual scaling** and **auto scaling** rules based on:
  - YARN memory/CPU usage
  - HDFS utilization
  - Pending queue size (Spark/Hadoop jobs)

---

## 8. Security

- **IAM Roles**: Service role + Instance profile
- **Encryption**:
  - At-rest: S3, HDFS, EBS
  - In-transit: TLS
- **Kerberos** optional for Hadoop authentication
- **Security Groups** restrict network access

---

## 9. Integrations

- **S3** (primary storage layer)
- **Glue Data Catalog** (Metadata / Hive metastore)
- **Athena** (query EMR-generated data in S3)
- **Kinesis / Kafka** (streaming data)
- **CloudWatch** (monitoring + logging)
- **Step Functions** (workflow automation)

---

## 10. Common Use Cases

- Large-scale Spark ETL
- Data lake transformations and materialization
- Machine learning preprocessing and feature engineering
- Log analytics (clickstream, IoT, application logs)
- Real-time streaming with Spark Streaming or Flink

---

## 11. EMR vs Other AWS Services

| Service | Best For |
|---------|----------|
| **EMR on EC2** | Full control, custom environments |
| **EMR Serverless** | Pay-per-job, no infrastructure |
| **AWS Glue** | Serverless ETL automation |
| **Athena** | Query data in S3 using SQL; no cluster |

---

## 12. Important Points

- Use **S3 (EMRFS)** for persistent data; HDFS is temporary.
- Task nodes are safe for **Spot instances**; Core/Master should be On-Demand.
- **EMR Serverless does not use HDFS** — uses S3 only.
- EMR can use **Glue Data Catalog** in place of Hive Metastore.
- Step-based clusters **auto-terminate** after job completion.
- Use **Instance Fleets** for automatic Spot replacement and cost optimization.

---
