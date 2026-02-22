# 🚀 AWS EC2 Masterclass: From Beginner to Pro 2026
> **Instructor:** PythonDevOps Academy  
> **Topic:** Mastering Elastic Compute Cloud (EC2)  
> **Level:** Beginner → Advanced  
> **Duration:** ~3 Hours  
> **Last Updated:** 2026

---

## 📌 Table of Contents
1. [What Exactly is EC2?](#1-what-exactly-is-ec2)
2. [Decoding the Instance Name](#2-decoding-the-name-t3medium)
3. [Instance Families](#3-instance-families-the-right-tool-for-the-job)
4. [Purchasing Options: Save Up to 90%](#4-purchasing-options-save-up-to-90)
5. [Storage: EBS, Instance Store & EFS](#5-storage-ebs-instance-store--efs)
6. [Networking: Security Groups & Elastic IPs](#6-networking-security-groups--elastic-ips)
7. [AMIs: Golden Images Explained](#7-amis-golden-images-explained)
8. [Auto Scaling & Load Balancing](#8-auto-scaling--load-balancing)
9. [The Decision Matrix (Real-World Thinking)](#9-the-decision-matrix-real-world-thinking)
10. [Pro Tips for 2026](#10-pro-tips-for-2026)
11. [Homework Lab](#11-homework-for-the-lab)
12. [Quick Reference Cheatsheet](#12-quick-reference-cheatsheet)

---

## 1. What Exactly is EC2?

**Elastic Compute Cloud (EC2)** is the backbone of AWS — it provides virtual servers (called **instances**) that run in AWS data centers around the world. Think of it as renting a computer in the cloud, where you control the OS, software, and configuration.

| Letter | Stands For | What It Means |
| :--- | :--- | :--- |
| **E** | Elastic | Scale up or down in minutes — pay only for what you use |
| **C** | Compute | CPU + RAM — the processing power of your application |
| **2** | Cloud (v2) | Hosted globally across AWS Regions and Availability Zones |

### Why EC2 Matters
- Powers millions of websites, APIs, and data pipelines globally
- Foundation of services like EKS, EMR, and CodeBuild under the hood
- Most AWS certifications (Solutions Architect, DevOps Pro) heavily test EC2 knowledge
- Understanding EC2 = understanding cloud infrastructure

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

> ⚠️ **Important:** Each family has different CPU/RAM ratios. `r6g.large` has much more RAM than `c6g.large` — always check the AWS documentation or `aws ec2 describe-instance-types` for exact specs.

---

## 3. Instance Families (The "Right Tool for the Job")

| Family | Name | Specialty | vCPU:RAM Ratio | Real-World Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **T** | Burstable General Purpose | Baseline CPU + burst credits | 1:2 | Dev/Test environments, low-traffic websites |
| **M** | General Purpose | Balanced CPU/RAM | 1:4 | Web servers, app servers, microservices |
| **C** | Compute Optimized | High-performance CPU | 1:2 | Scientific modeling, batch processing, gaming |
| **R** | Memory Optimized | High RAM | 1:8 | In-memory databases (Redis, Memcached), SAP HANA |
| **X** | Memory Extreme | Very High RAM | 1:15+ | Large-scale in-memory analytics, SAP |
| **I** | Storage Optimized | High NVMe I/O | Balanced | OLTP databases, Elasticsearch, Kafka |
| **D** | Dense Storage | High HDD Throughput | Balanced | Data warehousing, Hadoop, log processing |
| **G** | GPU General | NVIDIA GPU | — | ML inference, video transcoding |
| **P** | GPU HPC | NVIDIA High-Perf GPU | — | Deep learning training, HPC |
| **Inf** | Inferentia | AWS ML Chip | — | Cost-efficient ML inference at scale |
| **Trn** | Trainium | AWS Training Chip | — | Large-scale model training (LLMs) |

### 🔍 How to Choose: A Simple Flowchart

```
Is your bottleneck...

  CPU-bound (slow calculations)?  ──────► C family (c6g, c7g)
  
  RAM-bound (out of memory)?  ──────────► R or X family (r6g, x2gd)
  
  Disk I/O bound (slow reads/writes)?  ─► I or D family (i4i, d3)
  
  GPU needed (ML/rendering)?  ──────────► G or P family (g5, p4)
  
  General / unsure?  ────────────────────► M or T family (m6g, t3)
```

---

## 4. Purchasing Options: Save Up to 90%

*This is where Cloud Engineers save companies millions of dollars annually.*

### 🟢 On-Demand Instances

- **Pay by the second** (minimum 60 seconds).
- **No commitment** — start and stop whenever you want.
- **Use for:** New projects, unpredictable or spiky workloads, short experiments.
- **Cost:** Highest (baseline price).

```bash
# Example: Launch an On-Demand instance via AWS CLI
aws ec2 run-instances \
  --image-id ami-0abcdef1234567890 \
  --instance-type t3.micro \
  --key-name MyKeyPair
```

---

### 🔵 Reserved Instances (RI) / Savings Plans

- **Commit for 1 or 3 years** in exchange for a significant discount.
- **Two payment options:** All Upfront, Partial Upfront, or No Upfront.
- **Use for:** Stable, always-on workloads — production databases, backend APIs.
- **Discount:** Up to **72%** vs On-Demand.

| Option | Flexibility | Discount | Best For |
| :--- | :--- | :--- | :--- |
| Standard RI | Low (locked instance type) | Up to 72% | Known, stable workloads |
| Convertible RI | Medium (can change family) | Up to 54% | Workloads that may evolve |
| Compute Savings Plan | High (any instance type/region) | Up to 66% | Flexible teams/orgs |
| EC2 Savings Plan | High (any size in a family) | Up to 72% | Single region workloads |

> 💡 **Pro Tip:** In 2026, **Savings Plans are preferred over RIs** for most use cases — they're more flexible and simpler to manage.

---

### 🟠 Spot Instances

- **Bid for unused AWS capacity** at massive discounts.
- ⚠️ **WARNING:** AWS can reclaim them with only a **2-minute notice!**
- **Use for:** Stateless apps, CI/CD workers, batch jobs, Spark/EMR clusters, ML training.
- **Discount:** Up to **90%** vs On-Demand.

```bash
# Example: Request a Spot Instance
aws ec2 request-spot-instances \
  --instance-count 1 \
  --type "one-time" \
  --launch-specification file://spot-config.json
```

**Spot Interruption Handling Best Practices:**
- Use **Spot Interruption Notices** (via EC2 metadata endpoint) to gracefully drain work.
- Design jobs to be **checkpointed** — save progress so work can resume on a new instance.
- Use **Spot Fleet** or **EC2 Auto Scaling with mixed instances** for resilience.

---

### 🟡 Dedicated Hosts / Dedicated Instances

- **Physical server dedicated to your account** — no sharing with other AWS customers.
- **Use for:** Compliance requirements (HIPAA, PCI-DSS), BYOL (Bring Your Own License) for Oracle/Windows Server.
- **Cost:** Highest tier, but required for certain enterprise/regulatory workloads.

---

## 5. Storage: EBS, Instance Store & EFS

Understanding EC2 storage is **critical** — getting it wrong means data loss.

### 📦 EBS (Elastic Block Store) — Network-Attached Disk

| Volume Type | Use Case | Max IOPS | Max Throughput |
| :--- | :--- | :--- | :--- |
| gp3 (General Purpose SSD) | Default for most workloads | 16,000 | 1,000 MB/s |
| io2 Block Express | High-perf databases (Oracle, SQL Server) | 256,000 | 4,000 MB/s |
| st1 (Throughput HDD) | Big data, log processing | 500 | 500 MB/s |
| sc1 (Cold HDD) | Archival, infrequent access | 250 | 250 MB/s |

> ✅ **Best Practice (2026):** Always use **gp3** over gp2. gp3 is cheaper AND you can independently configure IOPS and throughput without paying for a larger volume.

### 💨 Instance Store — Ephemeral Local NVMe

- **Physically attached** to the host server — ultra-low latency.
- ⚠️ **Data is LOST** when instance is stopped, terminated, or fails.
- **Use for:** Temporary buffers, caches, scratch space (Spark shuffle, Kafka temp storage).

### 📁 EFS (Elastic File System) — Shared Network File System

- **Mount the same filesystem across multiple EC2 instances** simultaneously.
- **Use for:** Shared application configs, CMS media files, ML training data shared across nodes.
- Automatically scales — no provisioning needed.

```
Stop vs. Terminate (Critical Distinction!)

  ┌─────────┐   Stop    ┌─────────────────────────────┐
  │ Running │ ────────► │ Stopped (EBS data preserved) │
  └─────────┘           └─────────────────────────────┘

  ┌─────────┐ Terminate ┌───────────────────────────────────────┐
  │ Running │ ────────► │ Terminated (EBS ROOT deleted by default│
  └─────────┘           │ — unless DeleteOnTermination = false)  │
                        └───────────────────────────────────────┘
```

---

## 6. Networking: Security Groups & Elastic IPs

### 🔒 Security Groups — Virtual Firewalls

- Security Groups are **stateful** — if inbound traffic is allowed, the response is automatically allowed.
- Rules are **allow-only** — there is no "deny" rule (use NACLs for explicit deny).
- Multiple Security Groups can be attached to one instance.

```bash
# Allow SSH and HTTP inbound
aws ec2 authorize-security-group-ingress \
  --group-id sg-12345678 \
  --protocol tcp --port 22 --cidr 203.0.113.0/32   # Your IP only!

aws ec2 authorize-security-group-ingress \
  --group-id sg-12345678 \
  --protocol tcp --port 80 --cidr 0.0.0.0/0
```

> ⚠️ **Security Mistake to Avoid:** Never open port 22 (SSH) to `0.0.0.0/0` in production. Restrict to your IP or use **AWS Systems Manager Session Manager** (no SSH needed at all!).

### 🌐 Elastic IP (EIP)

- A **static public IPv4 address** that you can attach to any instance.
- **Survives** instance stop/start (unlike regular public IPs which change).
- ⚠️ **Cost:** AWS charges for EIPs that are **allocated but NOT attached** to a running instance (~$0.005/hr in 2026).

---

## 7. AMIs: Golden Images Explained

An **Amazon Machine Image (AMI)** is a snapshot of an EC2 instance — it includes the OS, applications, and configuration.

### AMI Lifecycle

```
EC2 Instance  ──── Create Image ────►  AMI (stored in S3)
                                          │
                                          ├── Launch in same Region
                                          ├── Copy to other Regions
                                          └── Share with other Accounts
```

### Types of AMIs

| Type | Description | Use Case |
| :--- | :--- | :--- |
| AWS Managed | Maintained by AWS (Amazon Linux 2023, Ubuntu) | General starting point |
| AWS Marketplace | Pre-configured software (CIS Benchmarks, Palo Alto) | Security-hardened or licensed software |
| Community AMIs | Shared by the community — **use with caution** | Quick experiments only |
| Custom "Golden AMI" | Your own pre-baked, company-standard AMI | **Production standard** |

> 💡 **Golden AMI Pattern (Best Practice):** Pre-bake your AMI with your agents (CloudWatch, SSM), security hardening, and base packages. This makes Auto Scaling group launches faster and more consistent.

---

## 8. Auto Scaling & Load Balancing

### ⚖️ Auto Scaling Groups (ASG)

Auto Scaling ensures you always have the right number of instances — no over-provisioning, no under-provisioning.

```
          ┌──────────────────────────────────────────────┐
          │            Auto Scaling Group                 │
          │                                              │
          │  Min: 2   ──── Desired: 4 ──── Max: 10       │
          │                                              │
          │  [i-001] [i-002] [i-003] [i-004]             │
          │                                              │
          │  Scale Out ────► Traffic spikes              │
          │  Scale In  ────► Traffic drops               │
          └──────────────────────────────────────────────┘
```

**Scaling Policies:**

| Policy Type | How It Works | Best For |
| :--- | :--- | :--- |
| Target Tracking | Keep CPU at ~60% automatically | Most web workloads |
| Step Scaling | Add N instances when CPU > 80% | Predictable step patterns |
| Scheduled Scaling | Scale up at 8AM, down at 8PM | Known traffic patterns |
| Predictive Scaling | ML-powered, pre-scales ahead of demand | Variable but predictable loads |

### 🔀 Load Balancer Types

| Type | Layer | Use Case |
| :--- | :--- | :--- |
| ALB (Application) | L7 (HTTP/HTTPS) | Web apps, microservices, path-based routing |
| NLB (Network) | L4 (TCP/UDP) | Ultra-low latency, gaming, IoT |
| GWLB (Gateway) | L3 (IP) | Inline security appliances (firewall, IDS) |

---

## 9. The Decision Matrix (Real-World Thinking)

> Use this framework to answer EC2 questions in interviews and on the job.

### Scenario A: New startup, unknown traffic
```
Requirement: Launch a new startup website, no traffic data yet.
Instance:    t3.micro or t3.small
Purchase:    On-Demand
Reasoning:   Burstable performance handles variable traffic;
             On-Demand avoids commitment before product-market fit.
```

### Scenario B: Production database, 24/7
```
Requirement: MySQL production database running for 18+ months.
Instance:    r6g.large (Memory Optimized, Graviton)
Purchase:    1-Year Reserved Instance or Savings Plan
Reasoning:   R-family for high RAM; Graviton for cost savings;
             RI/Savings Plan for ~60-70% discount on known workload.
```

### Scenario C: 5-hour video encoding job
```
Requirement: Encode 10,000 videos overnight. Restartable if interrupted.
Instance:    c6g.2xlarge (Compute Optimized, Graviton)
Purchase:    Spot Instance
Reasoning:   C-family for CPU-intensive encoding; Spot saves up to 90%;
             Job is designed to checkpoint and resume on interruption.
```

### Scenario D: Real-time ML inference API (1ms latency)
```
Requirement: Serve a trained deep learning model at <1ms p99 latency.
Instance:    inf2.xlarge (AWS Inferentia2 chip)
Purchase:    Savings Plan (steady traffic) or On-Demand (variable)
Reasoning:   Inferentia chip is purpose-built for inference;
             Much cheaper than GPU instances (g5/p4) for inference workloads.
```

### Scenario E: Compliance-sensitive financial workload
```
Requirement: PCI-DSS compliant payment processing server.
Instance:    m6i.large (Dedicated Host)
Purchase:    Dedicated Host + Reserved
Reasoning:   Dedicated Host ensures physical isolation for compliance;
             No shared hardware with other AWS tenants.
```

---

## 10. Pro Tips for 2026

### 1. 🦾 Always Prefer Graviton (ARM) Instances
Look for the **"g"** in the instance name (e.g., `m6g`, `r7g`, `c6g`).
- Up to **40% better price/performance** than equivalent x86 Intel/AMD.
- Fully supported by Amazon Linux 2023, Ubuntu 22.04+, Docker, Java, Python, Node.js.
- AWS's own services (Lambda, RDS, ElastiCache) run on Graviton.

### 2. 🔑 Always Use IAM Roles — Never Hardcode Keys
```bash
# ❌ WRONG — Never do this
export AWS_ACCESS_KEY_ID="AKIA..."
export AWS_SECRET_ACCESS_KEY="..."

# ✅ CORRECT — Attach an IAM Role to the EC2 instance
# The AWS SDK automatically picks up credentials from the metadata service
aws s3 ls  # Works via role — no keys needed!
```

### 3. 🕵️ Use SSM Session Manager Instead of SSH
- No SSH port (22) needed — no inbound rules!
- Full audit trail of all commands in CloudTrail + CloudWatch Logs.
- Works in private subnets with no internet gateway.
```bash
aws ssm start-session --target i-1234567890abcdef0
```

### 4. 📊 Enable Detailed Monitoring
- Default CloudWatch metrics are every **5 minutes**.
- Enable Detailed Monitoring for **1-minute** granularity (~$3.50/month per instance).
- Essential for Auto Scaling groups that need to react quickly to traffic spikes.

### 5. 🏷️ Tag Everything
```bash
# Good tagging strategy
Name:        "api-prod-us-east-1a"
Environment: "production"
Team:        "platform"
CostCenter:  "CC-1042"
Project:     "checkout-service"
```
Tags enable cost allocation reports, automation, and AWS Config rules.

### 6. 🔄 Use User Data for Bootstrap Scripts
```bash
#!/bin/bash
yum update -y
yum install -y nginx
systemctl start nginx
systemctl enable nginx
echo "Hello from $(hostname)" > /usr/share/nginx/html/index.html
```
> Pass this as `--user-data` when launching — it runs once on first boot.

### 7. 💰 Enable Spot Interruption Handler in 2026
AWS now provides **2-minute warnings** before reclaiming Spot instances.
Hook into the EC2 metadata endpoint to gracefully drain connections:
```bash
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/spot/termination-time
```

---

## 11. Homework for the Lab 🧪

Complete these exercises in order. Each builds on the previous one.

### Lab 1: Launch Your First Instance (Free Tier)
- [ ] Launch a `t2.micro` (or `t3.micro`) — Free Tier eligible
- [ ] Use **Amazon Linux 2023** AMI
- [ ] Create a new Security Group: allow SSH (port 22) from **your IP only**
- [ ] Create and download a new `.pem` key pair
- [ ] Connect via **EC2 Instance Connect** (browser-based, no key needed)
- [ ] Run: `uname -a` and confirm you're on Linux

### Lab 2: Install & Serve a Web Page
- [ ] SSH into your instance or use Instance Connect
- [ ] Run: `sudo dnf install -y nginx` and `sudo systemctl start nginx`
- [ ] Update Security Group: add inbound rule for **HTTP (port 80)** from `0.0.0.0/0`
- [ ] Visit your **Public IPv4 DNS** in a browser — you should see the Nginx welcome page
- [ ] Edit `/usr/share/nginx/html/index.html` with a custom message
- [ ] Verify your custom page loads

### Lab 3: EBS Snapshot & AMI
- [ ] Create an EBS snapshot of your running instance's root volume
- [ ] Create a custom AMI from your running instance
- [ ] Launch a second instance from your custom AMI
- [ ] Confirm the web server is already running (no reinstallation needed!)

### Lab 4: Spot Instance (Cost Savings)
- [ ] Request a **Spot Instance** using the AWS Console (`t3.micro`)
- [ ] Set maximum price to On-Demand price
- [ ] Observe the **Spot Price History** graph in the console
- [ ] Terminate the Spot request and instance when done

### Lab 5: Auto Scaling Group (Bonus)
- [ ] Create a **Launch Template** from your custom AMI (Lab 3)
- [ ] Create an **Auto Scaling Group**: Min 1, Desired 1, Max 3
- [ ] Attach an **Application Load Balancer**
- [ ] Add a **Target Tracking policy**: target CPU at 50%
- [ ] Use `stress` to spike CPU and watch new instances launch automatically

---

## 12. Quick Reference Cheatsheet

### Common AWS CLI Commands

```bash
# List all running instances
aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=running" \
  --query "Reservations[*].Instances[*].[InstanceId,InstanceType,PublicIpAddress,Tags[?Key=='Name'].Value|[0]]" \
  --output table

# Start / Stop / Terminate
aws ec2 start-instances --instance-ids i-1234567890abcdef0
aws ec2 stop-instances --instance-ids i-1234567890abcdef0
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0

# Get current Spot prices
aws ec2 describe-spot-price-history \
  --instance-types t3.micro \
  --product-descriptions "Linux/UNIX" \
  --start-time $(date -u +%Y-%m-%dT%H:%M:%SZ)

# Create AMI from running instance
aws ec2 create-image \
  --instance-id i-1234567890abcdef0 \
  --name "my-golden-ami-$(date +%Y%m%d)" \
  --no-reboot
```

### Key Instance Metadata Endpoints

```bash
# Get instance ID
curl http://169.254.169.254/latest/meta-data/instance-id

# Get instance type
curl http://169.254.169.254/latest/meta-data/instance-type

# Get current Region
curl http://169.254.169.254/latest/meta-data/placement/region

# Check for Spot interruption notice
curl http://169.254.169.254/latest/meta-data/spot/termination-time
# Returns 404 if not being interrupted; returns timestamp if interrupted
```

### Cost Estimator (Approximate 2026 US-East-1 Pricing)

| Instance | On-Demand/hr | 1-Yr RI/hr | Spot/hr (est.) |
| :--- | :---: | :---: | :---: |
| t3.micro | $0.0104 | $0.006 | ~$0.003 |
| t3.medium | $0.0416 | $0.024 | ~$0.012 |
| m6g.large | $0.077 | $0.046 | ~$0.023 |
| c6g.xlarge | $0.136 | $0.082 | ~$0.041 |
| r6g.large | $0.1008 | $0.060 | ~$0.030 |

> ⚠️ Prices are approximate. Always verify at [https://aws.amazon.com/ec2/pricing/](https://aws.amazon.com/ec2/pricing/)

---

## 📚 Further Reading & Resources

| Resource | URL |
| :--- | :--- |
| EC2 Official Docs | https://docs.aws.amazon.com/ec2 |
| EC2 Pricing | https://aws.amazon.com/ec2/pricing/ |
| Graviton Processor Info | https://aws.amazon.com/ec2/graviton/ |
| AWS Savings Plans | https://aws.amazon.com/savingsplans/ |
| Instance Selector (CLI Tool) | https://github.com/aws/amazon-ec2-instance-selector |
| EC2 Instance Comparison | https://instances.vantage.sh/ |

---

## 🎓 AWS Certifications That Cover EC2

| Certification | EC2 Coverage | Difficulty |
| :--- | :--- | :--- |
| AWS Cloud Practitioner | Basic concepts | ⭐ |
| AWS Solutions Architect – Associate | Core service, deep coverage | ⭐⭐⭐ |
| AWS SysOps Administrator – Associate | Operational focus | ⭐⭐⭐ |
| AWS Developer – Associate | SDK, user data, metadata | ⭐⭐⭐ |
| AWS Solutions Architect – Professional | Advanced patterns, cost optimization | ⭐⭐⭐⭐⭐ |

---

*Made with ❤️ by **PythonDevOps Academy** | Subscribe for more AWS content in 2026!*