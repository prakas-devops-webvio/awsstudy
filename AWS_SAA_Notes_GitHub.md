# ☁️ AWS Certified Solutions Architect – Associate (SAA-C03)

> **Concise exam notes covering all major topics for the SAA-C03 exam.**

![AWS](https://img.shields.io/badge/AWS-SAA--C03-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Exam](https://img.shields.io/badge/Passing%20Score-720%2F1000-success?style=for-the-badge)
![Questions](https://img.shields.io/badge/Questions-65-blue?style=for-the-badge)
![Duration](https://img.shields.io/badge/Duration-130%20mins-informational?style=for-the-badge)

---

## 📋 Table of Contents

| # | Topic |
|---|-------|
| 1 | [Exam Overview](#-exam-overview) |
| 2 | [IAM & Security](#1️⃣-iam--security) |
| 3 | [EC2, EBS & Storage](#2️⃣-ec2-ebs--storage) |
| 4 | [S3 & Glacier](#3️⃣-s3--glacier) |
| 5 | [VPC & Networking](#4️⃣-vpc--networking) |
| 6 | [ELB, Auto Scaling & HA](#5️⃣-elb-auto-scaling--high-availability) |
| 7 | [RDS, Aurora & Databases](#6️⃣-rds-aurora--databases) |
| 8 | [Route 53 & CloudFront](#7️⃣-route-53--cloudfront) |
| 9 | [Serverless](#8️⃣-serverless-lambda-api-gateway-sqs-sns) |
| 10 | [Additional Key Services](#9️⃣-additional-key-services) |
| 11 | [Disaster Recovery](#-disaster-recovery-strategies) |
| 12 | [Cost Optimization](#1️⃣1️⃣-cost-optimization) |
| 13 | [Well-Architected Framework](#1️⃣2️⃣-well-architected-framework) |
| 14 | [Quick Exam Tips](#-quick-exam-tips) |

---

## 📌 Exam Overview

- **Format:** 65 questions (MCQ + Multiple Response)
- **Duration:** 130 minutes
- **Passing Score:** 720 / 1000
- **Domains & Weights:**

| Domain | Weight |
|--------|--------|
| Design Resilient Architectures | 26% |
| Design High-Performing Architectures | 24% |
| Design Secure Architectures | 30% |
| Design Cost-Optimized Architectures | 20% |

---

## 1️⃣ IAM & Security

<details>
<summary><strong>IAM Basics</strong></summary>

- **Users** – individual people/services
- **Groups** – collection of users (policies attached to group)
- **Roles** – assigned to AWS services or federated users (no long-term credentials)
- **Policies** – JSON documents defining permissions (Allow/Deny)
- Policy evaluation order: **Explicit Deny > Explicit Allow > Default Deny**

</details>

<details>
<summary><strong>Key IAM Concepts</strong></summary>

- **Least Privilege** – grant only what's needed
- **MFA** – recommended for root and privileged users
- **Access Keys** – for CLI/SDK access; never embed in code
- **IAM Policy Conditions** – restrict by IP, time, MFA, tags
- **Permission Boundaries** – max permissions an entity can have
- **Service Control Policies (SCPs)** – apply to AWS Organizations; restrict member accounts
- **Resource-based policies** – attached to resources (e.g., S3 bucket policy)
- **Identity-based policies** – attached to users/groups/roles

</details>

<details>
<summary><strong>STS (Security Token Service)</strong></summary>

- Issues **temporary credentials** (Access Key + Secret + Session Token)
- Used for: cross-account access, federated login, EC2 instance roles
- Key API calls: `AssumeRole`, `AssumeRoleWithWebIdentity`, `AssumeRoleWithSAML`

</details>

<details>
<summary><strong>AWS Organizations</strong></summary>

- Manage multiple AWS accounts centrally
- **Consolidated Billing** – single bill for all accounts
- **SCPs** – apply permissions guardrails across OUs (Organizational Units)
- SCPs do **NOT** affect the management/root account

</details>

<details>
<summary><strong>Security Services Cheat Sheet</strong></summary>

| Service | Purpose |
|---------|---------|
| **AWS KMS** | Managed encryption key service; CMKs (Customer Managed Keys) |
| **CloudHSM** | Dedicated hardware security module (you manage keys) |
| **AWS Shield** | DDoS protection (Standard = free, Advanced = paid) |
| **AWS WAF** | Web Application Firewall (L7 – SQL injection, XSS) |
| **Amazon GuardDuty** | Threat detection using ML on VPC Flow Logs, CloudTrail, DNS |
| **AWS Macie** | Discovers & protects sensitive data in S3 (PII detection) |
| **AWS Inspector** | Automated security assessments for EC2/containers |
| **AWS Config** | Tracks resource config changes & compliance |
| **AWS Secrets Manager** | Store & auto-rotate secrets (DB passwords, API keys) |
| **AWS SSM Parameter Store** | Store config & secrets (cheaper, no auto-rotation) |
| **Amazon Cognito** | User auth for web/mobile apps (User Pools + Identity Pools) |

</details>

<details>
<summary><strong>Cognito</strong></summary>

- **User Pools** – Authentication (sign in/up, returns JWT tokens)
- **Identity Pools** – Authorization (exchange tokens for temporary AWS credentials)

</details>

---

## 2️⃣ EC2, EBS & Storage

<details>
<summary><strong>EC2 Instance Families</strong></summary>

| Family | Optimized For | Use Case |
|--------|--------------|----------|
| **C** (Compute) | CPU | Web servers, gaming, batch |
| **R** (RAM) | Memory | In-memory DBs, caches, SAP |
| **M** (General) | Balanced | Application servers |
| **I** (I/O) | Storage IOPS | NoSQL, data warehousing |
| **G / P** (GPU) | Graphics/ML | ML training, rendering |
| **T** (Burstable) | CPU burst | Dev/test, low-traffic apps |

</details>

<details>
<summary><strong>EC2 Purchasing Options</strong></summary>

| Type | When to Use | Savings |
|------|-------------|---------|
| **On-Demand** | Short-term, unpredictable | Baseline |
| **Reserved (1 or 3 yr)** | Steady-state workloads | Up to 72% |
| **Savings Plans** | Flexible Reserved alternative | Up to 72% |
| **Spot** | Fault-tolerant, interruptible | Up to 90% |
| **Dedicated Host** | Compliance, BYOL | Most expensive |
| **Dedicated Instance** | Hardware isolation, no cross-account sharing | High |

</details>

<details>
<summary><strong>Placement Groups</strong></summary>

| Group | Description | Use Case |
|-------|-------------|----------|
| **Cluster** | Same AZ, same rack | Low-latency HPC |
| **Spread** | Distinct hardware; max 7/AZ | Critical instances |
| **Partition** | Groups of instances per partition | Kafka, Hadoop, Cassandra |

</details>

<details>
<summary><strong>EC2 Networking</strong></summary>

- **ENI** – Elastic Network Interface; virtual NIC; movable between instances
- **Enhanced Networking (ENA)** – higher bandwidth, lower latency (no extra cost)
- **EFA** – Elastic Fabric Adapter; HPC/ML; bypasses OS network stack
- **Elastic IP** – static public IP; costs money when not attached to a running instance

</details>

<details>
<summary><strong>EC2 Hibernate</strong></summary>

- RAM state saved to encrypted EBS root volume
- Instance boots faster (state preserved)
- Root volume must be **encrypted**; max hibernate duration = **60 days**

</details>

<details>
<summary><strong>EBS Volume Types</strong></summary>

| Type | Use Case | Max IOPS |
|------|----------|----------|
| **gp3** | General purpose (default) | 16,000 |
| **gp2** | General purpose (IOPS tied to size) | 16,000 |
| **io1 / io2** | High-performance DBs, critical apps | 64,000 (256K for io2 Block Express) |
| **st1** | Throughput HDD – big data, Kafka | 500 MiB/s |
| **sc1** | Cold HDD – infrequent archival | 250 MiB/s |

- **Multi-Attach** – io1/io2 only; attach to multiple EC2s in same AZ
- **Snapshots** – incremental; stored in S3; can be copied cross-region
- EBS is **AZ-scoped** — must be in same AZ as EC2

</details>

<details>
<summary><strong>EFS, FSx & Instance Store</strong></summary>

**Instance Store**
- Physically attached to host (ephemeral — lost on stop/terminate)
- Very high IOPS; good for buffers/caches/scratch data

**EFS (Elastic File System)**
- Managed NFS; **multi-AZ, multi-instance** (Linux only)
- Auto-scales; pay per use
- Storage classes: Standard, Infrequent Access (EFS-IA)
- Performance modes: General Purpose, Max I/O
- Throughput modes: Bursting, Provisioned, Elastic

**FSx Services**

| Service | Protocol | Use Case |
|---------|----------|----------|
| **FSx for Windows** | SMB | Windows-native apps, Active Directory |
| **FSx for Lustre** | POSIX | HPC, ML, integrates with S3 |
| **FSx for NetApp ONTAP** | NFS/SMB/iSCSI | Multi-protocol, snapshots |
| **FSx for OpenZFS** | NFS | ZFS workloads, low-latency |

</details>

---

## 3️⃣ S3 & Glacier

<details>
<summary><strong>S3 Basics</strong></summary>

- **Object storage** – flat namespace; not a file system
- **Bucket** – globally unique name; regional resource
- **Object** – key (full path) + value (data) + metadata
- Max object size: **5 TB**; single PUT limit: **5 GB** → use **multipart upload** for > 5 GB
- **Strong read-after-write consistency** (since Dec 2020)

</details>

<details>
<summary><strong>S3 Storage Classes</strong></summary>

| Class | Use Case | Retrieval |
|-------|----------|-----------|
| **S3 Standard** | Frequently accessed | Immediate |
| **S3 Standard-IA** | Infrequent, rapid when needed | Immediate |
| **S3 One Zone-IA** | Infrequent, single AZ | Immediate |
| **S3 Glacier Instant** | Archive, accessed few times/year | Immediate |
| **S3 Glacier Flexible** | Archive, occasional access | Minutes–Hours |
| **S3 Glacier Deep Archive** | Long-term, rarely accessed | 12–48 Hours |
| **S3 Intelligent-Tiering** | Unknown/changing access patterns | Automatic |

</details>

<details>
<summary><strong>S3 Security</strong></summary>

- **Bucket Policies** – resource-based JSON; control cross-account/public access
- **Block Public Access** – account-level; overrides all policies
- **Encryption types:**

| Type | Key Management |
|------|---------------|
| **SSE-S3** | AWS manages keys (default) |
| **SSE-KMS** | KMS-managed; audit trail, key rotation |
| **SSE-C** | Customer provides key with each request |
| **Client-side** | Encrypt before uploading |

- **Presigned URLs** – temporary, time-limited access to specific objects
- **VPC Gateway Endpoint** – private S3 access without internet

</details>

<details>
<summary><strong>S3 Key Features</strong></summary>

- **Versioning** – multiple object versions; protects against accidental delete
- **MFA Delete** – requires MFA to delete versions (bucket owner only)
- **Replication:**
  - **CRR** (Cross-Region) – compliance, latency, redundancy
  - **SRR** (Same-Region) – log aggregation, test environments
  - Versioning must be enabled on **both** buckets
- **Lifecycle Policies** – auto transition/expire objects between storage classes
- **Transfer Acceleration** – faster uploads via CloudFront edge locations
- **S3 Select** – SQL queries directly on S3 objects (CSV, JSON, Parquet)
- **Object Lock** – WORM; Compliance or Governance mode
- **Access Logs** – log all requests to another S3 bucket

</details>

---

## 4️⃣ VPC & Networking

<details>
<summary><strong>VPC Basics</strong></summary>

- **CIDR Block** – IP range (e.g., `10.0.0.0/16`)
- **Subnets** – tied to one AZ; AWS reserves **5 IPs** per subnet (first 4 + last 1)
  - **Public** – has route to Internet Gateway
  - **Private** – no direct internet route
- Default VPC CIDR: `172.31.0.0/16`

</details>

<details>
<summary><strong>Internet Connectivity</strong></summary>

| Resource | Purpose |
|----------|---------|
| **Internet Gateway (IGW)** | Public subnet internet access; attached to VPC |
| **NAT Gateway** | Private subnet outbound internet (no inbound); AZ-specific; in public subnet |
| **NAT Instance** | Self-managed EC2; cheaper; must disable Source/Dest check |

> ✅ For HA: deploy **one NAT Gateway per AZ**

</details>

<details>
<summary><strong>Security Groups vs NACLs</strong></summary>

| Feature | Security Groups | NACLs |
|---------|----------------|-------|
| Level | Instance | Subnet |
| State | **Stateful** | **Stateless** |
| Rules | Allow only | Allow + Deny |
| Evaluation | All rules | Numbered order (lowest first) |

</details>

<details>
<summary><strong>VPC Connectivity Options</strong></summary>

| Option | Description |
|--------|-------------|
| **VPC Peering** | Connect 2 VPCs; same/cross-account/region; **non-transitive** |
| **Transit Gateway** | Hub-and-spoke; connect many VPCs + on-prem; **transitive** |
| **Site-to-Site VPN** | Encrypted tunnel over internet; Virtual Private Gateway |
| **AWS Direct Connect** | Dedicated private connection; consistent bandwidth |
| **Gateway Endpoint** | Private access to **S3 & DynamoDB** (free) |
| **Interface Endpoint (PrivateLink)** | Private access to most AWS services (ENI, paid) |

</details>

<details>
<summary><strong>Other VPC Features</strong></summary>

- **VPC Flow Logs** – capture IP traffic (VPC/subnet/ENI level); stored in S3 or CloudWatch
- **Bastion Host** – EC2 in public subnet; SSH gateway to private instances
- **Egress-Only IGW** – IPv6 only; outbound from private subnets
- **Direct Connect Gateway** – connect to multiple VPCs across regions
- **Direct Connect + VPN** – add IPSec encryption over Direct Connect

</details>

---

## 5️⃣ ELB, Auto Scaling & High Availability

<details>
<summary><strong>Load Balancer Types</strong></summary>

| Type | Layer | Protocol | Use Case |
|------|-------|----------|----------|
| **ALB** | L7 | HTTP/HTTPS, WebSocket | Microservices, path/host routing |
| **NLB** | L4 | TCP, UDP, TLS | Ultra-low latency, static IP, millions RPS |
| **GWLB** | L3+L4 | IP | Firewalls, IDS/IPS, 3rd-party appliances |
| **CLB** | L4/L7 | HTTP/HTTPS/TCP | Legacy only |

</details>

<details>
<summary><strong>ALB vs NLB Key Differences</strong></summary>

| Feature | ALB | NLB |
|---------|-----|-----|
| Routing | Path, host, query, headers | Port-based |
| Client IP | X-Forwarded-For header | Preserves source IP |
| Static IP | ❌ | ✅ (Elastic IP per AZ) |
| Latency | ~400ms | ~100ms |
| Targets | EC2, Lambda, IP, containers | EC2, IP, ALB |

</details>

<details>
<summary><strong>Auto Scaling Group (ASG) Scaling Policies</strong></summary>

| Policy | Description |
|--------|-------------|
| **Manual** | Set desired capacity directly |
| **Scheduled** | Scale at known times (e.g., 9 AM Mon) |
| **Simple** | Scale when CloudWatch alarm breaches |
| **Step** | Scale in steps based on alarm severity |
| **Target Tracking** | Maintain a metric target (e.g., CPU = 50%) ✅ recommended |
| **Predictive** | ML-based forecast scaling |

</details>

<details>
<summary><strong>High Availability Patterns</strong></summary>

- Deploy across **≥ 2 AZs** always
- **ELB + ASG** for elasticity and fault tolerance
- **Multi-AZ RDS** for DB failover
- **S3** is inherently multi-AZ
- **Route 53 health checks** for DNS-level failover

</details>

---

## 6️⃣ RDS, Aurora & Databases

<details>
<summary><strong>RDS Key Features</strong></summary>

- Managed relational DB: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora
- AWS manages: OS patching, backups, monitoring, replication

| Feature | Description |
|---------|-------------|
| **Multi-AZ** | Synchronous standby replica; auto failover ~1–2 min; HA only (not read scaling) |
| **Read Replicas** | Async replication; up to 15; cross-region possible; can be promoted |
| **Automated Backups** | Daily snapshot + transaction logs; retention 1–35 days; PITR enabled |
| **Manual Snapshots** | Persist even after DB deletion |
| **Encryption** | At-rest (KMS); in-transit (SSL); to encrypt existing DB → snapshot → copy encrypted → restore |

</details>

<details>
<summary><strong>RDS Proxy</strong></summary>

- Pools & shares DB connections between app and RDS
- Reduces failover time by up to **66%**
- Ideal for **Lambda** (avoids connection exhaustion)
- Serverless, auto-scaling, no code changes needed

</details>

<details>
<summary><strong>Aurora</strong></summary>

- AWS-proprietary; MySQL & PostgreSQL compatible
- **5x faster** than MySQL, **3x faster** than PostgreSQL
- Storage auto-scales: 10 GB → 128 TB (in 10 GB increments)
- **6 copies** of data across **3 AZs** (4 needed for writes, 3 for reads)
- Up to **15 Read Replicas** (faster replication than RDS)
- **Writer Endpoint** – single endpoint for writes
- **Reader Endpoint** – load-balanced across read replicas
- **Aurora Global Database** – 1 primary + up to 5 secondary regions; < 1s replication lag; failover < 1 min
- **Aurora Serverless v2** – auto-scales; pay-per-second; unpredictable/infrequent workloads

</details>

<details>
<summary><strong>ElastiCache: Redis vs Memcached</strong></summary>

| Feature | Redis | Memcached |
|---------|-------|-----------|
| Persistence | ✅ | ❌ |
| Replication | ✅ | ❌ |
| Multi-AZ | ✅ | ❌ |
| Pub/Sub | ✅ | ❌ |
| Sorted Sets | ✅ | ❌ |
| Multi-threaded | ❌ | ✅ |
| Backup/Restore | ✅ | ❌ |

</details>

<details>
<summary><strong>DynamoDB</strong></summary>

- Fully managed **NoSQL** key-value + document DB; serverless
- Single-digit ms latency; auto-scaling
- **Primary Key:** Partition Key (simple) or Partition + Sort Key (composite)
- **LSI** – same partition key, different sort key; defined **at creation** only
- **GSI** – different partition + sort key; can be added **anytime**
- **DAX** – in-memory cache; microsecond latency; write-through
- **DynamoDB Streams** – capture item-level changes → trigger Lambda
- **Global Tables** – multi-region active-active replication
- **TTL** – auto-delete expired items (no extra cost)
- **On-demand** vs **Provisioned** capacity modes
- Max item size: **400 KB**

</details>

<details>
<summary><strong>Other Database Services</strong></summary>

| Service | Use Case |
|---------|---------|
| **Redshift** | Data warehouse, OLAP, columnar, SQL on PBs of data |
| **Athena** | Serverless SQL queries on S3 (pay per query) |
| **OpenSearch** | Search & log analytics (successor to Elasticsearch) |
| **Neptune** | Graph database (social networks, fraud detection) |
| **DocumentDB** | MongoDB-compatible managed document DB |
| **Keyspaces** | Managed Apache Cassandra |
| **Timestream** | Time-series database (IoT, metrics) |
| **QLDB** | Immutable, cryptographically verifiable ledger DB |

</details>

---

## 7️⃣ Route 53 & CloudFront

<details>
<summary><strong>Route 53 Record Types</strong></summary>

| Record | Purpose |
|--------|---------|
| **A** | Domain → IPv4 |
| **AAAA** | Domain → IPv6 |
| **CNAME** | Hostname → Hostname (cannot be used at zone apex) |
| **Alias** | Hostname → AWS resource (works at zone apex; free) |
| **NS** | Name servers for hosted zone |
| **MX** | Mail servers |
| **TXT** | Verification, SPF |

> ✅ **Use Alias** for ELB, CloudFront, S3, API Gateway. Alias supports apex domain (e.g., `example.com`); CNAME doesn't.

</details>

<details>
<summary><strong>Route 53 Routing Policies</strong></summary>

| Policy | Use Case |
|--------|---------|
| **Simple** | Single resource; random if multiple values |
| **Weighted** | Split traffic – A/B testing, gradual rollout |
| **Latency** | Route to lowest latency region |
| **Failover** | Active-passive; requires health check |
| **Geolocation** | Route by user's country/continent |
| **Geoproximity** | Route by geographic distance; adjust with bias |
| **Multi-Value** | Return multiple healthy records (not an LB) |
| **IP-based** | Route based on client IP CIDR |

</details>

<details>
<summary><strong>CloudFront</strong></summary>

- AWS CDN; caches content at **400+ Edge Locations** worldwide
- Supports: S3, ALB, EC2, any HTTP backend

| Feature | Detail |
|---------|--------|
| **OAC** (Origin Access Control) | Restrict S3 access to CloudFront only (replaces OAI) |
| **Cache Behavior** | Rules for TTL, path patterns, headers, cookies |
| **Invalidations** | Force clear cache (`/*` for all); costs extra |
| **Geo Restriction** | Whitelist or blacklist countries |
| **Signed URLs** | Restrict access to one file (time-limited) |
| **Signed Cookies** | Restrict access to multiple files |
| **Field-Level Encryption** | Encrypt sensitive data at edge (e.g., credit cards) |

</details>

<details>
<summary><strong>CloudFront vs Global Accelerator</strong></summary>

| Feature | CloudFront | Global Accelerator |
|---------|-----------|-------------------|
| Caching | ✅ Yes | ❌ No |
| Protocol | HTTP/HTTPS | TCP/UDP |
| Static IP | ❌ | ✅ 2 Anycast IPs |
| Use Case | Web content delivery | Gaming, VoIP, IoT |
| Routing | Edge cache | AWS global backbone |

</details>

---

## 8️⃣ Serverless: Lambda, API Gateway, SQS, SNS

<details>
<summary><strong>Lambda</strong></summary>

- Serverless compute; no server management
- Supports: Node.js, Python, Java, Go, Ruby, .NET, custom runtime
- **Max timeout:** 15 minutes
- **Memory:** 128 MB – 10,240 MB (CPU scales with memory)
- **Ephemeral storage:** 512 MB – 10 GB (`/tmp`)
- **Default concurrency:** 1,000 per region (can increase)
- **Cold Start** – first invocation latency; use **Provisioned Concurrency** to eliminate

**Common Triggers:** API Gateway, ALB, SQS, SNS, S3, DynamoDB Streams, Kinesis, EventBridge, Cognito

</details>

<details>
<summary><strong>API Gateway</strong></summary>

- Managed API service (REST, HTTP, WebSocket)
- **Throttling:** 10,000 RPS default; 5,000 burst limit
- **Stages** – dev, prod, v1; deployments target stages
- **Stage Variables** – like env vars per stage
- **Canary Deployments** – % traffic to new version
- **Caching** – TTL 0–3600s
- **Usage Plans + API Keys** – rate/quota per client
- **Auth options:** Cognito, Lambda Authorizer, IAM, API Keys

</details>

<details>
<summary><strong>SQS vs SNS</strong></summary>

| Feature | SQS | SNS |
|---------|-----|-----|
| Model | Queue (pull) | Pub/Sub (push) |
| Consumers | One at a time | All subscribers |
| Persistence | Up to 14 days | No persistence |
| Ordering | FIFO (with FIFO queue) | FIFO topic available |
| Use Case | Decouple, buffer | Broadcast, fan-out |

**SQS Key Details:**
- **Standard** – best-effort order, at-least-once, unlimited TPS
- **FIFO** – exact order, exactly-once, 300 TPS (3,000 with batching)
- **Visibility Timeout** – default 30s, max 12h
- **DLQ** – failed messages after max receive count
- **Long Polling** – wait up to 20s (reduces empty responses & cost)
- **Delay Queue** – delay delivery up to 15 min
- Max message size: **256 KB**

**SNS Key Details:**
- **Subscribers:** SQS, Lambda, HTTP/S, Email, SMS, Mobile Push, Kinesis Firehose
- **Message Filtering** – JSON filter policy per subscriber
- **SNS + SQS Fan-Out** – common pattern for broadcasting to multiple queues

</details>

<details>
<summary><strong>Kinesis</strong></summary>

| Service | Description |
|---------|-------------|
| **Kinesis Data Streams** | Real-time streaming; shards; 1 MB/s in, 2 MB/s out per shard; replay; retention 1–365 days |
| **Kinesis Data Firehose** | Near real-time delivery to S3, Redshift, OpenSearch, Splunk (no replay) |
| **Kinesis Data Analytics** | Real-time SQL / Apache Flink on streams |
| **Kinesis Video Streams** | Streaming video for ML processing |

- **Shard** – 1 MB/s in, 2 MB/s out; add shards to scale
- **Partition Key** – determines which shard receives the record

</details>

<details>
<summary><strong>EventBridge & Step Functions</strong></summary>

**EventBridge:**
- Serverless event bus; routes events across AWS services, SaaS, custom apps
- **Rules** – match event patterns → trigger targets (Lambda, SQS, SNS, etc.)
- **Scheduled Rules** – cron or rate expressions

**Step Functions:**
- Serverless orchestration via visual state machines
- States: Task, Choice, Wait, Parallel, Map, Pass, Succeed, Fail
- Handles retries, branching, error handling
- Max duration: **1 year**

</details>

---

## 9️⃣ Additional Key Services

<details>
<summary><strong>CloudWatch</strong></summary>

- **Metrics** – CPU, Network, Disk, custom (memory requires Unified Agent)
- **Alarms** – trigger SNS, ASG scaling, EC2 actions on metric thresholds
- **Logs** – collect/store logs; log groups → log streams
- **Log Insights** – interactive query language
- **Dashboards** – cross-region metric visualization
- **Unified Agent** – custom metrics + logs from EC2 (memory, disk)

> ⚠️ Default EC2 metrics do **NOT** include memory or disk — install Unified Agent for those.

</details>

<details>
<summary><strong>CloudTrail</strong></summary>

- Records all **API calls** to AWS (who, what, when, from where)
- Enabled by default; 90-day event history in console
- Create **Trail** → long-term storage in S3
- **Management Events** – control plane (default enabled)
- **Data Events** – data plane (S3 object access, Lambda invocations) – must opt in
- **Insights** – detect unusual API activity patterns

</details>

<details>
<summary><strong>CloudFormation</strong></summary>

- **Infrastructure as Code** – define resources in YAML/JSON templates
- **Stack** – deployed resource set from a template
- **StackSets** – deploy across multiple accounts/regions
- **Change Sets** – preview changes before applying
- **Drift Detection** – detect manual changes to stack resources
- `Resources` section is the only **mandatory** section

</details>

<details>
<summary><strong>Containers: ECS, EKS, ECR</strong></summary>

| Service | Description |
|---------|-------------|
| **ECS (EC2 launch type)** | You manage EC2 instances in the cluster |
| **ECS (Fargate launch type)** | Serverless containers; AWS manages infra |
| **EKS** | Managed Kubernetes (EC2 or Fargate nodes) |
| **ECR** | Private container image registry |

- **Task Definition** – blueprint: image, CPU, memory, ports, IAM role
- **Service** – maintains desired running tasks; integrates with ALB
- **ECS Task Role** – per-task IAM permissions (separate from instance profile)

</details>

<details>
<summary><strong>AWS Snow Family</strong></summary>

| Device | Storage | Use Case |
|--------|---------|---------|
| **Snowcone** | 8–14 TB | Edge computing, small migrations |
| **Snowball Edge** | 80 TB | Large data migrations, edge compute |
| **Snowmobile** | 100 PB | Exabyte-scale migrations (physical truck) |

</details>

<details>
<summary><strong>Storage Gateway & DataSync</strong></summary>

**Storage Gateway (Hybrid Cloud)**

| Type | Description |
|------|-------------|
| **S3 File Gateway** | NFS/SMB → files stored in S3 |
| **FSx File Gateway** | SMB → FSx for Windows |
| **Volume Gateway** | iSCSI → backed by S3 + EBS snapshots |
| **Tape Gateway** | Virtual tape library → S3/Glacier |

**DataSync** – Online data transfer between on-premises ↔ AWS (faster than Storage Gateway)

</details>

---

## 🔴 Disaster Recovery Strategies

<details>
<summary><strong>DR Strategies Comparison</strong></summary>

| Strategy | RTO | RPO | Cost |
|----------|-----|-----|------|
| **Multi-Site Active-Active** | Near zero | Near zero | ⚠️ Highest |
| **Warm Standby** | Minutes | Minutes | 🔶 High |
| **Pilot Light** | Hours | Minutes | 🔷 Medium |
| **Backup & Restore** | Hours | Hours | ✅ Lowest |

- **RPO** (Recovery Point Objective) – max acceptable **data loss** (time)
- **RTO** (Recovery Time Objective) – max acceptable **downtime**

</details>

<details>
<summary><strong>DR Architecture Tips</strong></summary>

- **Route 53 Failover Routing** – automatic DNS failover
- **Aurora Global Database** – cross-region DR (< 1 min failover)
- **S3 CRR** – cross-region data replication
- **Elastic Disaster Recovery (DRS)** – replicate on-premises servers to AWS
- **RDS Read Replica** – promote to standalone for regional failover

</details>

---

## 1️⃣1️⃣ Cost Optimization

<details>
<summary><strong>Key Strategies</strong></summary>

- **Right-sizing** – use Cost Explorer/Compute Optimizer to find underused resources
- **Reserved Instances / Savings Plans** – commit 1 or 3 years (up to 72% savings)
- **Spot Instances** – fault-tolerant batch/flexible workloads (up to 90% off)
- **S3 Lifecycle Policies** – auto-move data to cheaper storage classes
- **Auto Scaling** – scale down during off-peak hours
- **Shut down idle resources** – schedule dev/test environments

</details>

<details>
<summary><strong>Cost Tools</strong></summary>

| Tool | Purpose |
|------|---------|
| **Cost Explorer** | Visualize, analyze, and forecast spending |
| **AWS Budgets** | Set cost/usage alerts and actions |
| **Cost & Usage Report** | Most granular billing data → export to S3 |
| **Trusted Advisor** | Best practice checks including cost savings |
| **Compute Optimizer** | Right-sizing recommendations (EC2, Lambda, EBS) |

</details>

---

## 1️⃣2️⃣ Well-Architected Framework

| Pillar | Key Principles |
|--------|---------------|
| ⚙️ **Operational Excellence** | IaC, small frequent changes, observability, runbooks |
| 🔒 **Security** | Least privilege, encryption, traceability, defense in depth |
| 🔁 **Reliability** | Multi-AZ, auto-recovery, backups, test failure scenarios |
| ⚡ **Performance Efficiency** | Right instance types, serverless, CDN, caching |
| 💰 **Cost Optimization** | Pay for what you use, right-size, Reserved/Spot |
| 🌍 **Sustainability** | Reduce carbon footprint, efficient code, managed services |

---

## ⚡ Quick Exam Tips

### ✅ Common Architecture Patterns

| Requirement | Solution |
|-------------|----------|
| High Availability | Multi-AZ + ELB + ASG |
| Disaster Recovery | Multi-Region + Route 53 Failover |
| Decouple components | SQS (async) or SNS (pub/sub) |
| Serverless API | API Gateway + Lambda + DynamoDB |
| Static website | S3 + CloudFront |
| Large data migration | Snow family (offline) or DataSync (online) |
| Hybrid storage | Storage Gateway |
| Log analysis | CloudWatch Logs + Athena on S3 |
| Cost savings (batch) | Spot Instances |
| Cost savings (steady) | Reserved Instances |

### ⚠️ Tricky Distinctions to Remember

```
SG vs NACL          → SG = stateful, instance-level | NACL = stateless, subnet-level
Multi-AZ vs Replica → Multi-AZ = HA failover (sync) | Read Replica = read scaling (async)
ALB vs NLB          → ALB = L7 HTTP routing         | NLB = L4 TCP/UDP, static IP, ultra-low latency
CloudFront vs GA    → CloudFront = caching           | Global Accelerator = routing + static IP
SQS vs SNS          → SQS = queue (pull)             | SNS = pub/sub (push); use both for fan-out
EBS vs EFS          → EBS = one AZ, block            | EFS = multi-AZ, file system (Linux only)
Alias vs CNAME      → Alias supports apex domain     | CNAME doesn't; Alias is free
NAT GW vs NAT Inst  → NAT GW = managed, scalable     | NAT Instance = self-managed, cheaper
```

### 📏 Key Limits to Memorize

| Resource | Limit |
|----------|-------|
| S3 max object size | 5 TB |
| S3 max single PUT | 5 GB (use multipart for > 5 GB) |
| Lambda max timeout | 15 minutes |
| SQS max message size | 256 KB |
| SQS max retention | 14 days |
| DynamoDB max item size | 400 KB |
| EC2 Spot interruption notice | 2 minutes |
| S3 buckets per account (default) | 100 (soft limit) |

---

<div align="center">

**Good luck on your exam! 🍀**

*Focus on understanding patterns, not memorizing every detail.*

![Made with ❤️](https://img.shields.io/badge/Made%20with-%E2%9D%A4%EF%B8%8F-red?style=flat-square)
![AWS SAA](https://img.shields.io/badge/AWS-Solutions%20Architect%20Associate-FF9900?style=flat-square&logo=amazon-aws)

</div>
