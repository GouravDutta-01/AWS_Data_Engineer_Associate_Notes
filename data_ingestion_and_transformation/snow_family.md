# AWS Snow Family

The **AWS Snow Family** provides **offline, edge-based, and petabyte-scale data migration** appliances for environments with **limited, no, or high-latency network connectivity**.

It includes:

*   **Snowcone**
*   **Snowball Edge (Storage Optimized / Compute Optimized)**
*   **Snowmobile**
    

Used mainly for:

*   Large-scale data migrations
*   Edge computing
*   Disaster recovery
*   Environments without reliable internet
    

---

# 1. Why Snow Family Exists

*   Online transfer (DataSync, Direct Connect) may be **too slow** or **impossible**.
*   You need to transfer **TB to PB** of data **offline**.
*   You need **compute at the edge** with intermittent connectivity.
    

---

# 2. Snowcone

## Overview

*   **Smallest** Snow device
*   **8 TB HDD** or **14 TB SSD**
*   Portable (4.5 lb), rugged
*   Can run **local edge compute** using **AWS IoT Greengrass** or **EC2-compatible AMIs**
*   Transfers data by:
    *   **Shipping device** 
    *   **Or uploading via Snowcone + AWS DataSync agent** (supports online AND offline)
        

## Use Cases

*   Remote sites
*   IoT edge compute
*   Tactical or mobile units
*   Small data collection devices
    

## Exam Notes

*   **Only device supporting both online and offline transfer**
*   SSD model is for harsh/field environments
*   Encrypted with **KMS-managed key through AWS OpsHub**
    

---

# 3. Snowball Edge

Two main models:

### 3.1 Snowball Edge _Storage Optimized_

*   **80 TB usable storage**
*   Used mainly for **large data transfers**
*   Lower compute capacity (but still supports EC2 instances)
    

### 3.2 Snowball Edge _Compute Optimized_

*   **104 vCPUs**
*   **Memory: ~416 GB**
*   ~42 TB storage
*   GPU variant (optional) for ML inferencing
*   Used for:
    *   Edge compute
    *   ML inference  
    *   Analytics at the edge
    *   Processing data before upload
        

## Features for Both

*   Rugged 50 lb appliance
*   Supports:
    *   **EC2 instances**   
    *   **AWS IoT Greengrass**    
    *   **NFS file interface**    
    *   **OpsHub** for management       
*   Ships data to S3 after return
*   **Cluster mode** possible (5–15 nodes)
    

## Use Cases

*   Massive migrations (100 TB – few PB)
*   Edge analytics, preprocessing
*   Low-bandwidth or military/isolated sites
    

## Exam Notes

*   **Snowball Edge DOES NOT support direct Glacier uploads**
*   Data arrives in **S3 Standard** → lifecycle handles Glacier transitions
*   Good for **edge compute** + **bulk transfer**
*   **Multiple devices often needed** due to size (80 TB each)
    

---

# 4. Snowmobile

## Overview

*   **45-foot shipping container**
*   Up to **100 PB** data transfer
*   Delivered by a truck
*   Requires special approval
*   For **data center evacuations** or **massive migration events**
    

## Exam Notes

*   Highest capacity option
*   Used when transferring **tens of PB to EB scale**
*   Security:
    *   GPS tracking
    *   24×7 video
    *   Encrypted
    *   Escort required 
*   NOT for edge compute — pure offline transfer
    

---

# 5. Encryption & Security

*   All Snow devices use **256-bit encryption**
*   Keys managed in **AWS KMS**
*   Keys are **never stored on the device**
*   Authentication via **Manifest file + Unlock code**
*   Device automatically wipes data after ingestion at AWS
    

---

# 6. Data Transfer Flow

1.  Create **Snow job** in AWS Console
2.  AWS prepares the device
3.  Device shipped to your location
4.  You copy data via NFS or OpsHub
5.  Ship back
6.  AWS uploads data **into S3**
7.  Data can then transition to Glacier using lifecycle policies
8.  AWS performs secure erasure
    
---

# 7. Key Differences 

| Feature | Snowcone | Snowball Edge | Snowmobile |
| --- | --- | --- | --- |
| Capacity | 8TB HDD / 14TB SSD | 80TB SO / 42TB CO | Up to 100 PB |
| Edge Compute | Yes | Yes (full EC2) | No |
| Online Transfer | Yes (DataSync) | No | No |
| Offline Transfer | Yes | Yes | Yes |
| Best For | Small/remote sites | PB-scale + edge compute | Data center evacuation |
| Weight | 4.5 lb | 50 lb | Truck-sized |
| Multiple Units | No (usually) | Yes (cluster/parallel) | N/A |

---

# 8. Common Scenarios

### Scenario 1: “Transfer 1 PB with poor connectivity”

 **Snowball Edge Storage Optimized** (multiple devices)

### Scenario 2: “Move an entire data center with 50 PB”

 **Snowmobile**

### Scenario 3: “Remote location with IoT sensors, 5 TB data, need edge compute”

 **Snowcone SSD**

### Scenario 4: “Need GPU or ML inference at edge”

 **Snowball Edge Compute Optimized (GPU model)**

### Scenario 5: “Need online + offline transfer”

 **Snowcone (only device with built-in DataSync option)**

### Scenario 6: “Need to analyze/clean data before upload”

 **Snowball Edge Compute Optimized**

### Scenario 7: “Need writable file interface like NFS”

 **Snowcone or Snowball Edge**, NOT Snowmobile

---

# 9. Important Notes

*   Snow devices **cannot** write data directly to **Glacier**. Must land in S3 first.
*   Snowball/Snowcone always use **encryption** and **automatic wipe**.
*   Snowmobile requires **special AWS approval**.
*   Snowball supports **EC2-compatible AMIs**, but only certain instance types.
*   Snowball Edge can create **clusters**; Snowcone cannot.
*   Snow Family is **not continuous sync** (except Snowcone online DataSync mode).
*   You cannot **append** to a file on Snow devices — they use object storage style workflows.