
# 📘 AWS VPC Complete Guide (Beginner → Production)

> A complete guide to understanding **Virtual Private Cloud (VPC)** — from fundamentals to real-world architecture.

---

# 📑 Table of Contents

1. What is VPC
2. CIDR & IP Addressing
3. Subnets & Routing
4. Security (SG vs NACL)
5. NAT, Peering & Transit Gateway
6. Production Architecture
7. Common Mistakes

---

# 🧠 1. What is a VPC?

A **Virtual Private Cloud (VPC)** is your **isolated private network inside AWS**.

Think of it like:

👉 **Your own data center inside AWS**

---

## 📌 Key Characteristics

* 🔒 **Isolated** → No access unless you allow it
* 🌍 **Region-wide** → Spans across all Availability Zones
* 📦 **Subnets are AZ-specific**
* 💰 **Free to create** (you pay for resources inside)
* ⚠️ Default limit: **5 VPCs per region**

---

## 🏗️ Basic Architecture

```
AWS Region (ap-south-1)

VPC (10.0.0.0/16)

├── AZ-a
│   ├── Public Subnet → ALB / EC2
│   └── Private Subnet → App / DB
│
├── AZ-b
│   ├── Public Subnet → ALB / EC2
│   └── Private Subnet → App / DB
│
└── Internet Gateway → Internet
```

---

# 🌐 2. CIDR & IP Addressing

CIDR defines your **IP range inside VPC**.

---

## 📌 Example

```
10.0.0.0/16
```

* `/16` → 65,536 IPs
* First part = Network
* Remaining = usable IPs

---

## 📊 CIDR Cheat Sheet

| CIDR | IPs     | Use Case             |
| ---- | ------- | -------------------- |
| /16  | 65,536  | Entire VPC           |
| /24  | 256     | Standard subnet      |
| /28  | 16      | Small subnet (DB)    |
| /0   | All IPs | Internet (0.0.0.0/0) |

---

## ⚠️ Important Rule

👉 AWS reserves **5 IPs per subnet**

So:

* `/24 = 256 → 251 usable`

---

## 🧩 IP Planning Example

```
VPC → 10.0.0.0/16

Public-A  → 10.0.1.0/24
Private-A → 10.0.11.0/24

Public-B  → 10.0.2.0/24
Private-B → 10.0.12.0/24
```

---

## 🚨 Important

👉 You **CANNOT change VPC CIDR later**

Plan carefully!

---

# 🚦 3. Subnets & Routing

Subnets divide your VPC into smaller networks.

---

## 📌 Types of Subnets

### 🌍 Public Subnet

* Has route to Internet Gateway
* Used for:

  * Load Balancer
  * Bastion Host
  * NAT Gateway

---

### 🔒 Private Subnet

* No direct internet access
* Used for:

  * App servers
  * Databases
  * Internal services

---

## 🧭 Route Table (Very Important)

👉 A **route table** decides where traffic goes.

---

## 🔁 Traffic Flow

```
Public Subnet:
0.0.0.0/0 → Internet Gateway

Private Subnet:
0.0.0.0/0 → NAT Gateway
```

---

## 🧠 Key Concept

👉 Route table = **Signboard for traffic**

---

# 🔐 4. Security: SG vs NACL

AWS provides **2 layers of security**

---

## 🛡️ Security Group (SG)

* Applied to: **EC2 / ENI**
* Type: **Stateful**
* Only supports: **Allow rules**

👉 If inbound is allowed → outbound automatically allowed

---

## 🚧 Network ACL (NACL)

* Applied to: **Subnet**
* Type: **Stateless**
* Supports: **Allow + Deny**
* Evaluated in order (rule numbers)

---

## ⚔️ SG vs NACL

| Feature    | Security Group | NACL         |
| ---------- | -------------- | ------------ |
| Scope      | Instance       | Subnet       |
| State      | Stateful       | Stateless    |
| Rules      | Allow only     | Allow + Deny |
| Evaluation | All rules      | Ordered      |

---

# 🌍 5. NAT, Peering & Transit Gateway

---

## 🔄 NAT Gateway

Used to give **internet access to private subnet**

---

### 🔁 Flow

```
Private EC2 → NAT Gateway → Internet Gateway → Internet
```

👉 Internet **cannot initiate connection back**

---

## 🔗 VPC Peering

Connects two VPCs privately.

```
VPC A ↔ VPC B
```

⚠️ **Non-transitive**

* A ↔ B and B ↔ C ❌ does NOT mean A ↔ C

---

## 🌐 Transit Gateway

* One central hub
* Connect multiple VPCs

```
        TGW
      /  |  \
    VPC VPC VPC
```

👉 Replaces complex peering mesh

---

# 🏗️ 6. Production VPC Architecture

A real-world **3-tier architecture**

---

## 📌 Design

### 🌍 Public Subnet

* ALB
* NAT Gateway

### ⚙️ App Tier (Private)

* EC2 / ECS
* Lambda

### 🗄️ Data Tier (Private)

* RDS (Primary + Replica)
* ElastiCache

---

## 📊 Architecture

```
Internet
   ↓
Internet Gateway
   ↓
Public Subnet (ALB, NAT)

   ↓
Private App Tier
   ↓
Private Data Tier
```

---

## 🧠 Best Practices

* Multi-AZ deployment
* Use **Security Groups**
* Enable **VPC Flow Logs**
* Use **Terraform (IaC)**
* Tag resources properly

---

# ⚠️ 7. Common VPC Mistakes

---

## ❌ 1. Overlapping CIDR

* Same CIDR in 2 VPCs → cannot peer

---

## ❌ 2. Database in Public Subnet

👉 NEVER do this

* Always private subnet
* Restrict access via SG

---

## ❌ 3. SSH Open to World

```
0.0.0.0/0 → Port 22 ❌
```

👉 Use:

* Your IP
* Or **SSM Session Manager**

---

## ❌ 4. No Flow Logs

👉 You cannot debug without logs

* Enable:

  * CloudWatch (real-time)
  * S3 (cheap storage)

---

# 🎯 What You Learned

* VPC fundamentals
* CIDR & IP planning
* Public vs Private subnet
* Routing & traffic flow
* Security layers (SG + NACL)
* NAT, Peering, Transit Gateway
* Production architecture
* Real-world mistakes

---





