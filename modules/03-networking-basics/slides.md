---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 03: Networking Basics'
---

# Module 03: Networking Basics (VPC)

**Phase 1: Cloud Foundations**
Estimated lecture time: 90 minutes

<!-- Speaker notes: Welcome students to Module 03. This module covers VPC networking, the foundation for every AWS resource that needs network connectivity. Total time: ~90 minutes. Breakdown: 15 min VPC fundamentals and CIDR, 15 min subnets, 15 min gateways, 15 min route tables, 15 min security groups and NACLs, 15 min design patterns and Q&A. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Define a Virtual Private Cloud (VPC) and explain its role as the networking foundation
- Describe the difference between the default VPC and a custom VPC
- Explain CIDR notation and how it determines VPC and subnet size
- Distinguish between public subnets and private subnets
- Describe how internet gateways and NAT gateways provide internet access
- Explain how route tables control network traffic flow
- Distinguish between security groups (stateful) and NACLs (stateless)
- Identify common VPC design patterns (two-tier and three-tier)

---

## Prerequisites and agenda

**Prerequisites:** Module 01 (Cloud Fundamentals), Module 02 (IAM and Security), AWS account with console access

**Agenda:**
1. VPC fundamentals
2. Subnets: public vs. private
3. Internet gateway and NAT gateway
4. Route tables
5. Security groups and network ACLs
6. VPC design patterns

---

# VPC fundamentals

<!-- Speaker notes: This section takes approximately 15 minutes. Start by connecting to Module 01's Regions and AZs. Draw a Region with two AZs, then draw a VPC spanning both. -->

---

## What is a VPC?

- A logically isolated virtual network within your AWS account
- Every networked AWS resource runs inside a VPC
- You control IP address range, subnets, route tables, and gateways
- A VPC exists in a single Region but spans multiple AZs
- Think of it as your own private data center in the cloud

---

## Default VPC vs. custom VPC

| Feature | Default VPC | Custom VPC |
|---------|-------------|------------|
| Created automatically | Yes (one per Region) | No (you create it) |
| CIDR block | `172.31.0.0/16` (fixed) | You choose |
| Internet gateway | Attached by default | You attach if needed |
| Subnets | Public by default | You configure |
| Use case | Quick testing, learning | Production workloads |

> Use the default VPC for labs. Create a custom VPC for real workloads.

---

## CIDR blocks and IP addressing

- CIDR notation combines an IP address with a prefix length
- The prefix determines how many bits are fixed
- Remaining bits are available for host addresses
- Use private IP ranges defined by RFC 1918

| CIDR Block | Prefix | Available IPs | Typical Use |
|------------|--------|---------------|-------------|
| `10.0.0.0/16` | /16 | 65,536 | Large VPC |
| `10.0.0.0/24` | /24 | 256 | Small subnet |
| `10.0.0.0/28` | /28 | 16 | Minimum subnet |

---

## Discussion: planning your CIDR block

You are designing a VPC that will eventually connect to your company's on-premises network (`10.1.0.0/16`) via VPN.

**What CIDR block would you choose for your VPC, and why does it matter?**

<!-- Speaker notes: Expected answer: Choose a non-overlapping range like 10.0.0.0/16 or 10.2.0.0/16. Overlapping CIDR blocks cause routing conflicts when connecting networks. This is why planning IP address space before creating the VPC is critical. You cannot change the primary CIDR block after creation. -->

---

# Subnets: public vs. private

<!-- Speaker notes: This section takes approximately 15 minutes. Explain that subnets divide the VPC's address space and each subnet lives in a single AZ. -->

---

## Public vs. private subnets

| Characteristic | Public Subnet | Private Subnet |
|----------------|---------------|----------------|
| Route to internet gateway | Yes | No |
| Public IP addresses | Auto-assigned or Elastic IP | Not needed |
| Inbound internet traffic | Allowed (filtered by SGs) | Blocked |
| Outbound internet access | Direct via internet gateway | Through NAT gateway only |
| Typical workloads | Web servers, load balancers | Databases, app servers |

---

## Availability Zone placement

- Create subnets in at least two AZs for high availability
- One public and one private subnet per AZ is a common pattern
- If one AZ has an outage, resources in the other AZ continue

```
VPC: 10.0.0.0/16
├── AZ-a
│   ├── Public Subnet:  10.0.1.0/24
│   └── Private Subnet: 10.0.2.0/24
└── AZ-b
    ├── Public Subnet:  10.0.3.0/24
    └── Private Subnet: 10.0.4.0/24
```

