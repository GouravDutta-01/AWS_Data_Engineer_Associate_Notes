# AWS VPC(Virtual Private Network)

## Key Concepts :
- VPC : private network to deploy your resources *(regional resource)*
- Subnet : Network partition inside your VPC *(availability zone resource)*
- Public Subnet : subnet that is accessible from the internet
- Private Subnet : subnet that is not accessible from the internet
- To define access to the internet and between subnets, we use Route Tables
- Internet Gateways helps our VPC instances connect with the Internet
- Public subnets have a route to the internet gateway
- NAT Gateways (AWS Managed) and NAT Instances (self managed) allow your instances in your private subnets to access the internet while remaining private

## Network ACL and Security Groups

### 1. Network ACL (NACL)
- A firewall that controls traffic **in/out of a subnet**
- Supports **ALLOW and DENY** rules
- Attached at the **subnet level**
- Rules use **IP addresses** (no SG references)
- **Stateless**: Return traffic is **not automatic** (needs a separate rule)

**How Stateless Works**
- NACL does not track connections
- You must define **both inbound and outbound** rules
- Every packet is checked individually

**Example**
If you allow **Inbound: TCP 80**, you must also allow **Outbound: ephemeral ports** (response).

---

### 2. Security Groups (SG)
- A firewall that controls traffic **for an ENI / EC2 instance**
- Supports **only ALLOW** rules
- Attached at the **instance/ENI level**
- Rules use **IP addresses + Security Group IDs**
- **Stateful**: Return traffic is **automatic**, no opposite rule required

**How Stateful Works**
- SG remembers connection state
- If inbound is allowed, outbound reply is auto-allowed
- No need to define both sides

**Example**
If you allow **Inbound: TCP 80**, the outgoing response is automatically allowed.

---

### Network ACLs vs Security Groups

| **Security Group**                                                                 | **Network ACL**                                                                 |
|------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| Operates at the **instance level**                                                 | Operates at the **subnet level**                                                 |
| Supports **allow rules only**                                                      | Supports **allow and deny rules**                                                |
| **Stateful**: Return traffic is automatically allowed regardless of any rules      | **Stateless**: Return traffic must be explicitly allowed by rules                |
| Evaluates **all rules** before deciding whether to allow traffic                   | Processes rules **in numbered order** when deciding whether to allow traffic     |
| Applies to an instance **only when specified** at launch or later association      | Automatically applies to **all instances in the subnet** it's associated with     |


## VPC Flow Logs

VPC Flow Logs capture information about **IP traffic** going to and from your network interfaces.

### Types of Flow Logs
- **VPC Flow Logs**
- **Subnet Flow Logs**
- **Elastic Network Interface (ENI) Flow Logs**

### Use Cases
Helps monitor and troubleshoot **connectivity issues**, for example:
- Subnet to Internet
- Subnet to Subnet
- Internet to Subnet

### AWS Managed Interfaces
Flow logs also captures network information from AWS-managed network interfaces:
- Elastic Load Balancers (ELB)
- ElastiCache
- RDS / Aurora
- Other managed services

### Destinations
Flow log data can be sent to:
- **Amazon S3**
- **CloudWatch Logs**
- **Kinesis Data Firehose**
 ---
## VPC Peering

VPC Peering allows two VPCs to communicate **privately** over the AWS network.

### Key Concepts
- Connects **two VPCs** so they behave as if they are in the **same network**
- Traffic stays on **AWS private network** (not the public internet)
- **No overlapping CIDR ranges** allowed
- **Not transitive**: each VPC pair needs its own peering connection

### Example (Non-Transitive)
If VPC A needs to talk to VPC B and VPC C:
- VPC Peering: A ↔ B
- VPC Peering: B ↔ C  
  → A cannot reach C automatically  
- For A ↔ C, create another peering:
  - VPC Peering: A ↔ C

---

## VPC Endpoints

VPC Endpoints allow you to connect to AWS services **privately** without using the public internet.

### Benefits
- Traffic stays within **private AWS network**
- Increased **security**
- Improved **latency**
- No need for public IPs, NAT Gateway, or Internet Gateway

### Types of VPC Endpoints
#### 1. Gateway Endpoints
- Used for:
  - **Amazon S3**
  - **DynamoDB**
- Route traffic via a **route table**

#### 2. Interface Endpoints (PrivateLink)
- Used for **most AWS services**, including:
  - S3, DynamoDB
  - CloudWatch, SNS, SQS, etc.
- Creates **ENIs in your subnets**
- Uses **PrivateLink** for private connectivity

---

## AWS PrivateLink (Endpoint Services)

PrivateLink provides **private service connectivity** across VPCs/accounts without exposing traffic to the internet.

- No VPC Peering, NAT, IGW, routing changes
- Scales to **many VPCs** (cross-account)
- One-way: **Consumer VPC → Provider service**
- Provider VPC uses **Network Load Balancer (NLB)**
- Consumer VPC uses **Interface Endpoint (ENI)**

**Use Cases**
- Private access to AWS services
- SaaS providers exposing services to many customers
- Cross-account microservices

---
## PrivateLink vs VPC Peering

### PrivateLink
- Service-level access
- One-way traffic
- Uses ENI + NLB
- No routing required
- Works with overlapping CIDRs

### VPC Peering
- Network-level connection
- Bi-directional traffic
- Needs route tables
- No transitive routing
- Overlapping CIDRs not allowed
---

## Site-to-Site VPN vs Direct Connect

### Site-to-Site VPN
- Connects **on-premises network to AWS** using a VPN tunnel
- Connection is **encrypted by default**
- Traffic goes over the **public internet**
- Quick to set up (minutes to hours)
- Good for **initial hybrid setup** or **backup** to Direct Connect

---

### Direct Connect (DX)
- Creates a **dedicated physical connection** between on-premises and AWS
- Connection is **private, secure, and high-bandwidth**
- Traffic flows over a **private network**, not the internet
- Takes **weeks (≈1 month)** to establish (provider coordination)
- Ideal for **enterprise workloads**, **low latency**, **high throughput**, and **data-intensive** workloads

