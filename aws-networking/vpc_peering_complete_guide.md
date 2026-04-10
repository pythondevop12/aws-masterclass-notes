# AWS VPC Peering — Complete Study Notes
> **PythonDevOps** | AWS Networking Series

---

## 1. What is VPC Peering?

A **VPC Peering Connection** is a networking connection between two VPCs that enables private routing between them using the **AWS global backbone** — no internet gateway, no VPN, no NAT device required.

- Traffic **never leaves the AWS network**
- Works across accounts and regions
- Uses IPv4 or IPv6 addressing
- Connection is identified by a **Peering Connection ID**: `pcx-xxxxxxxxxxxxxxxxx`

---

## 2. How It Works

```
VPC A (10.0.0.0/16)  ←——— pcx-xxxxx ———→  VPC B (172.16.0.0/16)
  └─ Private Subnet                            └─ Private Subnet
       EC2 Instance                                  RDS / EC2
```

**Traffic flow:**
1. EC2 in VPC A sends packet to `172.16.x.x`
2. Route table in VPC A has route: `172.16.0.0/16 → pcx-xxxxx`
3. Packet travels AWS backbone to VPC B
4. Route table in VPC B has route: `10.0.0.0/16 → pcx-xxxxx`
5. Packet arrives at destination EC2/RDS

> ⚠️ **Both sides must have routes configured** — it's not automatic.

---

## 3. Step-by-Step Setup

### Step 1 — Create Peering Request (Requester VPC)

**Console:**
```
VPC Dashboard → Peering Connections → Create Peering Connection
  ├── Name: my-peer-connection
  ├── VPC ID (Requester): vpc-aaaaaaaaa
  ├── Account: My account / Another account
  └── VPC ID (Accepter): vpc-bbbbbbbbb
```

**AWS CLI:**
```bash
aws ec2 create-vpc-peering-connection \
  --vpc-id vpc-aaaaaaaaa \
  --peer-vpc-id vpc-bbbbbbbbb \
  --peer-region us-west-2        # omit if same region
  --peer-owner-id 123456789012   # omit if same account
```

### Step 2 — Accept the Peering Connection (Accepter VPC)

**Console:**
```
VPC Dashboard → Peering Connections → Select pcx-xxxxx → Actions → Accept Request
```

**AWS CLI:**
```bash
aws ec2 accept-vpc-peering-connection \
  --vpc-peering-connection-id pcx-xxxxxxxxxxxxxxxxx
```

### Step 3 — Update Route Tables (BOTH VPCs)

**VPC A route table — add:**
| Destination     | Target        |
|-----------------|---------------|
| 172.16.0.0/16   | pcx-xxxxxxxxx |

**VPC B route table — add:**
| Destination   | Target        |
|---------------|---------------|
| 10.0.0.0/16   | pcx-xxxxxxxxx |

**AWS CLI (repeat for each route table):**
```bash
# VPC A side
aws ec2 create-route \
  --route-table-id rtb-aaaaaaaa \
  --destination-cidr-block 172.16.0.0/16 \
  --vpc-peering-connection-id pcx-xxxxxxxxxxxxxxxxx

# VPC B side
aws ec2 create-route \
  --route-table-id rtb-bbbbbbbb \
  --destination-cidr-block 10.0.0.0/16 \
  --vpc-peering-connection-id pcx-xxxxxxxxxxxxxxxxx
```

### Step 4 — Update Security Groups

On EC2/RDS in VPC B — allow inbound from VPC A's CIDR:
```
Inbound Rule:
  Type: Custom TCP / MySQL / HTTPS
  Port: 3306 / 443
  Source: 10.0.0.0/16   ← VPC A's CIDR
```

> 💡 Within the same region, you can reference security group IDs instead of CIDRs: `sg-xxxxxxxxxxxxxxxxx`

### Step 5 — Verify Connectivity

```bash
# From EC2 in VPC A, ping EC2 private IP in VPC B
ping 172.16.2.10

# Test port connectivity
nc -zv 172.16.2.10 3306

# Check peering connection status
aws ec2 describe-vpc-peering-connections \
  --filters "Name=status-code,Values=active"
```

---

## 4. Terraform Example

```hcl
resource "aws_vpc_peering_connection" "peer" {
  vpc_id        = aws_vpc.vpc_a.id
  peer_vpc_id   = aws_vpc.vpc_b.id
  peer_region   = "us-west-2"   # remove for same-region
  peer_owner_id = var.peer_account_id
  auto_accept   = false         # must be false for cross-account

  tags = {
    Name = "vpc-a-to-vpc-b-peering"
  }
}

resource "aws_vpc_peering_connection_accepter" "peer" {
  provider                  = aws.peer_account
  vpc_peering_connection_id = aws_vpc_peering_connection.peer.id
  auto_accept               = true
}

# Route in VPC A
resource "aws_route" "vpc_a_to_b" {
  route_table_id            = aws_route_table.vpc_a_private.id
  destination_cidr_block    = "172.16.0.0/16"
  vpc_peering_connection_id = aws_vpc_peering_connection.peer.id
}

# Route in VPC B
resource "aws_route" "vpc_b_to_a" {
  provider                  = aws.peer_account
  route_table_id            = aws_route_table.vpc_b_private.id
  destination_cidr_block    = "10.0.0.0/16"
  vpc_peering_connection_id = aws_vpc_peering_connection.peer.id
}
```

---

## 5. Real-World Use Cases

| Use Case | Description |
|----------|-------------|
| **Multi-Account** | Dev / Staging / Prod in separate accounts — peer for shared tooling |
| **Shared Services VPC** | Centralize logging (ELK), monitoring (Prometheus), secrets (Vault) |
| **Database Isolation** | RDS in its own VPC, app VPCs peer to it — no public DB exposure |
| **Microservices** | Services in separate VPCs communicate privately |
| **Cross-Region** | `us-east-1` ↔ `eu-west-1` over AWS backbone |
| **EKS Connectivity** | Worker node VPC peers with tooling VPC (ArgoCD, Prometheus) |

---

---

## 6. Quick Reference Cheatsheet

```bash
# List all peering connections
aws ec2 describe-vpc-peering-connections

# Filter by status
aws ec2 describe-vpc-peering-connections \
  --filters "Name=status-code,Values=active,pending-acceptance"

# Delete peering connection
aws ec2 delete-vpc-peering-connection \
  --vpc-peering-connection-id pcx-xxxxxxxxxxxxxxxxx

# Check routes referencing a peering connection
aws ec2 describe-route-tables \
  --filters "Name=route.vpc-peering-connection-id,Values=pcx-xxxxxxxxxxxxxxxxx"
```

---

## 7. Summary

```
VPC Peering = Private, low-latency, no-internet bridge between VPCs

✅ Plan unique CIDRs BEFORE creating VPCs
✅ Update route tables on BOTH sides
✅ Update security groups at the destination

```

---

*PythonDevOps | AWS Networking Series | VPC Peering*
