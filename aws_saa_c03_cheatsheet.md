# AWS Certified Solutions Architect Associate (SAA-C03) — Complete Exam Cheatsheet

**Pass-Only Study Guide | 100% Exam Coverage**

---

## Table of Contents

1. [Core Services](#section-1-core-services)
2. [Visual Architecture Thinking](#section-2-visual-architecture-thinking)
3. [High-Yield Comparison Tables](#section-3-high-yield-comparison-tables)
4. [Decision Trees](#section-4-decision-trees)
5. [Real Exam Scenarios](#section-5-real-exam-scenarios)
6. [Trick Questions & Exam Traps](#section-6-trick-questions--exam-traps)
7. [Memory Hacks](#section-7-memory-hacks)
8. [Hands-On Lab Scenarios](#section-8-hands-on-lab-scenarios)
9. [Rapid Revision](#section-9-rapid-revision)

---

# SECTION 1: CORE SERVICES

## COMPUTE

### EC2 (Elastic Compute Cloud)

**What it is:** Virtual servers in the cloud

**When to use:**
- Need full OS-level control
- Custom applications or legacy software
- Long-running workloads
- Applications requiring specific instance types (GPU, memory-optimized)

**When NOT to use:**
- Serverless workload → use Lambda
- Fully managed web apps → use Elastic Beanstalk
- Batch processing under 15 minutes → use Lambda

**Key features tested:**
- **Instance types:** General (t3, m5), Compute (c5), Memory (r5), Storage (i3), GPU (p3, g4)
- **Pricing:** On-Demand (pay per hour), Reserved (1-3 year commitment, 40-60% discount), Spot (up to 90% discount, can be terminated), Savings Plans, Dedicated Hosts
- **Placement groups:** Cluster (low latency, same AZ), Spread (different hardware, max 7 per AZ), Partition (different racks, good for Hadoop/Cassandra)
- **User Data:** Bootstrap script that runs at first launch only

**Common traps:**
- Reserved Instances are NOT physical reservations—they're billing discounts
- Spot instances can be interrupted with 2-minute warning
- Changing instance type requires stop → change → start (not available for instance store)

**Comparison:**
- vs Lambda: EC2 for long-running, Lambda for event-driven
- vs Lightsail: EC2 for complex apps, Lightsail for simple websites
- vs Elastic Beanstalk: EC2 for full control, EB for managed deployment

---

### Auto Scaling

**What it is:** Automatically adjusts EC2 instance count based on demand

**When to use:**
- Variable traffic patterns
- Need high availability across AZs
- Cost optimization (scale down during low usage)

**When NOT to use:**
- Stable, predictable load → fixed number of instances
- Serverless option available → use Lambda with API Gateway

**Key features tested:**
- **Scaling policies:** Target tracking (maintain metric at target), Step (add/remove based on CloudWatch alarms), Simple (legacy), Scheduled
- **Launch Templates** (newer, recommended) vs Launch Configurations (legacy)
- **Cooldown period:** Prevents rapid scaling actions (default 300 sec)
- **Health checks:** EC2 status check or ELB health check
- **Termination policy:** Default is oldest launch configuration, then closest to next billing hour

**Common traps:**
- Auto Scaling does NOT provide load balancing (use with ELB)
- Desired capacity can be manually adjusted (bypasses policies temporarily)
- Minimum capacity is respected even if no traffic

**Exam scenario:**
- "Application needs to handle traffic spikes" → Auto Scaling + ALB
- "Reduce costs during nights/weekends" → Scheduled scaling

---

### Lambda

**What it is:** Run code without managing servers (serverless compute)

**When to use:**
- Event-driven architectures
- Short-running tasks (<15 minutes)
- Intermittent workloads
- API backends (with API Gateway)
- Data processing (S3 uploads, DynamoDB streams)

**When NOT to use:**
- Tasks over 15 minutes → use ECS/Fargate or EC2
- Need persistent connections → use EC2
- Heavy compute needing GPU → use EC2 with GPU instances

**Key features tested:**
- **Runtime limit:** 15 minutes maximum
- **Memory:** 128 MB to 10,240 MB (CPU scales with memory)
- **Pricing:** Per request + compute time (GB-seconds), free tier: 1M requests + 400,000 GB-seconds/month
- **Concurrency:** 1000 concurrent executions per region (can request increase)
- **Environment variables:** Store config, not secrets (use Secrets Manager)
- **Layers:** Share code/dependencies across functions
- **Versions & Aliases:** Blue-green deployments

**Common traps:**
- Cold starts add latency (provisioned concurrency solves this but costs more)
- Must use /tmp for temporary storage (512 MB limit)
- Cannot directly access resources in private subnet without VPC config (and NAT for internet access)

**Comparison:**
- vs EC2: Lambda for event-driven, EC2 for always-on
- vs Fargate: Lambda for <15 min, Fargate for containers
- vs Step Functions: Lambda for single task, Step Functions for workflows

**Exam scenario:**
- "Process S3 uploads in real-time" → S3 event → Lambda
- "Resize images when uploaded" → Lambda
- "Need to run every hour" → EventBridge rule → Lambda

---

### Elastic Beanstalk

**What it is:** Platform as a Service (PaaS) for deploying web apps

**When to use:**
- Quick deployment without infrastructure management
- Standard web apps (Node.js, Python, Java, .NET, PHP, Ruby, Go)
- Developers who want to focus on code, not infrastructure

**When NOT to use:**
- Need low-level infrastructure control → use EC2
- Serverless architecture → use Lambda + API Gateway
- Complex multi-tier architectures → use CloudFormation or custom setup

**Key features tested:**
- **Deployment policies:** All at once (downtime), Rolling (batch updates), Rolling with additional batch (no capacity reduction), Immutable (new instances, then swap), Blue-green (manual via swap URLs)
- **Auto-handles:** Load balancing, auto scaling, monitoring, platform updates
- **Worker tier:** For background jobs (pulls from SQS)
- **Web tier:** For HTTP traffic
- Free service (pay only for underlying resources like EC2, ELB)

**Common traps:**
- You still pay for EC2, RDS, and other resources
- Elastic Beanstalk is a deployment tool, not a compute service
- Can customize with .ebextensions config files

**Exam scenario:**
- "Developer needs to quickly deploy web app without managing infrastructure" → Elastic Beanstalk
- "Need zero-downtime deployment" → Immutable or Blue-green

---

## STORAGE

### S3 (Simple Storage Service)

**What it is:** Object storage for any type of data

**When to use:**
- Static website hosting
- Backup and archival
- Data lakes and big data analytics
- Media hosting (images, videos)
- Distributing software

**When NOT to use:**
- Need file system (mount as drive) → use EFS
- Need block storage → use EBS
- Database storage → use RDS or DynamoDB
- High-frequency small updates → use EFS or database

**Key features tested:**
- **Storage classes:**
  - **Standard:** Frequent access, low latency, 99.99% availability
  - **Intelligent-Tiering:** Auto-moves between tiers based on access patterns (small monitoring fee)
  - **Standard-IA (Infrequent Access):** Less frequent access, cheaper storage, retrieval fee
  - **One Zone-IA:** Single AZ, 20% cheaper than Standard-IA, less durability
  - **Glacier Instant Retrieval:** Archive with millisecond retrieval, 68% cheaper than Standard-IA
  - **Glacier Flexible Retrieval:** Archive, retrieval in minutes to hours
  - **Glacier Deep Archive:** Cheapest, retrieval in 12-48 hours, for compliance/long-term backup
- **Versioning:** Keep multiple versions of objects (protects against deletes)
- **Lifecycle policies:** Automatically transition objects between storage classes or delete
- **Encryption:**
  - **Server-side:** SSE-S3 (S3-managed keys), SSE-KMS (AWS KMS keys), SSE-C (customer-provided keys)
  - **Client-side:** Encrypt before uploading
- **Access control:** Bucket policies, IAM policies, ACLs (legacy), pre-signed URLs (temporary access)
- **Static website hosting:** Index document, error document, custom domain via Route 53
- **Cross-Region Replication (CRR):** Replicate to different region (compliance, latency)
- **Same-Region Replication (SRR):** Replicate within region (log aggregation, prod/test sync)
- **Transfer Acceleration:** Uses CloudFront edge locations for faster uploads

**Common traps:**
- S3 is NOT a file system—you can't mount it as a drive
- Bucket names are globally unique
- Eventual consistency for overwrite PUTS and DELETES (now rare with strong consistency)
- Lifecycle transitions only go in one direction (can't go back from Glacier to Standard)

**Comparison:**
- vs EBS: S3 for objects, EBS for block storage attached to EC2
- vs EFS: S3 for object storage, EFS for shared file system
- vs Glacier: S3 for active data, Glacier for archival

**Exam scenarios:**
- "Store user-uploaded photos" → S3
- "Host static website" → S3 + Route 53
- "Archive logs for 7 years (rare access)" → Glacier Deep Archive
- "Automatically delete objects after 90 days" → Lifecycle policy
- "Comply with regulation requiring data in multiple regions" → CRR

---

### EBS (Elastic Block Store)

**What it is:** Block storage volumes for EC2 instances (like virtual hard drives)

**When to use:**
- Boot volumes for EC2
- Databases on EC2
- Need persistent storage for EC2
- High IOPS requirements

**When NOT to use:**
- Shared storage across instances → use EFS
- Object storage → use S3
- Data archival → use S3 Glacier
- Temporary storage → use instance store

**Key features tested:**
- **Volume types:**
  - **gp3:** General purpose SSD, baseline 3000 IOPS (can increase independently), cost-effective
  - **gp2:** General purpose SSD, 3 IOPS per GB (min 100 IOPS), older generation
  - **io2/io1:** Provisioned IOPS SSD, up to 64,000 IOPS (io2 Block Express: 256,000 IOPS), databases
  - **st1:** Throughput-optimized HDD, big data, data warehouses, cannot be boot volume
  - **sc1:** Cold HDD, lowest cost, infrequent access, cannot be boot volume
- **Snapshots:** Incremental backups to S3, can copy across regions
- **Encryption:** At-rest and in-transit, uses KMS
- **Multi-Attach:** io1/io2 only, attach single volume to multiple instances in same AZ
- **Attached to one instance** at a time (except io1/io2 with Multi-Attach)
- **AZ-specific:** Cannot directly attach to instance in different AZ (must snapshot and restore)

**Common traps:**
- Cannot attach EBS volume to instance in different AZ
- Snapshots are stored in S3 but not visible in S3 console
- Deleting an instance doesn't automatically delete attached EBS (unless "Delete on Termination" is set)
- Root volumes default to "Delete on Termination" = true

**Comparison:**
- vs Instance Store: EBS persists, Instance Store lost on stop/terminate
- vs EFS: EBS single instance, EFS multiple instances
- vs S3: EBS block storage, S3 object storage

**Exam scenarios:**
- "Database needs 20,000 IOPS" → io2
- "Cost-effective boot volume" → gp3
- "Share storage across multiple EC2 in same AZ" → io2 with Multi-Attach (or use EFS)
- "Backup EC2 volumes to another region" → snapshot → copy snapshot

---

### EFS (Elastic File System)

**What it is:** Managed NFS (network file system) that can be mounted to multiple EC2 instances

**When to use:**
- Shared file storage across multiple EC2 instances
- Content management systems
- Web serving (shared files across web servers)
- Big data applications
- Container persistent storage (ECS, EKS)

**When NOT to use:**
- Single EC2 instance → use EBS (cheaper)
- Windows instances → use FSx for Windows
- Object storage → use S3
- High-frequency writes from single instance → use EBS

**Key features tested:**
- **Supports thousands of concurrent connections** across multiple AZs
- **Performance modes:** General Purpose (default, low latency), Max I/O (higher latency, higher throughput, big data)
- **Throughput modes:** Bursting (scales with size), Provisioned (fixed throughput), Elastic (auto-scales)
- **Storage classes:** Standard (multi-AZ), One Zone (cheaper, single AZ), Infrequent Access (IA)
- **Lifecycle management:** Auto-move files to IA tier
- **Linux-based:** Uses NFS protocol
- **Encryption:** At-rest and in-transit

**Common traps:**
- Much more expensive than EBS (about 3x)
- Not supported on Windows natively (need FSx for Windows)
- One Zone EFS is cheaper but less durable

**Comparison:**
- vs EBS: EFS multi-instance, EBS single instance
- vs S3: EFS file system, S3 object storage
- vs FSx: EFS for Linux, FSx for Windows

**Exam scenarios:**
- "Multiple web servers need shared storage" → EFS
- "WordPress site with uploads directory shared across instances" → EFS
- "Linux-based application needs shared file system" → EFS

---

### FSx

**What it is:** Managed file systems for specialized workloads

**Types:**
- **FSx for Windows File Server:** SMB protocol, Windows-native, integrates with Active Directory
- **FSx for Lustre:** High-performance computing (HPC), machine learning, video processing, integrates with S3
- **FSx for NetApp ONTAP:** Multiprotocol (NFS, SMB), advanced features (snapshots, cloning)
- **FSx for OpenZFS:** High-performance, point-in-time snapshots

**When to use:**
- **FSx for Windows:** Windows-based applications, Active Directory integration, need SMB protocol
- **FSx for Lustre:** HPC workloads, machine learning training, data processing linked to S3

**When NOT to use:**
- Linux shared storage → use EFS
- Object storage → use S3
- Block storage → use EBS

**Key features tested:**
- FSx for Windows: Multi-AZ for HA, integrates with AD, DFS namespaces
- FSx for Lustre: Sub-millisecond latencies, can read/write directly to S3, SSD or HDD options

**Exam scenarios:**
- "Windows application needs shared file storage with AD integration" → FSx for Windows
- "Machine learning training needs high-speed access to S3 data" → FSx for Lustre

---

### S3 Glacier

**What it is:** Low-cost archival storage (part of S3 storage classes)

**When to use:**
- Long-term backup
- Compliance archival (7-10+ years)
- Rarely accessed data
- Data that can tolerate hours of retrieval time

**When NOT to use:**
- Frequently accessed → use S3 Standard
- Need instant access → use S3 Intelligent-Tiering or Instant Retrieval

**Key features tested:**
- **Glacier Instant Retrieval:** Millisecond retrieval, quarterly access, 68% cheaper than Standard-IA
- **Glacier Flexible Retrieval:** Minutes to hours retrieval, retrieval options (Expedited 1-5 min, Standard 3-5 hrs, Bulk 5-12 hrs)
- **Glacier Deep Archive:** 12-48 hours retrieval, lowest cost, for compliance/long-term storage
- **Vault Lock:** WORM (write once, read many) for compliance

**Exam scenarios:**
- "Store financial records for 10 years, rarely accessed" → Glacier Deep Archive
- "Archive data but need quarterly access" → Glacier Instant Retrieval

---

## DATABASES

### RDS (Relational Database Service)

**What it is:** Managed SQL databases (MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora)

**When to use:**
- Traditional relational databases
- ACID transactions
- Complex queries and joins
- Existing applications using SQL

**When NOT to use:**
- NoSQL requirements → use DynamoDB
- Extreme scale (millions of requests/sec) → use DynamoDB
- Data warehouse → use Redshift
- Full control over DB instance → use EC2 with self-managed DB

**Key features tested:**
- **Multi-AZ:** Synchronous replication to standby in different AZ, automatic failover, for high availability (NOT for read scaling)
- **Read Replicas:** Asynchronous replication, up to 15 replicas (Aurora), for read scaling, can be in different region
- **Backups:** Automated (1-35 days retention) and manual snapshots (keep until deleted)
- **Storage:** gp3, gp2, io1, magnetic (legacy)
- **Encryption:** At-rest (KMS), in-transit (SSL/TLS)
- **Automated patching:** During maintenance window

**Common traps:**
- Multi-AZ is for failover, NOT for load distribution (use Read Replicas for that)
- Read Replicas are eventually consistent
- Promoting a Read Replica breaks replication
- Can't SSH into RDS instances
- Oracle/SQL Server have license costs

**Comparison:**
- vs DynamoDB: RDS for relational/complex queries, DynamoDB for simple key-value/scale
- vs Aurora: Aurora is faster, more expensive, AWS-optimized
- vs Redshift: RDS for OLTP, Redshift for OLAP/analytics
- vs EC2 database: RDS for managed, EC2 for full control

**Exam scenarios:**
- "Need high availability for production database" → Multi-AZ
- "Application has heavy read traffic" → Read Replicas
- "Need to scale reads globally" → Cross-region Read Replicas
- "Disaster recovery in another region" → Cross-region Read Replica or snapshot copy

---

### Aurora

**What it is:** AWS-designed MySQL and PostgreSQL compatible database (5x MySQL, 3x PostgreSQL performance)

**When to use:**
- Need high performance and availability
- MySQL or PostgreSQL compatibility required
- Auto-scaling storage (up to 128 TB)
- Heavy read workloads

**When NOT to use:**
- Oracle/SQL Server required → use RDS
- Budget is tight → use RDS MySQL/PostgreSQL (Aurora is 20% more expensive)
- NoSQL → use DynamoDB

**Key features tested:**
- **Storage:** Auto-scales in 10 GB increments up to 128 TB
- **6 copies of data across 3 AZs** (2 copies per AZ)
- **Up to 15 Read Replicas** with <10ms lag
- **Aurora Serverless:** Auto-scales capacity, pay per second, good for infrequent/unpredictable workloads
- **Aurora Global Database:** Reads in <1 second from remote region, disaster recovery
- **Backtrack:** Rewind database to any point in time without using backups
- **Faster failover** than RDS Multi-AZ (<30 seconds)

**Common traps:**
- Only supports MySQL and PostgreSQL
- More expensive than standard RDS
- Serverless v1 has cold start delays (v2 is better)

**Exam scenarios:**
- "MySQL database needs to handle massive read traffic" → Aurora with Read Replicas
- "Database storage needs to auto-scale" → Aurora
- "Infrequent workload, don't want to pay for idle" → Aurora Serverless
- "Global application needs local reads" → Aurora Global Database

---

### DynamoDB

**What it is:** Fully managed NoSQL key-value and document database

**When to use:**
- Serverless applications
- Need single-digit millisecond latency at any scale
- Key-value or document data model
- Massive scale (millions of requests/second)
- Gaming, IoT, mobile backends

**When NOT to use:**
- Complex queries and joins → use RDS
- ACID transactions across multiple tables → use RDS
- Traditional SQL requirements → use RDS

**Key features tested:**
- **Capacity modes:** On-Demand (pay per request, unpredictable traffic), Provisioned (specify RCU/WCU, cheaper for predictable traffic)
- **Read consistency:** Eventually consistent (default, cheaper) or Strongly consistent (costs 2x RCU)
- **DynamoDB Streams:** Capture changes for 24 hours, trigger Lambda
- **Global Tables:** Multi-region, multi-active (write to any region), sub-second replication
- **DAX (DynamoDB Accelerator):** In-memory cache, microsecond latency, doesn't require code changes
- **Backup:** On-demand backups and Point-in-Time Recovery (PITR, up to 35 days)
- **Encryption:** At-rest (KMS), in-transit

**Common traps:**
- Hot partition causes throttling (distribute keys evenly)
- Scan operation reads entire table (expensive), use Query instead
- Cannot query on non-key attributes without secondary index
- Global Secondary Index (GSI) is eventually consistent, Local Secondary Index (LSI) can be strongly consistent

**Comparison:**
- vs RDS: DynamoDB for scale/speed, RDS for complex queries
- vs ElastiCache: DynamoDB is persistent, ElastiCache is cache
- vs S3: DynamoDB for structured data, S3 for objects

**Exam scenarios:**
- "IoT application generates millions of writes/second" → DynamoDB
- "Gaming leaderboard with millisecond reads" → DynamoDB + DAX
- "Shopping cart that needs global low latency" → DynamoDB Global Tables
- "Trigger function when item is added to table" → DynamoDB Streams + Lambda

---

### ElastiCache

**What it is:** Managed in-memory cache (Redis or Memcached)

**When to use:**
- Speed up database queries (caching)
- Store session data
- Leaderboards/real-time analytics (Redis)
- Pub/sub messaging (Redis)

**When NOT to use:**
- Long-term persistent storage → use RDS or DynamoDB
- Complex queries → use RDS
- Object storage → use S3

**Key features tested:**
- **Redis:** Persistence, backup/restore, sorted sets, pub/sub, Multi-AZ with auto-failover, replication
- **Memcached:** Simple, no persistence, multi-threaded, sharding
- **Caching strategies:** Lazy loading (cache miss loads from DB), Write-through (write to cache and DB simultaneously)

**Common traps:**
- Redis supports persistence and replication, Memcached does not
- Use Redis for complex data structures, Memcached for simple key-value

**Comparison:**
- Redis vs Memcached: Redis for persistence/HA/advanced features, Memcached for simple caching
- vs DynamoDB DAX: ElastiCache for general caching, DAX specifically for DynamoDB

**Exam scenarios:**
- "Reduce database load by caching frequent queries" → ElastiCache
- "Store user sessions across web servers" → ElastiCache Redis
- "Real-time gaming leaderboard" → ElastiCache Redis sorted sets

---

### Redshift

**What it is:** Petabyte-scale data warehouse for analytics (OLAP)

**When to use:**
- Business intelligence and reporting
- Complex analytics on large datasets
- Aggregations across historical data
- Data warehousing

**When NOT to use:**
- Transactional database (OLTP) → use RDS or DynamoDB
- Real-time analytics → use DynamoDB or ElastiCache
- Small datasets → use RDS

**Key features tested:**
- **Columnar storage:** Optimized for analytics (read columns, not rows)
- **Massively Parallel Processing (MPP):** Distributes queries across nodes
- **Redshift Spectrum:** Query S3 data directly without loading into Redshift
- **Snapshots:** Automated and manual, can copy to another region
- **Enhanced VPC Routing:** Forces traffic through VPC (for compliance)

**Common traps:**
- Not suitable for OLTP (transactional) workloads
- Single-AZ only (use snapshots for DR)
- Requires loading data (use Spectrum for S3 queries)

**Exam scenarios:**
- "Analyze sales data from past 5 years" → Redshift
- "Generate business reports from petabytes of data" → Redshift
- "Query both S3 and data warehouse" → Redshift Spectrum

---

## NETWORKING

### VPC (Virtual Private Cloud)

**What it is:** Isolated network in AWS where you launch resources

**Key concepts tested:**
- **CIDR Block:** IP range for VPC (e.g., 10.0.0.0/16)
- **Subnets:** Divide VPC into smaller networks, subnet is tied to single AZ
  - **Public subnet:** Has route to Internet Gateway
  - **Private subnet:** No direct route to internet
- **Default VPC:** Created automatically, all subnets are public, easy to use
- **Custom VPC:** You create, more control, recommended for production

**When to use:**
- Isolation and security
- Custom network configuration
- Hybrid cloud (VPN, Direct Connect)

**Exam scenarios:**
- "Launch EC2 with internet access" → Public subnet + Internet Gateway
- "Launch database without internet access" → Private subnet

---

### Internet Gateway (IGW)

**What it is:** Allows communication between VPC and internet

**Key features tested:**
- One IGW per VPC
- Horizontally scaled, redundant, highly available
- Must be attached to VPC
- Route table must point 0.0.0.0/0 to IGW for internet access

**Common traps:**
- Instance must have public IP or Elastic IP to communicate via IGW
- Private subnet instances cannot use IGW directly

**Exam scenario:**
- "EC2 in public subnet cannot access internet" → Check route table, check IGW, check public IP/EIP

---

### NAT Gateway / NAT Instance

**What it is:** Allows instances in private subnet to access internet (outbound only)

**NAT Gateway (recommended):**
- Managed by AWS
- Scales automatically up to 45 Gbps
- Highly available within single AZ (deploy one per AZ for HA)
- Charged per hour + data processed

**NAT Instance (legacy):**
- EC2 instance you manage
- Must disable source/destination checks
- Must be in public subnet
- Cheaper but requires management

**When to use:**
- **NAT Gateway:** Private instances need internet for updates/downloads
- **NAT Instance:** Budget is extremely tight

**Common traps:**
- NAT Gateway is per AZ (if AZ fails, instances in that AZ lose internet)
- NAT is unidirectional (outbound only, internet cannot initiate connection)
- NAT Gateway requires Elastic IP

**Exam scenarios:**
- "Private subnet EC2 needs to download patches" → NAT Gateway
- "High availability for private subnet internet access" → NAT Gateway in each AZ
- "Most cost-effective solution for private subnet internet" → NAT Instance

---

### Security Groups

**What it is:** Virtual firewall for EC2 instances (stateful)

**Key features tested:**
- **Stateful:** Return traffic automatically allowed
- **Default:** All outbound allowed, all inbound denied
- **Rules:** Only ALLOW rules (no DENY)
- **Operates at instance level**
- Can reference other security groups
- Changes take effect immediately

**Common traps:**
- Cannot block specific IPs (use NACL for that)
- All rules are evaluated (vs NACL which evaluates in order)

**Exam scenario:**
- "Allow EC2 to connect to RDS" → RDS security group allows inbound from EC2 security group

---

### Network ACLs (NACLs)

**What it is:** Firewall at subnet level (stateless)

**Key features tested:**
- **Stateless:** Must explicitly allow return traffic
- **Default NACL:** Allows all inbound and outbound
- **Custom NACL:** Denies all inbound and outbound by default
- **Rules evaluated in order** (lowest number first)
- Supports ALLOW and DENY rules
- Operates at subnet level

**Common traps:**
- Stateless means you must allow both request and response
- Rule numbers matter (100, 200, 300, etc.)
- Default NACL allows everything, custom NACL denies everything

**Comparison with Security Groups:**
| Feature | Security Group | NACL |
|---------|---------------|------|
| Level | Instance | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow and Deny |
| Evaluation | All rules | In order |
| Default | Deny inbound | Allow all (default NACL) |

**Exam scenarios:**
- "Block specific IP address" → NACL (security groups can't deny)
- "Subnet-level firewall" → NACL
- "Instance-level firewall" → Security Group

---

### VPC Peering

**What it is:** Private connection between two VPCs

**Key features tested:**
- Can peer across regions and accounts
- Non-transitive (if A peers with B, and B peers with C, A cannot reach C)
- CIDR blocks cannot overlap
- Charged for data transfer

**When to use:**
- Connect VPCs in different regions or accounts
- Share resources between VPCs

**When NOT to use:**
- Many VPCs need to connect → use Transit Gateway
- On-premises connection → use VPN or Direct Connect

**Exam scenario:**
- "Connect two VPCs in different regions" → VPC Peering

---

### VPC Endpoints

**What it is:** Private connection to AWS services without internet gateway

**Types:**
- **Interface Endpoint (PrivateLink):** ENI with private IP, supports most services, charged per hour + data
- **Gateway Endpoint:** Route table entry, supports S3 and DynamoDB only, free

**When to use:**
- Private subnet needs access to S3/DynamoDB without internet
- Enhanced security (traffic stays on AWS network)
- Compliance requirements

**Common traps:**
- Gateway endpoints are free (S3, DynamoDB)
- Interface endpoints have hourly charge

**Exam scenarios:**
- "Private EC2 needs to access S3 without internet" → S3 Gateway Endpoint
- "Private Lambda needs to call API Gateway" → Interface Endpoint

---

### PrivateLink

**What it is:** Privately access services across VPCs (uses Interface Endpoints)

**When to use:**
- Expose service to thousands of VPCs
- Share SaaS application privately
- Avoid VPC peering complexity

**Exam scenario:**
- "Share application with multiple customer VPCs securely" → PrivateLink

---

### VPN (Virtual Private Network)

**What it is:** Encrypted connection between on-premises and AWS over internet

**Components:**
- **Customer Gateway:** On-premises router
- **Virtual Private Gateway (VGW):** VPN concentrator on AWS side
- **Site-to-Site VPN:** IPsec tunnel

**When to use:**
- Quick setup for hybrid cloud
- Temporary connection
- Backup for Direct Connect

**When NOT to use:**
- Need consistent high bandwidth → use Direct Connect
- Multiple VPCs → add Transit Gateway

**Key features tested:**
- Uses internet (subject to internet performance)
- Encrypted by default
- Two tunnels for redundancy

**Exam scenario:**
- "Quickly connect on-premises data center to AWS" → Site-to-Site VPN

---

### Direct Connect

**What it is:** Dedicated private connection between on-premises and AWS (not over internet)

**When to use:**
- High throughput requirements
- Consistent network performance
- Regulatory compliance (data cannot traverse internet)
- Reduce bandwidth costs for large transfers

**When NOT to use:**
- Need quick setup → use VPN
- Low bandwidth requirements → use VPN

**Key features tested:**
- Takes weeks/months to provision
- Dedicated (1 Gbps, 10 Gbps) or Hosted (50 Mbps to 10 Gbps)
- Not encrypted by default (use VPN over Direct Connect for encryption)
- Use VPN as backup for Direct Connect

**Common traps:**
- Not redundant by default (provision two connections for HA)
- Not encrypted by default
- Long provisioning time

**Exam scenarios:**
- "Consistent high bandwidth to AWS" → Direct Connect
- "Transfer 500 TB with predictable performance" → Direct Connect
- "Encrypted connection with high bandwidth" → Direct Connect + VPN

---

### Transit Gateway

**What it is:** Hub to connect multiple VPCs and on-premises networks

**When to use:**
- Connect many VPCs (simplifies network topology)
- Hub-and-spoke model
- Share connection across multiple VPCs (VPN, Direct Connect)

**When NOT to use:**
- Only 2 VPCs → use VPC Peering

**Key features tested:**
- Supports transitive routing
- Can connect thousands of VPCs
- Regional resource but can peer across regions
- Integrates with VPN and Direct Connect

**Exam scenario:**
- "Connect 20 VPCs and on-premises network" → Transit Gateway

---

## SECURITY

### IAM (Identity and Access Management)

**What it is:** Manage access to AWS services and resources

**Key concepts tested:**
- **Users:** Individual accounts
- **Groups:** Collection of users (apply policies to group)
- **Roles:** Temporary credentials for services or users
- **Policies:** JSON documents defining permissions

**Policy types:**
- **Managed policies:** AWS-managed or customer-managed, reusable
- **Inline policies:** Directly attached to single user/group/role

**Key features tested:**
- **Least privilege:** Grant minimum permissions needed
- **MFA:** Multi-factor authentication for extra security
- **Password policy:** Enforce complexity, rotation
- **Access Advisor:** See when services were last accessed
- **IAM Roles for EC2:** Better than storing credentials on instance
- **Cross-account access:** Roles allow access across accounts

**Common traps:**
- IAM is global (not region-specific)
- Root user has full access (use sparingly, enable MFA)
- Explicit DENY always wins

**Exam scenarios:**
- "EC2 needs to access S3" → Create IAM role with S3 permissions, attach to EC2
- "Developer needs temporary access" → Assume role
- "Grant access to external auditor" → Cross-account role

---

### KMS (Key Management Service)

**What it is:** Managed encryption key creation and control

**When to use:**
- Encrypt data at rest
- Compliance requirements (control who can decrypt)
- Audit key usage

**Key features tested:**
- **Customer Managed Keys (CMK):** You control rotation, policies
- **AWS Managed Keys:** Created by AWS for specific service, automatic rotation
- **AWS Owned Keys:** Used by AWS, you don't see them
- **Envelope encryption:** Encrypt data key with master key
- **Key rotation:** Automatic annual rotation (CMK)
- **Regional:** Keys are per-region

**Common traps:**
- Cannot export CMK outside KMS (use CloudHSM for that)
- Deleting key has 7-30 day waiting period
- Encrypted snapshots/volumes cannot be shared directly (must copy and re-encrypt with recipient's key)

**Exam scenarios:**
- "Encrypt EBS volume" → Use KMS key
- "Share encrypted snapshot" → Copy and re-encrypt with recipient's key
- "Audit who decrypted data" → CloudTrail logs KMS API calls

---

### Cognito

**What it is:** User authentication and authorization for web/mobile apps

**Components:**
- **User Pools:** User directory, sign-up/sign-in, MFA
- **Identity Pools:** Grant AWS credentials to users (access AWS services)

**When to use:**
- Mobile or web app user management
- Social sign-in (Google, Facebook, Amazon)
- Grant temporary AWS credentials to app users

**Key features tested:**
- User Pools: Authentication (is the user who they say?)
- Identity Pools: Authorization (what can the user access?)
- Integrates with API Gateway, ALB

**Exam scenario:**
- "Mobile app users need to upload photos to S3" → Cognito Identity Pool grants temporary credentials

---

### AWS WAF (Web Application Firewall)

**What it is:** Protect web applications from common attacks

**When to use:**
- Block SQL injection, XSS
- Rate limiting (DDoS protection)
- Geo-blocking
- IP filtering

**Key features tested:**
- Works with CloudFront, ALB, API Gateway
- Rules: IP sets, SQL injection, XSS, rate-based rules
- WebACL: Container for rules

**Exam scenario:**
- "Block requests from specific countries" → WAF geo-blocking on CloudFront

---

### AWS Shield

**What it is:** DDoS protection

**Types:**
- **Shield Standard:** Free, automatic, protects against common DDoS
- **Shield Advanced:** $3,000/month, enhanced protection, 24/7 support, cost protection

**When to use:**
- **Standard:** Automatic for all AWS customers
- **Advanced:** Mission-critical applications, large-scale DDoS expected

**Exam scenario:**
- "Protect against DDoS attacks" → Shield Standard (mention it's automatic)

---

### Secrets Manager

**What it is:** Store and rotate secrets (passwords, API keys)

**When to use:**
- Database credentials
- API keys
- Automatic rotation of secrets

**Key features tested:**
- Automatic rotation for RDS, Redshift, DocumentDB
- Encrypted with KMS
- Fine-grained access control via IAM
- Charged per secret + API calls

**Common traps:**
- More expensive than Systems Manager Parameter Store
- Use Secrets Manager for rotation, Parameter Store for static config

**Exam scenarios:**
- "Automatically rotate RDS password" → Secrets Manager
- "Store database password securely" → Secrets Manager
- "Store simple config values" → Parameter Store (cheaper)

---

## INTEGRATION & MESSAGING

### SQS (Simple Queue Service)

**What it is:** Fully managed message queue

**When to use:**
- Decouple application components
- Asynchronous processing
- Buffer between producer and consumer

**Key features tested:**
- **Message retention:** 1 minute to 14 days (default 4 days)
- **Visibility timeout:** Message hidden after being picked up (default 30 sec, max 12 hours)
- **Standard queue:** At-least-once delivery, best-effort ordering, unlimited throughput
- **FIFO queue:** Exactly-once processing, strict ordering, 300 TPS (3000 with batching)
- **Long polling:** Reduces empty responses, saves costs (wait up to 20 seconds)
- **Dead Letter Queue (DLQ):** For messages that fail processing repeatedly

**Common traps:**
- Standard queue does not guarantee order
- FIFO queue has lower throughput
- Message size limit: 256 KB

**Comparison:**
- vs SNS: SQS is pull (consumers poll), SNS is push
- vs Kinesis: SQS for task queue, Kinesis for streaming data

**Exam scenarios:**
- "Decouple web tier from processing tier" → SQS
- "Process messages in exact order" → SQS FIFO
- "Handle failed messages" → Dead Letter Queue

---

### SNS (Simple Notification Service)

**What it is:** Pub/sub messaging (push notifications)

**When to use:**
- Send notifications to multiple subscribers
- Fan-out pattern (one message to many destinations)
- Mobile push notifications
- Email/SMS alerts

**Key features tested:**
- **Publishers:** Send messages to topics
- **Subscribers:** Receive messages (SQS, Lambda, HTTP/S, email, SMS)
- **Fan-out:** Publish once, deliver to multiple SQS queues or endpoints
- **Message filtering:** Subscribers receive only messages matching filter policy
- **FIFO topics:** Ordering and deduplication (must subscribe with FIFO SQS)

**Common traps:**
- SNS is push (vs SQS pull)
- Cannot replay messages (SQS can)

**Comparison:**
- vs SQS: SNS for notifications/fan-out, SQS for work queues
- vs EventBridge: SNS for simple pub/sub, EventBridge for event routing with filtering

**Exam scenarios:**
- "Send email when CloudWatch alarm triggers" → SNS
- "Process S3 upload with multiple systems" → S3 → SNS → Multiple SQS queues

---

### EventBridge (CloudWatch Events)

**What it is:** Serverless event bus for application integration

**When to use:**
- Schedule tasks (cron)
- React to AWS service events
- Route events from SaaS applications
- Event-driven architectures

**Key features tested:**
- **Event patterns:** Filter and route events
- **Scheduled rules:** Cron expressions
- **Targets:** Lambda, SQS, SNS, Step Functions, ECS tasks, and more
- **Custom event buses:** For your applications or third-party SaaS

**Exam scenarios:**
- "Run Lambda every hour" → EventBridge scheduled rule
- "Trigger Lambda when EC2 state changes" → EventBridge rule with event pattern

---

### Step Functions

**What it is:** Orchestrate serverless workflows (state machine)

**When to use:**
- Coordinate multiple Lambda functions
- Long-running workflows
- Human approval steps
- Error handling and retries
- Complex business logic

**Key features tested:**
- **State types:** Task, Choice, Parallel, Wait, Succeed, Fail
- **Standard workflows:** Up to 1 year, at-least-once execution
- **Express workflows:** Short-duration (<5 min), at-least-once or exactly-once

**Exam scenario:**
- "Coordinate data processing pipeline with error handling" → Step Functions

---

## MONITORING & MANAGEMENT

### CloudWatch

**What it is:** Monitoring and observability service

**Key features tested:**
- **Metrics:** Monitor AWS resources and applications (CPU, network, disk, custom metrics)
- **Alarms:** Trigger actions when threshold breached (SNS, Auto Scaling, EC2 actions)
- **Logs:** Collect and store logs (CloudWatch Logs)
- **Dashboards:** Visualize metrics
- **Events (now EventBridge):** React to AWS events

**Common use cases:**
- Monitor EC2 CPU utilization
- Create alarm for high latency
- Aggregate application logs
- Trigger Lambda on schedule

**Key concepts:**
- **Standard metrics:** 5-minute intervals (free)
- **Detailed monitoring:** 1-minute intervals (paid)
- **Custom metrics:** Application-specific data
- **Log retention:** Configurable (never expire to 10 years)

**Exam scenarios:**
- "Monitor application logs" → CloudWatch Logs
- "Trigger email when CPU > 80%" → CloudWatch Alarm → SNS
- "Custom metric for application errors" → PutMetricData API

---

### CloudTrail

**What it is:** Log all API calls in AWS account (audit trail)

**When to use:**
- Compliance and auditing
- Security analysis
- Troubleshooting (who did what when)

**Key features tested:**
- **Event types:** Management events (control plane), Data events (S3 object-level, Lambda invocations)
- **Logs stored in S3**
- **CloudTrail Insights:** Detect unusual activity
- **Multi-region:** Single trail for all regions
- **Organization trail:** For all accounts in AWS Organizations

**Common traps:**
- Enabled by default (90-day history in console)
- S3 data events and Lambda invocations not logged by default (must enable)
- Logs delivered with 15-minute delay

**Exam scenarios:**
- "Audit who deleted S3 objects" → Enable CloudTrail data events
- "Detect unusual API activity" → CloudTrail Insights

---

### AWS Config

**What it is:** Assess, audit, and evaluate configurations of AWS resources

**When to use:**
- Compliance checking
- Configuration drift detection
- Audit resource configurations over time

**Key features tested:**
- **Config Rules:** Check if resources comply with desired settings
- **Managed rules:** Pre-built by AWS
- **Custom rules:** Lambda-based
- **Remediation:** Auto-remediate non-compliant resources

**Comparison:**
- CloudTrail: Who did what (API logs)
- Config: What is the configuration now, and over time

**Exam scenario:**
- "Ensure all S3 buckets are encrypted" → AWS Config rule

---

## MIGRATION & TRANSFER

### DMS (Database Migration Service)

**What it is:** Migrate databases to AWS with minimal downtime

**When to use:**
- Migrate on-premises database to AWS
- Replicate databases for disaster recovery
- Consolidate databases
- Continuous data replication

**Key features tested:**
- **Supports homogeneous** (Oracle to Oracle) and **heterogeneous** (Oracle to Aurora) migrations
- **Source:** On-premises, EC2, RDS
- **Target:** RDS, Aurora, Redshift, DynamoDB, S3
- **SCT (Schema Conversion Tool):** Convert schema for heterogeneous migrations
- **CDC (Change Data Capture):** Replicate ongoing changes

**Exam scenarios:**
- "Migrate Oracle database to Aurora with minimal downtime" → DMS + SCT
- "Continuous replication from on-premises to RDS" → DMS

---

### Snow Family

**What it is:** Physical devices to transfer large amounts of data to/from AWS

**Types:**
- **Snowcone:** 8 TB usable, portable, can run edge computing
- **Snowball Edge:** 80 TB (storage optimized) or 42 TB (compute optimized), local processing
- **Snowmobile:** 100 PB, truck for exabyte-scale transfers

**When to use:**
- **Snowcone:** Small transfers, edge locations
- **Snowball:** Terabytes to petabytes, limited bandwidth
- **Snowmobile:** 10+ PB, data center migration

**When NOT to use:**
- Good internet connection → use DataSync or S3 Transfer Acceleration

**Exam scenarios:**
- "Transfer 50 TB with limited bandwidth" → Snowball
- "Migrate 80 PB data center to AWS" → Snowmobile

---

### DataSync

**What it is:** Automated data transfer between on-premises and AWS

**When to use:**
- Migrate files to S3, EFS, FSx
- Replicate data for backup
- Ongoing sync between on-premises and AWS

**Key features tested:**
- **Supports:** NFS, SMB, HDFS, S3, EFS, FSx
- **Bandwidth throttling:** Control network usage
- **Schedule:** One-time or recurring transfers
- **Encryption in-transit**

**Comparison:**
- vs Snowball: DataSync for good network, Snowball for limited bandwidth
- vs S3 Transfer Acceleration: DataSync for large files/many files, Transfer Acceleration for individual uploads

**Exam scenario:**
- "Daily sync of on-premises NAS to S3" → DataSync

---

## EDGE & DNS

### CloudFront

**What it is:** Content Delivery Network (CDN) to cache content at edge locations

**When to use:**
- Speed up delivery of static content (images, videos, CSS, JS)
- Reduce latency globally
- Protect against DDoS (with Shield)
- HTTPS enforcement

**Key features tested:**
- **Edge locations:** 400+ worldwide
- **Origin:** S3, EC2, ALB, on-premises
- **Behaviors:** Route based on path pattern
- **Signed URLs/Cookies:** Restrict access
- **Geo-restriction:** Block countries
- **Invalidations:** Remove cached content (costs money)
- **TTL:** Control how long content is cached

**Common traps:**
- Objects cached for TTL (default 24 hours)
- Invalidations cost money (consider versioning instead)
- Not just for S3 (can cache from any HTTP origin)

**Comparison:**
- vs S3 Transfer Acceleration: CloudFront for distribution, Transfer Acceleration for uploads
- vs Global Accelerator: CloudFront for HTTP/S, Global Accelerator for TCP/UDP

**Exam scenarios:**
- "Deliver videos globally with low latency" → CloudFront + S3
- "Restrict content to specific countries" → CloudFront geo-restriction
- "Protect static website from DDoS" → CloudFront + Shield

---

### Route 53

**What it is:** DNS service and domain registration

**When to use:**
- Domain name registration
- DNS routing
- Health checks and failover
- Traffic management

**Key routing policies tested:**
- **Simple:** Single resource, no health checks
- **Weighted:** Distribute traffic by percentage (A/B testing)
- **Latency:** Route to lowest latency region
- **Failover:** Primary/secondary (requires health check)
- **Geolocation:** Route based on user location
- **Geoproximity:** Route based on resource location (use with traffic flow)
- **Multi-value:** Return multiple IPs (simple load balancing with health checks)

**Key features tested:**
- **Health checks:** Monitor endpoint, trigger failover
- **Alias records:** Map to AWS resources (free queries)
- **CNAME vs Alias:** CNAME cannot be used for zone apex (example.com), use Alias

**Common traps:**
- Cannot create CNAME for zone apex (use Alias instead)
- Weighted routing with weights of 0 stops traffic to that resource

**Exam scenarios:**
- "Route traffic to closest region" → Latency-based routing
- "Failover to secondary site if primary fails" → Failover routing with health check
- "A/B test with 90% to old, 10% to new" → Weighted routing (90, 10)

---

### Global Accelerator

**What it is:** Improve global application availability and performance using AWS global network

**When to use:**
- Non-HTTP protocols (TCP, UDP)
- Static IP required (provides 2 anycast IPs)
- Fast regional failover
- Gaming, IoT, VoIP

**Key features tested:**
- **Anycast IP:** Single IP routes to nearest edge location
- **Health checks:** Automatic failover
- **Uses AWS backbone** (vs public internet)

**Comparison:**
- vs CloudFront: Global Accelerator for TCP/UDP, CloudFront for HTTP/S
- vs Route 53: Both can route traffic, but Global Accelerator provides static IPs and uses AWS network

**Exam scenarios:**
- "Static IP for global application" → Global Accelerator
- "Gaming application needs low latency worldwide" → Global Accelerator
- "HTTP website needs caching" → CloudFront

---

# SECTION 2: VISUAL ARCHITECTURE THINKING

Understanding request flows helps answer 60% of exam questions. Here's how to think through common patterns.

## Basic Web Application Flow

```
User → Route 53 → CloudFront → ALB → EC2 (Auto Scaling) → RDS (Multi-AZ)
```

**Breakdown:**
1. User types domain name
2. Route 53 resolves to CloudFront distribution
3. CloudFront serves cached content or forwards to ALB
4. ALB distributes to healthy EC2 instances
5. EC2 queries RDS for data

## Serverless Application Flow

```
User → API Gateway → Lambda → DynamoDB
                     ↓
                 CloudWatch Logs
```

**Breakdown:**
1. API Gateway receives HTTP request
2. Invokes Lambda function
3. Lambda reads/writes DynamoDB
4. Logs go to CloudWatch

## Real-time Data Processing

```
Data Source → Kinesis Data Streams → Lambda → DynamoDB
                                     ↓
                                    S3
```

**Breakdown:**
1. Application sends data to Kinesis
2. Lambda processes each record
3. Stores processed data in DynamoDB
4. Archives raw data to S3

## Hybrid Cloud Architecture

```
On-Premises → Direct Connect → Transit Gateway → Multiple VPCs
                                                   ↓
                                              AWS Services
```

**Analogy:** Direct Connect is a dedicated highway, Transit Gateway is the interchange connecting multiple roads (VPCs)

## Static Website Hosting

```
User → Route 53 → CloudFront → S3 Static Website
```

**Why this setup:**
- Route 53: DNS resolution
- CloudFront: Global caching, HTTPS, DDoS protection
- S3: Storage for HTML/CSS/JS files

## Three-Tier Architecture

```
Internet → ALB (Public Subnet) → App Tier (Private Subnet) → Database Tier (Private Subnet)
                                        ↓
                                   NAT Gateway (Public Subnet)
                                        ↓
                                    Internet (for updates)
```

**Security layers:**
1. ALB in public subnet (internet-facing)
2. App servers in private subnet (no direct internet)
3. Database in private subnet (most isolated)
4. NAT Gateway allows outbound internet (updates) without inbound access

---

# SECTION 3: HIGH-YIELD COMPARISON TABLES

## S3 vs EBS vs EFS

| Feature | S3 | EBS | EFS |
|---------|----|----|-----|
| Type | Object storage | Block storage | Network file system |
| Use case | Static files, backups, data lakes | OS disk, databases | Shared files across instances |
| Access | HTTP API, SDK | Attached to EC2 | Mount on Linux EC2 |
| Attachment | Unlimited users | Single EC2 (except io1/io2 Multi-Attach) | Thousands of EC2 |
| AZ | Multi-AZ by default | Single AZ | Multi-AZ |
| Pricing | Per GB stored + requests | Per GB provisioned | Per GB used |
| Max size | Unlimited | 16 TiB | Petabytes |
| Durability | 99.999999999% (11 nines) | Replicated in AZ | Multi-AZ replication |

**Exam tip:** Object storage → S3, Block storage → EBS, Shared file system → EFS

---

## RDS vs DynamoDB

| Feature | RDS | DynamoDB |
|---------|-----|----------|
| Type | Relational (SQL) | NoSQL (key-value/document) |
| Schema | Fixed schema | Flexible schema |
| Queries | Complex SQL (joins, transactions) | Simple queries (key-based) |
| Scaling | Vertical (bigger instance), Read Replicas | Horizontal (add partitions), auto-scales |
| Performance | Good | Single-digit millisecond |
| Use case | Complex queries, ACID | Simple queries, massive scale |
| Pricing | Per instance hour | Per request or provisioned capacity |

**Exam tip:** Complex queries → RDS, Scale/speed → DynamoDB

---

## ALB vs NLB vs CLB

| Feature | ALB | NLB | CLB |
|---------|-----|-----|-----|
| Layer | Layer 7 (HTTP/HTTPS) | Layer 4 (TCP/UDP) | Layer 4 & 7 |
| Use case | Web applications, microservices | Extreme performance, static IP | Legacy (not recommended) |
| Target | IP, instance, Lambda | IP, instance, ALB | Instance only |
| Protocol | HTTP, HTTPS, WebSocket, HTTP/2 | TCP, UDP, TLS | TCP, SSL, HTTP, HTTPS |
| Static IP | No (use Global Accelerator) | Yes | No |
| Path-based routing | Yes | No | No |
| Host-based routing | Yes | No | No |
| Performance | Good | Ultra-high (millions req/sec) | Good |
| Latency | ~ms | ~100 μs | ~ms |

**Exam tip:** 
- HTTP/S with routing → ALB
- TCP/UDP or need static IP → NLB
- Legacy → CLB (but avoid on exam)

---

## Multi-AZ vs Read Replica (RDS)

| Feature | Multi-AZ | Read Replica |
|---------|----------|--------------|
| Purpose | High availability | Read scaling |
| Replication | Synchronous | Asynchronous |
| Automatic failover | Yes | No (manual promotion) |
| Use for reads | No (standby not accessible) | Yes |
| Same region | Yes (different AZ) | Can be cross-region |
| Performance impact | Slight | None on source |
| Endpoint | Single DNS (auto-failover) | Each replica has own endpoint |
| Backups | From standby (no perf impact) | Can take from replica |

**Exam tip:** HA/DR → Multi-AZ, Read scaling → Read Replica

---

## Security Groups vs NACLs

| Feature | Security Group | NACL |
|---------|---------------|------|
| Level | Instance | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow and Deny |
| Rule evaluation | All rules | In order (by number) |
| Default | Deny all inbound | Allow all (default NACL) |
| Return traffic | Automatic | Must explicitly allow |
| Use case | Instance firewall | Subnet firewall, block IPs |

**Exam tip:** Block specific IP → NACL, Instance protection → Security Group

---

## NAT Gateway vs NAT Instance

| Feature | NAT Gateway | NAT Instance |
|---------|-------------|--------------|
| Management | AWS-managed | You manage (EC2) |
| Availability | HA within AZ | Manual (use scripts) |
| Bandwidth | Up to 45 Gbps | Depends on instance type |
| Cost | Per hour + data | EC2 instance cost |
| Security groups | No | Yes |
| Bastion host | No | Can be used as one |
| Port forwarding | No | Yes |

**Exam tip:** Production → NAT Gateway, Tight budget or need bastion → NAT Instance

---

## SQS vs SNS vs EventBridge

| Feature | SQS | SNS | EventBridge |
|---------|-----|-----|-------------|
| Pattern | Queue (pull) | Pub/sub (push) | Event bus |
| Persistence | Yes (up to 14 days) | No (unless subscriber is queue) | No (unless target is queue) |
| Ordering | FIFO queue | FIFO topic | No guarantee |
| Targets | 1 consumer per message | Multiple subscribers | Multiple targets |
| Use case | Work queue, decoupling | Notifications, fan-out | Event-driven, AWS service events |
| Filtering | No (consumer must filter) | Yes | Yes (advanced patterns) |

**Exam tip:** 
- Work queue → SQS
- Fan-out → SNS
- Event-driven → EventBridge

---

## CloudFront vs Global Accelerator

| Feature | CloudFront | Global Accelerator |
|---------|-----------|-------------------|
| Type | CDN (caching) | Network accelerator |
| Protocol | HTTP/HTTPS | TCP/UDP |
| Use case | Static/dynamic content | Gaming, IoT, non-HTTP |
| Caching | Yes | No |
| Static IP | No | Yes (2 anycast IPs) |
| Origin | S3, EC2, ALB, custom | ALB, NLB, EC2, EIP |
| Pricing | Per data transfer | Per hour + data transfer |

**Exam tip:** HTTP with caching → CloudFront, TCP/UDP or need static IP → Global Accelerator

---

## S3 Storage Classes

| Class | Use Case | Retrieval | Durability | Availability | Cost |
|-------|----------|-----------|-----------|--------------|------|
| Standard | Frequent access | Instant | 11 9s | 99.99% | Highest |
| Intelligent-Tiering | Unknown/changing patterns | Instant | 11 9s | 99.9% | Auto-optimized |
| Standard-IA | Infrequent access | Instant | 11 9s | 99.9% | Lower storage, retrieval fee |
| One Zone-IA | Infrequent, non-critical | Instant | 11 9s | 99.5% | 20% cheaper than Std-IA |
| Glacier Instant | Archive, quarterly access | Instant | 11 9s | 99.9% | 68% cheaper than Std-IA |
| Glacier Flexible | Archive, rare access | Min-hrs | 11 9s | 99.99% | Very low |
| Glacier Deep Archive | Long-term archive | 12-48 hrs | 11 9s | 99.99% | Lowest |

**Exam tip:** Match access pattern to class (frequent → Standard, archive → Glacier)

---

# SECTION 4: DECISION TREES

These quick decision rules help eliminate wrong answers in <10 seconds.

## Storage Decision Tree

**Need to store data?**
- Object storage (files, images, videos) → S3
- Block storage (EC2 disk) → EBS
- Shared file system (multiple EC2) → EFS (Linux) or FSx (Windows)
- Archive (rarely accessed) → S3 Glacier
- Database → RDS or DynamoDB

## Database Decision Tree

**Need a database?**
- Relational (SQL, joins, complex queries) → RDS or Aurora
- NoSQL (key-value, scale, speed) → DynamoDB
- In-memory cache → ElastiCache (Redis or Memcached)
- Data warehouse (analytics, BI) → Redshift
- Graph database → Neptune
- Time-series → Timestream

## Compute Decision Tree

**Need compute?**
- Event-driven, <15 min → Lambda
- Long-running, need control → EC2
- Containers → ECS, EKS, or Fargate
- Simple web app, no infra management → Elastic Beanstalk
- Batch processing → AWS Batch

## High Availability

**Need high availability?**
- Database → RDS Multi-AZ or Aurora
- EC2 → Auto Scaling across multiple AZs + ELB
- Storage → S3 (Multi-AZ by default), EFS (Multi-AZ)
- Static site → S3 + CloudFront

## Scaling

**Need to scale?**
- **Reads:** 
  - Database → Read Replicas (RDS), DAX (DynamoDB)
  - Cache → ElastiCache
- **Writes:** 
  - Database → DynamoDB (auto-scales), Aurora (better than RDS)
  - Compute → Auto Scaling
- **Storage:** S3 (unlimited), EFS (auto-scales), Aurora (auto-scales to 128 TB)

## Security

**Need to secure?**
- Encrypt data at rest → KMS
- Encrypt data in transit → TLS/SSL
- User authentication → Cognito
- Service-to-service auth → IAM roles
- Secrets → Secrets Manager
- DDoS protection → Shield (Standard is automatic)
- Web application firewall → WAF

## Networking

**Connect resources?**
- Internet access → Internet Gateway (public) or NAT Gateway (private)
- VPC to VPC → VPC Peering or Transit Gateway
- On-premises to AWS → VPN (quick) or Direct Connect (dedicated)
- Private AWS service access → VPC Endpoints

## Migration

**Move data to AWS?**
- Online (good internet) → DataSync, DMS
- Offline (limited internet) → Snowball, Snowmobile
- Database migration → DMS
- File transfer → DataSync, S3 Transfer Acceleration

## Monitoring

**Need visibility?**
- Metrics → CloudWatch
- Logs → CloudWatch Logs
- API calls (audit) → CloudTrail
- Configuration compliance → AWS Config

## Cost Optimization

**Reduce costs?**
- EC2 → Reserved Instances, Spot Instances, Savings Plans
- S3 → Lifecycle policies (move to cheaper classes)
- Compute → Lambda (pay per use), Auto Scaling (scale down)
- Database → Aurora Serverless (intermittent workloads)

## Messaging

**Decouple components?**
- Work queue → SQS
- Publish to multiple → SNS
- Event-driven → EventBridge
- Workflow orchestration → Step Functions

---

# SECTION 5: REAL EXAM SCENARIOS

## Cost Optimization Scenarios

### Question 1
A company runs a web application on 20 EC2 instances that handle steady traffic 24/7. The application has been running for 2 years and will continue for at least 2 more years. What is the MOST cost-effective solution?

A. Use Spot Instances  
B. Use On-Demand Instances  
C. Purchase Reserved Instances with a 1-year term  
D. Purchase Reserved Instances with a 3-year term  

**Answer: D**

**Why correct:** 3-year Reserved Instances offer the highest discount (up to 60-70%) for steady, long-term workloads.

**Why others are wrong:**
- A: Spot Instances can be terminated, unsuitable for steady production traffic
- B: On-Demand is most expensive for long-term use
- C: 1-year RI offers less discount than 3-year

---

### Question 2
A company stores 500 TB of log files in S3 Standard. The logs are accessed frequently for the first 30 days, rarely after 90 days, and must be retained for 7 years for compliance. What is the MOST cost-effective solution?

A. Keep all logs in S3 Standard  
B. Use S3 Intelligent-Tiering for all logs  
C. Create a lifecycle policy: transition to Standard-IA after 30 days, Glacier Flexible after 90 days  
D. Create a lifecycle policy: transition to Glacier Deep Archive after 30 days  

**Answer: C**

**Why correct:** Matches access pattern: Standard for frequent (0-30 days), Standard-IA for occasional (30-90 days), Glacier for archive (90+ days)

**Why others are wrong:**
- A: Expensive for rarely accessed data
- B: Intelligent-Tiering charges monitoring fee, not optimal when access pattern is known
- D: Cannot retrieve from Deep Archive fast enough if accessed in first 30 days

---

### Question 3
A company has a Lambda function that processes images uploaded to S3. The function runs for an average of 5 seconds but experiences cold starts adding 2-second delays. Usage is unpredictable (0-1000 invocations/hour). How can they reduce latency cost-effectively?

A. Increase Lambda memory to reduce execution time  
B. Use provisioned concurrency  
C. Switch to EC2 for consistent performance  
D. Keep the current setup  

**Answer: A**

**Why correct:** Increasing memory also increases CPU, reducing execution time. This is cheaper than provisioned concurrency for unpredictable loads.

**Why others are wrong:**
- B: Provisioned concurrency costs money even when idle, expensive for unpredictable traffic
- C: EC2 would cost more for intermittent workloads
- D: Can be improved

---

## Security Scenarios

### Question 4
A company needs to grant an external auditor temporary access to read CloudTrail logs in S3 without creating an IAM user. What is the BEST approach?

A. Create an IAM user and delete after audit  
B. Create a cross-account IAM role  
C. Generate S3 pre-signed URL  
D. Make S3 bucket public temporarily  

**Answer: B**

**Why correct:** Cross-account roles provide temporary, auditable access without creating users

**Why others are wrong:**
- A: Creating users for external access is not best practice
- C: Pre-signed URLs expire, not suitable for extended audits
- D: Never make sensitive data public

---

### Question 5
An application on EC2 needs to read objects from an S3 bucket. What is the MOST secure way to grant access?

A. Store AWS credentials in application code  
B. Store credentials in environment variables on EC2  
C. Create an IAM role with S3 permissions and attach to EC2  
D. Make S3 bucket public  

**Answer: C**

**Why correct:** IAM roles provide temporary credentials, no need to store or rotate keys

**Why others are wrong:**
- A: Hardcoding credentials is a security risk
- B: Better than A, but roles are more secure (no long-term credentials)
- D: Public buckets are security risks

---

### Question 6
A company wants to encrypt EBS volumes and ensure they can audit who decrypted data. What should they use?

A. EBS default encryption  
B. KMS with CloudTrail logging  
C. Third-party encryption software  
D. S3 encryption  

**Answer: B**

**Why correct:** KMS logs all encrypt/decrypt operations to CloudTrail for auditing

**Why others are wrong:**
- A: Default encryption works but doesn't specify auditing (though it uses KMS)
- C: Adds complexity, no built-in audit trail
- D: Wrong service (question is about EBS)

---

## High Availability / Fault Tolerance Scenarios

### Question 7
A company runs a critical MySQL database on RDS. They need to ensure the database remains available even if an AZ fails. What should they do?

A. Enable Read Replicas  
B. Enable Multi-AZ deployment  
C. Take automated snapshots  
D. Use DynamoDB instead  

**Answer: B**

**Why correct:** Multi-AZ provides synchronous replication and automatic failover

**Why others are wrong:**
- A: Read Replicas are for scaling, not automatic failover
- C: Snapshots are for backups, not high availability
- D: Changing database type is drastic and may not be compatible

---

### Question 8
A web application runs on EC2 in a single AZ with an Elastic IP. The company needs high availability across two AZs. What is the BEST architecture?

A. Keep single EC2, add EBS snapshots  
B. Add another EC2 in second AZ with another Elastic IP  
C. Use Auto Scaling group across 2 AZs with Application Load Balancer  
D. Migrate to Lambda  

**Answer: C**

**Why correct:** Auto Scaling ensures instances in multiple AZs, ALB distributes traffic

**Why others are wrong:**
- A: Snapshots don't provide HA
- B: Manual management, no automatic failover
- D: Not all applications suit Lambda

---

### Question 9
A company hosts a static website on S3 with CloudFront. They want to ensure 99.99% availability. What else should they configure?

A. Use S3 Standard-IA  
B. Enable versioning on S3  
C. Enable Origin Failover with a secondary S3 bucket  
D. Use multiple CloudFront distributions  

**Answer: C**

**Why correct:** Origin Failover redirects to secondary origin if primary fails

**Why others are wrong:**
- A: Storage class doesn't affect availability significantly (S3 Standard is 99.99%)
- B: Versioning protects from deletions, not availability
- D: Redundant, single distribution with failover is sufficient

---

## Performance Scenarios

### Question 10
A company's RDS MySQL database experiences heavy read traffic, causing slow responses. Write traffic is minimal. What is the MOST effective solution?

A. Upgrade to larger instance  
B. Enable Multi-AZ  
C. Add Read Replicas  
D. Migrate to DynamoDB  

**Answer: C**

**Why correct:** Read Replicas scale read capacity

**Why others are wrong:**
- A: More expensive, doesn't distribute load
- B: Multi-AZ is for HA, standby doesn't serve reads
- D: Migration is complex, not needed

---

### Question 11
A DynamoDB table experiences slow read performance during peak hours. The application can tolerate slightly outdated data. How can they improve performance cost-effectively?

A. Switch to provisioned capacity with higher RCUs  
B. Enable DynamoDB Accelerator (DAX)  
C. Use eventually consistent reads  
D. Migrate to RDS  

**Answer: C**

**Why correct:** Eventually consistent reads cost half (1 RCU vs 2 RCU) and are faster, acceptable if slight lag is okay

**Why others are wrong:**
- A: More expensive
- B: DAX is great but costs money, eventually consistent is cheaper
- D: Unnecessary migration

---

### Question 12
A video processing application uploads large files (10 GB each) to S3 from different continents. Uploads are slow. What can improve upload speed?

A. Use S3 Multipart Upload  
B. Use S3 Transfer Acceleration  
C. Use CloudFront  
D. Compress files before upload  

**Answer: B**

**Why correct:** Transfer Acceleration uses CloudFront edge locations to speed uploads globally

**Why others are wrong:**
- A: Multipart helps with reliability, not necessarily speed
- C: CloudFront is for downloads (content delivery), not uploads
- D: Compression reduces size but adds processing time

---

## Hybrid Cloud / Migration Scenarios

### Question 13
A company wants to connect their on-premises data center to AWS with consistent, high-bandwidth connectivity for a long-term migration project. Internet connection is unreliable. What should they use?

A. Site-to-Site VPN  
B. Direct Connect  
C. VPC Peering  
D. DataSync over internet  

**Answer: B**

**Why correct:** Direct Connect provides dedicated, consistent high-bandwidth connection

**Why others are wrong:**
- A: VPN uses internet, which is unreliable in this case
- C: VPC Peering is for VPC-to-VPC, not on-premises
- D: DataSync can use internet or Direct Connect, but doesn't provide the connection itself

---

### Question 14
A company needs to quickly establish a connection to AWS while waiting for Direct Connect provisioning (which takes 2 months). What should they use as an interim solution?

A. Wait for Direct Connect  
B. Use Site-to-Site VPN, then add Direct Connect  
C. Use DataSync  
D. Use Snowball  

**Answer: B**

**Why correct:** VPN can be set up in hours/days as interim solution

**Why others are wrong:**
- A: 2-month delay is not acceptable
- C: DataSync is for data transfer, not network connectivity
- D: Snowball is for offline data transfer

---

### Question 15
A company wants to migrate a 50 TB Oracle database to Aurora PostgreSQL with minimal downtime. What is the BEST approach?

A. Export database, upload to S3, import to Aurora  
B. Use AWS Database Migration Service (DMS)  
C. Use Snowball to transfer database backup  
D. Manually replicate using scripts  

**Answer: B**

**Why correct:** DMS supports heterogeneous migration (Oracle to PostgreSQL) with minimal downtime using continuous replication

**Why others are wrong:**
- A: Requires downtime during export/import
- C: Snowball is for offline transfer, causes downtime
- D: Error-prone, not recommended

---

## Serverless Scenarios

### Question 16
A company wants to build a serverless REST API that processes user requests and stores data in a database. What services should they use?

A. EC2 + RDS  
B. API Gateway + Lambda + DynamoDB  
C. Elastic Beanstalk + RDS  
D. Lambda + RDS  

**Answer: B**

**Why correct:** Fully serverless stack: API Gateway (API), Lambda (compute), DynamoDB (database)

**Why others are wrong:**
- A: Not serverless (EC2 requires management)
- C: Elastic Beanstalk manages infrastructure but isn't serverless
- D: RDS is not serverless (Aurora Serverless would be better, but DynamoDB is more suitable for serverless architectures)

---

### Question 17
A Lambda function needs to process files uploaded to S3. The function takes 10 minutes to process each file. What should the architect do?

A. Increase Lambda timeout to 10 minutes  
B. Use Step Functions to orchestrate and Lambda for subtasks  
C. Use EC2 instead  
D. Use Fargate for processing  

**Answer: D**

**Why correct:** Lambda has 15-minute limit. For 10-minute tasks, Lambda is acceptable (Answer A), but if you anticipate growth or want better architecture, Fargate is more scalable. However, Answer A is technically acceptable too. Let me revise:

**Corrected Answer: A**

**Why correct:** 10 minutes is within Lambda's 15-minute limit; simply increase timeout

**Why others are wrong:**
- B: Overcomplicating; Step Functions is for workflows, not needed for single-file processing
- C: EC2 requires more management
- D: Fargate works but adds complexity

---

### Question 18
A company wants to schedule a Lambda function to run every day at 3 AM to generate reports. What should they use?

A. CloudWatch scheduled event  
B. Manually trigger Lambda daily  
C. Use EC2 with cron job  
D. Use Step Functions  

**Answer: A**

**Why correct:** CloudWatch Events (now EventBridge) can schedule Lambda on cron expressions

**Why others are wrong:**
- B: Manual is not scalable or reliable
- C: EC2 for simple scheduling is overkill
- D: Step Functions is for workflows, not scheduling

---

## Multi-Region / Disaster Recovery Scenarios

### Question 19
A company needs a disaster recovery solution where the RTO (Recovery Time Objective) is 1 hour and RPO (Recovery Point Objective) is 5 minutes. What is the MOST cost-effective DR strategy?

A. Backup and Restore (snapshots in secondary region)  
B. Pilot Light (minimal resources running in secondary region)  
C. Warm Standby (scaled-down version running in secondary region)  
D. Multi-Site Active-Active (full production in both regions)  

**Answer: C**

**Why correct:** Warm Standby keeps minimal resources running, can scale up within 1 hour, and continuous replication achieves <5 min RPO

**Why others are wrong:**
- A: Backup/Restore can't meet 1-hour RTO (takes hours to restore)
- B: Pilot Light might meet RTO but requires more time to start resources
- D: Active-Active is most expensive (running full production in both regions)

---

### Question 20
A global application needs low-latency reads in multiple regions with a single write region. What database solution is BEST?

A. RDS with cross-region Read Replicas  
B. DynamoDB Global Tables  
C. Aurora Global Database  
D. Multi-region Redshift  

**Answer: C**

**Why correct:** Aurora Global Database provides <1 second replication to secondary regions, optimized for one write region + multiple read regions

**Why others are wrong:**
- A: Works but higher replication lag than Aurora Global
- B: DynamoDB Global Tables is multi-write (doesn't match "single write region" requirement)
- D: Redshift is for analytics, not transactional workloads

---

## Advanced Networking Scenarios

### Question 21
A company has 15 VPCs in different regions and an on-premises data center. They want to simplify connectivity. What is the BEST solution?

A. VPC Peering between all VPCs  
B. Transit Gateway with VPN  
C. Direct Connect to each VPC  
D. Use internet for connectivity  

**Answer: B**

**Why correct:** Transit Gateway is hub-and-spoke model, simplifies connectivity for many VPCs

**Why others are wrong:**
- A: VPC Peering requires n(n-1)/2 connections (105 connections for 15 VPCs), not scalable
- C: Direct Connect to each VPC is complex and expensive
- D: Internet is insecure and slow

---

### Question 22
An EC2 instance in a private subnet cannot connect to the internet for software updates. The VPC has an internet gateway and NAT gateway. What could be wrong?

A. Route table for private subnet does not point to NAT gateway  
B. Security group blocks outbound traffic  
C. Instance doesn't have public IP  
D. NAT gateway is in private subnet  

**Answer: A**

**Why correct:** Private subnet route table must point 0.0.0.0/0 to NAT gateway for internet access

**Why others are wrong:**
- B: Security groups allow all outbound by default
- C: Private instances don't need public IP (NAT gateway has it)
- D: NAT gateway must be in public subnet (if in private, this would also cause the issue, but A is more common)

---

### Question 23
A company wants to block traffic from a specific IP address to their web application running on EC2. What should they use?

A. Security Group with deny rule  
B. Network ACL with deny rule  
C. WAF with IP set rule  
D. Route 53  

**Answer: B**

**Why correct:** Security Groups don't support deny rules; NACLs do

**Why others are wrong:**
- A: Security Groups only support allow rules
- C: WAF can block IPs, but NACL is simpler and cheaper for this use case
- D: Route 53 is for DNS, not firewalling

---

## Data Processing Scenarios

### Question 24
A company receives real-time clickstream data (thousands of events per second) and needs to process and analyze it. What architecture is BEST?

A. Write directly to RDS, query for analytics  
B. Kinesis Data Streams → Lambda → S3, query with Athena  
C. Write directly to S3, use Lambda to process  
D. SQS → Lambda → RDS  

**Answer: B**

**Why correct:** Kinesis handles high-throughput streaming data, Lambda processes, S3 stores, Athena queries

**Why others are wrong:**
- A: RDS can't handle thousands of writes/second efficiently
- C: S3 is not designed for high-frequency writes
- D: SQS can handle it but Kinesis is better for streaming analytics

---

### Question 25
A company needs to run a nightly ETL job that processes 100 GB of data in S3, transforms it, and loads into Redshift. The job takes 2 hours. What is the MOST cost-effective solution?

A. Use Lambda  
B. Use EC2 On-Demand instance, terminate after job  
C. Use Fargate  
D. Use Glue  

**Answer: D**

**Why correct:** AWS Glue is a fully managed ETL service, serverless, pay only when running

**Why others are wrong:**
- A: Lambda has 15-minute limit (job takes 2 hours)
- B: Works but requires managing infrastructure
- C: Works but Glue is specifically designed for ETL

---

## Additional Scenarios

### Question 26
A company's application generates 10,000 small files (10 KB each) every hour. They need to store these files for 3 years. What is the MOST cost-effective solution?

A. Store each file individually in S3 Standard  
B. Combine files into larger objects, store in S3 Standard-IA  
C. Store in S3 Glacier Flexible Retrieval  
D. Store in EBS  

**Answer: B**

**Why correct:** S3 charges per request; combining reduces requests. Standard-IA is cheaper for long-term storage

**Why others are wrong:**
- A: High request costs for many small files
- C: Glacier is for archives (hard to retrieve frequently), but if files are never accessed, this could work
- D: EBS is for block storage, not object storage

---

### Question 27
A company runs batch jobs that can be interrupted and resumed. The jobs run for 6 hours daily during off-peak times. What EC2 pricing model should they use?

A. On-Demand  
B. Reserved Instances  
C. Spot Instances  
D. Dedicated Hosts  

**Answer: C**

**Why correct:** Spot Instances are up to 90% cheaper, suitable for interruptible workloads

**Why others are wrong:**
- A: On-Demand is most expensive
- B: Reserved requires commitment, not ideal for 6 hours/day
- D: Dedicated Hosts are for licensing, most expensive

---

### Question 28
An application uses SQS to queue messages. Some messages fail processing repeatedly. How can the architect handle failed messages?

A. Increase visibility timeout  
B. Configure Dead Letter Queue  
C. Delete failed messages  
D. Use SNS instead  

**Answer: B**

**Why correct:** Dead Letter Queue (DLQ) captures messages that fail processing after max retries

**Why others are wrong:**
- A: Visibility timeout affects when message becomes available again, doesn't handle failures
- C: Deleting loses data
- D: SNS is for pub/sub, doesn't solve failure handling

---

### Question 29
A company wants to host a website on S3 with a custom domain (www.example.com) and HTTPS. What do they need?

A. S3 + Route 53  
B. S3 + CloudFront + ACM certificate + Route 53  
C. S3 + ALB  
D. EC2 + Route 53  

**Answer: B**

**Why correct:** S3 for hosting, CloudFront for HTTPS (S3 doesn't support custom domain HTTPS), ACM for certificate, Route 53 for DNS

**Why others are wrong:**
- A: S3 alone doesn't support HTTPS with custom domain
- C: S3 static sites don't use ALB
- D: EC2 is unnecessary for static site

---

### Question 30
A Lambda function is triggered by S3 uploads but occasionally times out. The function queries an RDS database in a private subnet. What could be the issue?

A. Lambda needs more memory  
B. Lambda's VPC configuration is causing cold starts  
C. RDS is too slow  
D. S3 trigger is misconfigured  

**Answer: B**

**Why correct:** Lambda in VPC requires ENI setup, causing cold start delays that can cause timeouts

**Why others are wrong:**
- A: Memory might help but VPC cold start is more likely for timeouts
- C: RDS performance could be an issue but VPC cold start is more common cause
- D: If trigger is misconfigured, function wouldn't run at all

---

### Question 31
A company needs to share large files (50 GB each) with external partners securely. Files should only be accessible for 7 days. What is the BEST solution?

A. Make S3 bucket public  
B. Generate S3 pre-signed URLs with 7-day expiration  
C. Use CloudFront signed URLs  
D. Send files via email  

**Answer: B**

**Why correct:** Pre-signed URLs provide temporary, secure access without making bucket public

**Why others are wrong:**
- A: Public buckets are insecure
- C: CloudFront signed URLs work but add complexity (pre-signed URLs are simpler)
- D: Email can't handle 50 GB files

---

### Question 32
A company has an application that stores sensitive data in S3. They need to ensure data is encrypted at rest and they control the encryption keys. What should they use?

A. SSE-S3 (S3-managed keys)  
B. SSE-KMS (AWS KMS)  
C. SSE-C (Customer-provided keys)  
D. Client-side encryption  

**Answer: B**

**Why correct:** SSE-KMS allows customer to control keys via KMS, with audit trail

**Why others are wrong:**
- A: S3-managed keys don't give customer control
- C: Customer-provided keys give control but customer must manage keys themselves (more complex)
- D: Client-side encryption works but requires application changes

---

### Question 33
A gaming company needs to store player session data that is accessed frequently but can tolerate loss. The data should be shared across multiple EC2 instances. What storage should they use?

A. EBS Multi-Attach  
B. EFS  
C. ElastiCache Redis  
D. S3  

**Answer: C**

**Why correct:** ElastiCache Redis is in-memory, fast, shared across instances, suitable for session data that can tolerate loss

**Why others are wrong:**
- A: EBS Multi-Attach limited to io1/io2, not ideal for session data
- B: EFS works but slower than in-memory cache
- D: S3 is object storage, not ideal for frequent reads/writes of session data

---

### Question 34
A web application experiences traffic spikes during business hours (9 AM - 5 PM) and minimal traffic at night. What is the MOST cost-effective Auto Scaling strategy?

A. Target tracking (maintain CPU at 50%)  
B. Scheduled scaling (scale up at 8:55 AM, scale down at 5:05 PM)  
C. Step scaling based on CloudWatch alarms  
D. Keep instances running 24/7  

**Answer: B**

**Why correct:** Scheduled scaling matches predictable traffic patterns, scales proactively

**Why others are wrong:**
- A: Target tracking works but reactive (waits for load before scaling)
- C: Step scaling is reactive
- D: Wastes money on idle instances

---

### Question 35
A company wants to ensure their EC2 instances are compliant with company policy (no public IPs, specific security groups, etc.). What service should they use?

A. CloudTrail  
B. AWS Config  
C. CloudWatch  
D. Inspector  

**Answer: B**

**Why correct:** AWS Config evaluates resource configurations against rules, detects non-compliance

**Why others are wrong:**
- A: CloudTrail logs API calls, doesn't check compliance
- C: CloudWatch monitors metrics, not configuration compliance
- D: Inspector scans for vulnerabilities, not configuration compliance

---

### Question 36
An application needs to send email notifications to users. What is the simplest AWS service to use?

A. SES (Simple Email Service)  
B. SNS  
C. SQS  
D. Lambda with SMTP  

**Answer: A**

**Why correct:** SES is designed specifically for sending emails

**Why others are wrong:**
- B: SNS can send emails but SES is more feature-rich for email (templates, bounce handling, etc.)
- C: SQS is a queue, doesn't send emails
- D: Overcomplicating, SES is simpler

---

### Question 37
A company's application writes logs to CloudWatch Logs. They need to analyze logs to detect errors and trends. What should they use?

A. Download logs and analyze manually  
B. CloudWatch Logs Insights  
C. Kinesis  
D. Athena  

**Answer: B**

**Why correct:** CloudWatch Logs Insights is designed for querying and analyzing logs

**Why others are wrong:**
- A: Manual is inefficient
- C: Kinesis is for streaming, not log analysis
- D: Athena queries S3 data, would require exporting logs first

---

### Question 38
A company needs to transfer 10 PB of data from an on-premises data center to AWS. Network bandwidth is limited to 100 Mbps. What is the FASTEST solution?

A. Use Direct Connect  
B. Use Snowmobile  
C. Use DataSync over internet  
D. Use S3 Transfer Acceleration  

**Answer: B**

**Why correct:** Snowmobile is for exabyte-scale (10+ PB) transfers, faster than network transfer at 100 Mbps (would take years)

**Why others are wrong:**
- A: Direct Connect is fast but provisioning + transfer time for 10 PB would still take very long
- C: 100 Mbps would take months/years for 10 PB
- D: Transfer Acceleration speeds uploads but still limited by 100 Mbps

---

### Question 39
A company wants to run Docker containers without managing servers. What service should they use?

A. ECS on EC2  
B. ECS on Fargate  
C. EKS on EC2  
D. Lambda  

**Answer: B**

**Why correct:** Fargate is serverless container platform (no server management)

**Why others are wrong:**
- A: ECS on EC2 requires managing EC2 instances
- C: EKS on EC2 requires managing instances and Kubernetes
- D: Lambda runs functions, not containers (Lambda does support container images now, but Fargate is more flexible for containers)

---

### Question 40
An application running on EC2 needs to retrieve database credentials securely without hardcoding them. What should the architect use?

A. Store in environment variables  
B. Store in S3  
C. Store in Secrets Manager  
D. Store in code  

**Answer: C**

**Why correct:** Secrets Manager is designed for storing and rotating secrets securely

**Why others are wrong:**
- A: Environment variables are less secure (visible in process list)
- B: S3 is for objects, not secrets management
- D: Hardcoding is a security risk

---

# SECTION 6: TRICK QUESTIONS & EXAM TRAPS

The AWS exam uses specific language to mislead. Recognizing these patterns eliminates 2-3 wrong answers immediately.

## Common Traps

### Trap 1: "Most cost-effective" vs "Most secure" vs "Fastest to implement"

The exam often has multiple correct solutions with different priorities.

**Keywords:**
- **Cost-effective:** Cheapest option that meets requirements
- **Secure:** Best security practices
- **Fastest/Quickest:** Least time to implement
- **Scalable:** Can handle growth
- **Operationally efficient:** Least maintenance

**Example:**
- Cost-effective database → RDS (cheaper than Aurora)
- High-performance database → Aurora (faster than RDS)
- Fastest to deploy → Default VPC (vs custom VPC)

### Trap 2: Multi-AZ vs Read Replicas

**Multi-AZ:**
- For high availability (failover)
- Synchronous replication
- Standby is NOT accessible for reads
- Single DNS endpoint (automatic failover)

**Read Replicas:**
- For read scaling
- Asynchronous replication
- Can be in different region
- Each has separate endpoint

**Trap:** "Increase read capacity" → Read Replicas (NOT Multi-AZ)

### Trap 3: S3 vs EBS vs EFS

**S3:** Object storage (files, images, backups)  
**EBS:** Block storage (EC2 disk)  
**EFS:** Shared file system (multiple EC2)

**Trap:** "Share files across instances" → Many students choose S3, but answer is EFS (file system semantics)

### Trap 4: Security Groups only ALLOW

Security Groups cannot deny traffic—only NACLs can.

**Trap:** "Block specific IP" → NACL (NOT Security Group)

### Trap 5: VPC Peering is Non-Transitive

If VPC A peers with VPC B, and VPC B peers with VPC C, VPC A cannot reach VPC C.

**Trap:** "Connect 10 VPCs" → Transit Gateway (NOT VPC Peering)

### Trap 6: Lambda 15-Minute Limit

Lambda functions time out at 15 minutes.

**Trap:** "Process for 20 minutes" → Use Fargate or EC2 (NOT Lambda)

### Trap 7: NAT Gateway is Per-AZ

NAT Gateway is not multi-AZ. For high availability, deploy NAT Gateway in each AZ.

**Trap:** "HA for private subnet internet access" → NAT Gateway in each AZ

### Trap 8: CloudFront vs S3 Transfer Acceleration

**CloudFront:** Speeds up content delivery (downloads)  
**S3 Transfer Acceleration:** Speeds up uploads to S3

**Trap:** "Fast uploads to S3 from worldwide" → Transfer Acceleration (NOT CloudFront)

### Trap 9: Glacier Retrieval Times

- **Instant Retrieval:** Milliseconds (like S3)
- **Flexible Retrieval:** Minutes to hours (Expedited 1-5 min, Standard 3-5 hrs)
- **Deep Archive:** 12-48 hours

**Trap:** "Archive with occasional instant access" → Glacier Instant Retrieval (NOT Flexible)

### Trap 10: Spot Instances Can Be Terminated

Spot Instances are cheap but can be terminated with 2-minute warning.

**Trap:** "Cost-effective for critical production app" → Reserved or On-Demand (NOT Spot, unless app is fault-tolerant)

### Trap 11: EBS Cannot Attach Across AZs

EBS volumes are AZ-specific.

**Trap:** "Attach EBS to instance in different AZ" → Snapshot → Create volume in new AZ

### Trap 12: IAM is Global

IAM users, roles, policies are global (not region-specific).

**Trap:** "Create IAM user in specific region" → IAM is global, not regional

### Trap 13: S3 Bucket Names are Globally Unique

Even though S3 is region-specific, bucket names must be unique worldwide.

**Trap:** "Create bucket with same name in different region" → Cannot (names are globally unique)

### Trap 14: Default VPC vs Custom VPC

- **Default VPC:** All subnets are public, internet gateway attached
- **Custom VPC:** No internet gateway by default, all subnets private by default

**Trap:** "Launch EC2 with internet access" → Default VPC (easiest) or Custom VPC with IGW + public subnet

### Trap 15: RDS Automated Backups vs Snapshots

- **Automated backups:** Enabled by default, 1-35 days retention, deleted when RDS deleted
- **Manual snapshots:** Kept until manually deleted

**Trap:** "Long-term backup" → Manual snapshots (NOT automated)

### Trap 16: ElastiCache Redis vs Memcached

**Redis:** Persistence, replication, complex data structures  
**Memcached:** Simple, no persistence, multi-threaded

**Trap:** "In-memory cache with persistence" → Redis

### Trap 17: ALB vs NLB

**ALB:** HTTP/HTTPS, Layer 7, path/host routing  
**NLB:** TCP/UDP, Layer 4, extreme performance, static IP

**Trap:** "Static IP required" → NLB (ALB doesn't provide static IP)

### Trap 18: DynamoDB Eventually Consistent vs Strongly Consistent Reads

- **Eventually consistent:** Default, cheaper (1 RCU)
- **Strongly consistent:** Costs double (2 RCU), always up-to-date

**Trap:** "Reduce cost for reads" → Eventually consistent

### Trap 19: Step Functions vs Lambda

**Lambda:** Single task, <15 min  
**Step Functions:** Orchestrate workflows, long-running

**Trap:** "Coordinate multiple Lambdas with error handling" → Step Functions

### Trap 20: Secrets Manager vs Parameter Store

**Secrets Manager:** Automatic rotation, more expensive  
**Parameter Store:** Static config, cheaper

**Trap:** "Auto-rotate RDS password" → Secrets Manager (NOT Parameter Store)

---

## Misleading Keywords

| Keyword in Question | What They're Really Asking |
|---------------------|----------------------------|
| "Most secure" | Use encryption, least privilege, private subnets |
| "Most cost-effective" | Cheapest solution that works |
| "Fastest to implement" | Default options, managed services |
| "Least operational overhead" | Managed services over DIY |
| "Highly available" | Multi-AZ, Auto Scaling, redundancy |
| "Scalable" | Can handle growth automatically |
| "Durable" | Won't lose data (11 nines for S3) |
| "Low latency" | In-memory cache, CloudFront, regional resources |
| "Real-time" | Streaming (Kinesis), not batch |
| "Serverless" | Lambda, DynamoDB, S3, API Gateway |
| "Audit" | CloudTrail, CloudWatch Logs, Config |

---

## Answer Elimination Strategies

### Strategy 1: Eliminate Clearly Wrong

If an option mentions a service not related to the problem, eliminate it.

**Example:** Database question → Eliminate answers mentioning S3 or CloudFront

### Strategy 2: "Best" vs "Cheapest"

- **"Best"** usually means AWS recommended approach (managed, HA, secure)
- **"Cheapest"** might mean less ideal but functional

### Strategy 3: Managed Service Bias

When in doubt, AWS favors managed services over self-managed.

**Example:** NAT Gateway > NAT Instance, Aurora > self-managed database on EC2

### Strategy 4: Security Defaults

Assume question wants most secure unless it says "cost-effective."

**Example:** Encrypted by default, private subnets, IAM roles over access keys

---

# SECTION 7: MEMORY HACKS

## Mnemonics

### Storage Services: "Object, Block, File, Archive"
- **S3** = Object (think: S3 = "Simple Storage Service" = objects like photos)
- **EBS** = Block (think: EBS = "Elastic Block Store" = blocks for EC2)
- **EFS** = File (think: EFS = "Elastic File System" = shared files)
- **Glacier** = Archive (think: cold storage = ice = Glacier)

### Database ACID vs BASE
- **ACID** (RDS) = Relational, consistent, transactions
- **BASE** (DynamoDB) = NoSQL, eventually consistent, scalable

### Load Balancer Layers: "7-4-old"
- **ALB** = Layer 7 (HTTP/HTTPS)
- **NLB** = Layer 4 (TCP/UDP)
- **CLB** = Old/legacy (don't use)

### High Availability: "Multi-AZ = HA"
- Multi-AZ RDS = High Availability (not scaling)
- Auto Scaling across AZs = High Availability
- S3 = Multi-AZ by default

### Security: "Keys, Roles, Groups, ACLs"
- **KMS** = Keys (encryption)
- **IAM Roles** = Temporary credentials for services
- **Security Groups** = Instance firewall (allow only)
- **NACLs** = Subnet firewall (allow and deny)

---

## Pattern Recognition

### Pattern 1: "Shared across multiple EC2" = EFS
Anytime question mentions sharing files across instances → EFS

### Pattern 2: "Event-driven" = Lambda
Anytime something triggers on an event → Lambda

### Pattern 3: "Archive for years" = Glacier
Long-term storage at lowest cost → Glacier Deep Archive

### Pattern 4: "Cache" = ElastiCache
Speed up reads, reduce database load → ElastiCache

### Pattern 5: "Queue" = SQS
Decouple components, buffer messages → SQS

### Pattern 6: "Pub/Sub" = SNS
Send notification to multiple subscribers → SNS

### Pattern 7: "Serverless" = Lambda + DynamoDB + S3 + API Gateway
Fully managed, no servers → Serverless stack

### Pattern 8: "Hybrid" = Direct Connect or VPN
Connect on-premises to AWS → Direct Connect (dedicated) or VPN (quick)

### Pattern 9: "Global low latency" = CloudFront
Distribute content worldwide → CloudFront

### Pattern 10: "Audit logs" = CloudTrail
Who did what when → CloudTrail

---

## Fast Guessing Strategies

### If You Have No Idea, Choose:

1. **Managed service over self-managed**
   - NAT Gateway > NAT Instance
   - RDS > Database on EC2
   - Aurora > RDS

2. **Security: Always encrypt and use private**
   - Encrypted > unencrypted
   - Private subnet > public subnet
   - IAM role > access keys

3. **High Availability: Multi-AZ options**
   - Multi-AZ > single AZ
   - Auto Scaling > single instance
   - Multiple regions > single region

4. **Cost: Reserved > On-Demand > Spot (for appropriate workloads)**
   - Long-term → Reserved
   - Variable → On-Demand
   - Interruptible → Spot

5. **When multiple answers seem right, pick the most AWS-native**
   - Lambda > EC2 for event-driven
   - DynamoDB > RDS for scale
   - CloudFormation > manual setup

---

## Acronym Helper

**S3 GLACIER WORM:** Write Once, Read Many (Vault Lock for compliance)

**RDS MARIA POMS:** MySQL, Aurora, RDS, IAM, A (Aurora again), PostgreSQL, Oracle, MariaDB, SQL Server

**EBS TYPES "gp-io-st-sc":**
- **gp3/gp2** = General Purpose SSD
- **io2/io1** = Provisioned IOPS SSD
- **st1** = Throughput Optimized HDD
- **sc1** = Cold HDD

**IAM "UGPR":** Users, Groups, Policies, Roles

**VPC "SINR":** Subnets, Internet Gateway, NAT Gateway, Route Tables

**ELB "ANL":** ALB, NLB, (CLB - old)

---

# SECTION 8: HANDS-ON LAB SCENARIOS

Hands-on experience helps solidify concepts. Here are key labs to understand.

## Lab 1: Launch EC2 with User Data

**Goal:** Launch an EC2 instance that auto-installs a web server

**Services:** EC2, VPC

**What it teaches:** 
- EC2 basics
- User Data bootstrap scripts
- Security groups
- Public/private IP

**Why important:** Common in real scenarios, tests Auto Scaling knowledge

**Steps:**
1. Launch EC2 in default VPC
2. Add User Data script to install Apache/Nginx
3. Configure security group (allow HTTP port 80)
4. Access via public IP

---

## Lab 2: Host Static Website on S3 + CloudFront

**Goal:** Host HTML website on S3, distribute via CloudFront with custom domain

**Services:** S3, CloudFront, Route 53, ACM

**What it teaches:**
- S3 static hosting
- CloudFront distributions
- DNS with Route 53
- SSL/TLS certificates

**Why important:** Very common architecture, tests understanding of edge delivery

**Steps:**
1. Create S3 bucket, upload HTML files
2. Enable static website hosting
3. Create CloudFront distribution with S3 as origin
4. Request ACM certificate for custom domain
5. Configure Route 53 Alias to CloudFront

---

## Lab 3: RDS Multi-AZ with Read Replica

**Goal:** Create highly available database with read scaling

**Services:** RDS

**What it teaches:**
- Multi-AZ for failover
- Read Replicas for scaling
- Difference between HA and scaling

**Why important:** Core database concept on exam

**Steps:**
1. Launch RDS MySQL with Multi-AZ enabled
2. Create Read Replica
3. Test failover (reboot with failover)
4. Query Read Replica endpoint

---

## Lab 4: VPC with Public and Private Subnets

**Goal:** Create custom VPC with internet-facing and private resources

**Services:** VPC, EC2, NAT Gateway

**What it teaches:**
- VPC architecture
- Public vs private subnets
- Internet Gateway and NAT Gateway
- Route tables

**Why important:** Foundational networking knowledge

**Steps:**
1. Create VPC (10.0.0.0/16)
2. Create public subnet (10.0.1.0/24) and private subnet (10.0.2.0/24)
3. Create Internet Gateway, attach to VPC
4. Create NAT Gateway in public subnet
5. Configure route tables (public → IGW, private → NAT)
6. Launch EC2 in public subnet (web server)
7. Launch EC2 in private subnet (database)

---

## Lab 5: Auto Scaling + Load Balancer

**Goal:** Automatically scale web servers based on load

**Services:** EC2, Auto Scaling, ALB, CloudWatch

**What it teaches:**
- Auto Scaling Groups
- Load balancing
- Health checks
- Scaling policies

**Why important:** Key for HA and scaling questions

**Steps:**
1. Create Launch Template with User Data (web server)
2. Create Auto Scaling Group (min 2, max 5, desired 2)
3. Create ALB, attach to ASG
4. Configure health checks
5. Create scaling policy (target CPU 50%)
6. Test by generating load

---

## Lab 6: Lambda Triggered by S3

**Goal:** Process files uploaded to S3 automatically

**Services:** Lambda, S3, CloudWatch

**What it teaches:**
- Event-driven architecture
- Lambda triggers
- Serverless patterns

**Why important:** Common serverless pattern

**Steps:**
1. Create S3 bucket
2. Create Lambda function (e.g., resize image)
3. Configure S3 event notification to trigger Lambda
4. Upload file to S3, verify Lambda executes
5. Check CloudWatch Logs

---

## Lab 7: DynamoDB with Lambda

**Goal:** Build serverless API for CRUD operations

**Services:** Lambda, DynamoDB, API Gateway

**What it teaches:**
- DynamoDB operations
- Lambda + DynamoDB integration
- API Gateway

**Why important:** Classic serverless architecture

**Steps:**
1. Create DynamoDB table
2. Create Lambda functions (Create, Read, Update, Delete)
3. Create API Gateway REST API
4. Connect API methods to Lambda functions
5. Test CRUD operations via API

---

## Lab 8: CloudWatch Alarms + SNS

**Goal:** Get notified when EC2 CPU > 80%

**Services:** CloudWatch, SNS, EC2

**What it teaches:**
- CloudWatch metrics and alarms
- SNS notifications
- Monitoring

**Why important:** Operational monitoring

**Steps:**
1. Launch EC2 instance
2. Create SNS topic, subscribe email
3. Create CloudWatch Alarm (CPU > 80%, trigger SNS)
4. Generate CPU load, verify email received

---

## Lab 9: VPN Connection to VPC

**Goal:** Connect on-premises (simulated) to AWS VPC

**Services:** VPC, VPN

**What it teaches:**
- Hybrid cloud connectivity
- Site-to-Site VPN

**Why important:** Common enterprise requirement

**Steps:**
1. Create VPC
2. Create Virtual Private Gateway, attach to VPC
3. Create Customer Gateway (on-premises router IP)
4. Create Site-to-Site VPN connection
5. Configure routing
6. Test connectivity

---

## Lab 10: EFS Shared Storage

**Goal:** Share files across multiple EC2 instances

**Services:** EFS, EC2

**What it teaches:**
- Shared file systems
- NFS mounting

**Why important:** Understanding when to use EFS vs EBS

**Steps:**
1. Create EFS file system
2. Launch 2 EC2 instances in different AZs
3. Install NFS client on both
4. Mount EFS on both instances
5. Create file on one instance, verify visible on other

---

## Lab 11: Direct Connect (Simulated)

**Goal:** Understand Direct Connect architecture

**Services:** Direct Connect, VPC

**What it teaches:**
- Dedicated network connection
- Difference from VPN

**Why important:** Enterprise hybrid cloud

**Note:** Requires actual Direct Connect setup (costly), study architecture instead

---

## Lab 12: Redshift Data Warehouse

**Goal:** Load data into Redshift and run analytics queries

**Services:** Redshift, S3

**What it teaches:**
- Data warehousing
- OLAP vs OLTP
- Redshift Spectrum

**Why important:** Big data analytics

**Steps:**
1. Create Redshift cluster
2. Upload data to S3
3. Load data from S3 to Redshift (COPY command)
4. Run SQL analytics queries
5. (Optional) Query S3 data directly with Spectrum

---

## Lab 13: CloudFront with S3 Origin

**Goal:** Speed up global content delivery

**Services:** CloudFront, S3

**What it teaches:**
- CDN basics
- Edge locations
- Caching behavior

**Why important:** Common web architecture

**Steps:**
1. Upload files to S3
2. Create CloudFront distribution (S3 as origin)
3. Configure cache behavior (TTL)
4. Test access via CloudFront URL
5. Measure latency improvement

---

## Lab 14: IAM Roles for EC2

**Goal:** EC2 accesses S3 without access keys

**Services:** IAM, EC2, S3

**What it teaches:**
- IAM roles
- Secure access without keys
- Least privilege

**Why important:** Security best practice

**Steps:**
1. Create IAM role with S3 read permissions
2. Launch EC2, attach IAM role
3. SSH to EC2, use AWS CLI to access S3 (no keys needed)
4. Verify access

---

## Lab 15: Step Functions Workflow

**Goal:** Orchestrate multiple Lambda functions

**Services:** Step Functions, Lambda

**What it teaches:**
- Workflow orchestration
- Error handling
- State machines

**Why important:** Complex serverless workflows

**Steps:**
1. Create 3 Lambda functions (e.g., validate, process, notify)
2. Create Step Functions state machine
3. Define workflow (sequential, parallel, error handling)
4. Execute state machine, monitor progress

---

# SECTION 9: RAPID REVISION

## One-Page Summary

### Compute
- **EC2:** Virtual servers, use for control and long-running tasks
- **Lambda:** Serverless functions, max 15 min, event-driven
- **Auto Scaling:** Automatically adjust EC2 count
- **Elastic Beanstalk:** PaaS, deploy without managing infrastructure

### Storage
- **S3:** Object storage, unlimited, storage classes for cost optimization
- **EBS:** Block storage for EC2, AZ-specific
- **EFS:** Shared file system, multi-AZ
- **Glacier:** Archive storage, retrieval times vary

### Databases
- **RDS:** Managed relational databases, Multi-AZ for HA
- **Aurora:** AWS-optimized MySQL/PostgreSQL, 5x performance
- **DynamoDB:** NoSQL, millisecond latency, auto-scales
- **ElastiCache:** In-memory cache (Redis or Memcached)
- **Redshift:** Data warehouse, petabyte-scale analytics

### Networking
- **VPC:** Isolated network, public and private subnets
- **IGW:** Internet access for public subnet
- **NAT Gateway:** Internet for private subnet (outbound)
- **ELB:** Load balancers (ALB for HTTP, NLB for TCP/UDP)
- **Route 53:** DNS, routing policies, health checks
- **CloudFront:** CDN, global content delivery
- **Direct Connect:** Dedicated connection to AWS
- **VPN:** Encrypted connection over internet

### Security
- **IAM:** Users, groups, roles, policies
- **KMS:** Encryption key management
- **Security Groups:** Instance firewall (stateful, allow only)
- **NACLs:** Subnet firewall (stateless, allow and deny)
- **WAF:** Web application firewall
- **Shield:** DDoS protection

### Integration
- **SQS:** Message queue, decouple components
- **SNS:** Pub/sub notifications
- **EventBridge:** Event bus, schedule tasks
- **Step Functions:** Workflow orchestration

### Monitoring
- **CloudWatch:** Metrics, alarms, logs
- **CloudTrail:** API call logs (audit)
- **Config:** Configuration compliance

### Migration
- **DMS:** Database migration
- **Snow Family:** Offline data transfer (Snowball, Snowmobile)
- **DataSync:** Online data sync

---

## Key Facts to Memorize

1. **Lambda timeout:** 15 minutes max
2. **RDS Multi-AZ:** Synchronous, failover only (not for reads)
3. **RDS Read Replicas:** Asynchronous, for scaling reads
4. **S3 durability:** 11 nines (99.999999999%)
5. **S3 availability:** 99.99% (Standard), 99.9% (IA)
6. **EBS:** AZ-specific, cannot attach across AZs
7. **EFS:** Multi-AZ, Linux only
8. **Security Groups:** Stateful, allow only
9. **NACLs:** Stateless, allow and deny, rule order matters
10. **NAT Gateway:** Per AZ (not multi-AZ)
11. **VPC Peering:** Non-transitive
12. **IAM:** Global, not region-specific
13. **S3 bucket names:** Globally unique
14. **CloudFront:** HTTP/HTTPS, caching
15. **Global Accelerator:** TCP/UDP, static IP, AWS network
16. **DynamoDB:** Eventually consistent (default), strongly consistent (2x cost)
17. **ElastiCache Redis:** Persistence, replication
18. **ElastiCache Memcached:** No persistence, multi-threaded
19. **ALB:** Layer 7, path/host routing
20. **NLB:** Layer 4, extreme performance, static IP
21. **Route 53 Alias:** Free queries, can use for zone apex
22. **Spot Instances:** Up to 90% discount, can be terminated
23. **Reserved Instances:** 1-3 year commitment, up to 72% discount
24. **Glacier Deep Archive:** 12-48 hour retrieval, cheapest
25. **KMS:** Regional, keys cannot be exported

---

## Most Asked Concepts

1. **When to use Multi-AZ vs Read Replicas** (RDS)
2. **S3 vs EBS vs EFS** (storage types)
3. **Security Groups vs NACLs** (firewall differences)
4. **Public subnet vs Private subnet** (internet access)
5. **ALB vs NLB** (load balancer types)
6. **Lambda use cases and limits** (15 min, serverless)
7. **SQS vs SNS** (queue vs pub/sub)
8. **DynamoDB vs RDS** (NoSQL vs SQL)
9. **CloudFront vs S3 Transfer Acceleration vs Global Accelerator**
10. **VPN vs Direct Connect** (hybrid connectivity)
11. **Auto Scaling policies** (target tracking, scheduled, step)
12. **S3 storage classes** (Standard, IA, Glacier)
13. **IAM roles for EC2** (secure access without keys)
14. **VPC architecture** (subnets, route tables, gateways)
15. **High availability patterns** (Multi-AZ, Auto Scaling, ELB)
16. **Cost optimization** (Reserved, Spot, lifecycle policies)
17. **Encryption** (at-rest with KMS, in-transit with TLS)
18. **CloudWatch vs CloudTrail vs Config** (monitoring differences)
19. **Disaster recovery strategies** (backup/restore, pilot light, warm standby, multi-site)
20. **Serverless architecture** (Lambda, API Gateway, DynamoDB, S3)

---

## Final Exam Tips

1. **Read the question carefully:** Identify keywords (cost-effective, secure, fastest, scalable)
2. **Eliminate obviously wrong answers:** Remove 1-2 options immediately
3. **Look for qualifiers:** "MOST," "BEST," "LEAST"
4. **Time management:** 130 minutes for 65 questions = 2 minutes per question
5. **Flag and return:** Skip hard questions, come back later
6. **No penalty for guessing:** Answer every question
7. **Watch for "NOT" questions:** Read carefully
8. **Multi-AZ = HA, Read Replica = Scaling:** Don't confuse
9. **Managed > Self-managed:** AWS prefers managed services
10. **Security defaults:** Assume encrypted, private, least privilege

---

## Good Luck!

You're prepared. Trust your knowledge. When in doubt:
1. Eliminate wrong answers
2. Choose AWS-native, managed solutions
3. Prioritize security and high availability
4. Match keyword in question to service strength

**Remember:** The exam tests decision-making, not memorization. Think like a solutions architect: understand requirements, constraints, and trade-offs.

---

**END OF CHEATSHEET**
