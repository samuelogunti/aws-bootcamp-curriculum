---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 06: Databases (RDS and DynamoDB)'
---

# Module 06: Databases with Amazon RDS and DynamoDB

**Phase 2: Core Services**
Estimated lecture time: 90 minutes

<!-- Speaker notes: Welcome to Module 06. This module covers both relational (RDS) and NoSQL (DynamoDB) databases. Breakdown: 10 min database landscape, 15 min RDS fundamentals, 10 min Multi-AZ and read replicas, 10 min backups, 15 min DynamoDB fundamentals, 10 min GSIs and capacity, 10 min SQL vs NoSQL decision, 10 min security and Q&A. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Demonstrate the differences between managed and self-managed databases
- Configure an RDS database instance with appropriate settings
- Set up an RDS Multi-AZ deployment for high availability
- Implement RDS read replicas to scale read-heavy workloads
- Configure RDS automated backups and point-in-time recovery
- Deploy a DynamoDB table with primary key schema and GSIs
- Use DynamoDB on-demand and provisioned capacity modes
- Implement database security controls including encryption and VPC placement

---

## Prerequisites and agenda

**Prerequisites:** Module 02 (IAM), Module 03 (VPC, private subnets, security groups), Module 04 (EC2 for connectivity testing)

**Agenda:**
1. Database landscape on AWS
2. Amazon RDS fundamentals
3. Multi-AZ deployments and read replicas
4. RDS automated backups
5. Amazon DynamoDB fundamentals
6. DynamoDB keys, GSIs, and capacity modes
7. SQL vs. NoSQL decision framework
8. Database security

---

# Database landscape on AWS

<!-- Speaker notes: This section takes approximately 10 minutes. Start by drawing a VPC with an RDS instance in a private subnet and an EC2 instance connecting to it. -->

---

## Managed vs. self-managed databases

| Responsibility | Self-Managed (EC2) | Managed (RDS) | Fully Managed (DynamoDB) |
|----------------|-------------------|---------------|--------------------------|
| Hardware and OS | You manage | AWS manages | AWS manages |
| Patching | You patch | AWS patches | AWS patches transparently |
| Backups | You configure | AWS automates | AWS automates |
| High availability | You build | AWS provides | Built in |
| Scaling | You resize | You resize | Automatic (on-demand) |

---

## Relational vs. NoSQL

| Characteristic | Relational (SQL) | NoSQL (DynamoDB) |
|----------------|-----------------|------------------|
| Data model | Tables, rows, columns | Key-value, documents |
| Schema | Fixed, defined upfront | Flexible, per-item |
| Query language | SQL (joins, aggregations) | API-based (GetItem, Query) |
| Scaling | Vertical (larger instance) | Horizontal (partitioning) |
| Best for | Complex queries, transactions | High-throughput, low-latency |

> Many architectures use both. Each handles the workload it is best suited for.

---

# Amazon RDS fundamentals

<!-- Speaker notes: This section takes approximately 15 minutes. Cover supported engines, instance classes, and the free tier. -->

---

## Supported database engines

- Amazon Aurora (MySQL and PostgreSQL compatible)
- MySQL
- PostgreSQL
- MariaDB
- Oracle Database
- Microsoft SQL Server

Aurora provides up to 5x MySQL throughput and 3x PostgreSQL throughput with fault-tolerant storage across three AZs.

---

## DB instance classes

| Instance Class Type | Prefix | Optimized For |
|---------------------|--------|---------------|
| General purpose | db.m6g, db.m7g | Balanced compute and memory |
| Memory optimized | db.r6g, db.r7g | Large databases, analytics |
| Burstable | db.t3, db.t4g | Dev, testing, small production |

> `db.t3.micro` is eligible for the AWS Free Tier (750 hours/month for 12 months).

---

## Discussion: managed vs. self-managed

Your team has two database administrators who currently manage PostgreSQL on EC2 instances. They spend 40% of their time on patching, backups, and failover configuration.

**How would migrating to RDS change their workload?**

<!-- Speaker notes: Expected answer: RDS handles patching, automated backups, and Multi-AZ failover automatically. The DBAs could redirect that 40% of time toward query optimization, schema design, and application performance. The trade-off is less control over the OS and database engine configuration. This is the core value proposition of managed services. -->

---

# Multi-AZ and read replicas

<!-- Speaker notes: This section takes approximately 10 minutes. Use the analogy: Multi-AZ is a backup generator, read replicas are additional staff for peak hours. -->

---

## Multi-AZ deployments

- Synchronous standby replica in a different AZ
- Automatic failover in 1-2 minutes if primary fails
- Standby is not readable (failover target only)
- DNS endpoint automatically updates on failover

---

## Read replicas

- Asynchronous read-only copies for scaling read traffic
- Small replica lag (typically seconds)
- Can be created in another Region (cross-Region)
- Manual promotion required (no automatic failover)

---

## Multi-AZ vs. read replicas

| Feature | Multi-AZ | Read Replica |
|---------|----------|--------------|
| Purpose | High availability | Read scaling |
| Replication | Synchronous | Asynchronous |
| Readable | No | Yes |
| Cross-Region | No | Yes |
| Auto failover | Yes | No |

> You can combine both: Multi-AZ on the primary for HA, plus read replicas for scaling.

---

# RDS automated backups

<!-- Speaker notes: This section takes approximately 10 minutes. Cover retention, point-in-time recovery, and manual snapshots. -->

---

## Backups and recovery

- **Automated backups:** daily snapshots with transaction logs
- Retention period: 1 to 35 days (default 7 days)
- **Point-in-time recovery:** restore to any second within retention
- Creates a new DB instance (does not overwrite existing)
- **Manual snapshots:** persist until you delete them

