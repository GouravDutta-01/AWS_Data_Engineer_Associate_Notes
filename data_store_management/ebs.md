# Amazon EBS (Elastic Block Store)

## 1. Overview

Amazon **Elastic Block Store (EBS)** is a **block-level storage service** for EC2 instances.

* Durable, persistent storage.
* Designed for **high availability**, **low-latency**, and **consistent performance**.
* Volumes behave like raw unformatted block devices.
* Data persists even after an EC2 instance is stopped or terminated (unless it's an instance store).

---

## 2. EBS Volume Types

EBS offers multiple volume types optimized for different workloads.

### **A. SSD-backed (for transactional workloads)**

Used for **high IOPS** and **low latency**.

| Type                          | Purpose                            | Max IOPS | Max Throughput |
| ----------------------------- | ---------------------------------- | -------- | -------------- |
| **gp3** (General Purpose SSD) | Default choice; balanced cost/perf | 16,000   | 1,000 MB/s     |
| **io1**                       | High-performance IOPS-intensive    | 64,000   | 1,000 MB/s     |
| **io2**                       | Higher durability & IOPS           | 64,000   | 1,000 MB/s     |
| **io2 Block Express**         | Ultra-high performance             | 256,000  | 4,000 MB/s     |

### **B. HDD-backed (for throughput workloads)**

Used for **large, sequential, streaming** workloads.

| Type                               | Purpose                        | Max Throughput |
| ---------------------------------- | ------------------------------ | -------------- |
| **st1** (Throughput Optimized HDD) | Big data, log processing       | 500 MB/s       |
| **sc1** (Cold HDD)                 | Lowest cost; infrequent access | 250 MB/s       |

**Note:** HDD volumes **cannot be used as boot volumes**.

---

## 3. EBS Performance Concepts

### **IOPS (Input/Output Operations per Second)**

* Critical for transactional workloads.
* gp3: 3,000 baseline IOPS, scalable to 16,000.
* io2/io1: provisioned IOPS.

### **Throughput (MB/s)**

* Important for big data/streaming workloads.

### **Queue Depth**

* Number of pending I/O requests.

### **Bursting**

* gp3 does NOT burst (fixed performance).
* gp2 had burst credits; gp3 is better.

---

## 4. Snapshots

### **Key Facts**

* EBS Snapshots are **incremental backups** stored in **Amazon S3**.
* Snapshots store **only changed data blocks**.
* Can be used to:

  * Create new volumes.
  * Migrate volumes across AZs.
  * Create AMIs.
  * Back up data across regions.

### **Features**

* **Fast Snapshot Restore (FSR)** improves performance of volumes restored from snapshots.
* **Archive Snapshots**: Lower cost, 24–72 hours restore time.

---

## 5. EBS Multi-Attach

* Supported only for **io1** & **io2** volumes.
* Allows a single volume to be attached to **multiple EC2 instances** (up to 16).
* Use cases:

  * Clustered filesystems (e.g., Oracle RAC).
  * High availability workloads.
* **Not** supported for gp3.

---

## 6. Encryption

* EBS encryption uses **AWS KMS**.
* Encrypts:

  * Data at rest.
  * Data in transit between EC2 and EBS.
  * Snapshots.
  * Volume clones.
* **Cannot decrypt** an encrypted snapshot/volume.
* **Default encryption** can be enabled per account.

---

## 7. EBS vs. Instance Store

| Feature     | EBS                                   | Instance Store               |
| ----------- | ------------------------------------- | ---------------------------- |
| Persistence | Persists even if EC2 stops/terminates | Data lost on stop/terminate  |
| Durability  | High (replicated in AZ)               | Low                          |
| Use cases   | Databases, OS disks                   | Cache, buffers, temp storage |
| Snapshots   | Supported                             | Not supported                |
| Resize      | Yes                                   | No                           |

---

## 8. Availability & Durability

* EBS automatically replicates data **within the same Availability Zone**.
* To move data across AZs or regions → use snapshots.
* EBS volumes are **AZ-scoped**.

---

## 9. Volume Lifecycle Operations

### **Create Volume** → Attach to EC2 → Mount & Format → Use → Snapshot → Detach → Delete

### **Modifying EBS Volumes**

* Can increase size.
* Change type (gp3 → io2, etc).
* Increase IOPS or throughput.
* **No downtime** using EBS online resizing.

---

## 10. EBS Features

* **Elastic Volumes**: Resize without downtime.
* **Recycling performance**: Warm-up required after snapshot restore.
* **Pre-warming**: Use `dd` or `fio` to access all blocks.
* **Fast Snapshot Restore** eliminates the need for warm-up.
* **Lifecycle Manager (DLM)**: Automated snapshot scheduling.

---

## 11. Best Practices

* Use **gp3** as default.
* Use **io2** for mission-critical applications requiring high durability.
* Use **EBS-optimized EC2 instances** for maximum throughput.
* Regularly backup using snapshots.
* Use monitoring (CloudWatch):

  * VolumeReadOps
  * VolumeWriteOps
  * BurstBalance
  * Queue Length

---


## 12. EMR, RDS, ECS, and EKS Usage

* EC2-based services use EBS internally.
* EMR nodes may use multiple EBS volumes.
* RDS uses EBS under the hood.
* ECS/EKS use EBS for persistent volumes.

---

## 13. Pricing

EBS pricing is based on:

* Volume type (gp3, io2, st1, etc.)
* Provisioned size
* Provisioned IOPS (for IO types)
* Snapshot storage
* Data transfer across AZs

---

## 14. Important Points

* EBS = block storage for EC2.
* Snapshots are incremental.
* EBS is AZ-scoped.
* Multi-Attach only for io1/io2.
* gp3 > gp2; gp3 has no burst bucket.
* Use io2 for high durability (99.999%).
* EBS is Single-AZ only. Data is automatically replicated only within the same AZ, not across multiple AZs. For cross-AZ or cross-region durability, you must use EBS Snapshots (which are stored in S3).
* Snapshot restore requires warm-up unless using FSR.
* Instance store is ephemeral.
* EBS encryption is seamless.

---

