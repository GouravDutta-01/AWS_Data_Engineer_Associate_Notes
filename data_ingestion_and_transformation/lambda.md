# AWS Lambda 

## 1. Overview
AWS Lambda is a **serverless compute service** that runs your code **without provisioning or managing servers**.  
- Automatically scales based on demand.  
- Pay only for the compute time used (per 100ms).  
- Supports multiple languages: Python, Node.js, Java, Go, .NET, Ruby, custom runtimes.

---

## 2. Key Concepts

- **Function**: The unit of execution in Lambda. Contains code and configuration.  
- **Handler**: Entry point of the Lambda function (e.g., `index.handler`).  
- **Event Source**: AWS service or custom app that triggers the Lambda.  
- **Runtime**: Language environment Lambda executes your code in.  
- **Layers**: Additional code or libraries shared across functions.  
- **Environment Variables**: Key-value pairs available to the function at runtime.  
- **Execution Role (IAM Role)**: Permissions Lambda needs to access AWS resources.

---

## 3. How Lambda Works

1. Lambda receives an **event** from a trigger (S3, API Gateway, CloudWatch, etc.).  
2. AWS launches a **container** (if needed) with the function code.  
3. Function executes, optionally writes to **/tmp** (ephemeral storage).  
4. Returns a response or performs an action.  
5. Container may be **reused** for subsequent invocations (warm start).  

---

## 4. Storage in Lambda

| Type | Location | Notes |
|------|---------|------|
| `/tmp` | Ephemeral storage inside container | 512 MB default, up to 10 GB; temporary and isolated per container |
| S3 | Persistent object storage | Use for long-term data storage |
| EFS | Network file system | Shared persistent storage across multiple Lambda instances |
| DynamoDB / RDS | Database storage | Structured or relational data persistence |

**Tip:** `/tmp` is **temporary scratch space**; use S3/EFS for persistent or shared storage.

---

## 5. Triggers (Event Sources)

- **AWS Services**:  
  - S3 (object created), DynamoDB (stream events), CloudWatch (scheduled or log events), Kinesis, SNS, SQS.  
- **API Gateway**: HTTP REST/HTTP APIs trigger Lambda functions.  
- **Custom Applications**: Invoke Lambda using SDKs or EventBridge.  

**Note:** Lambda can be **synchronously** or **asynchronously** invoked.

---

## 6. Event Source Mapping (ESM) & Parallelism

- **ESM connects poll-based sources** (Kinesis, DynamoDB Streams, SQS, Kafka) to Lambda.  
- Lambda **internally polls**, **batches records**, **manages checkpoints**, **retries failed records**, and supports **parallel processing** (shards/partitions).  
- **Configurable for ESM:**
  - `BatchSize` – number of records per invocation  
  - `StartingPosition` – `TRIM_HORIZON` (oldest) or `LATEST` (new records only)  
  - `MaximumRetryAttempts` – number of retries on failure  
  - `BisectBatchOnFunctionError` – splits batch in half on error for retry  
- Guarantees **at-least-once processing**; **idempotency is recommended** (safe to process the same record multiple times).  
- **Push-based sources** (S3, SNS, EventBridge, API Gateway) **do NOT use ESM**; Lambda is invoked immediately per event.  
- **Parallelism**: 
  - Lambda automatically scales per shard; more shards → more parallel Lambda instances.  
  - `ParallelizationFactor` allows multiple Lambda invocations per shard (max 10).  
- **Large datasets**: Split work into batches, use Step Functions, or offload to Glue/EMR/Batch to avoid timeouts.

---

## 7. Execution & Scaling

- Lambda is **serverless** → no servers to manage.  
- **Scaling**: AWS automatically handles concurrency by creating **multiple containers** as needed.  
- **Concurrency**: Default limit 1000 per region (can request increase).  
- **Cold Start**: First invocation of a container may take longer; subsequent invocations in the same container are faster (warm start).  

---

## 8. Limits

| Resource | Limit |
|----------|-------|
| Memory | 128 MB – 10 GB |
| Runtime | Up to 15 minutes execution |
| `/tmp` storage | 512 MB – 10 GB |
| Deployment package | 50 MB (zip), 250 MB (unzipped with layers) |
| Concurrent executions | Default 1000 (can request increase) |

---

## 9. Security

- **IAM Execution Role**: Grants Lambda permissions to access other AWS resources.  
- **VPC Access**: Lambda can access resources inside a VPC.  
- **Environment Variables**: Can be encrypted using KMS.  
- **Event Source Validation**: Ensure triggers are trusted sources (e.g., S3 bucket policies).  

---

## 10. Important Points

- Lambda is **stateless**; persistent storage must use S3, EFS, or databases.  
- **/tmp** is ephemeral; max 10 GB; isolated per container; may persist across warm starts.  
- Lambda **auto-scales** with events; AWS manages containers.  
- **Cold start** occurs for new containers; warm start reuses container.  
- Can be triggered by **AWS services, API Gateway, EventBridge, SQS, SNS, or custom apps**.  
- Execution time max: **15 minutes**; memory configurable up to 10 GB.  
- Use **Layers** for reusable libraries/code.  
- Lambda can be deployed via **console, CLI, SDK, CloudFormation, CDK, or SAM**.  
- Event-driven architecture → Lambda integrates well with serverless workflows.  
- For large or persistent file storage → use **S3 or EFS**, not `/tmp`.  
- **Large datasets**: Process in **batches**, use Step Functions, or offload to Glue/EMR/Batch to avoid timeouts.  
- **Parallelism**: More shards → more Lambda instances automatically; increase `ParallelizationFactor` for multiple concurrent invocations per shard.  
- **Idempotency** is important for at-least-once processing in retries.  
