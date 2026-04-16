# Module 06: Quiz

1. Which of the following best describes the difference between Amazon RDS Multi-AZ deployments and read replicas?

   A) Multi-AZ uses asynchronous replication for high availability; read replicas use synchronous replication for read scaling
   B) Multi-AZ provides a synchronous standby for automatic failover; read replicas provide asynchronous copies that serve read traffic
   C) Multi-AZ and read replicas both serve read traffic, but Multi-AZ is limited to the same Region
   D) Read replicas provide automatic failover, while Multi-AZ requires manual promotion

2. True or False: Amazon Aurora is a standalone database engine that is not compatible with MySQL or PostgreSQL.

3. A DynamoDB table uses `CustomerId` as the partition key and `OrderDate` as the sort key. Which of the following statements about this table's primary key are correct? (Select TWO.)

   A) Multiple items can share the same `CustomerId` value as long as their `OrderDate` values differ
   B) The `CustomerId` value alone must be unique across all items in the table
   C) You can use the Query operation to retrieve all orders for a specific customer, sorted by date
   D) The sort key is optional and can be omitted when inserting items
   E) Items with the same `CustomerId` are stored in different partitions for performance

4. In your own words, explain the difference between a DynamoDB Query operation and a Scan operation in terms of how each accesses data and their relative efficiency.

5. Which DynamoDB capacity mode should you choose for a new application with unpredictable traffic patterns?

   A) Provisioned mode with a fixed number of read and write capacity units
   B) Provisioned mode with auto scaling enabled
   C) On-demand mode
   D) Reserved capacity mode

6. True or False: When you create a Global Secondary Index (GSI) on a DynamoDB table, reads from the GSI are strongly consistent by default.

7. An RDS PostgreSQL instance is deployed in a private subnet with no route to an internet gateway. The application server connects to the database through the VPC's internal network. Which security benefit does this configuration provide?

   A) It encrypts all data stored on the database volume
   B) It prevents the database from being directly accessible from the public internet
   C) It eliminates the need for a security group on the database instance
   D) It automatically enables IAM database authentication

8. Which of the following are valid RDS database engines? (Select THREE.)

   A) Amazon Aurora (MySQL-compatible)
   B) MongoDB
   C) PostgreSQL
   D) Amazon DynamoDB
   E) MariaDB
   F) Redis

9. An RDS DB instance has automated backups enabled with a retention period of 7 days. A developer accidentally deletes critical data from a table at 2:30 PM today. Using point-in-time recovery, what happens when the developer restores the database to 2:25 PM?

   A) The existing DB instance is rolled back to 2:25 PM and continues running
   B) RDS creates a new DB instance with data as it existed at 2:25 PM
   C) RDS restores only the deleted table to the existing DB instance
   D) The restore fails because point-in-time recovery only works at daily backup boundaries

10. A DynamoDB table stores user session data with `SessionId` as the partition key. The application frequently needs to look up sessions by `UserId`, which is a non-key attribute. What is the most efficient way to support this access pattern without scanning the entire table?

    A) Add a filter expression to the Scan operation to match `UserId`
    B) Change the table's partition key from `SessionId` to `UserId`
    C) Create a Global Secondary Index (GSI) with `UserId` as the partition key
    D) Use the Query operation with a filter expression on `UserId`

---

<details>
<summary>Answer Key</summary>

1. **B) Multi-AZ provides a synchronous standby for automatic failover; read replicas provide asynchronous copies that serve read traffic**
   Multi-AZ deployments maintain a synchronous standby replica in a different Availability Zone. The standby is not accessible for read traffic; its sole purpose is automatic failover if the primary fails. Read replicas use asynchronous replication and actively serve read queries, which helps scale read-heavy workloads. Option A reverses the replication types. Option C is incorrect because the Multi-AZ standby does not serve read traffic. Option D is incorrect because read replicas require manual promotion, not automatic failover.
   Further reading: [Multi-AZ DB instance deployments](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZSingleStandby.html), [Working with read replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html)

2. **False.**
   Amazon Aurora is an AWS-built database engine that is fully compatible with MySQL and PostgreSQL. Applications that run on MySQL or PostgreSQL can typically run on Aurora with little or no modification. Aurora provides up to five times the throughput of standard MySQL and up to three times the throughput of standard PostgreSQL, with a distributed, fault-tolerant storage system.
   Further reading: [What is Amazon Aurora?](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)