---

## Subnet sizing

- Each subnet CIDR must be a subset of the VPC CIDR
- AWS reserves five IP addresses in every subnet
- In a /24 subnet (256 addresses), only 251 are usable
- In a /28 subnet (16 addresses), only 11 are usable

> Account for the five reserved addresses when planning subnet sizes. For small subnets, this overhead is significant.

---

## Quick check: subnet placement

Your application has a web server and a PostgreSQL database.

**Which subnet type would you place the database in, and why?**

A) Public subnet
B) Private subnet

<!-- Speaker notes: Answer: B) Private subnet. Placing the database in a private subnet means it has no direct internet access, reducing the attack surface. The web server in the public subnet connects to the database through security group rules. This follows the principle of least privilege applied to network access. -->

---

# Internet gateway and NAT gateway

<!-- Speaker notes: This section takes approximately 15 minutes. Explain the two gateway types and when each is needed. -->

---

## Internet gateway

- Allows communication between your VPC and the internet
- Horizontally scaled, redundant, and highly available
- Provides a target in your route table for internet traffic
- Performs NAT for instances with a public IPv4 address

To enable internet access for a subnet:
1. Attach an internet gateway to your VPC
2. Add a route for `0.0.0.0/0` to the internet gateway
3. Ensure instances have a public IP or Elastic IP
4. Configure security groups to allow desired traffic

---

## NAT gateway

- Allows private subnet instances to reach the internet (outbound only)
- Prevents the internet from initiating inbound connections
- Sits in a public subnet with an Elastic IP address
- Managed by AWS (no patching or maintenance required)

```
Private Instance --> NAT Gateway (public subnet) --> Internet Gateway --> Internet
```

> NAT gateways incur hourly and data processing charges. Only create one if private instances need internet access.

---

## Internet gateway vs. NAT gateway

| Gateway Type | Direction | Location | Use Case |
|-------------|-----------|----------|----------|
| Internet gateway | Inbound and outbound | Attached to VPC | Public subnet internet access |
| NAT gateway | Outbound only | In a public subnet | Private subnet outbound access |

---

# Route tables

<!-- Speaker notes: This section takes approximately 15 minutes. Explain how route tables control traffic flow and the difference between main and custom route tables. -->

---

## How route tables work

- A route table contains rules that determine where traffic is directed
- Every subnet must be associated with a route table
- The most specific route (longest prefix match) wins
- AWS creates a main route table with a local route automatically

---

## Public vs. private route tables

**Public subnet route table:**

| Destination | Target | Purpose |
|-------------|--------|---------|
| `10.0.0.0/16` | local | VPC internal traffic |
| `0.0.0.0/0` | igw-xxxxxxxx | Internet-bound traffic |

**Private subnet route table:**

| Destination | Target | Purpose |
|-------------|--------|---------|
| `10.0.0.0/16` | local | VPC internal traffic |
| `0.0.0.0/0` | nat-xxxxxxxx | Outbound via NAT gateway |

---

## Viewing route tables with the CLI

```bash
aws ec2 describe-route-tables \
    --filters "Name=vpc-id,Values=vpc-xxxxxxxx"
```

> Keep route tables simple and well-documented. Each subnet should have a clear purpose (public or private), and its route table should reflect that purpose.

---

# Security groups and network ACLs

<!-- Speaker notes: This section takes approximately 15 minutes. This is where students often get confused. Emphasize the stateful vs. stateless distinction and use the comparison table. -->

---

## Security groups (stateful)

- Virtual firewall at the resource level (EC2, RDS, Lambda in VPC)
- **Stateful:** return traffic is automatically allowed
- **Allow rules only:** traffic not matching a rule is denied
- All rules evaluated together (no ordering)
- Default: no inbound allowed, all outbound allowed

---

## Security group example: web server

| Direction | Protocol | Port | Source | Purpose |
|-----------|----------|------|--------|---------|
| Inbound | TCP | 80 | 0.0.0.0/0 | Allow HTTP |
| Inbound | TCP | 443 | 0.0.0.0/0 | Allow HTTPS |
| Inbound | TCP | 22 | 203.0.113.0/24 | Allow SSH |
| Outbound | All | All | 0.0.0.0/0 | Allow all outbound |

---

## Network ACLs (stateless)

