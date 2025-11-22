# AWS EC2 

## 1. Overview
Amazon EC2 (Elastic Compute Cloud) provides **resizable virtual servers in the cloud**.  
- Launch virtual machines called **instances** with different **OS, CPU, memory, and storage**.  
- Pay-as-you-go pricing.  
- Supports **scaling**, **security**, **networking**, and **storage options**.  
- Use cases: web servers, applications, databases, batch processing, ML workloads, HPC.

---

## 2. EC2 Instance Concepts

- **Instance**: Virtual server running your application code.  
- **AMI (Amazon Machine Image)**: Template with operating system and software pre-installed; used to launch instances.  
- **Instance Types**: Define CPU, memory, storage, and network performance.  
- **Key Pair**: Public/private key pair for SSH (Linux) or RDP (Windows) access.  
- **Security Group**: Virtual firewall controlling inbound/outbound traffic.  
- **Elastic IP**: Static public IP address that can be attached to an instance.  
- **Placement Groups**: Control how instances are placed on hardware to optimize latency, throughput, or fault tolerance.

---

## 3. EC2 Instance Types & Families

| Family | Purpose | Examples | Notes |
|--------|--------|---------|------|
| General Purpose | Balanced CPU, memory, networking | t3, t4g, m5, m6g | Good for most standard workloads like web servers, small databases |
| Compute Optimized | High CPU performance | c5, c6g | Batch processing, high-performance web servers, compute-heavy tasks |
| Memory Optimized | Large RAM workloads | r5, r6g, x1e | Databases, caching, in-memory analytics |
| Storage Optimized | High I/O for storage | i3, d2 | NoSQL databases, data warehousing, big data processing |
| Accelerated Computing | GPU / FPGA | p3, g4, inf1 | ML training, HPC, graphics rendering |

---

## 4. Storage Options

| Storage Type | Persistence | Notes |
|--------------|------------|------|
| EBS (Elastic Block Store) | Persistent | Block-level storage attached to instance; survives stop/restart (if not deleted) |
| Instance Store | Ephemeral | Temporary high-speed storage; data lost on stop/termination |
| S3 | Persistent | Object storage; not attached to instance; accessed over network |
| EFS | Persistent | Network file system; can be shared by multiple instances simultaneously |

**Tip:** Use instance store for temporary processing, EBS for OS/data disks, S3/EFS for shared or long-term storage.

---

## 5. Lifecycle of an EC2 Instance

- **Pending**: Launching the instance.  
- **Running**: Instance is active and ready to use.  
- **Stopping**: Shutting down instance; ephemeral storage lost, EBS persists.  
- **Stopped**: Instance stopped; can restart.  
- **Terminated**: Instance deleted; all ephemeral storage gone; EBS may also be deleted depending on settings.  

---

## 6. Networking & Access

- **VPC (Virtual Private Cloud)**: Isolated network where EC2 instances run.  
- **Subnet**: Subdivision of a VPC; can be public (internet-accessible) or private.  
- **Security Groups**: Control instance-level inbound/outbound traffic.  
- **NACLs (Network ACLs)**: Control subnet-level traffic.  
- **Elastic IP**: Assign static public IP for persistent access.  
- **Load Balancer (ELB)**: Distributes incoming traffic across multiple instances for high availability.

---

## 7. Pricing Models

- **On-Demand**: Pay per hour/second; no commitment; flexible.  
- **Reserved Instances**: Commit 1–3 years; lower cost; good for predictable workloads.  
- **Spot Instances**: Up to 90% cheaper; can be terminated anytime; ideal for fault-tolerant, flexible workloads.  
- **Savings Plans**: Flexible discount model across compute usage.

---

## 8. Scaling & High Availability

- **Auto Scaling Groups**: Automatically increase/decrease instance count based on demand.  
- **Elastic Load Balancer**: Routes traffic across multiple instances for fault tolerance.  
- **Placement Groups**: Optimize for low latency (cluster), high throughput (spread), or fault tolerance (partition).

---

## 9. Important Points 

- EC2 is a **virtual server**, not physical hardware.  
- **Instance store is ephemeral** → use only for temporary data.  
- **EBS is persistent**, survives stop/restart (if configured).  
- **Key Pair required** for SSH/RDP access.  
- **Security Groups** act as virtual firewalls; NACLs control subnet-level traffic.  
- **Spot instances** = cheap but interruptible; **Reserved** = committed for discount; **On-Demand** = pay-as-you-go.  
- **Auto Scaling** + **ELB** = high availability and fault tolerance.  
- **Instance families** must be chosen based on workload: CPU, memory, storage, or GPU needs.  
- **Placement Groups** affect latency, throughput, and fault isolation.  
- **AMI** is the base image; can use public, marketplace, or custom AMIs.  
- **Monitoring**: Use CloudWatch for metrics, alarms, and logging.  
- **Networking**: Understand VPC, subnets, Elastic IPs, and routing for access and security.

