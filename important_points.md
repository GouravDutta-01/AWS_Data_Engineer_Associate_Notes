# Important Points

- **Amazon EventBridge**
  - EventBridge is the only event-based service that integrates directly with **third-party SaaS partners**.
  - It automatically ingests events from **90+ AWS services** without needing developers to create resources manually.

- **CloudTrail vs CloudWatch**
  - **CloudTrail** provides an audit trail of *who did what and when* — tracking API activity and user actions for **security and compliance**.
  - **CloudWatch** focuses on *what is happening* — providing **metrics, logs, and alarms** for real-time monitoring and performance management.

- **Amazon MemoryDB vs ElastiCache**
  - **MemoryDB** → Redis-compatible **in-memory database** designed for **durability and multi-AZ fault tolerance**. Data is stored in memory for speed and written to a **durable, distributed transaction log** replicated across Availability Zones. Suitable as a **primary data store**.
  - **ElastiCache** → **In-memory cache** for performance improvement, not for persistence. Data may be lost if a node fails unless snapshots are manually configured.
  - **Use case difference:** MemoryDB → real-time, durable workloads (microservices, finance). ElastiCache → caching layer in front of RDS/DynamoDB.

- **MemoryDB vs DynamoDB vs Aurora**
  - **MemoryDB** → Ultra-low latency (≈100–400 µs), Redis API, in-memory with durability.
  - **DynamoDB** → Serverless NoSQL key-value store, millisecond latency, automatically scalable, supports **Global Tables** for multi-region replication.
  - **Aurora** → Relational (MySQL/PostgreSQL-compatible) database, ACID transactions, sub-10 ms latency, **6-way replication** across 3 AZs, supports **Aurora Global Database**.
  - **Choosing:**
    - MemoryDB → speed-critical and durable in-memory workloads.
    - DynamoDB → high-scale, flexible schema, low-ops NoSQL.
    - Aurora → transactional, relational data with SQL and joins.

- **Disaster Recovery (DR) Strategies**
  - **Backup & Restore (Cold Standby)** – Only backups (e.g., S3, RDS snapshots) stored; recovery requires redeployment.  
    *RTO: hours  |  RPO: hours  |  Cost: low*
  - **Pilot Light** – Core systems (like DB, IAM, minimal VPC) always running; remaining resources launched on failover.  
    *RTO: minutes  |  RPO: seconds–minutes*
  - **Warm Standby** – Scaled-down but functional environment running continuously, can scale up quickly.  
    *RTO: minutes  |  RPO: seconds*
  - **Hot Standby (Active-Passive)** – Fully provisioned secondary environment, synchronized in real time, activated automatically on failure.  
    *RTO: seconds  |  RPO: near-zero*
  - **Active-Active (Multi-Region/Multi-AZ)** – Multiple environments actively serving traffic and replicating data bidirectionally.  
    *RTO/RPO: zero  |  Cost: highest*

- **Key DR Concepts**
  - **RTO (Recovery Time Objective)** – Maximum acceptable downtime.
  - **RPO (Recovery Point Objective)** – Maximum acceptable data loss (time since last sync).
  - **Multi-AZ** – High availability within a region.
  - **Multi-Region** – Geographic redundancy for disaster recovery.
  - **Failover** – Automatic redirection to standby systems during failure.
  - **Replication** – Continuous data copy between AZs/regions to maintain durability.

- **Service Examples by DR Type**
  - **MemoryDB** → Active-Active (multi-AZ, synchronous replication)
  - **ElastiCache** → Hot standby (replicas promoted on failure)
  - **Aurora** → Hot standby (Multi-AZ) or Active-Active (Global DB)
  - **RDS** → Warm/Hot standby (Multi-AZ synchronous)
  - **DynamoDB** → Active-Active (Global Tables)
  - **S3** → Active-Active by default (multi-AZ redundancy)