- Optional firewall at the subnet level
- **Stateless:** must explicitly allow return traffic
- **Allow and deny rules:** useful for blocking specific IPs
- Rules evaluated in number order; first match wins
- Default NACL allows all traffic; custom NACL denies all

---

## Security groups vs. NACLs

| Feature | Security Group | Network ACL |
|---------|---------------|-------------|
| Operates at | Resource level | Subnet level |
| Statefulness | Stateful | Stateless |
| Rule types | Allow only | Allow and deny |
| Rule evaluation | All rules together | Number order, first match |
| Default behavior | Deny inbound, allow outbound | Default allows all |

> Use security groups as your primary firewall. Use NACLs as a secondary defense for blocking specific IPs.

---

## Think about it: troubleshooting network access

An EC2 instance in a public subnet can receive HTTP requests on port 80, but clients never get a response.

The security group allows inbound TCP port 80 from `0.0.0.0/0`. The NACL allows inbound TCP port 80.

**What is the most likely cause?**

<!-- Speaker notes: Answer: The NACL is stateless, so the outbound rule for ephemeral ports (1024-65535) is missing. The security group is stateful and would allow the response automatically, but the NACL requires an explicit outbound rule for the return traffic. This is the most common NACL troubleshooting scenario. -->

---

# VPC design patterns

<!-- Speaker notes: This section takes approximately 15 minutes. Cover two-tier and three-tier architectures. Draw the three-tier architecture on the whiteboard and ask students to define security group rules for each tier. -->

---

## Two-tier architecture

- **Public tier:** web servers or load balancers with public IPs
- **Data tier:** databases in private subnets, no public access

```
Internet
   |
Internet Gateway
   |
Public Subnets (AZ-a, AZ-b)
   ├── Web Server (AZ-a)
   └── Web Server (AZ-b)
   |
Private Subnets (AZ-a, AZ-b)
   ├── Database Primary (AZ-a)
   └── Database Standby (AZ-b)
```

---

## Three-tier architecture

- **Presentation tier (public):** load balancers
- **Application tier (private):** app servers
- **Data tier (private):** databases and caches

| Tier | Subnet Type | SG Allows Inbound From |
|------|-------------|------------------------|
| Presentation | Public | Internet (HTTP/HTTPS) |
| Application | Private | Presentation tier SG |
| Data | Private | Application tier SG |

> The three-tier architecture is the most common VPC design pattern for web applications on AWS.

---

## Discussion: designing security group rules

You are building a three-tier web application. The ALB is in public subnets, app servers are in private subnets, and an RDS database is in private subnets.

**Define the inbound security group rules for each tier.**

<!-- Speaker notes: Expected answers: ALB SG allows inbound HTTP/HTTPS from 0.0.0.0/0. App server SG allows inbound on the app port from the ALB security group only. RDS SG allows inbound on port 5432 (PostgreSQL) from the app server security group only. Each tier only accepts traffic from the tier above it. This is layered security following the principle of least privilege. -->

---

## Key takeaways

- A VPC is your isolated virtual network in AWS, spanning a single Region across multiple AZs. You control the CIDR block, subnets, routing, and security.
- Public subnets route traffic through an internet gateway; private subnets use a NAT gateway for outbound-only access. Place databases in private subnets.
- Route tables determine where traffic flows. Each subnet is associated with a route table, and the most specific route wins.
- Security groups (stateful, resource-level, allow-only) are your primary firewall. NACLs (stateless, subnet-level, allow and deny) provide an additional defense layer.
- The three-tier architecture (presentation, application, data) uses layered security groups to restrict traffic between tiers following least privilege.

---

## Lab preview: VPC setup

**Objective:** Build a custom VPC with public and private subnets, an internet gateway, a NAT gateway, route tables, and security groups

**Key services:** Amazon VPC, subnets, internet gateway, NAT gateway, security groups, EC2

**Duration:** 60 minutes

<!-- Speaker notes: Remind students they will create a VPC from scratch in us-east-1. They will launch an EC2 instance in the public subnet and verify internet connectivity, then launch one in the private subnet and verify it can reach the internet only through the NAT gateway. Have students review Module 03 prerequisites before starting. -->

---

# Questions?

Review `modules/03-networking-basics/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions at this point involve CIDR math and the stateful vs. stateless distinction. If time permits, do a quick whiteboard exercise calculating usable IPs in a /24 and /28 subnet. -->
