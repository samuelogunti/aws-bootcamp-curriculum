---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 04: Compute with Amazon EC2'
---

# Module 04: Compute with Amazon EC2

**Phase 2: Core Services**
Estimated lecture time: 90 minutes

<!-- Speaker notes: Welcome to Module 04, the first module in Phase 2 (Core Services). This module covers EC2, the foundational compute service. Breakdown: 10 min EC2 overview, 10 min instance types, 10 min AMIs, 10 min connecting, 15 min EBS, 10 min user data, 15 min Auto Scaling, 10 min pricing. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Deploy EC2 instances into a VPC with appropriate subnet and security group configurations
- Demonstrate how to select an instance type using the naming convention
- Configure an AMI for launching instances
- Set up secure access using key pairs, Instance Connect, and Session Manager
- Implement EBS volumes with appropriate types, snapshots, and encryption
- Use user data scripts to bootstrap instances at launch
- Configure Auto Scaling groups with launch templates
- Demonstrate the differences between EC2 pricing models

---

## Prerequisites and agenda

**Prerequisites:** Module 02 (IAM), Module 03 (VPC, subnets, security groups), AWS account

**Agenda:**
1. EC2 overview
2. Instance types and sizing
3. Amazon Machine Images (AMIs)
4. Connecting to instances
5. Amazon EBS volumes
6. User data scripts
7. Auto Scaling groups and launch templates
8. EC2 pricing models

---

# EC2 overview

<!-- Speaker notes: This section takes approximately 10 minutes. Connect EC2 to the VPC concepts from Module 03. Draw a VPC with public and private subnets, then place EC2 instances in each. -->

---

## What is Amazon EC2?

- Resizable virtual servers (instances) in the AWS cloud
- IaaS: AWS manages hardware, you manage OS and applications
- Launch into VPC subnets with security groups (Module 03)
- Attach IAM roles via instance profiles (Module 02)

Common use cases:
- Web and application servers
- Development and test environments
- Batch processing
- High-performance computing (HPC)

> `t3.micro` instances are eligible for the AWS Free Tier (750 hours/month for 12 months).

---

# Instance types and sizing

<!-- Speaker notes: This section takes approximately 10 minutes. Write the naming convention on the board and decode examples together. -->

---

## Instance type naming convention

Every instance type follows a structured format:

```
[family][generation].[size]
```

Example: `t3.micro`
- **t** = instance family (burstable general purpose)
- **3** = generation (third generation)
- **micro** = size (CPU and memory allocation)

Additional letters indicate features (e.g., `m6g` = Graviton/ARM processors).

---

## Instance families

| Family | Category | Optimized For | Example Use Cases |
|--------|----------|---------------|-------------------|
| T | General purpose (burstable) | Variable workloads | Dev environments, small web servers |
| M | General purpose | Balanced compute/memory | App servers, mid-size databases |
| C | Compute optimized | CPU-intensive tasks | Batch processing, video encoding |
| R | Memory optimized | Large in-memory datasets | Caches, large databases |

> Start with T family for dev/test. Use Compute Optimizer after 14 days for right-sizing recommendations.

---

## Quick check: choosing an instance family

Match each workload to the best instance family:

1. Machine learning training job
2. Web application with variable traffic
3. In-memory caching layer

<!-- Speaker notes: Answers: 1) P or G family (GPU acceleration), 2) T family (burstable) or M family (consistent), 3) R family (memory optimized). Ask students to explain their reasoning. -->

---

# Amazon Machine Images (AMIs)

<!-- Speaker notes: This section takes approximately 10 minutes. Explain what AMIs contain and the three sources. -->

---

## What is an AMI?

- A template containing OS, applications, and configuration
- Every instance launch requires an AMI
- Includes root volume template, launch permissions, and block device mapping

| Source | Description | Examples |
|--------|-------------|---------|
| AWS-provided | Maintained by AWS with security patches | Amazon Linux 2023, Ubuntu |
| Marketplace | Third-party vendor images | WordPress, NGINX Plus |
| Custom | Created by you from a configured instance | Your app pre-installed |

> AMIs are Region-specific. Copy an AMI to another Region before launching instances there.

---

# Connecting to instances

<!-- Speaker notes: This section takes approximately 10 minutes. Cover the three connection methods and recommend Session Manager for production. -->

---

## Connection methods

| Method | Requires Public IP | Open SSH Port | Key Pair | Best For |
|--------|--------------------|---------------|----------|----------|
| SSH with key pair | Yes | Yes (port 22) | Yes | Direct access from known IP |
| EC2 Instance Connect | Yes | Yes (port 22) | No | Quick browser-based access |
| Session Manager | No | No | No | Secure access, audit logging |

> Prefer Session Manager for production. It eliminates open ports, provides IAM-based access control, and logs every session.

---

# Amazon EBS volumes

<!-- Speaker notes: This section takes approximately 15 minutes. Cover volume types, snapshots, and encryption. -->

---

## EBS volume types: SSD

| Volume Type | API Name | Max IOPS | Max Throughput | Use Cases |
|-------------|----------|----------|----------------|-----------|
| General Purpose SSD | gp3 | 16,000 | 1,000 MiB/s | Boot volumes, small databases |
| Provisioned IOPS SSD | io2 Block Express | 256,000 | 4,000 MiB/s | Large databases, latency-sensitive |

---

## EBS volume types: HDD