```bash
aws rds create-db-snapshot \
    --db-instance-identifier my-database \
    --db-snapshot-identifier my-snapshot-2024
```

---

# Amazon DynamoDB fundamentals

<!-- Speaker notes: This section takes approximately 15 minutes. Emphasize the "access patterns first" design approach. -->

---

## What is DynamoDB?

- Fully managed, serverless NoSQL database
- Single-digit millisecond performance at any scale
- No servers to provision, patch, or manage
- Supports key-value and document data models
- Encryption at rest enabled by default

---

## Tables, items, and attributes

- **Table:** collection of items (no fixed schema beyond primary key)
- **Item:** single data record, uniquely identified by primary key
- **Attribute:** data element within an item (string, number, list, map)

```bash
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST
```

---

## Primary key types

| Type | Attributes | Query Capability |
|------|-----------|------------------|
| Partition key only | 1 attribute | Retrieve single item by exact key |
| Composite key | Partition + sort key | Retrieve all items with a partition key; filter by sort key |

> Choose a partition key with high cardinality to distribute data evenly. Poor choices create "hot partitions."

---

## Quick check: designing a DynamoDB key

You are building a chat application. You need to retrieve all messages in a conversation, sorted by timestamp.

**What would you choose as the partition key and sort key?**

<!-- Speaker notes: Answer: ConversationId as the partition key and Timestamp as the sort key. This allows you to Query all messages for a conversation (same partition key) sorted by time (sort key). This is a classic composite key pattern. A common wrong answer is using MessageId as the partition key, which would require a Scan to find all messages in a conversation. -->

---

# GSIs and capacity modes

<!-- Speaker notes: This section takes approximately 10 minutes. Cover GSIs briefly and compare on-demand vs. provisioned. -->

---

## Global Secondary Indexes (GSIs)

- Query a table using an alternate key (different from primary key)
- DynamoDB maintains a separate copy organized by the GSI key
- Reads from GSIs are eventually consistent
- Each GSI adds storage and write cost

> Design GSIs around your application's actual query patterns. Avoid creating indexes you do not need.

---

## Capacity modes

| Feature | On-Demand | Provisioned |
|---------|-----------|-------------|
| Pricing | Pay per request | Pay per RCU/WCU per hour |
| Capacity planning | None required | You specify RCUs and WCUs |
| Scaling | Automatic, instant | Auto scaling with slight delay |
| Best for | Unpredictable traffic | Predictable, steady-state traffic |

> Start with on-demand for new tables. Switch to provisioned after traffic patterns stabilize.

---

# SQL vs. NoSQL decision framework

<!-- Speaker notes: This section takes approximately 10 minutes. Present the comparison table and walk through scenarios. -->

---

## When to use RDS vs. DynamoDB

| Criterion | Amazon RDS | Amazon DynamoDB |
|-----------|-----------|-----------------|
| Query flexibility | High (SQL, joins) | Limited (key-based, GSIs) |
| Schema | Fixed, enforced | Flexible, per-item |
| Transactions | Multi-table ACID | Single-table ACID |
| Scaling | Vertical | Horizontal |
| Latency | Varies with query | Single-digit ms |
| Best for | Complex queries, reporting | High-scale, key-based access |

---

## Think about it: database selection

Scenario A: A financial reporting system that joins data across 10 tables and generates monthly aggregate reports.

Scenario B: A real-time gaming leaderboard that handles 50,000 reads per second with simple key lookups.

**Which database service would you choose for each?**

<!-- Speaker notes: Answer: Scenario A is RDS (complex joins, aggregations, reporting across multiple tables). Scenario B is DynamoDB (high throughput, low latency, simple key-based access pattern). This reinforces that the choice depends on access patterns, not on which service is "better." -->

---

# Database security

<!-- Speaker notes: This section takes approximately 10 minutes. Connect to Module 02 (IAM) and Module 03 (VPC, private subnets). -->

---

## Security layers

- **VPC placement:** always place RDS in private subnets
- **Security groups:** allow connections only from app server SGs
- **Encryption at rest:** RDS uses KMS; DynamoDB encrypts by default
- **Encryption in transit:** RDS supports SSL/TLS; DynamoDB uses HTTPS
- **IAM authentication:** RDS supports IAM DB auth; DynamoDB uses IAM policies
- **VPC endpoints:** keep DynamoDB traffic off the public internet

---

## Key takeaways

- Amazon RDS is a managed relational database handling provisioning, patching, backups, and failover. Use it for complex SQL queries, transactions, and reporting.
- RDS Multi-AZ provides high availability with automatic failover. Read replicas provide read scaling and can span Regions for disaster recovery.
- Amazon DynamoDB is a fully managed, serverless NoSQL database with single-digit millisecond performance. Design your table schema around access patterns.
- Always place RDS in private subnets, enable encryption, and use IAM authentication. For DynamoDB, use VPC endpoints to keep traffic off the public internet.
- Use the SQL vs. NoSQL decision framework to choose the right database. Many architectures use both RDS and DynamoDB together.

---

## Lab preview: working with RDS and DynamoDB

**Objective:** Create an RDS PostgreSQL instance, connect from EC2, create a DynamoDB table with a composite key, and compare Query vs. Scan

**Key services:** Amazon RDS, Amazon DynamoDB, VPC, EC2, IAM

**Duration:** 60 minutes

<!-- Speaker notes: Students will create a DB subnet group, security group, and RDS instance in a private subnet. They will connect using psql from an EC2 instance. Then they will create a DynamoDB table with a composite key and run Query and Scan operations to see the performance difference. Remind students to delete the RDS instance after the lab to avoid charges. -->

---

# Questions?

Review `modules/06-databases-rds-dynamodb/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions involve choosing between Aurora and standard RDS, and designing DynamoDB partition keys. Transition to the lab when ready. -->
