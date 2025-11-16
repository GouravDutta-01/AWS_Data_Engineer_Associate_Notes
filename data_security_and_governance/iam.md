# IAM – Identity and Access Management (AWS Data Engineer Associate Notes)

## 1. What is IAM?
IAM (Identity and Access Management) is AWS’s service for controlling **who** can access **what** resources.  
Data Engineers use IAM heavily for securing data pipelines, S3, Glue, EMR, Redshift, Kinesis, and cross-service access.

---

## 2. IAM Core Identities

### **IAM Users**
- Represent **individual people**.
- Have long-term credentials (password, access keys).
- Used for human access → AWS Console or CLI.

### **IAM Groups**
- Collection of IAM users.
- Helps manage permissions for multiple people at once.
- Cannot have credentials.

### **IAM Roles**
- **Not for humans** — used by **AWS services**, applications, or external identities.
- Provide **temporary credentials** via STS.
- Common in data engineering:
  - Glue job role
  - Lambda execution role
  - EC2/EMR instance role
  - Redshift COPY/UNLOAD role
  - Kinesis/Firehose delivery roles

---
### **IAM Role Trust Policy (Trust Relationship)**

A **trust policy** defines **who is allowed to assume an IAM role**.  
It is attached **only to roles** and uses the action **sts:AssumeRole**.

#### Why Trust Policies Exist?
- Roles do not have access keys.
- AWS services (EC2, Lambda, Glue, EMR) or other AWS accounts must **assume** the role to get temporary credentials.
- A trust policy controls **who may assume the role**.
- A permissions policy controls **what the role can do**.
---

## 3. IAM Policies

### **Identity-Based Policies**
Attached to:
- Users  
- Groups  
- Roles  

Define **what the identity is allowed to do**.

### **Resource-Based Policies**
Attached to AWS resources:
- S3 bucket policies
- SQS policies
- KMS key policies
- Lambda resource policies

Enable:
- Cross-account access  
- Fine-grained access control  

### **Policy Types**
1. **AWS Managed Policies** – maintained by AWS  
2. **Customer Managed Policies** – recommended for production  
3. **Inline Policies** – attached directly inside a single user/role (avoid unless needed)

---

## 4. IAM Best Practices 
- Enforce **least privilege**  
- Use **IAM roles**, not access keys  
- Enable **MFA** for users  
- Rotate credentials regularly  
- Use **AWS SSO / Identity Center** for teams  
- Centralize logging (CloudTrail + IAM Access Analyzer)

---

## 5. IAM in Data Engineering Scenarios

### **S3 Access**
- EC2/Glue/EMR use **roles** to read/write S3 datasets.
- Cross-account access → **bucket policy** + IAM role.

### **KMS**
- IAM controls:
  - Who can encrypt/decrypt data
  - Which role can use a CMK
- SSE-KMS restrictions appear often.

### **Glue**
- Glue jobs require an **IAM role** with:
  - S3 read/write
  - CloudWatch logs write
  - Glue service permissions

### **Kinesis / Firehose**
- Firehose delivery roles write to S3 and Redshift.

### **Redshift**
- Redshift COPY/UNLOAD uses an IAM role attached to the cluster.

### **EMR**
- EMR EC2 instance profile = permissions for Hadoop/Spark jobs.

---

## 6. IAM Access Evaluation Logic 

Order of evaluation:
1. **Explicit Deny** (always wins)
2. **Explicit Allow**
3. **Default Deny**

Bucket policies + IAM policies are evaluated **together**.

---

## 7. IAM vs Bucket Policy vs ACL (Quick Table)

| Feature | IAM Policy | Bucket Policy | ACL |
|--------|------------|----------------|-----|
| Attached To | Users/Groups/Roles | S3 Bucket | Bucket or Object |
| Purpose | Identity permissions | Resource permissions | Legacy object-level control |
| Cross-Account? | No | Yes | Yes |
| Use Today? | Yes | Yes | Only when needed |

---

## 8. IAM Access Analyzer
- Helps detect unintended public or cross-account access.
- Very useful for S3 and KMS misconfigurations.
- Frequently referenced in security scenarios.

---

## 9. Important Notes
- Roles = temporary credentials; cannot have access keys.
- Users = long-term credentials.
- Inline policies should be avoided for teams.
- KMS permissions require BOTH an IAM policy and a KMS key policy.

---