3. **A and C**
   With a composite primary key (partition key + sort key), multiple items can share the same partition key value as long as the combination of partition key and sort key is unique (A). The Query operation retrieves all items with a given partition key and returns them sorted by the sort key (C). Option B is incorrect because uniqueness is enforced on the combination, not the partition key alone. Option D is incorrect because the sort key is required for every item when the table uses a composite key. Option E is incorrect because items with the same partition key are stored together in the same partition, not in different partitions.
   Further reading: [Core components of Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html)

4. **Sample answer:** A Query operation retrieves items from a single partition by specifying the partition key value. It reads only the items in that partition, making it efficient regardless of the total table size. You can optionally use a sort key condition to narrow the results further. A Scan operation reads every item in the entire table (or index) and then applies any filter expressions after reading. Even with a filter, DynamoDB charges you for all items scanned, not just the items returned. For large tables, Scan is significantly slower and more expensive than Query. You should prefer Query whenever you know the partition key.
   Further reading: [Querying tables in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html), [Scanning tables in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html)

5. **C) On-demand mode**
   On-demand mode automatically scales to accommodate your workload without requiring you to specify expected throughput in advance. You pay per request, which is ideal for new applications with unknown or unpredictable traffic patterns. Provisioned mode with fixed capacity (A) risks throttling if traffic exceeds the provisioned amount. Provisioned mode with auto scaling (B) is better suited for workloads with established, predictable patterns. There is no "reserved capacity mode" (D) in DynamoDB.
   Further reading: [DynamoDB on-demand capacity mode](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/on-demand-capacity-mode.html)

6. **False.**
   Reads from a Global Secondary Index are eventually consistent. DynamoDB propagates changes from the base table to the GSI asynchronously, so there may be a brief delay before the GSI reflects the latest writes. You cannot request strongly consistent reads from a GSI.
   Further reading: [Using Global Secondary Indexes in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html)

7. **B) It prevents the database from being directly accessible from the public internet**
   Placing an RDS instance in a private subnet (one with no route to an internet gateway) ensures that the database cannot be reached from the public internet. Only resources within the VPC (or connected through VPN/Direct Connect) can access it. Option A describes encryption at rest, which is a separate configuration. Option C is incorrect because security groups are still required to control which resources within the VPC can connect. Option D is incorrect because IAM database authentication must be explicitly enabled.
   Further reading: [Working with a DB instance in a VPC](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html)

8. **A, C, E**
   Amazon RDS supports six database engines: Amazon Aurora (MySQL-compatible and PostgreSQL-compatible), MySQL, PostgreSQL, MariaDB, Oracle Database, and Microsoft SQL Server. MongoDB (B) is a NoSQL database not offered through RDS (AWS offers Amazon DocumentDB for MongoDB compatibility). DynamoDB (D) is a separate NoSQL service, not an RDS engine. Redis (F) is an in-memory data store offered through Amazon ElastiCache, not RDS.
   Further reading: [What is Amazon RDS?](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)

9. **B) RDS creates a new DB instance with data as it existed at 2:25 PM**
   Point-in-time recovery creates a new DB instance restored to the specified time. It does not modify or roll back the existing instance (A). It restores the entire DB instance, not individual tables (C). Point-in-time recovery can restore to any second within the retention period, not just daily backup boundaries (D). RDS combines automated snapshots with transaction logs to enable this granularity.
   Further reading: [Backing up and restoring an Amazon RDS DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_CommonTasks.BackupRestore.html)

10. **C) Create a Global Secondary Index (GSI) with `UserId` as the partition key**
    A GSI lets you query the table using an alternate key. By creating a GSI with `UserId` as the partition key, you can efficiently retrieve all sessions for a specific user without scanning the entire table. Option A (Scan with filter) reads every item in the table, which is inefficient and expensive. Option B (changing the partition key) would require recreating the table and would break the ability to look up sessions by `SessionId`. Option D (Query with filter on a non-key attribute) does not work because Query requires a partition key condition on the table's primary key or a GSI key.
    Further reading: [Using Global Secondary Indexes in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html)

</details>
