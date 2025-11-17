# AWS DataSync – Exam Notes

## 1. Overview

AWS DataSync is a **managed data transfer service** used to move data **to, from, and between AWS storage services**.  
It is **10× faster** than traditional tools (rsync, cp, scp) due to a **purpose-built protocol**.

---

## 2. What DataSync Is Used For

*   **Migrate large datasets** (files, objects) to AWS.
*   **Hybrid cloud workflows** (on-prem <→ AWS).
*   **Archive cold data to S3**, with lifecycle rules transitioning into **Glacier tiers** (DataSync does NOT write directly to Glacier).
*   **AWS-to-AWS transfers** (S3 ↔ EFS ↔ FSx).
    
---

## 3. Supported Endpoints

### On-Premises / Other Cloud → AWS

Requires **DataSync Agent** for:
*   NFS 
*   SMB 
*   HDFS  
*   Self-managed object storage
    

### AWS → AWS (No agent needed)

*   S3 (Standard, IA classes; Glacier only via lifecycle transition)
*   EFS 
*   FSx (Lustre, Windows, NetApp ONTAP, OpenZFS)
    
Glacier transitions happen through S3 restore + lifecycle; DataSync does not “sync directly” into cold classes; it writes to S3 and lifecycle handles transitions.

---

## 4. Key Characteristics

*   **NOT continuous replication**
    *   DataSync = **task-based**, scheduled, or manual runs.
    *   For continuous database sync → use **DMS**.
    *   For continuous file changes on-prem ↔ AWS → use **Storage Gateway File Gateway**.      
*   **Supports one-time migration + recurring sync**, but **not real-time**.
*   **Preserves metadata** (ownership, permissions, timestamps).
*   **Data verification** built-in:
    *   Checksums before and after transfer.    
*   **Throttling available** (limit bandwidth usage).
*   **Scale-out**: Many tasks can run in parallel.
    

---

## 5. Agents

### When Needed
*   Required for **on-prem or self-managed** sources/destinations.
### When Not Needed
*   For **AWS-to-AWS** transfers.

---

## 6. Performance

*   A single agent can push **up to 10 Gbps**.
*   Parallelization across multiple agents.
*   Purpose-built protocol = optimized for WAN transfer.
    

---

## 7. Security

*   **End-to-end encrypted** during transfer.
*   Integrates with:
    *   **CloudWatch** (metrics/logs)
    *   **CloudTrail** (API auditing)   
*   IAM policies required to access destinations (S3, EFS, etc.).
    
---

## 8. Scheduling & Automation

*   Tasks can be run:
    *   **On-demand**
    *   **Hourly**
    *   **Daily**
    *   **Weekly**   
* DataSync is **NOT event-driven** by itself (EventBridge must trigger it if needed).
* DataSync is **NOT continuous sync** — it only runs when scheduled or started manually.
    

---

## 9. Use Cases (Exam-Relevant)

*   Migrating shared file systems to AWS.
*   Large migration from **NFS/SMB/HDFS → S3/EFS/FSx**.
*   Syncing on-prem data lakes to S3.
*   Replicating AWS file systems between regions/accounts.
*   Archive on-prem cold datasets to:
    *   **S3 Glacier**
    *   **S3 Glacier Deep Archive**
*   Complementary with **Storage Gateway File Gateway** for ongoing access.
    

---

## 10. Limitations (Frequently Tested)

*   **Not for databases**  
    Use **AWS DMS** for database migrations.
*   **Not for continuous near-real-time syncing**.
*   **Does not sync block storage/boot volumes**.
*   **Requires agent for non-AWS endpoints**.
    

---

## 11. DataSync vs Storage Gateway 

### **AWS DataSync**
- Built for **large-scale bulk transfers (TB–PB)**.
- Up to **10× faster** than traditional tools (rsync, cp).
- Optimized for **high-speed migrations**, not continuous access.
- Runs **on-demand or scheduled** tasks.
- Best for **moving data** into AWS quickly.

### **AWS Storage Gateway**
- Not designed for bulk migration; meant for **ongoing hybrid access**.
- Uses a **local cache**, performance depends on network + cache size.
- Suitable for **moderate-size datasets**, not massive transfers.
- Provides **file/block/tape interfaces** for existing on-prem apps.
- Best for **accessing S3/EFS continuously**, not high-speed movement.

---

## 12. Important Notes

* DataSync does **not** do continuous sync → Storage Gateway File Gateway does.  
* DataSync cannot migrate **databases** → use DMS.  
* DataSync cannot write _directly_ into Glacier classes → lifecycle transitions handle it.  
* DataSync agent is **not** needed for AWS-to-AWS transfers.  
* DataSync can transfer **from HDFS** (often tested).  
* DataSync preserves **POSIX, NFS, and SMB** metadata.