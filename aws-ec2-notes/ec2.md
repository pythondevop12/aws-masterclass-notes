# 🚀 AWS EC2 Masterclass: From Beginner to Pro
> **Instructor:** PythonDevOps Academy  
> **Topic:** Mastering Elastic Compute Cloud (EC2)  
> **Level:** Beginner → Advanced  

---

## 📌 Table of Contents
1. [What Exactly is EC2?](#1-what-exactly-is-ec2)
2. [Decoding the Instance Name](#2-decoding-the-name-t3medium)
3. [Instance Families](#3-instance-families-the-right-tool-for-the-job)
4. [Purchasing Options: Save Up to 90%](#4-purchasing-options-save-up-to-90)
5. [EBS: Elastic Block Store](#5-ebs-elastic-block-store)
6. [Quick Reference Cheatsheet](#6-quick-reference-cheatsheet)

---

## 1. What Exactly is EC2?

**Elastic Compute Cloud (EC2)** provides virtual servers (called **instances**) hosted in AWS data centers globally. Think of it as renting a computer in the cloud — you control the OS, software, and configuration.

| Letter | Stands For | What It Means |
| :--- | :--- | :--- |
| **E** | Elastic | Scale up or down in minutes — pay only for what you use |
| **C** | Compute | CPU + RAM — the processing power of your application |
| **2** | Cloud (v2) | Hosted globally across AWS Regions and Availability Zones |

> 💡 **Mental Model:** An EC2 instance is like a laptop in the cloud — you pick the specs (CPU/RAM/storage), choose the OS, and pay by the second while it's running.

---

## 2. Decoding the Name: "t3.medium"

Understanding the naming convention is a **common interview question** and critical for choosing the right instance.

```
┌──────────────────────────────────────────────────────────┐
│              Instance Type: t3a.medium                   │
│                                                          │
│   t      3      a    .    medium                         │
│   │      │      │         │                              │
│   │      │      │         └── Size: nano/micro/small/    │
│   │      │      │              medium/large/xlarge/2xl   │
│   │      │      │                                        │
│   │      │      └─────────── Processor variant:          │
│   │      │                   (a = AMD, g = Graviton ARM, │
│   │      │                    i = Intel, blank = mixed)  │
│   │      │                                               │
│   │      └────────────────── Generation: higher = newer  │
│   │                           hardware & better perf/$$  │
│   │                                                      │
│   └───────────────────────── Family: t = General Purpose │
└──────────────────────────────────────────────────────────┘
```

### Size Scaling (vCPU / RAM)

| Size | vCPU | RAM (t3 example) | Relative Cost |
| :--- | :---: | :---: | :--- |
| nano | 2 | 0.5 GiB | 💰 |
| micro | 2 | 1 GiB | 💰💰 |
| small | 2 | 2 GiB | 💰💰💰 |
| medium | 2 | 4 GiB | 💰💰💰💰 |
| large | 2 | 8 GiB | 💰💰💰💰💰 |
| xlarge | 4 | 16 GiB | 💰💰💰💰💰💰 |
| 2xlarge | 8 | 32 GiB | 💰💰💰💰💰💰💰 |

---

## 3. Instance Families (The "Right Tool for the Job")

| Family | Name | Specialty | vCPU:RAM Ratio | Real-World Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **T** | Burstable General Purpose | Baseline CPU + burst credits | 1:2 | Dev/Test environments, low-traffic websites |
| **M** | General Purpose | Balanced CPU/RAM | 1:4 | Web servers, app servers, microservices |
| **C** | Compute Optimized | High-performance CPU | 1:2 | Scientific modeling, batch processing, gaming |
| **R** | Memory Optimized | High RAM | 1:8 | In-memory databases (Redis, Memcached), SAP HANA |
| **I** | Storage Optimized | High NVMe I/O | Balanced | OLTP databases, Elasticsearch, Kafka |
| **G** | GPU General | NVIDIA GPU | — | ML inference, video transcoding |
| **P** | GPU HPC | NVIDIA High-Perf GPU | — | Deep learning training, HPC |

### 🔍 How to Choose

```
Is your bottleneck...

  CPU-bound (slow calculations)?       ──► C family (c6g, c7g)
  RAM-bound (out of memory)?           ──► R family (r6g, r7g)
  Disk I/O bound (slow reads/writes)?  ──► I family (i4i, i3en)
  GPU needed (ML/rendering)?           ──► G or P family (g5, p4)
  General / unsure?                    ──► M or T family (m6g, t3)
```

---

## 4. Purchasing Options: Save Up to 90%

*This is where Cloud Engineers save companies millions of dollars annually.*

### 🟢 On-Demand
- **Pay by the second** (minimum 60 seconds). No commitment.
- **Use for:** New projects, unpredictable workloads, short experiments.
- **Cost:** Highest (baseline price).

### 🔵 Reserved Instances (RI) / Savings Plans
- **Commit for 1 or 3 years** in exchange for a significant discount.
- **Use for:** Stable, always-on workloads — production databases, backend APIs.
- **Discount:** Up to **72%** vs On-Demand.

| Option | Flexibility | Discount |
| :--- | :--- | :--- |
| Standard RI | Low (locked instance type) | Up to 72% |
| Convertible RI | Medium (can change family) | Up to 54% |
| Compute Savings Plan | High (any instance type/region) | Up to 66% |

> 💡 **Pro Tip:**  **Savings Plans are preferred over RIs** — more flexible and simpler to manage.

### 🟠 Spot Instances
- **Bid for unused AWS capacity** at massive discounts.
- ⚠️ **WARNING:** AWS can reclaim them with only a **2-minute notice!**
- **Use for:** CI/CD workers, batch jobs, Spark/EMR clusters, ML training.
- **Discount:** Up to **90%** vs On-Demand.

### 🟡 Dedicated Hosts
- **Physical server dedicated to your account.** No sharing with other AWS customers.
- **Use for:** Compliance requirements (HIPAA, PCI-DSS), BYOL licensing for Oracle/Windows Server.

---

```
Stop vs. Terminate

  ┌─────────┐   Stop    ┌──────────────────────────────┐
  │ Running │ ────────► │ Stopped (EBS data preserved)  │
  └─────────┘           └──────────────────────────────┘

  ┌─────────┐ Terminate ┌────────────────────────────────────────┐
  │ Running │ ────────► │ Terminated (root EBS deleted by default │
  └─────────┘           │ unless DeleteOnTermination = false)     │
                        └────────────────────────────────────────┘
```

---

## 5. EBS: Elastic Block Store

**Elastic Block Store (EBS)** is the persistent, network-attached storage used with EC2 instances. Think of it as an external hard drive that automatically stays connected to your instance — and survives reboots and even instance termination (if configured).

> 💡 **Mental Model:** EBS is like a USB drive in the cloud — you can attach it to one EC2 instance at a time, detach it, and re-attach it elsewhere. Your data stays intact even when the instance is off.

### Key Characteristics

- **Persistent by default** — data survives instance stop/restart (unlike instance store).
- **Availability Zone-scoped** — an EBS volume lives in a specific AZ and can only attach to instances in the same AZ.
- **Snapshots** — point-in-time backups stored in S3; used to copy volumes across AZs or regions.
- **Encryption** — volumes can be encrypted at rest using AWS KMS, with zero performance overhead on modern instances.

### EBS Volume Types

| Type | Name | Best For | Max IOPS | Max Throughput |
| :--- | :--- | :--- | :---: | :---: |
| **gp3** | General Purpose SSD | Boot volumes, dev/test, most workloads | 16,000 | 1,000 MB/s |
| **gp2** | General Purpose SSD (legacy) | Older workloads (prefer gp3) | 16,000 | 250 MB/s |
| **io2 Block Express** | Provisioned IOPS SSD | Critical databases (Oracle, SAP HANA) | 256,000 | 4,000 MB/s |
| **st1** | Throughput Optimized HDD | Big data, log processing, data warehouses | 500 | 500 MB/s |
| **sc1** | Cold HDD | Infrequently accessed archives | 250 | 250 MB/s |

> 💡 **Pro Tip:** **Always use gp3 over gp2** — it's cheaper and lets you independently scale IOPS and throughput without increasing volume size.

### 🔍 How to Choose

```
Workload type...

  OS boot / general apps?              ──► gp3 (default choice)
  High-performance database?           ──► io2 Block Express
  Sequential big-data / logs?          ──► st1
  Cold archive / rarely accessed?      ──► sc1
```

### Common CLI Commands

```bash
# Create a 20 GiB gp3 volume
aws ec2 create-volume \
  --size 20 \
  --volume-type gp3 \
  --availability-zone us-east-1a

# Attach volume to an instance
aws ec2 attach-volume \
  --volume-id vol-0abcdef1234567890 \
  --instance-id i-1234567890abcdef0 \
  --device /dev/xvdf

# Take a snapshot
aws ec2 create-snapshot \
  --volume-id vol-0abcdef1234567890 \
  --description "my-backup-$(date +%Y%m%d)"
```

---

## 6. Quick Reference Cheatsheet

### Common AWS CLI Commands

```bash
# List all running instances
aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=running" \
  --query "Reservations[*].Instances[*].[InstanceId,InstanceType,PublicIpAddress]" \
  --output table

# Start / Stop / Terminate
aws ec2 start-instances     --instance-ids i-1234567890abcdef0
aws ec2 stop-instances      --instance-ids i-1234567890abcdef0
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0

# Get current Spot prices
aws ec2 describe-spot-price-history \
  --instance-types t3.micro \
  --product-descriptions "Linux/UNIX"

# Create AMI from running instance
aws ec2 create-image \
  --instance-id i-1234567890abcdef0 \
  --name "my-golden-ami-$(date +%Y%m%d)" \
  --no-reboot
```

### Key Instance Metadata Endpoints

```bash
# Instance ID
curl http://169.254.169.254/latest/meta-data/instance-id

# Instance type
curl http://169.254.169.254/latest/meta-data/instance-type

# Current Region
curl http://169.254.169.254/latest/meta-data/placement/region

# Check for Spot interruption notice (404 = safe, timestamp = interrupted)
curl http://169.254.169.254/latest/meta-data/spot/termination-time
```

### Cost Estimator (Approximate US-East-1 Pricing)

| Instance | On-Demand/hr | 1-Yr RI/hr | Spot/hr (est.) |
| :--- | :---: | :---: | :---: |
| t3.micro | $0.0104 | $0.006 | ~$0.003 |
| t3.medium | $0.0416 | $0.024 | ~$0.012 |
| m6g.large | $0.077 | $0.046 | ~$0.023 |
| c6g.xlarge | $0.136 | $0.082 | ~$0.041 |
| r6g.large | $0.1008 | $0.060 | ~$0.030 |

> ⚠️ Always verify at [https://aws.amazon.com/ec2/pricing/](https://aws.amazon.com/ec2/pricing/)

---

*Made with ❤️ by **PythonDevOps Academy** | Subscribe for more AWS content!*  
👉 https://www.youtube.com/@PythonDevOpsAcademy