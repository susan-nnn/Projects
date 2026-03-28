# ☁️ AWS CERTIFIED CLOUD PRACTITIONER (CLF-C02)
# 🏆 ULTIMATE MASTER CHEAT SHEET

> **Goal:** Read this. Pass the exam. Simple.
> **Format:** Exam-focused, memory-optimized, beginner-friendly.
> **Exam Domains:** Cloud Concepts (24%) | Security & Compliance (30%) | Technology (34%) | Billing & Support (12%)

---

# 📋 TABLE OF CONTENTS

1. [Exam Overview](#exam-overview)
2. [Domain 1 – Cloud Concepts](#domain-1--cloud-concepts)
3. [Domain 2 – Security & Compliance](#domain-2--security--compliance)
4. [Domain 3 – Technology (Core AWS Services)](#domain-3--technology-core-aws-services)
5. [Domain 4 – Billing, Pricing & Support](#domain-4--billing-pricing--support)
6. [Common Confusions & Fast Decision Rules](#common-confusions--fast-decision-rules)
7. [Final Revision Sections](#final-revision-sections)

---

# 🎯 EXAM OVERVIEW

| Item | Detail |
|---|---|
| **Exam Code** | CLF-C02 |
| **Questions** | 65 questions |
| **Format** | Multiple choice + Multiple response |
| **Duration** | 90 minutes |
| **Passing Score** | 700 / 1000 |
| **Cost** | $100 USD |
| **Validity** | 3 years |

### Domain Weights

| Domain | Weight |
|---|---|
| Cloud Concepts | 24% |
| Security & Compliance | 30% |
| Technology | 34% |
| Billing, Pricing & Support | 12% |

---

# 📗 DOMAIN 1 – CLOUD CONCEPTS

## 1.1 What is Cloud Computing?

**Definition:** Delivery of IT services (servers, storage, databases, networking, software) over the internet — on demand, pay-as-you-go.

### 6 Advantages of Cloud (MEMORIZE THESE)

| # | Advantage | Simple Meaning |
|---|---|---|
| 1 | Trade capital expense for variable expense | Pay only what you use, not upfront |
| 2 | Benefit from massive economies of scale | AWS buys in bulk → you save |
| 3 | Stop guessing capacity | Scale up or down anytime |
| 4 | Increase speed and agility | Deploy in minutes, not weeks |
| 5 | Stop spending money on data centers | AWS handles hardware |
| 6 | Go global in minutes | Deploy worldwide with one click |

---

## 1.2 Cloud Deployment Models

| Model | What It Is | When to Use | Exam Keyword |
|---|---|---|---|
| **Public Cloud** | Everything on AWS | Most businesses | "AWS hosted", "fully managed" |
| **Private Cloud** | On your own data center | Govt, banking | "on-premises only" |
| **Hybrid Cloud** | Mix of both | Transition period | "on-prem + cloud" |

---

## 1.3 Cloud Service Models

| Model | What AWS Manages | You Manage | Examples |
|---|---|---|---|
| **IaaS** | Hardware, network | OS, apps, data | EC2 |
| **PaaS** | Hardware + OS + runtime | Just your app code | Elastic Beanstalk |
| **SaaS** | Everything | Just use the app | Gmail, Salesforce |

> 🧠 **Mnemonic:** IaaS = I manage most → PaaS = Platform manages middle → SaaS = Someone manages all

---

## 1.4 Global Infrastructure

### 🌍 Regions vs AZs vs Edge Locations

| Concept | What It Is | Count (approx.) | Exam Keyword |
|---|---|---|---|
| **Region** | Geographic area (e.g., us-east-1) | 30+ | "geographic location", "data residency" |
| **Availability Zone (AZ)** | 1-6 data centers in a Region | 90+ | "fault tolerance", "multi-AZ" |
| **Edge Location** | CDN servers for caching content | 400+ | "low latency", "CloudFront", "end users" |
| **Local Zone** | AWS infrastructure closer to cities | Few | "single-digit ms latency" |
| **Wavelength Zone** | Embedded in telecom networks | Few | "5G", "ultra-low latency mobile" |

> 🧠 **Rule:** Edge Locations > AZs > Regions (in count)

### ⚡ EXAM TRIGGER
- Question mentions **"low latency for global users"** → **CloudFront + Edge Locations**
- Question mentions **"high availability"** → **Multi-AZ**
- Question mentions **"disaster recovery across geography"** → **Multi-Region**

---

## 1.5 High Availability vs Fault Tolerance vs Scalability vs Elasticity

| Concept | Simple Meaning | Example |
|---|---|---|
| **High Availability** | System stays online despite failures (99.9%+ uptime) | Multi-AZ RDS |
| **Fault Tolerance** | System KEEPS WORKING even during failure (zero downtime) | Active-active setup |
| **Scalability** | Ability to handle more load (manual or planned) | Adding more EC2s |
| **Elasticity** | Auto scale up AND down based on demand | Auto Scaling |
| **Durability** | Data won't be lost | S3 11 9s durability |
| **Reliability** | System performs correctly consistently | Multi-AZ deployments |

> 🧠 **Key difference:** Fault Tolerance = NO downtime. High Availability = MINIMAL downtime.

---

## 1.6 Serverless Concept

**What it means:** You run code WITHOUT managing servers. AWS handles all infrastructure.

| Feature | Serverless |
|---|---|
| Server management | AWS handles it |
| Scaling | Automatic |
| Billing | Per invocation (not per hour) |
| Services | Lambda, Fargate, DynamoDB, S3, Aurora Serverless |

> 🧠 **Exam clue:** "No server management" OR "pay per execution" → **Serverless / Lambda**

---

## 1.7 The 6 R's – Cloud Migration Strategies

| Strategy | What It Means | Exam Keyword |
|---|---|---|
| **Rehost** (Lift & Shift) | Move app as-is to cloud | "fastest", "minimal changes" |
| **Replatform** (Lift, Tinker & Shift) | Small optimizations, no code change | "move to managed DB" |
| **Repurchase** | Switch to SaaS product | "move to Salesforce/ServiceNow" |
| **Refactor / Re-architect** | Rewrite app using cloud-native features | "microservices", "most expensive" |
| **Retire** | Shut down unused apps | "decommission" |
| **Retain** | Keep on-premises for now | "not ready to migrate yet" |

> 🧠 **Mnemonic:** **R**ehost, **R**eplatform, **R**epurchase, **R**efactor, **R**etire, **R**etain = **6 R's**

---

# 🔐 DOMAIN 2 – SECURITY & COMPLIANCE

## 2.1 Shared Responsibility Model ⭐ MOST TESTED

> **AWS is responsible FOR the cloud. YOU are responsible IN the cloud.**

| AWS Responsibility ("Security OF the Cloud") | Your Responsibility ("Security IN the Cloud") |
|---|---|
| Physical data centers | Your data |
| Hardware, networking, hypervisor | OS patches (on EC2) |
| Global infrastructure | IAM users & permissions |
| Managed service security (RDS patching) | Encryption of data |
| Regions, AZs, Edge Locations | Security group rules / firewall config |

### Quick Examples

| Service | AWS Manages | You Manage |
|---|---|---|
| **EC2** | Hypervisor, hardware | OS, apps, security groups |
| **RDS** | DB software patching, hardware | DB users, encryption settings |
| **S3** | Infrastructure durability | Bucket policies, ACLs, encryption |
| **Lambda** | All infrastructure | Function code, IAM role |

> ⚡ **EXAM TRIGGER:** "Who is responsible for patching the OS on EC2?" → **YOU (customer)**
> ⚡ **EXAM TRIGGER:** "Who is responsible for patching the RDS database engine?" → **AWS**

---

## 2.2 IAM – Identity and Access Management ⭐

- **What it is:** Control who can access what in AWS
- **Category:** Security / Identity
- **Analogy:** Security badge system for your office
- **Cost:** FREE

### IAM Core Components

| Component | What It Is | Exam Keyword |
|---|---|---|
| **User** | Individual person or app | "specific person needs access" |
| **Group** | Collection of users | "apply same policy to team" |
| **Role** | Temporary permissions (no password) | "EC2 needs S3 access", "cross-account" |
| **Policy** | JSON document defining permissions | "Allow/Deny actions on resources" |
| **MFA** | Extra verification step | "increase security", "add second factor" |

### IAM Key Rules

- **Root account** = Never use for daily tasks. Lock it down with MFA.
- **Principle of Least Privilege** = Grant ONLY the permissions needed.
- **IAM is global** (not region-specific)
- **Roles > Access Keys** for EC2/Lambda (more secure)

> ⚡ **EXAM TRIGGER:**
> - "Application on EC2 needs to access S3" → **IAM Role** (NOT access keys)
> - "Apply same permissions to all developers" → **IAM Group**
> - "Temporary access for external consultant" → **IAM Role**

---

## 2.3 Security Services Quick Reference

### AWS Shield
- **What it is:** Protection against DDoS attacks
- **Shield Standard:** FREE, automatic, protects all AWS customers
- **Shield Advanced:** Paid ($3,000/month), 24/7 DDoS response team, cost protection
- **Exam keyword:** "DDoS protection" → Shield
- **NOT for:** Application-layer filtering (that's WAF)

### AWS WAF (Web Application Firewall)
- **What it is:** Filter malicious web traffic (SQL injection, XSS)
- **Analogy:** Bouncer checking IDs at the door
- **Works with:** CloudFront, ALB, API Gateway
- **Exam keyword:** "block SQL injection", "web traffic filtering", "Layer 7"
- **NOT for:** Network-level DDoS (that's Shield)

### Shield vs WAF Quick Difference

| | Shield | WAF |
|---|---|---|
| Protects against | DDoS (volumetric attacks) | Web exploits (SQL injection, XSS) |
| Layer | Network (Layer 3/4) | Application (Layer 7) |
| Auto-enabled | Standard = Yes | No, must configure rules |

---

### AWS KMS (Key Management Service)
- **What it is:** Create and manage encryption keys
- **Analogy:** Master key safe for your data locks
- **Exam keyword:** "encryption keys", "encrypt S3/EBS/RDS", "customer managed keys"
- **NOT for:** Storing secrets like DB passwords (use Secrets Manager)

### AWS Secrets Manager
- **What it is:** Store, rotate, and retrieve secrets (DB passwords, API keys)
- **Exam keyword:** "rotate credentials automatically", "store database password securely"
- **NOT for:** Encryption key management (use KMS)

### AWS Certificate Manager (ACM)
- **What it is:** Provision SSL/TLS certificates for FREE
- **Exam keyword:** "HTTPS", "SSL certificate", "encrypt in transit"

### AWS Cognito
- **What it is:** User sign-up/sign-in for web/mobile apps
- **Analogy:** Login system for your app (like "Sign in with Google")
- **Exam keyword:** "user authentication for app", "social login", "identity for mobile app"
- **NOT for:** AWS console access (use IAM)

### AWS Inspector
- **What it is:** Automated security scanning for EC2 and containers
- **Exam keyword:** "vulnerability scanning", "security assessment", "find CVEs"

### AWS GuardDuty
- **What it is:** Threat detection using ML — monitors logs for suspicious activity
- **Exam keyword:** "threat detection", "unusual activity", "suspicious API calls"
- **Difference from Inspector:** GuardDuty = threats/behavior | Inspector = vulnerabilities

### AWS Macie
- **What it is:** Automatically discovers and protects sensitive data in S3 (PII, credit cards)
- **Exam keyword:** "sensitive data in S3", "PII detection", "data privacy"

### AWS Config
- **What it is:** Records and evaluates AWS resource configurations over time
- **Analogy:** Audit trail for your AWS settings
- **Exam keyword:** "compliance", "configuration history", "did this change?", "audit resource settings"
- **NOT for:** Performance monitoring (use CloudWatch)

### AWS Artifact
- **What it is:** On-demand access to AWS compliance reports and agreements
- **Analogy:** AWS's compliance filing cabinet
- **Exam keyword:** "compliance reports", "SOC 2", "ISO 27001", "PCI DSS documentation"
- **NOT for:** Security scanning or monitoring

### AWS Security Hub
- **What it is:** Central dashboard for all security findings across services
- **Exam keyword:** "single view of security", "aggregate security alerts"

### AWS Detective
- **What it is:** Investigate security incidents using graph analysis
- **Exam keyword:** "investigate security incident", "root cause analysis"

---

## 2.4 Compliance & Governance

| Concept | Simple Meaning |
|---|---|
| **AWS Compliance Programs** | AWS meets HIPAA, PCI-DSS, SOC, ISO, GDPR standards |
| **Customer Compliance** | YOU must configure services correctly for YOUR compliance |
| **AWS Artifact** | Download compliance docs (not a service you configure) |
| **AWS Config** | Check if your resources meet your rules |

---

## 2.5 AWS Organizations

- **What it is:** Manage multiple AWS accounts centrally
- **Analogy:** Parent company managing all subsidiaries
- **Key features:**
  - **SCP (Service Control Policies):** Restrict what accounts CAN'T do
  - **Consolidated Billing:** One bill for all accounts
  - **OUs (Organizational Units):** Group accounts by dept/team
- **Exam keyword:** "multiple accounts", "central governance", "restrict services across accounts"

> ⚡ **EXAM TRIGGER:** "Prevent developers from deleting CloudTrail logs in any account" → **SCP in Organizations**

---

# 💻 DOMAIN 3 – TECHNOLOGY (CORE AWS SERVICES)

## 3.1 COMPUTE SERVICES

### EC2 (Elastic Compute Cloud) ⭐

- **What it is:** Virtual server (computer) in the cloud
- **Analogy:** Renting a computer in AWS's data center
- **Category:** Compute / IaaS
- **When to use:** Long-running apps, custom OS needs, full control
- **NOT for:** Running code without managing servers (use Lambda)
- **Exam keywords:** "virtual machine", "web server", "custom OS", "persistent server"

#### EC2 Instance Types

| Type | Optimized For | Exam Keyword |
|---|---|---|
| **General Purpose** (t3, m5) | Balance of compute/memory/network | "general workloads", "small apps" |
| **Compute Optimized** (c5) | High CPU tasks | "batch processing", "gaming servers", "ML inference" |
| **Memory Optimized** (r5, x1) | High RAM tasks | "in-memory databases", "SAP HANA" |
| **Storage Optimized** (i3, d2) | High disk I/O | "data warehousing", "Hadoop", "NoSQL DBs" |
| **Accelerated Computing** (p3, g4) | GPU tasks | "ML training", "video rendering" |

#### EC2 Pricing Models ⭐

| Model | What It Is | When to Use | Savings |
|---|---|---|---|
| **On-Demand** | Pay per hour/second, no commitment | Unpredictable workloads, testing | 0% (baseline) |
| **Reserved (1 or 3 yr)** | Commit to 1 or 3 years upfront | Steady, predictable workloads | Up to 72% |
| **Spot** | Bid on unused EC2 capacity | Fault-tolerant, flexible workloads | Up to 90% |
| **Savings Plans** | Commit to $/hour spend for 1-3 yr | Flexible across instance types | Up to 66% |
| **Dedicated Host** | Physical server just for you | Compliance, licensing requirements | Expensive |
| **Dedicated Instance** | Instance on dedicated hardware | Regulatory isolation | Less than host |

> ⚡ **EXAM TRIGGERS:**
> - "Cheapest option, can be interrupted" → **Spot**
> - "Predictable 24/7 workload" → **Reserved**
> - "Compliance requires dedicated hardware" → **Dedicated Host**
> - "Short-term, unpredictable" → **On-Demand**

---

### Lambda ⭐

- **What it is:** Run code without managing servers (serverless functions)
- **Analogy:** Vending machine — it works when you press a button, off otherwise
- **Category:** Compute / Serverless
- **Billing:** Per request + per ms of execution time
- **Timeout:** Max 15 minutes per execution
- **When to use:** Event-driven tasks, short-running code, automated responses
- **NOT for:** Long-running processes (>15 min) or apps needing persistent state
- **Exam keywords:** "serverless", "event-driven", "no server management", "pay per invocation"

---

### Elastic Beanstalk

- **What it is:** Deploy apps without managing infrastructure (PaaS)
- **Analogy:** Auto-everything deployment — just upload your code
- **Category:** Compute / PaaS
- **When to use:** Developers who want to deploy quickly without DevOps knowledge
- **NOT for:** Serverless (it still uses EC2 under the hood)
- **Exam keywords:** "deploy web app", "no infrastructure management", "PaaS"

---

### ECS (Elastic Container Service)

- **What it is:** Run Docker containers on AWS (AWS-managed)
- **Category:** Containers
- **When to use:** Containerized apps, microservices with Docker
- **Exam keywords:** "containers", "Docker", "AWS-managed orchestration"

### EKS (Elastic Kubernetes Service)

- **What it is:** Run Kubernetes on AWS (AWS-managed)
- **Category:** Containers
- **When to use:** Already using Kubernetes, need managed K8s
- **Exam keywords:** "Kubernetes", "K8s"

### ECS vs EKS Quick Difference

| | ECS | EKS |
|---|---|---|
| Orchestrator | AWS proprietary | Kubernetes |
| Best for | AWS-native Docker | Kubernetes users |

### AWS Fargate

- **What it is:** Serverless compute for containers (no EC2 management)
- **Works with:** ECS and EKS
- **Exam keywords:** "serverless containers", "no EC2 needed for containers"

---

### Auto Scaling

- **What it is:** Automatically add/remove EC2 instances based on demand
- **Analogy:** Hiring more staff during rush hour, sending them home after
- **Category:** Compute / Elasticity
- **Types:**
  - **Dynamic Scaling:** React to current demand
  - **Scheduled Scaling:** Scale at specific times
  - **Predictive Scaling:** ML-based future scaling
- **Exam keywords:** "automatically scale", "handle traffic spikes", "elasticity"

---

## 3.2 STORAGE SERVICES

### S3 (Simple Storage Service) ⭐

- **What it is:** Object storage — store any file (images, videos, backups, code)
- **Analogy:** Unlimited cloud hard drive / Google Drive for developers
- **Category:** Storage / Object Storage
- **Key facts:**
  - Objects up to **5 TB** each
  - **11 nines (99.999999999%) durability**
  - Global service (buckets have globally unique names)
  - **NOT** a file system (no folders, just keys)
- **Exam keywords:** "store files", "static website", "backup", "data lake"

#### S3 Storage Classes ⭐

| Class | Use Case | Retrieval | Cost |
|---|---|---|---|
| **S3 Standard** | Frequently accessed data | Instant | Highest |
| **S3 Intelligent-Tiering** | Unknown access patterns | Instant | Auto-optimized |
| **S3 Standard-IA** | Infrequently accessed, needs fast retrieval | Instant | Lower storage, retrieval fee |
| **S3 One Zone-IA** | Infrequent, one AZ only (less resilient) | Instant | Cheapest IA |
| **S3 Glacier Instant Retrieval** | Archive, rarely accessed, instant needed | Milliseconds | Low |
| **S3 Glacier Flexible Retrieval** | Archive, retrieval in minutes-hours | Minutes to hours | Very low |
| **S3 Glacier Deep Archive** | Long-term archive (7-10 years) | 12-48 hours | Cheapest |

> ⚡ **EXAM TRIGGERS:**
> - "Cheapest long-term storage" → **S3 Glacier Deep Archive**
> - "Frequently accessed" → **S3 Standard**
> - "Don't know access patterns" → **S3 Intelligent-Tiering**
> - "Compliance archive, rarely accessed" → **S3 Glacier**

---

### EBS (Elastic Block Store)

- **What it is:** Block storage for EC2 — like a hard drive attached to your server
- **Analogy:** USB drive plugged into your EC2 (stays attached)
- **Category:** Storage / Block Storage
- **Key facts:** Attached to ONE EC2 at a time (usually), same AZ only, persistent
- **When to use:** EC2 boot volume, databases, OS storage
- **NOT for:** Sharing across multiple EC2s (use EFS) or storing files without EC2
- **Exam keywords:** "EC2 storage", "block storage", "database storage for EC2"

### EFS (Elastic File System)

- **What it is:** Shared file system — multiple EC2s can access it simultaneously
- **Analogy:** Shared network drive (like a company file server)
- **Category:** Storage / File Storage
- **Key facts:** Scales automatically, works across AZs, Linux only (NFS)
- **When to use:** Content management, shared code, home directories
- **NOT for:** Windows file shares (use FSx for Windows) or single EC2 storage
- **Exam keywords:** "shared storage", "multiple EC2 instances access same files", "NFS"

### EBS vs EFS vs S3 — THE BIG THREE COMPARISON ⭐

| Feature | EBS | EFS | S3 |
|---|---|---|---|
| **Type** | Block | File | Object |
| **Access** | One EC2 (mostly) | Many EC2s simultaneously | Any client via internet |
| **AZ** | Single AZ | Multi-AZ | Global |
| **Use case** | EC2 boot disk, DB | Shared files across EC2s | Images, backups, static sites |
| **Analogy** | Hard drive | Network drive | Google Drive |
| **Scales** | Manually | Automatically | Automatically |

---

### AWS Storage Gateway

- **What it is:** Connect on-premises storage to AWS cloud
- **Analogy:** Bridge between your office and AWS
- **Exam keywords:** "hybrid storage", "on-premises to cloud", "backup to S3 from local"

### AWS Snow Family

| Service | Capacity | Use Case |
|---|---|---|
| **Snowcone** | 8 TB | Small edge locations |
| **Snowball Edge** | 80 TB | Data migration, edge computing |
| **Snowmobile** | 100 PB | Massive data center migration |

> ⚡ **EXAM TRIGGER:** "Transfer huge amount of data to AWS without internet" → **AWS Snow Family**

---

## 3.3 DATABASE SERVICES

### RDS (Relational Database Service) ⭐

- **What it is:** Managed relational database (SQL)
- **Analogy:** AWS handles all DB maintenance for you
- **Supported engines:** MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora
- **AWS manages:** Patching, backups, Multi-AZ failover
- **You manage:** Schema, queries, DB users
- **When to use:** Traditional SQL workloads, OLTP
- **NOT for:** NoSQL (use DynamoDB) or massive scale analytics (use Redshift)
- **Exam keywords:** "relational database", "SQL", "managed database", "OLTP"

#### RDS Multi-AZ vs Read Replicas

| Feature | Multi-AZ | Read Replica |
|---|---|---|
| **Purpose** | High availability / DR | Read performance scaling |
| **Replication** | Synchronous (real-time) | Asynchronous (slight lag) |
| **Failover** | Automatic | Manual promotion |
| **Exam keyword** | "disaster recovery", "failover" | "read-heavy workload", "scale reads" |

---

### Aurora ⭐

- **What it is:** AWS's own cloud-native relational DB (MySQL/PostgreSQL compatible)
- **Key facts:** 5x faster than MySQL, 3x faster than PostgreSQL, auto-scales storage to 128 TB, 6 copies across 3 AZs
- **Aurora Serverless:** Auto-scales compute capacity
- **When to use:** High-performance relational DB, need MySQL/PostgreSQL compatibility
- **Exam keywords:** "MySQL compatible", "PostgreSQL compatible", "high performance relational", "Aurora Serverless"

---

### DynamoDB ⭐

- **What it is:** Fully managed NoSQL (key-value and document) database
- **Analogy:** Super-fast filing cabinet that scales infinitely
- **Category:** Database / NoSQL / Serverless
- **Key facts:** Single-digit millisecond latency, serverless (no servers to manage), scales automatically
- **When to use:** High-scale web apps, gaming, IoT, shopping carts, session data
- **NOT for:** Complex SQL joins, OLAP analytics
- **Exam keywords:** "NoSQL", "serverless database", "key-value", "millisecond latency", "gaming", "IoT"

#### DynamoDB Features

| Feature | What It Does |
|---|---|
| **DynamoDB Accelerator (DAX)** | In-memory cache for DynamoDB (microsecond latency) |
| **Global Tables** | Multi-region active-active replication |
| **Streams** | Capture item-level changes (trigger Lambda) |
| **On-demand mode** | Pay per request (no capacity planning) |

---

### Redshift

- **What it is:** Data warehouse for analytics — query massive datasets fast
- **Analogy:** Giant analytics engine for business intelligence
- **Category:** Database / Data Warehouse / OLAP
- **When to use:** Business intelligence, data analytics, run SQL on petabytes
- **NOT for:** Real-time transaction processing (use RDS/DynamoDB)
- **Exam keywords:** "data warehouse", "analytics", "OLAP", "business intelligence", "petabyte"

---

### ElastiCache

- **What it is:** In-memory caching service (Redis or Memcached)
- **Analogy:** Short-term memory for your app — super fast data retrieval
- **When to use:** Reduce database load, session management, real-time leaderboards
- **NOT for:** Permanent data storage
- **Exam keywords:** "caching", "in-memory", "reduce DB load", "session store", "Redis"

---

### RDS vs DynamoDB vs Redshift — QUICK COMPARE

| | RDS / Aurora | DynamoDB | Redshift |
|---|---|---|---|
| **Type** | Relational (SQL) | NoSQL | Data Warehouse |
| **Use case** | OLTP transactions | High-scale key-value | Analytics / OLAP |
| **Scaling** | Vertical + Read Replicas | Auto/serverless | Cluster-based |
| **Latency** | Milliseconds | Single-digit ms | Seconds (batch) |
| **Exam clue** | "SQL", "relational" | "NoSQL", "serverless DB" | "analytics", "BI" |

---

## 3.4 NETWORKING SERVICES

### VPC (Virtual Private Cloud) ⭐

- **What it is:** Your private isolated network in AWS
- **Analogy:** Fenced yard within AWS's big field — you control what goes in/out
- **Category:** Networking
- **Key components:**

| Component | What It Does | Exam Keyword |
|---|---|---|
| **Subnet** | Sub-section of VPC (public or private) | "isolate resources" |
| **Internet Gateway (IGW)** | Allows public internet access to/from VPC | "public internet access" |
| **NAT Gateway** | Lets private subnet reach internet (outbound only) | "private EC2 needs internet" |
| **Route Table** | Rules for routing traffic | "direct traffic" |
| **Security Group** | Firewall at instance level (stateful) | "instance-level firewall" |
| **Network ACL (NACL)** | Firewall at subnet level (stateless) | "subnet-level firewall" |

#### Security Group vs NACL

| Feature | Security Group | Network ACL |
|---|---|---|
| Level | Instance | Subnet |
| State | Stateful (return traffic auto-allowed) | Stateless (must allow both ways) |
| Rules | Allow only | Allow AND Deny |
| Default | Deny all inbound, allow all outbound | Allow all |

> ⚡ **EXAM TRIGGER:** "Block specific IP from reaching subnet" → **NACL** (because SG can't deny, only allow)

---

### Route 53

- **What it is:** AWS's DNS (Domain Name System) service
- **Analogy:** Phone book for the internet — converts domain names to IP addresses
- **Category:** Networking / DNS
- **Also does:** Domain registration, health checks, traffic routing
- **Routing policies:**

| Policy | What It Does | Exam Keyword |
|---|---|---|
| **Simple** | One record, one resource | Basic routing |
| **Weighted** | Split traffic by % | "A/B testing", "blue/green" |
| **Latency** | Route to lowest latency region | "global app, best performance" |
| **Failover** | Route to backup if primary fails | "disaster recovery" |
| **Geolocation** | Route based on user's country | "serve local content" |
| **Multi-value** | Return multiple IPs (basic LB) | "multiple healthy resources" |

---

### CloudFront ⭐

- **What it is:** CDN (Content Delivery Network) — cache content at Edge Locations
- **Analogy:** Mini copies of your content placed close to users worldwide
- **Category:** Networking / CDN
- **Works with:** S3, EC2, ALB, Lambda@Edge
- **When to use:** Deliver static/dynamic content globally with low latency
- **NOT for:** Routing traffic to different servers (use Route 53 or ALB)
- **Exam keywords:** "CDN", "low latency globally", "cache content", "edge locations", "static website delivery"

---

### Elastic Load Balancing (ELB)

- **What it is:** Distribute incoming traffic across multiple targets
- **Analogy:** Traffic cop directing cars to different lanes
- **Category:** Networking / Load Balancing
- **Types:**

| Type | Best For | Layer | Exam Keyword |
|---|---|---|---|
| **ALB** (Application LB) | HTTP/HTTPS, path-based routing | 7 (App) | "web apps", "microservices", "path routing" |
| **NLB** (Network LB) | Ultra-high performance, TCP/UDP | 4 (Transport) | "millions of requests/sec", "gaming", "low latency" |
| **GLB** (Gateway LB) | Third-party virtual appliances | 3 (Network) | "firewall appliances", "intrusion detection" |

> 🧠 **Note:** Classic LB (old) is being phased out. Focus on ALB and NLB.

---

### Global Accelerator

- **What it is:** Routes traffic through AWS's global network for better performance
- **Analogy:** Express highway vs regular roads — faster, more reliable
- **Category:** Networking
- **Key difference from CloudFront:**

| Feature | CloudFront | Global Accelerator |
|---|---|---|
| **Best for** | Static content caching | Dynamic content, TCP/UDP apps |
| **Uses** | Edge cache | AWS backbone network |
| **Exam clue** | "cache", "CDN", "static files" | "improve speed for non-HTTP", "consistent performance" |

---

### AWS Direct Connect

- **What it is:** Dedicated private network connection from your office to AWS
- **Analogy:** Private highway just for you (not shared internet)
- **When to use:** Consistent bandwidth, compliance, high data transfer needs
- **NOT for:** Quick setup (takes weeks to provision)
- **Exam keywords:** "dedicated connection", "private network to AWS", "bypass internet"

### VPN (Site-to-Site VPN)

- **What it is:** Encrypted tunnel over public internet to AWS
- **Exam keywords:** "encrypted connection", "quick setup", "cheaper than Direct Connect"
- **vs Direct Connect:** VPN = faster setup, uses internet | Direct Connect = dedicated, more reliable

---

## 3.5 MESSAGING & INTEGRATION

### SQS (Simple Queue Service) ⭐

- **What it is:** Message queue — stores messages until another service processes them
- **Analogy:** To-do list app — tasks sit in queue until someone picks them up
- **Category:** Application Integration / Messaging
- **Types:**
  - **Standard Queue:** At-least-once delivery, best-effort ordering
  - **FIFO Queue:** Exactly-once, guaranteed order (first in, first out)
- **When to use:** Decouple services, buffer traffic spikes, async processing
- **NOT for:** Sending to MULTIPLE subscribers simultaneously (use SNS)
- **Exam keywords:** "decouple", "queue", "buffer", "async", "1-to-1 messaging"

### SNS (Simple Notification Service) ⭐

- **What it is:** Pub/Sub messaging — publish once, fan out to many subscribers
- **Analogy:** Radio broadcast — one publisher, many listeners
- **Category:** Application Integration / Messaging
- **Subscribers can be:** SQS, Lambda, email, SMS, HTTP/S endpoints
- **When to use:** Alert many systems at once, notifications, fan-out pattern
- **NOT for:** Guaranteed delivery with retry or storing messages (use SQS)
- **Exam keywords:** "notifications", "fan-out", "1-to-many", "pub/sub", "alert multiple"

### SQS vs SNS — KEY DIFFERENCE ⭐

| Feature | SQS | SNS |
|---|---|---|
| **Pattern** | Queue (1-to-1) | Pub/Sub (1-to-many) |
| **Message stored?** | Yes, until processed | No (push immediately) |
| **Delivery** | Pulled by consumer | Pushed to all subscribers |
| **Use case** | Buffer between services | Alert multiple services at once |
| **Analogy** | To-do list | Email newsletter blast |

> ⚡ **Common Pattern:** SNS + SQS = "Fan-out" — SNS pushes to multiple SQS queues

---

### Amazon EventBridge

- **What it is:** Serverless event bus — route events from AWS services or your apps to targets
- **Exam keywords:** "event-driven", "trigger automation", "schedule tasks", "connect SaaS apps"

### Amazon Kinesis

- **What it is:** Real-time data streaming and analytics
- **Exam keywords:** "real-time streaming", "IoT data", "clickstream", "live analytics"
- **vs SQS:** Kinesis = real-time streams/analytics | SQS = async task queue

---

## 3.6 MONITORING & MANAGEMENT

### CloudWatch ⭐

- **What it is:** Monitor AWS resources and applications (metrics, logs, alarms)
- **Analogy:** Dashboard + alert system for your AWS environment
- **Category:** Management / Monitoring
- **Key features:**
  - **Metrics:** CPU, network, disk stats
  - **Logs:** Collect and store log files
  - **Alarms:** Trigger actions when metric crosses threshold
  - **Dashboards:** Visual overview
- **When to use:** Monitor performance, set alerts, view logs
- **NOT for:** Tracking WHO did WHAT (use CloudTrail)
- **Exam keywords:** "monitor", "alarm", "metrics", "logs", "performance"

### CloudTrail ⭐

- **What it is:** Logs ALL API calls made to your AWS account (who did what, when)
- **Analogy:** CCTV recording every action in your AWS account
- **Category:** Management / Auditing / Governance
- **When to use:** Compliance, security auditing, governance
- **NOT for:** Performance monitoring (use CloudWatch)
- **Exam keywords:** "audit", "who deleted", "API activity", "compliance", "who made changes"

### CloudWatch vs CloudTrail ⭐

| Feature | CloudWatch | CloudTrail |
|---|---|---|
| **What it tracks** | Resource metrics & logs | API calls & user actions |
| **Answers** | "Is my app slow?" | "Who deleted my S3 bucket?" |
| **Use case** | Performance, monitoring | Auditing, compliance |
| **Analogy** | Health monitor | Security camera |

---

### AWS Trusted Advisor ⭐

- **What it is:** Automated best-practice advisor across 5 categories
- **Analogy:** Personal AWS consultant checking your account
- **5 Categories:**
  1. **Cost Optimization** — Find unused resources
  2. **Performance** — Improve speed
  3. **Security** — Find security gaps
  4. **Fault Tolerance** — Improve resilience
  5. **Service Limits** — Warn before hitting limits
- **Free checks:** Basic & Developer plan get 7 core checks
- **All checks:** Require Business or Enterprise Support
- **Exam keywords:** "best practices", "cost savings", "unused resources", "security recommendations"

---

### AWS Systems Manager

- **What it is:** Operational hub for managing AWS resources at scale
- **Key features:**
  - **Parameter Store:** Store config values and secrets
  - **Session Manager:** Connect to EC2 without SSH/port 22
  - **Patch Manager:** Automate OS patching
- **Exam keywords:** "manage fleet of EC2", "patch instances", "no SSH needed", "store parameters"

### AWS Config *(revisited)*
- Records configuration changes to AWS resources over time
- Check if resources comply with your rules
- **Exam keywords:** "did this change?", "compliance", "configuration history"

---

## 3.7 DEVELOPER & DEVOPS TOOLS

| Service | What It Does | Exam Keyword |
|---|---|---|
| **CodeCommit** | Git repository (like GitHub on AWS) | "source control", "code repository" |
| **CodeBuild** | Build and test code | "CI/CD", "compile code", "run tests" |
| **CodeDeploy** | Deploy code to EC2/Lambda/ECS | "automate deployments", "rolling deploy" |
| **CodePipeline** | Orchestrate CI/CD pipeline | "end-to-end CI/CD pipeline" |
| **Cloud9** | Cloud-based IDE (code editor) | "cloud IDE", "browser-based coding" |
| **CloudFormation** | Infrastructure as Code (IaC) | "IaC", "provision resources via template", "automate infrastructure" |
| **CDK** | Define cloud infra using code (Python, JS, etc.) | "code-based IaC", "developer-friendly IaC" |

---

## 3.8 AI & MACHINE LEARNING SERVICES (Quick Reference)

| Service | What It Does | Exam Keyword |
|---|---|---|
| **Rekognition** | Image & video analysis (faces, objects) | "facial recognition", "image analysis" |
| **Comprehend** | NLP — analyze text for sentiment, entities | "text analysis", "sentiment" |
| **Transcribe** | Speech-to-text | "audio to text", "transcription" |
| **Polly** | Text-to-speech | "text to audio" |
| **Translate** | Language translation | "translate text" |
| **SageMaker** | Build, train, deploy ML models | "custom ML model", "machine learning platform" |
| **Lex** | Chatbot / conversational AI | "chatbot", "Alexa-like", "NLU" |
| **Kendra** | Intelligent enterprise search | "enterprise search", "find info in documents" |
| **Personalize** | Real-time personalization/recommendations | "recommendation engine" |
| **Forecast** | Time-series forecasting | "predict future demand" |

---

## 3.9 OTHER IMPORTANT SERVICES

### AWS Well-Architected Framework — 6 Pillars

| Pillar | Focus | Exam Keyword |
|---|---|---|
| **Operational Excellence** | Run and monitor workloads | "automation", "operations" |
| **Security** | Protect data and systems | "security controls" |
| **Reliability** | Recover from failures, scale | "fault tolerance", "disaster recovery" |
| **Performance Efficiency** | Use resources efficiently | "right instance type" |
| **Cost Optimization** | Avoid unnecessary costs | "cost savings" |
| **Sustainability** | Reduce environmental impact | "green IT", "carbon footprint" |

### AWS CAF (Cloud Adoption Framework) — 6 Perspectives

| Perspective | Focus |
|---|---|
| Business | Business goals alignment |
| People | HR, culture, training |
| Governance | IT governance, risk |
| Platform | Architecture, infrastructure |
| Security | Data and system security |
| Operations | IT operations, support |

### Amazon Connect
- **What it is:** Cloud-based contact center
- **Exam keyword:** "call center", "customer service center"

### AWS Outposts
- **What it is:** AWS hardware installed in YOUR data center
- **Exam keyword:** "run AWS on-premises", "hybrid", "low latency local"

### AWS Lightsail
- **What it is:** Simple virtual servers for beginners (simplified EC2)
- **Exam keyword:** "simple web server", "small projects", "beginners", "fixed price"

### Amazon API Gateway
- **What it is:** Create, manage, and secure APIs at scale
- **Exam keyword:** "REST API", "API management", "backend for mobile/web app"

### Amazon WorkSpaces
- **What it is:** Desktop-as-a-Service (virtual desktops)
- **Exam keyword:** "virtual desktop", "DaaS", "remote workers"

---

# 💰 DOMAIN 4 – BILLING, PRICING & SUPPORT

## 4.1 AWS Pricing Fundamentals

### 3 Pricing Drivers

| Driver | What It Means |
|---|---|
| **Compute** | Pay per hour/second for compute resources |
| **Storage** | Pay per GB stored |
| **Data Transfer** | Pay for data OUT of AWS (inbound is usually FREE) |

> ⚡ **KEY RULE:** Data transfer IN to AWS = FREE | Data transfer OUT of AWS = PAID

---

## 4.2 AWS Free Tier

| Type | What It Is | Examples |
|---|---|---|
| **Always Free** | Never expires | Lambda (1M requests/month), DynamoDB (25 GB) |
| **12 Months Free** | First 12 months only | EC2 (750 hrs/month t2.micro), S3 (5 GB) |
| **Trial** | Short-term free trial | Inspector (90 days), Lightsail (90 days) |

---

## 4.3 EC2 Pricing Models (Detailed) ⭐

| Model | Savings | Commitment | Best For |
|---|---|---|---|
| **On-Demand** | 0% | None | Dev/test, unpredictable |
| **Reserved (All Upfront)** | Up to 72% | 1 or 3 years | Steady workloads |
| **Reserved (Partial Upfront)** | ~60% | 1 or 3 years | Balance of savings & flexibility |
| **Reserved (No Upfront)** | ~40% | 1 or 3 years | Cash flow flexibility |
| **Spot** | Up to 90% | None (can interrupt) | Batch, fault-tolerant |
| **Savings Plans (Compute)** | Up to 66% | 1 or 3 years | Flexible across regions/types |
| **Dedicated Host** | Variable | On-demand or reserved | License compliance |

---

## 4.4 Billing & Cost Management Tools ⭐

### AWS Cost Explorer
- **What it is:** Visualize and analyze your AWS spending
- **Analogy:** Bank statement with charts for AWS
- **Exam keyword:** "analyze costs", "view spending trends", "forecast costs"

### AWS Budgets
- **What it is:** Set spending/usage limits and get alerts
- **Analogy:** Budget alarm — warns you before overspending
- **Exam keyword:** "set budget alerts", "cost threshold", "notify when over budget"
- **Types:** Cost budget, Usage budget, Reservation budget

### AWS Pricing Calculator
- **What it is:** Estimate your AWS costs BEFORE you build
- **Exam keyword:** "estimate costs", "before deploying", "TCO estimate"

### AWS Cost and Usage Report (CUR)
- **What it is:** Most detailed billing data — delivered to S3
- **Exam keyword:** "detailed billing", "most granular cost data"

### AWS Billing Dashboard
- **What it is:** Overview of current month's charges
- **Exam keyword:** "view current bill", "billing overview"

### Cost Allocation Tags
- **What it is:** Tag resources to track costs by project/team/env
- **Exam keyword:** "track costs by department", "cost allocation by team"

### Consolidated Billing (AWS Organizations)
- **What it is:** One bill for all accounts in AWS Organizations
- **Benefits:** Single payment, volume discounts, share Reserved Instance savings
- **Exam keyword:** "one bill for multiple accounts", "volume discounts"

---

## 4.5 AWS Support Plans ⭐ MEMORIZE THIS TABLE

| Feature | Basic | Developer | Business | Enterprise On-Ramp | Enterprise |
|---|---|---|---|---|---|
| **Cost** | FREE | $29/month | $100/month | $5,500/month | $15,000/month |
| **Tech Support** | No | Business hours (1 person) | 24/7 phone/chat | 24/7 | 24/7 |
| **Response - Critical** | No | No | < 1 hour | < 30 min | < 15 min |
| **Response - Urgent** | No | No | < 4 hours | < 4 hours | < 1 hour |
| **Trusted Advisor** | 7 checks | 7 checks | All checks | All checks | All checks |
| **TAM (Technical Account Manager)** | No | No | No | Pool of TAMs | Dedicated TAM |
| **Infrastructure Event Management** | No | No | Extra fee | Included | Included |

> ⚡ **EXAM TRIGGERS:**
> - "Dedicated TAM" → **Enterprise**
> - "24/7 phone support" → **Business or higher**
> - "Cheapest with tech support" → **Developer**
> - "All Trusted Advisor checks" → **Business or higher**
> - "< 15 minute response" → **Enterprise**

---

## 4.6 TCO (Total Cost of Ownership)

- **What it is:** Compare on-premises costs vs AWS cloud costs
- **AWS Migration Evaluator (formerly TSO Logic):** Tool to calculate TCO
- **Factors included:** Hardware, software, power, cooling, facility, staffing
- **Key point:** Cloud often cheaper when you include ALL on-prem hidden costs

---

# 🔄 COMMON CONFUSIONS & FAST DECISION RULES

## ⚡ EXAM TRIGGERS — "If question says X, choose Y"

| If the question mentions... | Choose... |
|---|---|
| "Serverless compute" | Lambda |
| "Serverless containers" | Fargate |
| "NoSQL, millisecond latency" | DynamoDB |
| "In-memory cache, reduce DB load" | ElastiCache |
| "Data warehouse, analytics" | Redshift |
| "DDoS protection" | Shield |
| "Block SQL injection, web attacks" | WAF |
| "Store encryption keys" | KMS |
| "Store DB password, auto-rotate" | Secrets Manager |
| "Detect sensitive data in S3" | Macie |
| "Detect threats, suspicious activity" | GuardDuty |
| "Vulnerability scanning" | Inspector |
| "Audit API calls, who did what" | CloudTrail |
| "Monitor metrics, set alarms" | CloudWatch |
| "Best practices recommendations" | Trusted Advisor |
| "Compliance reports, SOC2, ISO" | AWS Artifact |
| "Config compliance, did it change?" | AWS Config |
| "CDN, cache at edge, global delivery" | CloudFront |
| "DNS, domain name" | Route 53 |
| "Multiple accounts, one bill" | AWS Organizations + Consolidated Billing |
| "Restrict what accounts can do" | SCP (Service Control Policy) |
| "Move data offline, no internet" | Snow Family |
| "Dedicated connection, not internet" | Direct Connect |
| "Encrypted connection over internet" | VPN |
| "Estimate costs before building" | AWS Pricing Calculator |
| "Analyze past spending" | Cost Explorer |
| "Alert when spending too much" | AWS Budgets |
| "Shared file system, multiple EC2s" | EFS |
| "EC2 storage, one instance" | EBS |
| "Object storage, images, backups" | S3 |
| "Deploy app without managing servers" | Elastic Beanstalk |
| "Container orchestration, Kubernetes" | EKS |
| "Container orchestration, AWS-native" | ECS |
| "Infrastructure as Code" | CloudFormation |
| "Connect on-prem to cloud storage" | Storage Gateway |
| "Virtual desktop for remote workers" | WorkSpaces |
| "Chatbot, conversational AI" | Lex |
| "Image recognition, facial analysis" | Rekognition |
| "Build train deploy ML models" | SageMaker |
| "Real-time data streaming" | Kinesis |
| "Event-driven automation" | EventBridge |
| "1-to-many notifications, pub/sub" | SNS |
| "Decouple services, message queue" | SQS |

---

## 🔴 FREQUENTLY CONFUSED SERVICE PAIRS

### 1. EC2 vs Lambda

| | EC2 | Lambda |
|---|---|---|
| Server | You manage | AWS manages |
| Duration | Always running | Up to 15 min |
| Billing | Per hour | Per invocation |
| Best for | Long-running apps | Event-driven tasks |
| Clue | "persistent server" | "run code", "serverless" |

---

### 2. SQS vs SNS

| | SQS | SNS |
|---|---|---|
| Pattern | Queue (1-to-1) | Pub/Sub (1-to-many) |
| Storage | Stores messages | No storage |
| Delivery | Pull (consumer fetches) | Push (instant broadcast) |
| Best for | Decouple two services | Alert/notify many |
| Clue | "queue", "decouple" | "notification", "fan-out" |

---

### 3. EBS vs EFS vs S3

| | EBS | EFS | S3 |
|---|---|---|---|
| Type | Block | File | Object |
| Multi-access | No (1 EC2) | Yes (many EC2s) | Yes (internet) |
| Use case | EC2 hard drive | Shared files | Store any file/object |

---

### 4. RDS vs DynamoDB

| | RDS / Aurora | DynamoDB |
|---|---|---|
| Type | Relational / SQL | NoSQL |
| Schema | Fixed | Flexible |
| Scaling | Vertical + Read Replicas | Auto/serverless |
| Best for | Complex queries, OLTP | High-scale, simple access |

---

### 5. CloudWatch vs CloudTrail

| | CloudWatch | CloudTrail |
|---|---|---|
| Tracks | Performance metrics, logs | API calls, user activity |
| Answers | "Is CPU high?" | "Who deleted this?" |
| Use case | Monitoring | Auditing |
| Analogy | Health dashboard | Security camera |

---

### 6. Shield vs WAF

| | Shield | WAF |
|---|---|---|
| Protects against | DDoS attacks | SQL injection, XSS |
| Layer | 3/4 (Network) | 7 (Application) |
| Auto-enabled | Standard = Yes | No, configure rules |

---

### 7. CloudFront vs Global Accelerator

| | CloudFront | Global Accelerator |
|---|---|---|
| Best for | Static content, caching | Dynamic content, TCP/UDP |
| Uses | Edge cache | AWS backbone |
| Clue | "CDN", "cache", "files" | "speed for all content", "TCP" |

---

### 8. Direct Connect vs VPN

| | Direct Connect | VPN |
|---|---|---|
| Connection | Dedicated private line | Encrypted over internet |
| Speed | Consistent, high | Variable |
| Setup time | Weeks | Hours/minutes |
| Cost | Higher | Lower |
| Clue | "dedicated", "reliable" | "quick", "encrypted" |

---

### 9. Inspector vs GuardDuty vs Macie

| | Inspector | GuardDuty | Macie |
|---|---|---|---|
| Finds | Vulnerabilities in EC2/containers | Threats and suspicious behavior | Sensitive data in S3 |
| Clue | "CVE", "patch needed" | "hacker", "anomaly" | "PII", "GDPR", "S3 data" |

---

## 🚦 FAST DECISION RULES

### Storage Decision Tree
```
Need to store data?
├── Files for multiple EC2 instances? → EFS
├── Attached to one EC2 (OS/DB)? → EBS
├── Store objects (images, backups, static site)? → S3
│   ├── Frequent access? → S3 Standard
│   ├── Unknown access? → S3 Intelligent-Tiering
│   ├── Rare access, instant needed? → S3 Glacier Instant Retrieval
│   └── Long-term archive, hours OK? → S3 Glacier Deep Archive
└── Move data offline (no internet)? → Snow Family
```

### Compute Decision Tree
```
Need to run code/app?
├── Full control, custom OS? → EC2
├── Short-running, event-triggered? → Lambda (serverless)
├── Deploy app, no infra management? → Elastic Beanstalk
├── Containers?
│   ├── Kubernetes? → EKS
│   ├── AWS-managed? → ECS
│   └── No servers for containers? → Fargate
└── Simple hosting for beginners? → Lightsail
```

### Database Decision Tree
```
Need a database?
├── Relational / SQL?
│   ├── High performance, cloud-native? → Aurora
│   ├── Managed SQL? → RDS
│   └── Analytics / data warehouse? → Redshift
├── NoSQL / key-value? → DynamoDB
├── In-memory cache? → ElastiCache
└── Search (full-text)? → OpenSearch (Elasticsearch)
```

---

# 🔥 FINAL REVISION SECTIONS

## ⚡ ONE-PAGE ULTRA FAST REVISION SHEET

### CLOUD MODELS
- **IaaS** = EC2 | **PaaS** = Elastic Beanstalk | **SaaS** = Gmail/Salesforce
- **Public** = AWS | **Private** = On-prem | **Hybrid** = Both

### COMPUTE
- **EC2** = Virtual server | **Lambda** = Serverless functions | **Beanstalk** = PaaS deployment
- **ECS** = Docker/AWS | **EKS** = Kubernetes | **Fargate** = Serverless containers

### STORAGE
- **S3** = Object storage (ANY file) | **EBS** = EC2 disk | **EFS** = Shared file system
- **Glacier** = Archive | **Snow** = Offline data transfer

### DATABASES
- **RDS/Aurora** = SQL | **DynamoDB** = NoSQL | **Redshift** = Analytics/DW
- **ElastiCache** = In-memory cache

### NETWORKING
- **VPC** = Private network | **IGW** = Public internet | **NAT GW** = Private → internet
- **Route 53** = DNS | **CloudFront** = CDN | **ELB** = Load balancer | **Direct Connect** = Private line

### SECURITY
- **IAM** = Access control | **KMS** = Encryption keys | **WAF** = Block web attacks
- **Shield** = DDoS | **GuardDuty** = Threat detection | **Inspector** = Vulnerability scan
- **Macie** = S3 sensitive data | **Cognito** = App user auth | **Artifact** = Compliance docs

### MESSAGING
- **SQS** = Queue (1-to-1, pull) | **SNS** = Pub/Sub (1-to-many, push)
- **EventBridge** = Event bus | **Kinesis** = Real-time streaming

### MONITORING
- **CloudWatch** = Metrics & alarms | **CloudTrail** = API audit log
- **Trusted Advisor** = Best practice recommendations | **Config** = Compliance tracking

### BILLING
- **Free Tier** = 12 months + always free | **Cost Explorer** = Analyze spending
- **Budgets** = Set alerts | **Pricing Calculator** = Estimate before building
- **Consolidated Billing** = One bill for all accounts

### SUPPORT PLANS (cheapest → priciest)
Basic (free) → Developer ($29) → Business ($100) → Enterprise On-Ramp ($5,500) → Enterprise ($15,000)

---

## 📋 TOP 30 MUST-MEMORIZE SERVICES

| # | Service | One-Line Summary |
|---|---|---|
| 1 | **EC2** | Virtual servers in the cloud |
| 2 | **Lambda** | Serverless code execution |
| 3 | **S3** | Object storage for any file |
| 4 | **RDS** | Managed relational SQL database |
| 5 | **DynamoDB** | Serverless NoSQL database |
| 6 | **VPC** | Your private network on AWS |
| 7 | **IAM** | Control who accesses what |
| 8 | **CloudFront** | CDN for global content delivery |
| 9 | **Route 53** | DNS and domain management |
| 10 | **ELB** | Distribute traffic across resources |
| 11 | **Auto Scaling** | Auto add/remove EC2 capacity |
| 12 | **SQS** | Message queue between services |
| 13 | **SNS** | Pub/Sub notifications to many |
| 14 | **CloudWatch** | Monitor metrics and set alarms |
| 15 | **CloudTrail** | Audit log of all API calls |
| 16 | **EBS** | Disk storage attached to EC2 |
| 17 | **EFS** | Shared file storage for EC2s |
| 18 | **Shield** | DDoS protection |
| 19 | **WAF** | Block web application attacks |
| 20 | **KMS** | Manage encryption keys |
| 21 | **Trusted Advisor** | Best practice recommendations |
| 22 | **Elastic Beanstalk** | PaaS app deployment |
| 23 | **ECS / EKS** | Container orchestration |
| 24 | **Redshift** | Data warehouse for analytics |
| 25 | **Cognito** | User authentication for apps |
| 26 | **Aurora** | AWS high-performance SQL DB |
| 27 | **GuardDuty** | Threat detection and monitoring |
| 28 | **AWS Organizations** | Multi-account management |
| 29 | **Cost Explorer** | Analyze AWS spending |
| 30 | **CloudFormation** | Infrastructure as Code |

---

## 🗝️ TOP 50 EXAM KEYWORDS

| Keyword | Points To |
|---|---|
| Serverless | Lambda, DynamoDB, Aurora Serverless, Fargate |
| DDoS | Shield |
| SQL injection / XSS | WAF |
| Encryption keys | KMS |
| Audit / who did what | CloudTrail |
| Performance metrics / alarms | CloudWatch |
| Best practices check | Trusted Advisor |
| Compliance reports | AWS Artifact |
| Config compliance / history | AWS Config |
| Sensitive data in S3 | Macie |
| Threat detection | GuardDuty |
| Vulnerability scanning | Inspector |
| CDN / low latency globally | CloudFront |
| DNS | Route 53 |
| Pub/Sub / 1-to-many | SNS |
| Queue / decouple / 1-to-1 | SQS |
| NoSQL / key-value | DynamoDB |
| Relational / SQL | RDS or Aurora |
| Analytics / data warehouse | Redshift |
| In-memory cache | ElastiCache |
| Object storage | S3 |
| Archive storage | S3 Glacier |
| Block storage / EC2 disk | EBS |
| Shared file system | EFS |
| Offline data transfer | Snow Family |
| Hybrid storage | Storage Gateway |
| Dedicated network connection | Direct Connect |
| Encrypted over internet | VPN |
| Virtual server | EC2 |
| Containers + Kubernetes | EKS |
| Containers + AWS managed | ECS |
| Serverless containers | Fargate |
| PaaS / deploy app fast | Elastic Beanstalk |
| IaC / Infrastructure as code | CloudFormation |
| App user login | Cognito |
| Multiple accounts | AWS Organizations |
| Restrict account permissions | SCP |
| One bill for accounts | Consolidated Billing |
| Estimate costs | AWS Pricing Calculator |
| Analyze past spending | Cost Explorer |
| Alert on overspending | AWS Budgets |
| Spot capacity / cheapest | Spot Instances |
| Predictable, steady workload | Reserved Instances |
| Dedicated hardware | Dedicated Host |
| Real-time streaming | Kinesis |
| Event bus / triggers | EventBridge |
| Load balancer HTTP/HTTPS | ALB |
| Load balancer TCP/UDP | NLB |
| Virtual desktop | WorkSpaces |
| Chatbot | Lex |

---

## ✅ 5-MINUTE PRE-EXAM CHECKLIST

### Cloud Concepts
- [ ] 6 advantages of cloud ✓
- [ ] IaaS vs PaaS vs SaaS ✓
- [ ] Region vs AZ vs Edge Location counts ✓
- [ ] HA vs Fault Tolerance vs Elasticity ✓
- [ ] 6 R's of migration ✓
- [ ] Serverless = no server management ✓

### Security & Compliance
- [ ] Shared Responsibility: AWS = OF cloud, You = IN cloud ✓
- [ ] EC2 OS patching = YOUR job ✓
- [ ] RDS engine patching = AWS's job ✓
- [ ] Root account = lock it, use MFA ✓
- [ ] IAM Role = no password, for services ✓
- [ ] Least privilege principle ✓
- [ ] Shield = DDoS | WAF = web attacks ✓
- [ ] GuardDuty = threats | Inspector = vulnerabilities | Macie = S3 data ✓
- [ ] CloudTrail = audit log | CloudWatch = performance ✓
- [ ] KMS = encryption keys | Secrets Manager = passwords ✓
- [ ] Artifact = compliance docs (no scanning) ✓
- [ ] SCP = restrict org accounts ✓

### Technology
- [ ] EC2 pricing: On-Demand/Reserved/Spot/Savings Plans ✓
- [ ] S3 storage classes (Standard → Glacier Deep Archive) ✓
- [ ] EBS = 1 EC2 | EFS = many EC2s | S3 = objects ✓
- [ ] RDS = SQL | DynamoDB = NoSQL | Redshift = analytics ✓
- [ ] SQS = queue (pull) | SNS = pub/sub (push) ✓
- [ ] CloudFront = CDN | Global Accelerator = AWS backbone ✓
- [ ] Security Group = stateful | NACL = stateless ✓
- [ ] Lambda max = 15 min | Beanstalk = PaaS ✓

### Billing & Support
- [ ] Data IN = free | Data OUT = paid ✓
- [ ] Support plans order + prices ✓
- [ ] Enterprise = dedicated TAM ✓
- [ ] Business = all Trusted Advisor checks ✓
- [ ] Cost Explorer = analyze | Budgets = alert | Pricing Calculator = estimate ✓
- [ ] Consolidated billing = volume discounts ✓
- [ ] Spot = 90% off, can interrupt | Reserved = 72% off, commit ✓

---

## 🎯 MOST ASKED QUESTION PATTERNS

### Pattern 1: "Which service provides X?" (Service Selection)
> Match the description to the service. Use the Exam Triggers table.
> **Example:** "Which service allows you to analyze spending trends?" → Cost Explorer

### Pattern 2: "A company needs to..." (Scenario-Based)
> Identify the core need: storage? compute? security? database?
> Then pick the best-fit service.
> **Example:** "A company needs to run a containerized app without managing servers" → Fargate

### Pattern 3: "Who is responsible for X?" (Shared Responsibility)
> **Physical infra, hardware, managed service software** → AWS
> **OS, app, data, IAM, security groups, encryption settings** → Customer
> **Example:** "Who patches EC2 operating systems?" → Customer

### Pattern 4: "What is the cheapest option for...?" (Pricing)
> For compute: Spot (cheapest but can interrupt) → Reserved → Savings Plans → On-Demand
> For storage: S3 Glacier Deep Archive (cheapest for archiving)
> For support: Basic (free, but no tech support)

### Pattern 5: "How to improve performance/availability?" (Architecture)
> Performance → ElastiCache, CloudFront, Read Replicas, Global Accelerator
> Availability → Multi-AZ, Auto Scaling, ELB
> Cost reduction → Reserved/Spot Instances, S3 lifecycle rules, Trusted Advisor

### Pattern 6: "Which pillar of Well-Architected Framework?" (Framework)
> Cost savings → Cost Optimization
> Recover from failure → Reliability
> Protect data → Security
> Use right resources → Performance Efficiency
> Day-to-day operations → Operational Excellence

### Pattern 7: "Multi-select — choose 2/3 answers"
> Eliminate obviously wrong options first.
> Look for complementary services that work together.
> **Example:** "Two ways to reduce EC2 costs" → Spot Instances + Reserved Instances

---

## 🧠 FINAL MEMORY AIDS

### Shared Responsibility Shortcut
> **AWS = The building** (physical security, locks, electricity)
> **You = Your apartment** (furniture, guests, door chain)

### Cloud Benefits Shortcut: **"TSGIAS"**
> **T**rade capex for opex | **S**cale economies | **G**uess no capacity | **I**ncrease agility | **A**void data center costs | **S**cale globally

### Support Plans Shortcut: **"BDBEE"**
> **B**asic (free) → **D**eveloper ($29) → **B**usiness ($100) → **E**nterprise On-Ramp ($5.5K) → **E**nterprise ($15K)

### 6 R's Shortcut: **"Re-Re-Re-Re-Re-Re"**
> Rehost | Replatform | Repurchase | Refactor | Retire | Retain

### Storage Class Shortcut (expensive → cheap):
> Standard → Intelligent-Tiering → Standard-IA → One Zone-IA → Glacier Instant → Glacier Flexible → **Glacier Deep Archive**

---

> 🏆 **FINAL TIP:** The CLF-C02 is about RECOGNIZING the right service for a scenario — not deep technical knowledge. Focus on:
> 1. **What each service does** (one sentence)
> 2. **When to use it** (keywords)
> 3. **What it's NOT for** (avoid traps)
>
> You've got this. Go pass that exam! ☁️✅

---
*AWS Certified Cloud Practitioner (CLF-C02) Ultimate Cheat Sheet*
*Aligned with official exam guide domains. Study smart, not hard.*
