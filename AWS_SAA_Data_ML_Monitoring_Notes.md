# 📊 AWS SAA-C03 — Data, Analytics, ML & Monitoring

> **Deep-dive notes on Data & Analytics, Machine Learning, and AWS Monitoring & Audit services.**

![AWS](https://img.shields.io/badge/AWS-SAA--C03-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Topic](https://img.shields.io/badge/Topic-Data%20%7C%20ML%20%7C%20Monitoring-blue?style=for-the-badge)
![Format](https://img.shields.io/badge/Format-Concise%20Bullet%20Points-success?style=for-the-badge)

---

## 📋 Table of Contents

| # | Topic |
|---|-------|
| 1 | [Data & Analytics](#1️⃣-data--analytics) |
| 2 | [Machine Learning](#2️⃣-machine-learning) |
| 3 | [CloudWatch](#3️⃣-cloudwatch) |
| 4 | [CloudTrail](#4️⃣-cloudtrail) |
| 5 | [AWS Config](#5️⃣-aws-config) |
| 6 | [Comparison Cheat Sheet](#-comparison-cheat-sheet) |
| 7 | [Quick Exam Tips](#-quick-exam-tips) |

---

## 1️⃣ Data & Analytics

### 🔴 Amazon Athena

<details>
<summary><strong>Overview & Key Features</strong></summary>

- **Serverless** interactive query service — run SQL directly on data in **S3**
- No infrastructure to manage; no ETL required
- Pay **per query** — $5 per TB of data scanned
- Supports formats: **CSV, JSON, ORC, Avro, Parquet** (columnar = cheaper + faster)
- Built on **Presto** engine
- Use **Glue Data Catalog** as the metadata/schema store

</details>

<details>
<summary><strong>Performance & Cost Optimization</strong></summary>

- Use **columnar formats** (Parquet, ORC) → less data scanned → lower cost
- **Partition data** by date, region, etc. → reduces scan range
- **Compress data** (GZIP, Snappy, ZSTD) → smaller files = less cost
- **Bucket data** – group files by a column to optimize query joins
- Use **larger files** (> 128 MB) to avoid small-file overhead

</details>

<details>
<summary><strong>Federated Query</strong></summary>

- Query data across **S3, RDS, DynamoDB, on-premises, and other data sources**
- Uses **Lambda-based Data Source Connectors**
- Results stored back in S3
- Removes need for ETL pipelines for ad-hoc analysis

</details>

<details>
<summary><strong>Common Exam Scenarios</strong></summary>

- ✅ Analyze **VPC Flow Logs / CloudTrail / ELB logs** stored in S3 → use Athena
- ✅ **One-time or ad-hoc SQL** on S3 data → Athena (no Redshift cluster needed)
- ✅ Cost-effective log analysis → Athena + Parquet format

</details>

---

### 🔴 Amazon Redshift

<details>
<summary><strong>Overview</strong></summary>

- Fully managed **Data Warehouse** (OLAP — Online Analytical Processing)
- Columnar storage + massively parallel processing (**MPP**)
- Based on **PostgreSQL** (but NOT used for OLTP)
- 10x better performance than traditional data warehouses
- Scale to **petabytes** of data
- Pay as you go (per instance) or **Reserved Instances** for savings

</details>

<details>
<summary><strong>Architecture</strong></summary>

- **Leader Node** – query planning, result aggregation, client communication
- **Compute Nodes** – execute queries, store data; send results to leader node
- **Node Types:**
  - `ra3` – managed storage, separate compute/storage scaling (recommended)
  - `dc2` – compute-intensive, SSD storage

</details>

<details>
<summary><strong>Key Features</strong></summary>

- **Redshift Spectrum** – query data **directly in S3** without loading into Redshift
- **Redshift Serverless** – no cluster management; auto-scales; pay per use
- **Snapshots** – automated or manual; stored in S3; incremental; can copy cross-region
- **Multi-AZ** – available for `ra3` cluster type
- **Enhanced VPC Routing** – force COPY/UNLOAD traffic through VPC
- **AQUA** (Advanced Query Accelerator) – distributed hardware cache; faster queries

</details>

<details>
<summary><strong>Loading Data into Redshift</strong></summary>

- **COPY command** – most efficient bulk load from S3, DynamoDB, EMR, SSH
- **Kinesis Data Firehose** – near real-time streaming into Redshift (via S3)
- **AWS Glue** – ETL jobs loading transformed data
- **JDBC/ODBC** – row-by-row insert (not recommended for large volumes)

</details>

<details>
<summary><strong>Redshift vs Athena</strong></summary>

| Feature | Redshift | Athena |
|---------|---------|--------|
| Type | Data Warehouse (cluster) | Serverless SQL on S3 |
| Best for | Complex joins, dashboards, regular queries | Ad-hoc, one-time queries |
| Performance | Faster for repeated complex queries | Slower for complex joins |
| Cost model | Per node-hour | Per TB scanned |
| Setup | Cluster required | Zero setup |

</details>

---

### 🔴 Amazon EMR (Elastic MapReduce)

<details>
<summary><strong>Overview</strong></summary>

- Managed **big data platform** using open-source frameworks
- Supported frameworks: **Hadoop, Spark, HBase, Presto, Flink, Hive**
- Provisions EC2 instances (you choose instance type and count)
- Auto-scales; integrates with S3 (via EMRFS), DynamoDB, Kinesis

</details>

<details>
<summary><strong>Cluster Node Types</strong></summary>

| Node Type | Role |
|-----------|------|
| **Master Node** | Manages cluster; tracks task status; health monitoring |
| **Core Node** | Runs tasks + stores data in HDFS; must keep running |
| **Task Node** | Runs tasks only; no HDFS storage; optional; great for Spot |

</details>

<details>
<summary><strong>Key Features</strong></summary>

- **Spot Instances** for Task Nodes → significant cost reduction
- **EMR Studio** – web-based IDE for notebooks (Jupyter)
- **EMR Serverless** – run frameworks without managing clusters
- **Instance Fleet** – mix of instance types/pricing (On-Demand + Spot)
- **Bootstrapping** – run custom scripts on cluster nodes at launch

</details>

<details>
<summary><strong>Common Use Cases</strong></summary>

- Large-scale ETL (extract, transform, load)
- Machine learning with Spark MLlib
- Clickstream analysis, log processing
- Genomics and financial modeling

</details>

---

### 🔴 AWS Glue

<details>
<summary><strong>Overview</strong></summary>

- Fully managed **serverless ETL** (Extract, Transform, Load) service
- Discovers, catalogs, and transforms data for analytics
- Generates ETL code automatically (Python or Scala)
- Integrates with: S3, RDS, Redshift, DynamoDB, JDBC sources

</details>

<details>
<summary><strong>Key Components</strong></summary>

| Component | Description |
|-----------|-------------|
| **Glue Data Catalog** | Central metadata repository (tables, schemas, partitions); used by Athena, Redshift Spectrum, EMR |
| **Glue Crawlers** | Auto-discover data schemas and populate the Data Catalog |
| **Glue ETL Jobs** | Transform data using auto-generated or custom PySpark/Python scripts |
| **Glue Studio** | Visual drag-and-drop ETL job builder |
| **Glue DataBrew** | Visual data preparation (no-code/low-code) for data cleaning |
| **Glue Elastic Views** | Combine/replicate data across multiple stores using SQL |

</details>

<details>
<summary><strong>Glue Job Bookmarks</strong></summary>

- Tracks previously processed data → prevents **re-processing old data**
- Important for incremental ETL workloads

</details>

<details>
<summary><strong>Convert to Parquet Pattern</strong></summary>

```
S3 (CSV/JSON) → Glue Crawler → Glue ETL Job → S3 (Parquet) → Athena Query
```

- Common architecture to reduce Athena query costs

</details>

---

### 🔴 Amazon Kinesis (Deep Dive)

<details>
<summary><strong>Kinesis Data Streams</strong></summary>

- Real-time data streaming; **millisecond latency**
- Data divided into **Shards** (capacity unit)
  - 1 shard = **1 MB/s in**, **2 MB/s out**
  - Scale by adding/removing shards (**resharding**)
- **Retention:** 1 day (default) → up to **365 days**
- **Replay** data (consumers can re-read records)
- **Partition Key** → determines target shard (use high-cardinality keys)
- Producers: SDK, Kinesis Producer Library (KPL), Kinesis Agent
- Consumers: Lambda, Kinesis Data Analytics, Kinesis Firehose, custom apps

| Mode | Description |
|------|-------------|
| **Provisioned** | Manually set shard count; pay per shard-hour |
| **On-Demand** | Auto-scales; pay per GB in/out |

</details>

<details>
<summary><strong>Kinesis Data Firehose</strong></summary>

- **Near real-time** delivery (buffer-based, 60s minimum latency)
- **No replay** – fire and forget
- **Fully managed** – no shards to manage
- Destinations: **S3, Redshift, OpenSearch, Splunk, HTTP endpoints, 3rd-party**
- Optional: Lambda transformation before delivery
- Auto-scales; pay per data volume

```
Producers → Kinesis Data Streams → Kinesis Firehose → S3 / Redshift / OpenSearch
```

</details>

<details>
<summary><strong>Kinesis Data Analytics</strong></summary>

- Real-time analytics using **SQL** or **Apache Flink**
- Sources: Kinesis Data Streams, Kinesis Firehose
- Outputs: Kinesis Streams, Kinesis Firehose, Lambda
- Use cases: real-time dashboards, anomaly detection, time-series analytics

</details>

<details>
<summary><strong>Kinesis vs SQS</strong></summary>

| Feature | Kinesis Data Streams | SQS |
|---------|---------------------|-----|
| Ordering | Per shard | FIFO queue only |
| Replay | ✅ Yes | ❌ No |
| Multiple consumers | ✅ Yes | ❌ No (one consumer per message) |
| Retention | Up to 365 days | Up to 14 days |
| Scaling | Add shards | Automatic |
| Use case | Real-time streaming | Decoupling / task queues |

</details>

---

### 🔴 Amazon OpenSearch Service

<details>
<summary><strong>Overview</strong></summary>

- Successor to **Amazon Elasticsearch Service**
- Managed OpenSearch/Elasticsearch clusters
- Used for: **full-text search, log analytics, application monitoring, clickstream analytics**
- Supports: **OpenSearch Dashboards** (formerly Kibana) for visualization

</details>

<details>
<summary><strong>Key Features</strong></summary>

- Can query **any field** (unlike DynamoDB which requires primary key)
- Multi-AZ deployment with replication
- Encryption at rest (KMS) and in transit (SSL)
- **Index** = collection of documents (like a table)
- **Shard** = partition of an index (horizontal scaling)

</details>

<details>
<summary><strong>OpenSearch Ingestion Patterns</strong></summary>

```
DynamoDB Streams → Lambda → OpenSearch   (search on DynamoDB data)
CloudWatch Logs → Subscription Filter → Lambda/Firehose → OpenSearch
Kinesis Firehose → OpenSearch            (near real-time log delivery)
```

</details>

---

### 🔴 Amazon QuickSight

<details>
<summary><strong>Overview</strong></summary>

- Serverless **Business Intelligence (BI)** and visualization service
- Create interactive dashboards, graphs, and reports
- Connects to: Athena, Redshift, RDS, S3, Aurora, OpenSearch, Salesforce, on-premises DBs
- **SPICE** – in-memory engine for fast query performance (Super-fast, Parallel, In-memory Calculation Engine)

</details>

<details>
<summary><strong>Use Cases & Features</strong></summary>

- **Ad-hoc analysis** of business data
- **ML Insights** – anomaly detection, forecasting, auto-narratives
- **Row-Level Security (RLS)** – restrict data per user
- **Column-Level Security** – restrict specific columns per user/group
- **Embedded dashboards** – embed in apps using QuickSight APIs

</details>

<details>
<summary><strong>QuickSight Pricing Models</strong></summary>

| Edition | Features |
|---------|---------|
| **Standard** | Per-user pricing; basic dashboards |
| **Enterprise** | Group access, RLS, encryption, AD integration |

</details>

---

### 🔴 AWS Lake Formation

<details>
<summary><strong>Overview</strong></summary>

- Service to **build, secure, and manage a Data Lake** on S3 in days
- Automates: data ingestion, cataloging, transformation, deduplication, security
- Sits on top of **Glue** (uses Glue Data Catalog under the hood)
- Centralized **fine-grained access control** (column, row, cell level)

</details>

<details>
<summary><strong>Key Features</strong></summary>

- **Blueprints** – pre-built templates to ingest data from common sources
- **FindMatches ML** – deduplication and record matching
- **Cross-account access** – share data catalog across accounts
- **Tag-based access control (LF-Tags)** – simplified permission management
- Consumers: Athena, Redshift, EMR, Glue

</details>

<details>
<summary><strong>Data Lake Architecture</strong></summary>

```
Sources (S3, RDS, on-prem)
        ↓
  Lake Formation (ingest, clean, catalog)
        ↓
  S3 Data Lake (raw + processed)
        ↓
  Athena / Redshift Spectrum / EMR (query)
        ↓
  QuickSight (visualize)
```

</details>

---

### 🔴 AWS Data Pipeline

<details>
<summary><strong>Overview</strong></summary>

- Managed **ETL workflow orchestration** service
- Move and transform data between **AWS storage and compute services**
- Supports: S3, RDS, DynamoDB, Redshift, EMR
- Handles: scheduling, retries, failure notifications
- Runs on **EC2 or EMR** (not serverless)

</details>

> ⚠️ **Exam tip:** For modern pipelines prefer **Glue** (serverless). Data Pipeline is older and used when specific EC2 control is needed.

---

### 🔴 Amazon MSK (Managed Streaming for Apache Kafka)

<details>
<summary><strong>Overview</strong></summary>

- Fully managed **Apache Kafka** service (alternative to Kinesis)
- Kafka = distributed event streaming platform
- AWS manages: broker provisioning, patching, replication, storage
- Deploy in your **VPC** across multiple AZs

</details>

<details>
<summary><strong>Key Concepts</strong></summary>

- **Brokers** – Kafka servers that store and serve data
- **Topics** – named streams of records (like Kinesis streams)
- **Partitions** – subdivisions of a topic (like Kinesis shards)
- **Producers** → write to topics; **Consumers** → read from topics
- **Consumer Groups** – multiple consumers share topic partitions
- Retention: configurable (up to unlimited with tiered storage)
- Message size: up to **1 MB** (default); configurable up to 16 MB+

</details>

<details>
<summary><strong>MSK vs Kinesis Data Streams</strong></summary>

| Feature | MSK (Kafka) | Kinesis Data Streams |
|---------|------------|---------------------|
| Message size | Up to 1 MB+ (configurable) | 1 MB max |
| Retention | Unlimited (tiered storage) | Up to 365 days |
| Scaling | Add brokers/partitions | Add shards |
| Management | More control (Kafka config) | Fully managed |
| Protocol | Kafka API | AWS proprietary |
| Use case | Existing Kafka workloads | AWS-native streaming |

</details>

<details>
<summary><strong>MSK Serverless</strong></summary>

- Run Kafka without managing capacity
- Auto-scales storage and compute
- Pay per usage

</details>

---

## 2️⃣ Machine Learning

### 🤖 Amazon Rekognition

<details>
<summary><strong>Overview & Use Cases</strong></summary>

- **Computer vision** service using deep learning
- Analyzes **images and videos**
- No ML expertise required

| Feature | Description |
|---------|-------------|
| **Object/Scene Detection** | Identify objects, scenes, activities |
| **Facial Analysis** | Detect faces, emotions, age range, gender |
| **Facial Comparison** | Compare faces across images |
| **Text in Image (OCR)** | Extract text from images |
| **Content Moderation** | Detect inappropriate/unsafe content |
| **Celebrity Recognition** | Identify famous people |
| **PPE Detection** | Detect hard hats, masks, vests |
| **Custom Labels** | Train custom models with your images |
| **Video Analysis** | Detect activities, track people in video |

</details>

---

### 🤖 Amazon Transcribe

<details>
<summary><strong>Overview</strong></summary>

- Automatic **Speech-to-Text** (ASR – Automatic Speech Recognition)
- Converts audio/video files or real-time streams to text
- Supports 100+ languages
- **Custom Vocabulary** – improve accuracy for domain-specific terms
- **Speaker Diarization** – identify who said what (multiple speakers)
- **Redaction** – auto-remove PII from transcripts
- Use cases: call center analysis, subtitles, meeting notes, medical transcription

</details>

---

### 🤖 Amazon Polly

<details>
<summary><strong>Overview</strong></summary>

- **Text-to-Speech** (TTS) service — turn text into lifelike speech
- 60+ voices, 30+ languages
- **Neural TTS (NTTS)** – more natural, human-like voices
- **SSML** (Speech Synthesis Markup Language) – control pronunciation, speed, pitch
- **Lexicons** – customize pronunciation of specific words (brand names, acronyms)
- Output formats: MP3, OGG, PCM, Speech Marks (for lip-sync)
- Use cases: voice-enabled apps, e-learning, accessibility, IVR systems

</details>

---

### 🤖 Amazon Translate

<details>
<summary><strong>Overview</strong></summary>

- **Neural machine translation** service
- Real-time and batch translation
- 75+ languages
- **Custom Terminology** – preserve brand names / specific terms during translation
- Use cases: multi-language websites, real-time chat translation, content localization

</details>

---

### 🤖 Amazon Comprehend

<details>
<summary><strong>Overview</strong></summary>

- **Natural Language Processing (NLP)** service
- Extracts insights and relationships from text
- Fully managed, no ML expertise needed

| Feature | Description |
|---------|-------------|
| **Entity Recognition** | Identify people, places, dates, organizations |
| **Sentiment Analysis** | Positive, negative, neutral, mixed |
| **Key Phrase Extraction** | Important phrases from text |
| **Language Detection** | Identify language of the text |
| **Syntax Analysis** | Parts of speech tagging |
| **PII Detection** | Find and redact personal information |
| **Custom Classification** | Train custom text classifiers |
| **Custom Entity Recognition** | Train custom entity models |

</details>

<details>
<summary><strong>Comprehend Medical</strong></summary>

- Specialized version for **healthcare/medical text**
- Extracts: medical conditions, medications, anatomy, dosages, test results
- Detects **Protected Health Information (PHI)**
- Use cases: clinical note analysis, medical record processing

</details>

---

### 🤖 Amazon SageMaker

<details>
<summary><strong>Overview</strong></summary>

- Fully managed platform to **build, train, and deploy ML models** at scale
- For data scientists and ML engineers
- End-to-end ML workflow in one service

</details>

<details>
<summary><strong>Key Components</strong></summary>

| Component | Description |
|-----------|-------------|
| **SageMaker Studio** | Web-based IDE for ML development |
| **SageMaker Notebooks** | Managed Jupyter notebooks |
| **SageMaker Autopilot** | AutoML — auto-builds best model for your dataset |
| **SageMaker Training** | Scalable, distributed model training on EC2 |
| **SageMaker Tuning** | Hyperparameter optimization (HPO) |
| **SageMaker Inference** | Deploy models as real-time endpoints or batch |
| **SageMaker Pipelines** | MLOps — CI/CD pipelines for ML workflows |
| **SageMaker Feature Store** | Centralized store for ML features |
| **SageMaker Clarify** | Model bias detection and explainability |
| **SageMaker Canvas** | No-code ML model building |
| **SageMaker Ground Truth** | Data labeling (human + automated) |

</details>

<details>
<summary><strong>SageMaker Training</strong></summary>

- Brings training data from S3
- Spins up EC2 (or GPU) instances, trains, saves model artifacts to S3
- Supports built-in algorithms, custom algorithms (Docker containers), frameworks (TensorFlow, PyTorch, XGBoost)
- **Managed Spot Training** – use Spot instances to save up to 90% on training costs (must handle checkpointing)

</details>

---

### 🤖 Amazon Forecast

<details>
<summary><strong>Overview</strong></summary>

- Fully managed **time-series forecasting** service using ML
- Based on same technology as Amazon.com's forecasting
- No ML experience needed
- Use cases: product demand, inventory, financial, staffing, energy forecasting
- Combines your data with additional variables (weather, holidays, etc.)
- Up to **50% better accuracy** than traditional methods

</details>

---

### 🤖 Amazon Kendra

<details>
<summary><strong>Overview</strong></summary>

- Fully managed **intelligent enterprise search** service powered by ML
- **Natural language search** — understands questions, not just keywords
- Indexes content from: S3, SharePoint, Salesforce, ServiceNow, RDS, OneDrive, websites
- **Incremental learning** – improves from user feedback
- Returns **direct answers** from documents (not just links)
- Use cases: internal knowledge base search, customer support, HR portals

</details>

---

### 🤖 Amazon Personalize

<details>
<summary><strong>Overview</strong></summary>

- Fully managed **real-time personalization and recommendation** service
- Same technology used by Amazon.com
- No ML expertise needed
- Integrates directly with your existing websites, apps, SMS, email

| Recipe Type | Example |
|-------------|---------|
| **User Personalization** | "Recommended for you" |
| **Related Items** | "Customers also viewed" |
| **Personalized Ranking** | Reorder search results per user |
| **User Segmentation** | Group users by behavior |

</details>

---

### 🤖 Amazon Textract

<details>
<summary><strong>Overview</strong></summary>

- **Automatically extract** text and data from scanned documents
- Goes beyond OCR — understands **forms and tables** structure
- Extracts: printed text, handwriting, key-value pairs, tables, checkboxes
- Use cases: medical records, tax documents, ID cards, invoices, contracts

</details>

---

### 🤖 Amazon Lex & Amazon Connect

<details>
<summary><strong>Amazon Lex</strong></summary>

- Build **conversational chatbots** (text + voice)
- Powers **Amazon Alexa** under the hood
- Uses ASR (speech-to-text) + NLU (natural language understanding)
- **Intents** – what the user wants to do
- **Slots** – variables/parameters in the intent
- **Fulfillment** – Lambda function to execute the intent

</details>

<details>
<summary><strong>Amazon Connect</strong></summary>

- Cloud-based **contact center** service
- Integrates with Lex for AI-powered IVR (Interactive Voice Response)
- No upfront payments; pay per minute of usage
- Integrations: CRM systems, Lambda, S3

```
Customer Call → Amazon Connect → Amazon Lex (understand intent) → Lambda (action) → Agent (if needed)
```

</details>

---

### ML Services Quick Reference

| Service | What It Does | Input | Output |
|---------|-------------|-------|--------|
| **Rekognition** | Image/video analysis | Image, Video | Labels, faces, text |
| **Transcribe** | Speech → Text | Audio | Text transcript |
| **Polly** | Text → Speech | Text | Audio (MP3/OGG) |
| **Translate** | Language translation | Text | Translated text |
| **Comprehend** | NLP / text insights | Text | Entities, sentiment |
| **SageMaker** | Build/train/deploy ML | Any | Custom ML model |
| **Forecast** | Time-series forecasting | Historical data | Future predictions |
| **Kendra** | Enterprise search | Documents | Direct answers |
| **Personalize** | Recommendations | User behavior | Ranked items |
| **Textract** | Extract from documents | Scanned docs | Structured data |
| **Lex** | Chatbot / voice bot | Text, Voice | Responses, intents |

---

## 3️⃣ CloudWatch

### 📊 CloudWatch Metrics

<details>
<summary><strong>Overview</strong></summary>

- Collects **metrics** from AWS services (and custom sources)
- Metrics are grouped by **Namespace** and identified by **Dimensions**
- **Default metrics** – collected automatically, no agent needed
- **Custom metrics** – pushed via PutMetricData API (requires Unified Agent or SDK)

</details>

<details>
<summary><strong>Default vs Custom Metrics</strong></summary>

| Type | Examples | Resolution |
|------|----------|------------|
| **Default (EC2)** | CPUUtilization, NetworkIn/Out, Disk I/O | 5 minutes (basic), 1 min (detailed) |
| **Custom** | Memory usage, disk space, app metrics | 1 min (standard), 1 sec (high resolution) |

> ⚠️ **EC2 Memory and Disk are NOT default metrics** — must install **CloudWatch Unified Agent**

</details>

<details>
<summary><strong>Metric Resolution</strong></summary>

- **Standard Resolution** – 1-minute granularity
- **High Resolution** – 1-second granularity (custom metrics only); higher cost
- Metrics retained for:
  - 3 hours at 1-second resolution
  - 15 days at 1-minute resolution
  - 63 days at 5-minute resolution
  - 15 months at 1-hour resolution

</details>

---

### 📊 CloudWatch Alarms

<details>
<summary><strong>Overview</strong></summary>

- **Monitor metrics** and trigger actions when thresholds are breached
- Alarm States:
  - `OK` – metric within threshold
  - `ALARM` – metric breached threshold
  - `INSUFFICIENT_DATA` – not enough data yet

</details>

<details>
<summary><strong>Alarm Actions</strong></summary>

| Action | Description |
|--------|-------------|
| **SNS Notification** | Send email, SMS, trigger Lambda |
| **EC2 Action** | Stop, start, terminate, reboot instance |
| **ASG Action** | Scale in or scale out |
| **Systems Manager Action** | Run automation document |

</details>

<details>
<summary><strong>Composite Alarms</strong></summary>

- Combine **multiple alarms** with AND/OR conditions
- Reduces alarm noise (only alert when multiple conditions are true)
- Example: alarm only when CPU > 80% AND network is low (not just high CPU)

</details>

<details>
<summary><strong>Key Alarm Settings</strong></summary>

- **Period** – evaluation window (e.g., 60 seconds)
- **Evaluation Periods** – number of periods to evaluate
- **Datapoints to Alarm** – how many breaching datapoints trigger alarm
- **Missing Data Treatment** – treat missing data as: good, bad, ignore, breaching

</details>

---

### 📊 CloudWatch Logs

<details>
<summary><strong>Overview</strong></summary>

- Centralized **log collection, storage, and analysis**
- **Log Groups** – container for log streams (typically one per application)
- **Log Streams** – sequence of log events (typically one per instance/container)
- **Retention** – configurable: 1 day to 10 years (or never expire)

</details>

<details>
<summary><strong>Log Sources</strong></summary>

- **SDK / CloudWatch Logs Agent** – from EC2 / on-premises servers
- **CloudWatch Unified Agent** – recommended; collects logs + custom metrics
- **Beanstalk** – app logs from EC2
- **ECS** – container logs
- **Lambda** – auto-sends function logs
- **API Gateway** – request/response logs
- **CloudTrail** – API call logs
- **Route 53** – DNS query logs
- **VPC Flow Logs** – network traffic logs

</details>

<details>
<summary><strong>Log Encryption & Destinations</strong></summary>

- Logs encrypted by default with **SSE** (server-side encryption)
- Enable **KMS encryption** for customer-managed keys
- **Export to S3** – batch export (up to 12 hours delay; not real-time)
- **Subscriptions** – real-time streaming of logs:
  - → **Kinesis Data Streams** → Kinesis Firehose → S3/Redshift/OpenSearch
  - → **Lambda** – real-time processing
  - → **OpenSearch** – direct via subscription filter

</details>

<details>
<summary><strong>CloudWatch Logs Insights</strong></summary>

- **Interactive query language** for log analysis
- Query multiple log groups simultaneously
- Auto-discovers fields from JSON logs
- Can save queries and add results to **dashboards**
- Query syntax: `fields`, `filter`, `stats`, `sort`, `limit`, `parse`

```sql
-- Example: Find top 10 IPs by request count
fields @timestamp, @message
| filter @message like /ERROR/
| stats count(*) as errorCount by bin(5m)
| sort errorCount desc
| limit 10
```

</details>

<details>
<summary><strong>Subscription Filters & Cross-Account</strong></summary>

- **Subscription Filter** – filter log events and stream in real-time to a destination
- **Cross-Account Log Aggregation:**
  ```
  Account A (Log Group) → Subscription Filter → Kinesis Data Streams (central account)
  Account B (Log Group) → Subscription Filter → ↗
  ```
- **Destination** – Kinesis Data Streams, Kinesis Firehose, Lambda

</details>

---

### 📊 CloudWatch Agent & Unified Agent

<details>
<summary><strong>CloudWatch Agent</strong></summary>

- Installed on EC2 or on-premises to collect **logs and custom metrics**
- Old agent: **CloudWatch Logs Agent** – logs only
- **Unified Agent** (recommended) – logs + system metrics

</details>

<details>
<summary><strong>Unified Agent Metrics (not available by default)</strong></summary>

| Metric Category | Examples |
|----------------|----------|
| **CPU** | cpu_usage_active, cpu_usage_idle, cpu_usage_user |
| **Disk** | disk_used_percent, disk_inodes_free |
| **Disk I/O** | diskio_read_bytes, diskio_write_bytes, diskio_iops_in_progress |
| **RAM** | mem_used_percent, mem_available |
| **Network** | net_bytes_sent, net_bytes_recv, netstat connections |
| **Processes** | processes_total, processes_running, processes_sleeping |
| **Swap** | swap_used_percent |

</details>

<details>
<summary><strong>SSM Parameter Store Integration</strong></summary>

- Store **agent configuration** centrally in **SSM Parameter Store**
- Push same config to multiple EC2 instances
- Avoids manual configuration on each instance

</details>

---

### 📊 CloudWatch Dashboards

<details>
<summary><strong>Overview</strong></summary>

- Create **custom operational dashboards** with graphs, numbers, alarms
- **Cross-region and cross-account** dashboards supported
- Can be shared publicly or with AWS accounts (no AWS login required for public)
- **Automatic Refresh** – 1m, 2m, 5m, 15m intervals
- **Pricing:** 3 dashboards (up to 50 metrics) free; $3/dashboard/month after

</details>

---

### 📊 CloudWatch Events / EventBridge

<details>
<summary><strong>Overview</strong></summary>

- **CloudWatch Events** (now part of **Amazon EventBridge**)
- React to AWS service events and schedule actions

| Feature | Description |
|---------|-------------|
| **Event Pattern** | Match events from AWS services (e.g., EC2 state change) |
| **Scheduled Rules** | Cron or rate expressions (e.g., every 5 minutes) |
| **Targets** | Lambda, SQS, SNS, Step Functions, EC2, Kinesis, and 20+ more |

</details>

---

### 📊 CloudWatch Container & Lambda Insights

<details>
<summary><strong>Container Insights</strong></summary>

- Collect, aggregate, and summarize **metrics from ECS, EKS, Kubernetes on EC2, Fargate**
- Metrics: CPU, memory, disk, network per container/task/service/cluster
- Uses **CloudWatch Agent** as DaemonSet in EKS

</details>

<details>
<summary><strong>Lambda Insights</strong></summary>

- Enhanced monitoring for **Lambda functions**
- Collects: cold starts, worker shutdowns, memory usage, CPU, network
- Provided as a **Lambda layer**

</details>

<details>
<summary><strong>Application Insights</strong></summary>

- Automated **dashboards for .NET and SQL Server applications**
- Detects anomalies; surfaces issues in related AWS resources
- Uses SageMaker-powered insights

</details>

---

## 4️⃣ CloudTrail

### 🔍 Overview

<details>
<summary><strong>What CloudTrail Does</strong></summary>

- Records **all API calls** made in your AWS account
- Captures: **who** called, **what** action, **when**, **from where**, **on what resource**
- Enabled **by default** in all regions
- **90-day event history** available in console (no setup needed)
- Create a **Trail** to store logs long-term in **S3** (+ optional CloudWatch Logs)

</details>

---

### 🔍 Event Types

<details>
<summary><strong>Management Events</strong></summary>

- Operations on **AWS resources** (control plane)
- Examples: `CreateBucket`, `RunInstances`, `AttachRolePolicy`, `CreateVPC`
- **Enabled by default** in CloudTrail
- Logged for: read events and write events (can separate)

</details>

<details>
<summary><strong>Data Events</strong></summary>

- **High-volume, resource-level** operations
- **Not enabled by default** (extra cost)
- Examples:
  - S3 object operations: `GetObject`, `PutObject`, `DeleteObject`
  - Lambda function invocations
  - DynamoDB item-level activity

</details>

<details>
<summary><strong>CloudTrail Insights Events</strong></summary>

- **Automatically detects unusual API activity** using ML
- Baselines normal activity then detects anomalies
- Examples: unusual spike in IAM actions, high rate of EC2 `TerminateInstances`
- Delivers Insights events to: CloudTrail console, S3, EventBridge

</details>

---

### 🔍 Trail Configuration

<details>
<summary><strong>Trail Options</strong></summary>

| Option | Description |
|--------|-------------|
| **Single-Region Trail** | Logs events in one region only |
| **Multi-Region Trail** | Logs all regions (recommended; default for new trails) |
| **Organization Trail** | Covers all accounts in AWS Organization |
| **S3 Storage** | Long-term log storage (indefinite retention) |
| **CloudWatch Logs** | Real-time monitoring + alarms on API events |
| **SNS Notifications** | Alert when new log file is delivered |
| **Log File Integrity Validation** | Detect if logs were tampered with (SHA-256 hash) |

</details>

<details>
<summary><strong>CloudTrail Log Encryption</strong></summary>

- Logs encrypted in S3 with **SSE-S3** by default
- Optional: use **SSE-KMS** (customer-managed key) for additional control
- **Log File Integrity Validation** – creates digest file; verify with AWS CLI

</details>

---

### 🔍 CloudTrail + EventBridge Integration

<details>
<summary><strong>React to API Calls in Real Time</strong></summary>

```
API Call (e.g., DeleteBucket)
    ↓
CloudTrail captures event
    ↓
EventBridge rule matches event
    ↓
Target: SNS alert / Lambda / SQS
```

- Use for: security alerts, compliance automation, audit triggers
- Example: alert when root account logs in, notify when security group rule is changed

</details>

---

### 🔍 CloudTrail Use Cases

<details>
<summary><strong>Common Scenarios</strong></summary>

| Scenario | Solution |
|----------|---------|
| Who deleted an S3 bucket? | CloudTrail → filter by `DeleteBucket` |
| Detect unauthorized API calls | CloudTrail → CloudWatch Alarm → SNS |
| Compliance audit | CloudTrail → S3 (with integrity validation) |
| Root account usage alert | CloudTrail → EventBridge → SNS |
| Track IAM changes | CloudTrail → filter by IAM events |
| Investigate security incident | CloudTrail event history or S3 logs |

</details>

---

## 5️⃣ AWS Config

### ⚙️ Overview

<details>
<summary><strong>What AWS Config Does</strong></summary>

- **Records configuration changes** of AWS resources over time
- Answers: *"What did my AWS resource look like at point X in time?"*
- Evaluates resources against **compliance rules**
- Does **NOT prevent** changes (only records and evaluates)
- Regional service; can aggregate across regions and accounts
- Data stored in **S3** (config snapshots + history)

</details>

---

### ⚙️ Config Rules

<details>
<summary><strong>AWS Managed Rules</strong></summary>

- 150+ pre-built rules by AWS
- Examples:
  - `restricted-ssh` – no inbound SSH from 0.0.0.0/0
  - `s3-bucket-public-read-prohibited` – S3 buckets should not be public
  - `encrypted-volumes` – EBS volumes must be encrypted
  - `iam-password-policy` – IAM password policy must meet requirements
  - `rds-storage-encrypted` – RDS instances must be encrypted
  - `mfa-enabled-for-iam-console-access`

</details>

<details>
<summary><strong>Custom Config Rules</strong></summary>

- Define custom compliance rules using **Lambda functions**
- Trigger on: **configuration change** or **periodic** (hourly, daily, etc.)
- Example: check that all EC2 instances are of approved instance types

</details>

<details>
<summary><strong>Rule Evaluation Triggers</strong></summary>

| Trigger | When It Runs |
|---------|-------------|
| **Configuration Change** | When a resource config changes |
| **Periodic** | At scheduled intervals (1hr, 3hr, 6hr, 12hr, 24hr) |

</details>

---

### ⚙️ Config Remediations

<details>
<summary><strong>Automatic Remediation</strong></summary>

- When a rule is non-compliant → trigger **SSM Automation Document**
- **AWS-managed remediation actions** – pre-built SSM docs (e.g., `AWS-DisablePublicAccessForSecurityGroup`)
- **Custom remediation** – your own SSM Automation or Lambda
- **Retry remediation** – auto-retry if remediation fails (up to 5 times)

</details>

<details>
<summary><strong>Remediation Example</strong></summary>

```
Config Rule: IAM access keys must be rotated every 90 days
      ↓ (non-compliant)
Config Remediation → SSM Automation → Disable the access key
      ↓
SNS Notification → Alert security team
```

</details>

---

### ⚙️ Config Notifications & Aggregation

<details>
<summary><strong>Notifications</strong></summary>

- Config changes → **SNS topic** for notifications
- Non-compliant resources → **EventBridge** → Lambda/SNS/SQS
- All config data → **S3** for long-term analysis

</details>

<details>
<summary><strong>Config Aggregator</strong></summary>

- Aggregate compliance data across **multiple accounts and regions**
- Creates **Aggregator** in a central account
- View organization-wide compliance dashboard
- Works with **AWS Organizations**

</details>

---

### ⚙️ Config vs CloudTrail vs CloudWatch

<details>
<summary><strong>Key Differences</strong></summary>

| Question | Service |
|----------|---------|
| What is happening right now on my resources? | **CloudWatch** |
| Who made this API call and when? | **CloudTrail** |
| What did this resource look like over time? | **AWS Config** |
| Is my resource compliant with a rule? | **AWS Config** |
| Alert me when a metric threshold is breached | **CloudWatch Alarms** |
| Alert me when a specific API call is made | **CloudTrail + EventBridge** |
| Alert me when a resource becomes non-compliant | **Config + EventBridge** |

</details>

---

## 📑 Comparison Cheat Sheet

### Data & Analytics Services

| Service | Category | Key Words |
|---------|----------|-----------|
| **Athena** | Serverless SQL | Query S3, pay per scan, Parquet |
| **Redshift** | Data Warehouse | OLAP, MPP, columnar, petabytes |
| **EMR** | Big Data Platform | Hadoop, Spark, managed clusters |
| **Glue** | ETL / Catalog | Serverless ETL, Glue Crawler, Data Catalog |
| **Kinesis Streams** | Real-time Ingest | Shards, replay, milliseconds |
| **Kinesis Firehose** | Real-time Delivery | Near real-time, no replay, S3/Redshift |
| **MSK** | Managed Kafka | Kafka API, topics, partitions |
| **OpenSearch** | Search & Analytics | Full-text search, dashboards, log analytics |
| **QuickSight** | BI & Visualization | Dashboards, SPICE, serverless |
| **Lake Formation** | Data Lake | Fine-grained access, S3-based lake |

### ML Services

| Service | Category | Key Words |
|---------|----------|-----------|
| **Rekognition** | Computer Vision | Images, video, faces, labels |
| **Transcribe** | Speech → Text | ASR, diarization, PII redaction |
| **Polly** | Text → Speech | TTS, SSML, neural voices |
| **Translate** | Language | Neural translation, real-time |
| **Comprehend** | NLP | Entities, sentiment, key phrases |
| **SageMaker** | ML Platform | Build, train, deploy custom models |
| **Forecast** | Time-Series | Demand forecasting, ML-based |
| **Kendra** | Enterprise Search | NLP search, document indexing |
| **Personalize** | Recommendations | Real-time, behavioral data |
| **Textract** | Document AI | OCR, forms, tables extraction |
| **Lex** | Chatbot | Intents, slots, Alexa-powered |

### Monitoring Services

| Concern | CloudWatch | CloudTrail | AWS Config |
|---------|-----------|-----------|-----------|
| **What it monitors** | Metrics & Logs | API calls | Resource configurations |
| **Key question** | Is my app performing? | Who did what? | Is this compliant? |
| **Storage** | CloudWatch Logs | S3 | S3 |
| **Real-time** | ✅ Yes | Near real-time | On change |
| **Alarms** | ✅ Yes | Via EventBridge | Via EventBridge |
| **Retention** | Configurable | 90 days console; S3 unlimited | Indefinite in S3 |

---

## ⚡ Quick Exam Tips

### ✅ Must-Know Patterns

```
Ad-hoc SQL on S3           → Athena (+ Parquet for cost savings)
Data Warehouse / OLAP      → Redshift
Real-time stream + replay  → Kinesis Data Streams
Near real-time to S3       → Kinesis Data Firehose
Big data ETL (Spark)       → EMR
Serverless ETL             → Glue
Enterprise search (NLP)    → Kendra
Image/video analysis       → Rekognition
Speech to text             → Transcribe
Text to speech             → Polly
Extract from documents     → Textract
Build custom ML model      → SageMaker
Time-series forecasting    → Forecast
Product recommendations    → Personalize
BI Dashboard               → QuickSight
```

### ⚠️ Tricky Distinctions

```
Kinesis Streams vs Firehose  → Streams = replay, real-time | Firehose = no replay, near real-time delivery
Redshift vs Athena           → Redshift = warehouse, fast repeated queries | Athena = serverless, ad-hoc
Glue vs EMR                  → Glue = serverless ETL | EMR = managed cluster (Spark/Hadoop)
Transcribe vs Comprehend     → Transcribe = speech→text | Comprehend = analyze text for insights
Rekognition vs Textract      → Rekognition = images/video analysis | Textract = extract structured data from docs
Kendra vs OpenSearch         → Kendra = NLP enterprise search | OpenSearch = full-text search + log analytics
CloudWatch vs CloudTrail     → CloudWatch = performance/ops | CloudTrail = API audit
CloudTrail vs Config         → CloudTrail = who did what | Config = what did it look like over time
```

### 📏 Key Numbers to Remember

| Service | Important Number |
|---------|----------------|
| Kinesis Data Streams | 1 MB/s in, 2 MB/s out per shard |
| Kinesis Firehose | 60 second minimum buffer |
| MSK default message size | 1 MB (configurable) |
| CloudWatch high-res metrics | 1-second granularity |
| CloudWatch metric retention | Up to 15 months |
| Config Rule triggers | Periodic or on change |
| CloudTrail default history | 90 days in console |

---

<div align="center">

**You've got this! 💪 Keep grinding! 🍀**

*Focus on understanding WHEN to use each service — that's the key to SAA-C03.*

![AWS](https://img.shields.io/badge/AWS-Solutions%20Architect%20Associate-FF9900?style=flat-square&logo=amazon-aws)
![Topic](https://img.shields.io/badge/Data%20%7C%20ML%20%7C%20Monitoring-Notes-blue?style=flat-square)

</div>