| Volume Type | API Name | Max IOPS | Max Throughput | Use Cases |
|-------------|----------|----------|----------------|-----------|
| Throughput Optimized | st1 | 500 | 500 MiB/s | Big data, log processing |
| Cold HDD | sc1 | 250 | 250 MiB/s | Infrequent access, lowest cost |

> For most workloads, gp3 is the best starting point. It provides 3,000 IOPS baseline at no extra cost.

---

## EBS snapshots and encryption

- **Snapshots:** point-in-time backups stored in S3 (incremental)
- Use for backup, AMI creation, and cross-Region replication
- **Encryption:** uses AWS KMS, transparent to the OS
- Enable encryption by default for your account
- EBS volumes exist in a single AZ

> You cannot encrypt an existing unencrypted volume directly. Create a snapshot, copy with encryption, then create a new volume.

---

## Discussion: choosing storage for a database

Your team needs persistent storage for a PostgreSQL database that requires 10,000 IOPS consistently.

**Which EBS volume type would you choose, and why?**

<!-- Speaker notes: Expected answer: gp3 with IOPS scaled to 10,000 (gp3 supports up to 16,000 IOPS). If the requirement exceeds 16,000 IOPS, use io2 Block Express. gp3 is more cost-effective because you can independently provision IOPS without paying for a larger volume. -->

---

# User data scripts

<!-- Speaker notes: This section takes approximately 10 minutes. Show a simple web server bootstrap example. -->

---

## Bootstrapping instances at launch

User data scripts run automatically on first boot as root:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from EC2</h1>" > /var/www/html/index.html
```

- Runs only on first boot by default
- Limited to 16 KB (download larger scripts from S3)
- Combine with custom AMIs for faster deployments

---

# Auto Scaling groups

<!-- Speaker notes: This section takes approximately 15 minutes. Cover launch templates, capacity settings, scaling policies, and health checks. -->

---

## Launch templates and capacity settings

- **Launch template:** specifies AMI, instance type, key pair, security groups, user data, and IAM profile
- Supports versioning for rollback

| Setting | Description |
|---------|-------------|
| Minimum capacity | Lowest number of instances maintained |
| Desired capacity | Target number of instances at any time |
| Maximum capacity | Highest number the group can scale to |

---

## Scaling policies

- **Target tracking:** maintain a metric target (e.g., CPU at 50%)
- **Step scaling:** add instances based on CloudWatch alarm thresholds
- **Scheduled scaling:** scale on a time-based schedule

Health checks detect unhealthy instances and replace them automatically. Always span at least two AZs for high availability.

> Target tracking is the simplest and most commonly used policy type.

---

## Think about it: Auto Scaling scenario

Your web application runs on an Auto Scaling group with min=2, desired=2, max=6. During a flash sale, CPU utilization spikes to 90%.

**What happens if you have a target tracking policy set to 50% CPU?**

<!-- Speaker notes: Auto Scaling adds instances to bring average CPU back to 50%. It might scale to 4-6 instances depending on the load. When the sale ends and CPU drops, Auto Scaling removes instances back to the desired count of 2. The key insight is that Auto Scaling reacts to the metric, not to the event. -->

---

# EC2 pricing models

<!-- Speaker notes: This section takes approximately 10 minutes. Use the hotel analogy: On-Demand is a hotel room, Reserved is a lease, Spot is a last-minute deal. -->

---

## Pricing model comparison

| Model | Commitment | Discount | Best For |
|-------|------------|----------|----------|
| On-Demand | None | 0% (baseline) | Short-term, unpredictable workloads |
| Reserved Instances | 1 or 3 years | Up to 72% | Steady-state, predictable usage |
| Savings Plans | 1 or 3 years | Up to 72% | Flexible commitment across services |
| Spot Instances | None | Up to 90% | Fault-tolerant batch processing |

---

## Spot Instances: the trade-off

- Up to 90% discount on spare EC2 capacity
- AWS can reclaim with a two-minute warning
- Ideal for fault-tolerant, flexible, stateless workloads
- Common uses: batch processing, CI/CD builds, containerized services

> Do not use Spot for databases or single-instance apps without failover.

---

## Key takeaways

- EC2 provides virtual servers launched into VPC subnets with security groups and IAM roles (via instance profiles) granting permissions to other AWS services.
- Instance types follow a naming convention (family, generation, size) mapping to workload categories: general purpose (T, M), compute (C), memory (R), storage (I), accelerated (G, P).
- Amazon EBS provides persistent block storage with four volume types (gp3, io2, st1, sc1). Use snapshots for backups and encryption for data protection.
- Auto Scaling groups with launch templates maintain desired instance count, replace unhealthy instances, and scale based on demand across multiple AZs.
- EC2 pricing models (On-Demand, Reserved, Savings Plans, Spot) offer trade-offs between flexibility and cost savings.

---

## Lab preview: launching and managing EC2 instances

**Objective:** Launch EC2 instances with user data, attach EBS volumes, create snapshots, and configure an Auto Scaling group

**Key services:** Amazon EC2, EBS, Auto Scaling, VPC, IAM

**Duration:** 60 minutes

<!-- Speaker notes: Students will use the default VPC for this lab. They will launch an instance with a web server user data script, attach an additional EBS volume, create a snapshot, build a launch template, and set up an Auto Scaling group with a target tracking policy. Remind students to use t3.micro for Free Tier eligibility. -->

---

# Questions?

Review `modules/04-compute-ec2/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions involve instance type selection and the difference between stopping and terminating instances. Transition to the lab when ready. -->
