# Phase 2 Exam: Core Services

## Exam Information

| Field | Details |
|-------|---------|
| Phase | Phase 2: Core Services |
| Modules Covered | Module 04 (Compute with Amazon EC2), Module 05 (Storage with Amazon S3), Module 06 (Databases with Amazon RDS and DynamoDB), Module 07 (Load Balancing and DNS), Module 08 (Messaging and Integration) |
| Estimated Duration | 60 to 90 minutes |
| Passing Score | 70% |
| Total Questions | 25 |
| Question Types | Multiple choice (single correct), multiple choice (multiple correct), scenario-based, ordering/sequencing |

> **Tip:** Read each question carefully. For questions that say "select TWO" or "select THREE," you must choose the exact number of answers specified. Partial credit is not awarded.

---

## Questions

**Question 1**

A startup is building a web application that experiences unpredictable traffic spikes. The development team wants to minimize costs during low-traffic periods while ensuring the application can handle sudden increases in demand. The team does not have enough historical data to predict usage patterns. Which EC2 pricing model is most appropriate for this workload?

A. Reserved Instances with a one-year All Upfront commitment, because the upfront payment provides the largest discount.

B. Spot Instances, because they offer up to 90% savings compared to On-Demand pricing.

C. On-Demand Instances, because they provide full flexibility with no commitment and the team can start or stop instances at any time based on actual demand.

D. Savings Plans with a three-year commitment, because they automatically apply discounts across all instance types.

---

**Question 2**

A company stores application logs in Amazon S3. The logs are accessed frequently during the first 30 days for troubleshooting, occasionally accessed between 30 and 90 days for compliance audits, and must be retained for one year but are rarely accessed after 90 days. Which S3 Lifecycle configuration minimizes storage costs while meeting these requirements?

A. Store all logs in S3 Standard permanently and delete them after one year.

B. Store logs in S3 Standard, transition to S3 Standard-IA after 30 days, transition to S3 Glacier Flexible Retrieval after 90 days, and expire after 365 days.

C. Store logs in S3 One Zone-IA from day one and expire after 365 days.

D. Store logs in S3 Glacier Deep Archive from day one and expire after 365 days.

---

**Question 3**

A solutions architect is designing a database tier for an e-commerce application. The application requires complex SQL queries that join data across multiple tables, ACID transactions spanning several tables, and the ability to generate monthly sales reports with aggregations. Which AWS database service is the best fit?

A. Amazon DynamoDB, because it provides single-digit millisecond latency at any scale.

B. Amazon RDS, because it supports SQL queries with joins, multi-table ACID transactions, and complex aggregations for reporting.

C. Amazon ElastiCache, because it stores data in memory for the fastest possible query response times.

D. Amazon S3 with Amazon Athena, because S3 provides virtually unlimited storage for transaction data.

---

**Question 4**

An application running on EC2 instances behind an Application Load Balancer (ALB) is experiencing intermittent 503 errors. The operations team discovers that the ALB health checks are failing for some instances. Which TWO actions should the team take to diagnose and resolve the issue? (Select TWO.)

A. Change the ALB to a Network Load Balancer, because NLBs do not perform health checks.

B. Verify that the health check path returns an HTTP 200 status code and that the application is listening on the health check port.

C. Increase the health check interval to 300 seconds to give instances more time to respond.

D. Check the security group attached to the EC2 instances to confirm it allows inbound traffic from the ALB on the health check port.

E. Disable health checks entirely so the ALB sends traffic to all registered targets.

---

**Question 5**

A company needs to process customer orders in the exact sequence they are submitted. Each order must be processed exactly once to prevent duplicate charges. Which Amazon SQS queue type should the company use, and why?

A. Standard queue, because it guarantees exactly-once delivery and strict ordering for all messages.

B. FIFO queue, because it guarantees first-in, first-out ordering and exactly-once processing using message deduplication.

C. Standard queue with long polling enabled, because long polling ensures messages are delivered in order.

D. FIFO queue with a visibility timeout of zero, because this ensures each message is processed immediately without delay.

---

**Question 6**

A media company stores video files in Amazon S3. The company wants to protect against accidental deletion of video files by users. Which TWO S3 features should the company enable to meet this requirement? (Select TWO.)

A. Enable S3 Versioning on the bucket so that deleted objects receive a delete marker instead of being permanently removed.

B. Enable S3 Transfer Acceleration to speed up uploads and prevent data loss during transfer.

C. Enable S3 Block Public Access to prevent unauthorized users from deleting objects.

D. Enable MFA Delete on the versioning-enabled bucket so that permanently deleting an object version requires Multi-Factor Authentication.

E. Enable S3 Cross-Region Replication to create copies of objects in another Region for backup.

---

**Question 7**

A solutions architect needs to deploy a PostgreSQL database on AWS for a production application. The database must remain available if a single Availability Zone experiences an outage. The architect also needs to minimize the recovery time in the event of a failure. Which RDS deployment option meets these requirements?

A. Deploy a Single-AZ RDS instance and create manual snapshots every hour for backup.

B. Deploy an RDS Multi-AZ instance, which maintains a synchronous standby replica in a different Availability Zone with automatic failover.

C. Deploy an RDS read replica in a different Availability Zone and manually promote it if the primary fails.

D. Deploy two independent Single-AZ RDS instances in different Availability Zones and use application-level logic to switch between them.

---

**Question 8**

A company is deploying a web application across two Availability Zones. The application runs on EC2 instances managed by an Auto Scaling group. The team wants to distribute incoming HTTPS traffic across the instances and route requests to different backend services based on the URL path (for example, `/api/*` to one set of servers and `/static/*` to another). Which load balancer type should the team use?

A. Network Load Balancer (NLB), because it supports path-based routing for HTTP traffic.

B. Gateway Load Balancer (GLB), because it inspects all traffic at the network layer.

C. Application Load Balancer (ALB), because it operates at Layer 7 and supports path-based and host-based routing for HTTP and HTTPS traffic.

D. Classic Load Balancer, because it supports both Layer 4 and Layer 7 routing.

---

**Question 9**

A development team is building a notification system. When a new order is placed, the system must simultaneously send an email to the customer, add a message to a processing queue, and invoke a Lambda function for analytics. Which AWS service is best suited for this fan-out requirement?

A. Amazon SQS, because it can deliver messages to multiple consumers simultaneously.

B. Amazon SNS, because it uses a publish/subscribe model that pushes messages to multiple subscriber endpoints (email, SQS, Lambda) from a single topic.

C. Amazon EventBridge, because it is the only service that supports Lambda as a target.

D. AWS Step Functions, because it can orchestrate parallel execution of multiple tasks.

---

**Question 10**

A data engineering team needs to store sensor readings from IoT devices. Each device sends thousands of readings per second. The data model is simple: each reading has a device ID, a timestamp, and a set of metric values. The team needs single-digit millisecond read latency when querying the latest readings for a specific device. Which database solution is the best fit?

A. Amazon RDS for MySQL with a Multi-AZ deployment for high availability.

B. Amazon DynamoDB with a composite primary key using device ID as the partition key and timestamp as the sort key.

C. Amazon RDS for PostgreSQL with read replicas to distribute the read load.

D. Amazon S3 with objects organized by device ID prefix for fast lookups.

---

**Question 11**

A company runs a batch processing workload that analyzes large log files stored on Amazon EBS volumes attached to EC2 instances. The workload reads data sequentially and requires high throughput but does not need high IOPS. The company wants to minimize storage costs. Which EBS volume type is the best choice for this workload?

A. General Purpose SSD (gp3), because it provides a balance of price and performance for most workloads.

B. Provisioned IOPS SSD (io2 Block Express), because it delivers the highest performance for any workload.

C. Throughput Optimized HDD (st1), because it is designed for sequential, throughput-intensive workloads at a lower cost than SSD volumes.

D. Cold HDD (sc1), because it provides the lowest storage cost of any EBS volume type.

---

**Question 12**

A solutions architect is designing a disaster recovery strategy for a web application. The application runs behind an Application Load Balancer in the `us-east-1` Region. The architect deploys a standby copy of the application behind another ALB in `us-west-2`. The architect wants DNS to automatically direct traffic to the standby Region if the primary Region becomes unavailable. Which Route 53 configuration achieves this?

A. Create a simple routing record that points to both ALBs and let Route 53 return both addresses to the client.

B. Create weighted routing records with equal weights for both ALBs so traffic is split 50/50 between Regions.

C. Create failover routing records with a health check on the primary (`us-east-1`) ALB. Configure the `us-east-1` record as primary and the `us-west-2` record as secondary.

D. Create geolocation routing records that direct North American users to `us-east-1` and all other users to `us-west-2`.

---

**Question 13**

An application uses an Amazon SQS queue to process image uploads. Each image takes approximately 120 seconds to process. The operations team notices that some images are being processed more than once. Which configuration change is most likely to resolve the duplicate processing issue?

A. Increase the message retention period to 14 days so messages are not lost.

B. Increase the visibility timeout to at least 120 seconds (with buffer) so the message remains hidden while the consumer processes it.

C. Enable long polling with a wait time of 20 seconds to reduce empty responses.

D. Switch to a FIFO queue, because FIFO queues never deliver duplicate messages regardless of visibility timeout settings.

---

**Question 14**

A company wants to host a static marketing website on AWS. The website consists of HTML, CSS, JavaScript, and image files. The company wants the simplest and most cost-effective hosting solution. Which approach should the company use?

A. Launch an EC2 instance, install a web server, and upload the website files to the instance.

B. Enable S3 static website hosting on a bucket, upload the website files, configure an index document, and add a bucket policy that grants public read access.

C. Deploy the website files to an Amazon RDS database and serve them through a custom application.

D. Use AWS Lambda to serve each HTML page as a response to API Gateway requests.

---

**Question 15**

A solutions architect is configuring an Auto Scaling group for a web application. The application must maintain at least 2 instances at all times, typically runs 4 instances during normal business hours, and can scale up to 10 instances during peak traffic. Which Auto Scaling group configuration correctly represents these requirements?

A. Minimum capacity: 0, Desired capacity: 4, Maximum capacity: 10

B. Minimum capacity: 2, Desired capacity: 4, Maximum capacity: 10

C. Minimum capacity: 4, Desired capacity: 4, Maximum capacity: 10

D. Minimum capacity: 2, Desired capacity: 10, Maximum capacity: 10

---

**Question 16**

A company has an SNS topic that receives order events. Three different teams need to process these events independently: the fulfillment team, the analytics team, and the notification team. Each team processes events at a different speed, and a failure in one team's processing must not affect the others. Which architecture pattern should the company implement?

A. Subscribe all three teams' applications directly to the SNS topic using HTTP endpoints.

B. Create three separate SQS queues (one per team), subscribe each queue to the SNS topic, and have each team's application poll its own queue.

C. Create a single SQS queue subscribed to the SNS topic and have all three teams poll the same queue.

D. Use Amazon EventBridge instead of SNS, because EventBridge is the only service that supports multiple consumers.

---

**Question 17**

A database administrator needs to scale read traffic for an Amazon RDS MySQL database that serves a reporting dashboard. The dashboard generates complex read queries that are slowing down the primary database, which also handles write operations from the application. Which approach should the administrator use to offload read traffic without affecting write performance?

A. Enable RDS Multi-AZ to distribute read traffic across the primary and standby instances.

B. Create one or more RDS read replicas and direct the reporting dashboard queries to the read replica endpoints.

C. Increase the instance class of the primary RDS instance to handle both read and write traffic.

D. Migrate the database to Amazon DynamoDB, which automatically scales to handle any read volume.

---

**Question 18**

A company is migrating an application to AWS. The application produces events that must be routed to different processing targets based on the event content. For example, events from the "payments" source should go to a Lambda function, while events from the "inventory" source should go to an SQS queue. The company also needs to capture events generated by AWS services such as EC2 instance state changes. Which service is the best fit for this event routing requirement?

A. Amazon SQS, because it can filter messages based on message attributes.

B. Amazon SNS, because it supports message filtering on subscriptions.

C. Amazon EventBridge, because it provides content-based routing with event patterns, supports AWS service events natively, and can route to multiple target types.

D. AWS Step Functions, because it can evaluate event content and branch to different processing paths.

---

**Question 19**

Place the following steps in the correct order for how an Auto Scaling group responds when a target tracking scaling policy detects that average CPU utilization has exceeded the target value.

1. The Auto Scaling group launches new instances using the launch template configuration.
2. CloudWatch reports that the average CPU utilization metric exceeds the target value.
3. New instances are registered with the ALB target group and begin receiving traffic after passing health checks.
4. The Auto Scaling group calculates how many instances to add to bring the metric back to the target.

A. 2, 4, 1, 3

B. 1, 2, 3, 4

C. 4, 2, 1, 3

D. 2, 1, 4, 3

---

**Question 20**

A solutions architect is designing a high-availability architecture for a web application. The application runs on EC2 instances in an Auto Scaling group behind an ALB in `us-east-1`. The architect configures Route 53 with a failover routing policy and a health check on the primary ALB. A standby ALB exists in `us-west-2`. Which combination correctly describes how the ALB health checks and Route 53 health checks work together in this architecture? (Select TWO.)

A. The ALB health checks monitor individual EC2 instances within the target group and remove unhealthy instances from the load balancer's rotation.

B. The Route 53 health check monitors the ALB endpoint and determines whether to return the primary or secondary DNS record to clients.

C. The ALB health checks automatically update Route 53 DNS records when an instance fails.

D. The Route 53 health check replaces the ALB health check, so only one type of health check is needed.

E. The ALB health checks operate at the DNS level and control which Region receives traffic.

---

**Question 21**

A company stores 500 TB of historical data in Amazon S3. The data is accessed approximately once per quarter for regulatory audits. When accessed, the data must be available within 12 hours. The company wants to minimize storage costs. Which S3 storage class is the most cost-effective for this use case?

A. S3 Standard, because it provides immediate access with no retrieval fees.

B. S3 Standard-IA, because it is designed for infrequently accessed data with immediate retrieval.

C. S3 Glacier Flexible Retrieval, because it provides low-cost archival storage with retrieval times ranging from minutes to hours.

D. S3 Glacier Deep Archive, because it provides the lowest storage cost and supports retrieval within 12 to 48 hours, which meets the 12-hour requirement.

---

**Question 22**

A development team is building a DynamoDB table to store user session data. Each user can have multiple active sessions. The team needs to query all active sessions for a specific user efficiently. They also need to query sessions by expiration time across all users to clean up expired sessions. Which table design supports both access patterns?

A. Use a partition key of `SessionId` only. Scan the entire table and filter by `UserId` or `ExpirationTime` as needed.

B. Use a composite primary key with `UserId` as the partition key and `SessionId` as the sort key. Create a Global Secondary Index (GSI) with `ExpirationTime` as the partition key.

C. Use a partition key of `UserId` only. Create a local secondary index on `ExpirationTime`.

D. Create two separate DynamoDB tables: one keyed by `UserId` and one keyed by `ExpirationTime`.

---

**Question 23**

A company runs a web application on EC2 instances. The application stores user-uploaded images on the instance's root EBS volume. The operations team wants to ensure that images are not lost if an instance is terminated. Which TWO changes should the team make? (Select TWO.)

A. Store user-uploaded images in Amazon S3 instead of on the instance's local EBS volume.

B. Create EBS snapshots of the root volume on a regular schedule to enable point-in-time recovery.

C. Change the instance type to a storage-optimized instance to increase local disk durability.

D. Enable S3 Transfer Acceleration on the instance to speed up image uploads.

E. Attach an additional EBS volume and configure it to not delete on termination, then store images on that volume.

---

**Question 24**

A solutions architect needs to configure an SQS queue for a payment processing application. Messages that fail processing after three attempts should be moved to a separate queue for manual investigation. Which SQS feature should the architect configure?

A. Enable long polling with a 20-second wait time to ensure all messages are received.

B. Configure a dead-letter queue (DLQ) with a redrive policy that sets the maximum receive count to 3.

C. Set the message retention period to 14 days so failed messages are not deleted.

D. Switch to a FIFO queue, because FIFO queues automatically move failed messages to a separate queue.

---

**Question 25**

A company is designing a decoupled, event-driven architecture for an e-commerce platform. When a customer places an order, the following must happen independently and in parallel: the inventory service deducts stock, the payment service charges the customer, and the shipping service schedules delivery. Each service processes at a different speed, and a temporary failure in one service must not block the others. The architecture must also handle failed messages gracefully. Which architecture meets all of these requirements? (Select THREE.)

A. Publish order events to an SNS topic that fans out to three separate SQS queues, one for each service.

B. Send order events directly from the application to each service using synchronous HTTP calls.

C. Configure a dead-letter queue (DLQ) on each SQS queue to capture messages that fail processing after multiple attempts.

D. Use a single SQS queue shared by all three services, with each service filtering for its own message type.

E. Have each service poll its own SQS queue independently, processing messages at its own pace.

F. Use Amazon EventBridge to send order events directly to each service's HTTP endpoint without any buffering.

---

<details>
<summary>Answer Key</summary>

### Question 1

**Correct Answer: C**

On-Demand Instances provide full flexibility with no long-term commitment. You pay by the second (Linux) or by the hour (Windows) and can start or stop instances at any time. For a startup with unpredictable traffic and no historical usage data, On-Demand is the appropriate choice because it avoids the risk of over-committing or under-committing capacity.

- A is incorrect because Reserved Instances require a one-year or three-year commitment to a specific instance type. Without historical data to predict usage, the startup risks paying for capacity it does not use.
- B is incorrect because Spot Instances can be interrupted by AWS with a two-minute warning when capacity is needed. A web application serving live user traffic cannot tolerate sudden instance termination without a more complex architecture (such as a mixed instance Auto Scaling group).
- D is incorrect because Savings Plans require a commitment to a consistent amount of compute usage (in dollars per hour) for one or three years. Without usage history, the startup cannot determine the right commitment level.

Reference: [Amazon EC2 Pricing](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html)

---

### Question 2

**Correct Answer: B**

This Lifecycle configuration matches the access pattern described: S3 Standard for the first 30 days (frequent access), S3 Standard-IA from 30 to 90 days (occasional access with lower storage cost), and S3 Glacier Flexible Retrieval from 90 to 365 days (rare access with the lowest practical storage cost for data that must be retained). Expiration at 365 days automatically deletes the logs after the retention period.

- A is incorrect because keeping all logs in S3 Standard for the entire year is the most expensive option. S3 Standard has the highest per-GB storage cost, and the logs are rarely accessed after 90 days.
- C is incorrect because S3 One Zone-IA stores data in a single Availability Zone, which reduces durability compared to Standard-IA. For compliance data, the reduced durability is a risk. Additionally, storing logs in One Zone-IA from day one means paying retrieval fees during the first 30 days when logs are accessed frequently.
- D is incorrect because S3 Glacier Deep Archive has retrieval times of 12 to 48 hours. Logs accessed frequently during the first 30 days for troubleshooting require immediate access, which Glacier Deep Archive cannot provide.

Reference: [Managing the Lifecycle of Objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)

---

### Question 3

**Correct Answer: B**

Amazon RDS supports relational database engines (MySQL, PostgreSQL, Oracle, SQL Server, MariaDB, Aurora) that provide SQL queries with joins, multi-table ACID transactions, and complex aggregations. These capabilities are essential for an e-commerce application that needs to join order, customer, and product tables and generate monthly sales reports.

- A is incorrect because DynamoDB is a NoSQL database that does not support SQL joins or multi-table ACID transactions. DynamoDB excels at key-based access patterns with single-digit millisecond latency, but it is not designed for complex relational queries or cross-table reporting.
- C is incorrect because Amazon ElastiCache is an in-memory caching service, not a primary database. It is used to cache frequently accessed data to reduce database load, not to store and query transactional data.
- D is incorrect because Amazon S3 is an object storage service, and Amazon Athena is a query service for analyzing data in S3 using SQL. While Athena supports SQL, it is designed for analytics on data lakes, not for transactional workloads that require ACID guarantees and low-latency writes.

Reference: [Amazon RDS Overview](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)

---

### Question 4

**Correct Answers: B, D**

When ALB health checks fail, the two most common causes are: the application is not returning the expected HTTP status code on the health check path (B), and the security group on the EC2 instances is not allowing inbound traffic from the ALB (D). The ALB sends health check requests from its own IP addresses, so the instance security group must allow traffic from the ALB's security group or IP range on the health check port.

- A is incorrect because NLBs also perform health checks. Switching load balancer types does not resolve health check failures and would remove the Layer 7 routing capabilities the application may depend on.
- C is incorrect because increasing the health check interval to 300 seconds delays detection of unhealthy instances but does not fix the underlying cause of the failure. If the application is not responding correctly, a longer interval only means it takes longer to detect the problem.
- E is incorrect because disabling health checks would cause the ALB to send traffic to unhealthy instances, resulting in errors for end users. Health checks are a critical component of high availability.

Reference: [Health Checks for ALB Target Groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html)

---

### Question 5

**Correct Answer: B**

FIFO (First-In, First-Out) queues guarantee that messages are delivered in the exact order they are sent and that each message is processed exactly once. FIFO queues use message deduplication IDs to eliminate duplicate messages and message group IDs to maintain ordering within groups. For order processing where sequence and exactly-once delivery matter, FIFO is the correct choice.

- A is incorrect because Standard queues provide at-least-once delivery (messages may be delivered more than once) and best-effort ordering (messages may arrive out of order). Neither guarantee meets the requirements for sequential, exactly-once order processing.
- C is incorrect because long polling controls how consumers retrieve messages from the queue (waiting up to 20 seconds for a message to arrive). Long polling does not affect message ordering or deduplication.
- D is incorrect because setting the visibility timeout to zero would make messages immediately visible to other consumers after being received, increasing the likelihood of duplicate processing. The visibility timeout should be set to at least the expected processing time.

Reference: [Amazon SQS FIFO Queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-fifo-queues.html)

---

### Question 6

**Correct Answers: A, D**

S3 Versioning (A) protects against accidental deletion by inserting a delete marker instead of permanently removing the object. Previous versions remain in the bucket and can be recovered by deleting the delete marker. MFA Delete (D) adds a second layer of protection by requiring Multi-Factor Authentication to permanently delete an object version or change the versioning state of the bucket.

- B is incorrect because S3 Transfer Acceleration speeds up uploads by routing data through CloudFront edge locations. It does not protect against accidental deletion.
- C is incorrect because S3 Block Public Access prevents public access to the bucket. It does not prevent authenticated users with appropriate IAM permissions from deleting objects.
- E is incorrect because Cross-Region Replication creates copies of objects in another Region, which provides geographic redundancy. However, if a user deletes an object in the source bucket, the delete marker is also replicated to the destination bucket (by default), so CRR alone does not protect against accidental deletion.

Reference: [S3 Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html)

---

### Question 7

**Correct Answer: B**

RDS Multi-AZ deployments maintain a synchronous standby replica in a different Availability Zone. If the primary instance fails, RDS automatically promotes the standby to become the new primary, typically within one to two minutes. The application connects through a DNS endpoint that RDS updates automatically, so no application changes are needed during failover.

- A is incorrect because a Single-AZ instance with hourly snapshots does not provide automatic failover. If the AZ experiences an outage, the database is unavailable until you manually restore from a snapshot, which can take significant time depending on the database size.
- C is incorrect because read replicas use asynchronous replication and do not support automatic failover. Promoting a read replica to a standalone instance is a manual process that requires updating the application's connection string. Read replicas are designed for scaling read traffic, not for high availability.
- D is incorrect because managing two independent Single-AZ instances with application-level failover logic is complex, error-prone, and does not provide synchronous replication. Data written to the primary may not exist on the secondary, leading to potential data loss during failover.

Reference: [RDS Multi-AZ Deployments](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZSingleStandby.html)

---

### Question 8

**Correct Answer: C**

An Application Load Balancer operates at Layer 7 (the application layer) and can inspect HTTP/HTTPS request content to make routing decisions. ALB supports path-based routing (routing `/api/*` to one target group and `/static/*` to another), host-based routing, and header-based routing. It also supports HTTPS listeners with SSL/TLS termination.

- A is incorrect because a Network Load Balancer operates at Layer 4 (the transport layer) and routes traffic based on IP protocol data (TCP/UDP port). NLB does not inspect HTTP content and does not support path-based or host-based routing.
- B is incorrect because a Gateway Load Balancer is designed for deploying and scaling third-party virtual network appliances (firewalls, intrusion detection systems). It is not used for routing web application traffic.
- D is incorrect because Classic Load Balancers are a previous-generation load balancer. While they support some Layer 7 features, they do not support path-based routing or host-based routing. AWS recommends using ALB for new HTTP/HTTPS workloads.

Reference: [Application Load Balancer Overview](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)

---

### Question 9

**Correct Answer: B**

Amazon SNS uses a publish/subscribe model where a publisher sends a message to a topic, and SNS delivers that message to all subscribed endpoints. SNS supports multiple subscription protocols including email, SQS, Lambda, HTTP/HTTPS, and SMS. Publishing a single message to an SNS topic can simultaneously trigger email delivery, queue a message in SQS, and invoke a Lambda function.

- A is incorrect because SQS is a point-to-point messaging service where each message is processed by a single consumer. SQS does not natively fan out messages to multiple consumers. To achieve fan-out with SQS, you would need to combine it with SNS.
- C is incorrect because EventBridge is not the only service that supports Lambda as a target. SNS can also invoke Lambda functions directly. While EventBridge is excellent for event routing with complex patterns, SNS is simpler and more appropriate for straightforward fan-out to multiple subscriber types.
- D is incorrect because Step Functions orchestrates multi-step workflows with sequential or parallel execution. While Step Functions can invoke multiple services in parallel, it is designed for workflow orchestration, not for simple message fan-out. Using Step Functions for this use case adds unnecessary complexity.

Reference: [Subscribing an SQS Queue to an SNS Topic](https://docs.aws.amazon.com/sns/latest/dg/subscribe-sqs-queue-to-sns-topic.html)

---

### Question 10

**Correct Answer: B**

DynamoDB with a composite primary key (device ID as partition key, timestamp as sort key) is the best fit. The partition key distributes data across partitions by device, and the sort key enables efficient range queries on timestamps. This design supports querying the latest readings for a specific device by querying the partition key with a sort key condition. DynamoDB provides single-digit millisecond latency at any scale, which meets the latency requirement.

- A is incorrect because RDS for MySQL is a relational database that scales vertically (by increasing instance size). At thousands of readings per second per device across many devices, the write throughput would require a very large instance, and the relational model adds overhead for a simple key-value access pattern.
- C is incorrect because RDS read replicas help with read scaling but do not address the write throughput challenge. Read replicas also introduce replication lag, and the relational model is unnecessarily complex for this access pattern.
- D is incorrect because S3 is an object storage service that does not provide single-digit millisecond read latency for individual records. S3 is designed for storing and retrieving files, not for high-frequency, low-latency data lookups.

Reference: [DynamoDB Core Components](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html)

---

### Question 11

**Correct Answer: C**

Throughput Optimized HDD (st1) volumes are designed for frequently accessed, throughput-intensive workloads that perform large, sequential I/O operations. st1 provides up to 500 MiB/s throughput at a lower cost per GB than SSD volumes. Log file analysis with sequential reads is a textbook use case for st1.

- A is incorrect because gp3 (General Purpose SSD) provides a balance of IOPS and throughput, but its per-GB cost is higher than st1. Since the workload does not require high IOPS (random I/O), the SSD performance characteristics are unnecessary, and st1 is more cost-effective.
- B is incorrect because io2 Block Express (Provisioned IOPS SSD) is the most expensive EBS volume type, designed for latency-sensitive transactional workloads that require very high IOPS. Using io2 for sequential batch processing would be a significant waste of budget.
- D is incorrect because sc1 (Cold HDD) is designed for infrequently accessed data with the lowest storage cost. The question states the workload processes log files actively (not cold storage), and sc1 provides only 250 MiB/s maximum throughput, which is half of st1's throughput.

Reference: [Amazon EBS Volume Types](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volume-types.html)

---

### Question 12

**Correct Answer: C**

Route 53 failover routing with a health check on the primary ALB provides automatic DNS-based disaster recovery. When the health check detects that the primary ALB in `us-east-1` is unhealthy, Route 53 stops returning the primary record and returns the secondary record (`us-west-2`) instead. When the primary recovers, Route 53 automatically switches traffic back.

- A is incorrect because simple routing returns all values to the client, and the client chooses which one to use. Simple routing does not perform health checks or automatic failover. If the primary ALB is down, clients may still attempt to connect to it.
- B is incorrect because weighted routing distributes traffic proportionally between both Regions at all times. This is useful for gradual deployments or load distribution, but it does not provide automatic failover. If the primary Region fails, 50% of traffic would still be directed to the failed Region (unless health checks are also configured, but failover routing is the correct pattern for active-passive DR).
- D is incorrect because geolocation routing directs traffic based on the user's geographic location, not based on the health of the endpoint. If the primary Region fails, users routed to that Region by geolocation would experience an outage.

Reference: [Active-Active and Active-Passive Failover](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-types.html)

---

### Question 13

**Correct Answer: B**

The default SQS visibility timeout is 30 seconds. If the consumer takes 120 seconds to process a message, the visibility timeout expires after 30 seconds, and the message becomes visible to other consumers (or the same consumer on the next poll). This causes the message to be processed multiple times. Increasing the visibility timeout to at least 120 seconds (with additional buffer for variability) keeps the message hidden until the consumer finishes processing and deletes it.

- A is incorrect because the message retention period controls how long SQS keeps unprocessed messages in the queue (default 4 days, maximum 14 days). Increasing retention does not affect duplicate processing, which is caused by the visibility timeout expiring before the consumer finishes.
- C is incorrect because long polling controls how the consumer retrieves messages from the queue. Long polling reduces empty responses and API costs but does not affect message visibility or duplicate processing.
- D is incorrect because while FIFO queues provide exactly-once processing, the duplicate processing described in this scenario is caused by the visibility timeout being too short, not by the queue type. Switching to a FIFO queue would also reduce throughput (FIFO queues have lower throughput limits than Standard queues) and may not be necessary if the visibility timeout is configured correctly.

Reference: [Amazon SQS Visibility Timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html)

---

### Question 14

**Correct Answer: B**

S3 static website hosting is the simplest and most cost-effective way to host a static website. You enable website hosting on a bucket, specify an index document (typically `index.html`), upload your files, and add a bucket policy for public read access. S3 handles availability, durability, and scaling automatically. You pay only for storage and data transfer, with no server management required.

- A is incorrect because launching an EC2 instance to serve static files requires provisioning, patching, and managing a server. This adds operational overhead and cost compared to S3 static hosting, which is fully managed.
- C is incorrect because Amazon RDS is a relational database service, not a web hosting service. Storing and serving website files from a database is architecturally inappropriate and unnecessarily complex.
- D is incorrect because using Lambda and API Gateway to serve static HTML pages adds complexity and cost. Lambda is designed for dynamic compute, not for serving static files. S3 static hosting is simpler, cheaper, and purpose-built for this use case.

Reference: [Hosting a Static Website on Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)

---

### Question 15

**Correct Answer: B**

An Auto Scaling group with minimum = 2, desired = 4, and maximum = 10 meets all three requirements. The minimum of 2 ensures at least 2 instances are always running. The desired of 4 represents the normal operating capacity. The maximum of 10 allows the group to scale up during peak traffic but prevents runaway scaling.

- A is incorrect because a minimum of 0 means the Auto Scaling group could scale down to zero instances, violating the requirement to maintain at least 2 instances at all times.
- C is incorrect because a minimum of 4 means the group can never scale below 4 instances. The requirement states the application must maintain at least 2 instances, not 4. Setting the minimum to 4 wastes resources during low-traffic periods.
- D is incorrect because a desired capacity of 10 means the group starts at maximum capacity. The requirement states the application typically runs 4 instances during normal hours and scales to 10 only during peak traffic. Starting at 10 wastes resources during normal periods.

Reference: [Auto Scaling Groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)

---

### Question 16

**Correct Answer: B**

The SNS + SQS fan-out pattern is the correct architecture. SNS delivers a copy of each message to all subscribed SQS queues. Each team polls its own queue independently, processes messages at its own speed, and a failure in one team's consumer does not affect the others. SQS provides buffering, so if a consumer is temporarily down, messages accumulate in the queue and are processed when the consumer recovers.

- A is incorrect because subscribing HTTP endpoints directly to SNS means SNS pushes messages to each endpoint immediately. If an endpoint is temporarily unavailable, the message delivery fails (SNS retries with a delivery policy, but messages can be lost if retries are exhausted). There is no buffering, and a slow consumer cannot process at its own pace.
- C is incorrect because a single SQS queue delivers each message to only one consumer. If three teams poll the same queue, each message is processed by only one team, not all three. SQS is a point-to-point service, not a broadcast service.
- D is incorrect because EventBridge is not the only service that supports multiple consumers. SNS supports multiple subscribers natively. While EventBridge could work for this use case, the statement that it is the "only" service is factually wrong, and the SNS + SQS fan-out pattern is the standard approach for this requirement.

Reference: [Subscribing an SQS Queue to an SNS Topic](https://docs.aws.amazon.com/sns/latest/dg/subscribe-sqs-queue-to-sns-topic.html)

---

### Question 17

**Correct Answer: B**

RDS read replicas are read-only copies of the primary database that receive data through asynchronous replication. You can direct read-heavy queries (such as reporting dashboard queries) to the read replica endpoint, which offloads the read traffic from the primary instance. The primary instance continues to handle write operations without contention from reporting queries.

- A is incorrect because the Multi-AZ standby instance is not accessible for read traffic. Its sole purpose is to provide a failover target for high availability. You cannot direct queries to the standby instance.
- C is incorrect because increasing the instance class (vertical scaling) addresses the symptom but not the root cause. A larger instance handles more load, but the reporting queries still compete with write operations on the same instance. Read replicas provide a dedicated resource for read traffic, which is a more scalable and cost-effective solution.
- D is incorrect because migrating from RDS to DynamoDB would require rewriting the application's data access layer, changing from SQL to the DynamoDB API. The reporting dashboard uses complex SQL queries with joins and aggregations, which DynamoDB does not support. This migration would be impractical for the described use case.

Reference: [Working with RDS Read Replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html)

---

### Question 18

**Correct Answer: C**

Amazon EventBridge is designed for content-based event routing. You define rules with event patterns that match specific fields in the event (such as the "source" field), and EventBridge routes matching events to the appropriate target (Lambda, SQS, SNS, Step Functions, and more). EventBridge also natively receives events from AWS services (such as EC2 instance state changes) on the default event bus, which meets the requirement to capture AWS service events.

- A is incorrect because SQS is a message queuing service, not an event routing service. SQS does not support content-based routing to different targets. A consumer polls the queue and processes all messages; there is no built-in mechanism to route different messages to different targets.
- B is incorrect because while SNS supports message filtering on subscriptions, it does not natively receive AWS service events. You would need to configure each AWS service to publish to the SNS topic manually. EventBridge receives AWS service events automatically on the default event bus, making it the better fit.
- D is incorrect because Step Functions is a workflow orchestration service, not an event routing service. While Step Functions can evaluate input and branch to different states, it is designed for coordinating multi-step workflows, not for routing events from multiple sources to multiple targets based on content.

Reference: [Amazon EventBridge Event Patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html)

---

### Question 19

**Correct Answer: A**

The correct sequence is: (1) CloudWatch reports that the CPU metric exceeds the target (step 2), (2) the Auto Scaling group calculates how many instances to add (step 4), (3) the group launches new instances from the launch template (step 1), (4) new instances register with the ALB target group and receive traffic after passing health checks (step 3). The sequence is 2, 4, 1, 3.

- B is incorrect because instances are not launched (step 1) before CloudWatch detects the metric breach (step 2). The scaling action is triggered by the metric, not the other way around.
- C is incorrect because the calculation (step 4) cannot happen before CloudWatch reports the metric (step 2). The Auto Scaling group needs to know the current metric value before it can calculate the adjustment.
- D is incorrect because the Auto Scaling group calculates the number of instances to add (step 4) before launching them (step 1), not after. The group must determine how many instances are needed before it can launch them.

Reference: [Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)

---

### Question 20

**Correct Answers: A, B**

ALB health checks and Route 53 health checks operate at different levels and serve complementary purposes. ALB health checks (A) monitor individual targets (EC2 instances) within a target group and remove unhealthy instances from the load balancer's rotation, ensuring traffic goes only to healthy instances within a Region. Route 53 health checks (B) monitor the ALB endpoint itself and determine whether to return the primary or secondary DNS record, enabling cross-Region failover when the entire primary Region or ALB becomes unavailable.

- C is incorrect because ALB health checks do not update Route 53 DNS records. ALB health checks manage target registration within the load balancer. Route 53 health checks independently monitor the ALB endpoint and manage DNS failover.
- D is incorrect because Route 53 health checks do not replace ALB health checks. They serve different purposes: ALB health checks manage instance-level traffic within a Region, while Route 53 health checks manage Region-level DNS failover. Both are needed for a complete high-availability architecture.
- E is incorrect because ALB health checks operate at the load balancer level (Layer 7), not at the DNS level. They check whether individual targets are healthy and route traffic within the ALB. DNS-level routing is handled by Route 53.

Reference: [Creating Route 53 Health Checks](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover.html)

---

### Question 21

**Correct Answer: D**

S3 Glacier Deep Archive provides the lowest storage cost of any S3 storage class. It is designed for data that is accessed once or twice per year and can tolerate retrieval times of 12 to 48 hours. Since the company accesses the data quarterly and can wait up to 12 hours for retrieval, Glacier Deep Archive meets both the access pattern and the retrieval time requirement at the lowest cost.

- A is incorrect because S3 Standard has the highest per-GB storage cost. Storing 500 TB of rarely accessed data in S3 Standard would be significantly more expensive than archival storage classes.
- B is incorrect because S3 Standard-IA has a lower storage cost than S3 Standard but is still significantly more expensive than Glacier storage classes. Standard-IA is designed for data accessed monthly, not quarterly.
- C is incorrect because S3 Glacier Flexible Retrieval has a higher storage cost than Glacier Deep Archive. While Glacier Flexible Retrieval offers faster retrieval options (expedited retrieval in 1 to 5 minutes), the company does not need sub-hour retrieval. Glacier Deep Archive provides the same data durability at a lower price.

Reference: [S3 Storage Classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html)

---

### Question 22

**Correct Answer: B**

A composite primary key with `UserId` as the partition key and `SessionId` as the sort key enables efficient queries for all sessions belonging to a specific user (query by partition key). A Global Secondary Index (GSI) with `ExpirationTime` as the partition key enables querying sessions by expiration time across all users for cleanup. This design supports both access patterns without scanning the entire table.

- A is incorrect because using `SessionId` as the only key means you cannot efficiently query all sessions for a specific user. A Scan operation reads every item in the table and filters client-side, which is slow and expensive at scale.
- C is incorrect because a Local Secondary Index (LSI) shares the same partition key as the base table. If the partition key is `UserId`, an LSI on `ExpirationTime` would only let you query expiration times within a single user's sessions, not across all users. A GSI is needed for the cross-user expiration query.
- D is incorrect because maintaining two separate tables with the same data creates data synchronization challenges. You must ensure both tables are updated atomically when sessions are created, updated, or deleted. A single table with a GSI achieves the same query flexibility without the synchronization overhead.

Reference: [DynamoDB Global Secondary Indexes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html)

---

### Question 23

**Correct Answers: A, E**

Storing images in Amazon S3 (A) decouples the data from the EC2 instance lifecycle. S3 provides 99.999999999% durability and persists data independently of any instance. Alternatively, attaching a separate EBS volume configured to not delete on termination (E) ensures the volume and its data survive instance termination. Both approaches protect against data loss when an instance is terminated.

- B is incorrect as a standalone solution because EBS snapshots provide point-in-time backups, but the root volume is still deleted by default when the instance is terminated. Snapshots help with recovery but do not prevent data loss between the last snapshot and the termination event. Snapshots are a complementary measure, not a primary solution.
- C is incorrect because changing the instance type to a storage-optimized instance does not change the behavior of EBS volumes on termination. Storage-optimized instances provide higher local storage performance but do not improve data durability when the instance is terminated.
- D is incorrect because S3 Transfer Acceleration speeds up uploads to S3 by routing through CloudFront edge locations. It does not protect data stored on EBS volumes from being lost when an instance is terminated.

Reference: [Amazon EBS Volume Types](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volume-types.html)

---

### Question 24

**Correct Answer: B**

A dead-letter queue (DLQ) with a redrive policy is the SQS feature designed for this exact use case. The redrive policy specifies the DLQ ARN and the maximum receive count. When a message is received from the source queue more times than the maximum receive count (3 in this case) without being deleted, SQS automatically moves it to the DLQ. The operations team can then inspect the DLQ to investigate why the messages failed.

- A is incorrect because long polling controls how consumers retrieve messages from the queue. It reduces empty responses and API costs but does not handle failed messages or move them to a separate queue.
- C is incorrect because the message retention period controls how long unprocessed messages remain in the queue before being automatically deleted. Increasing retention keeps messages longer but does not separate failed messages from successful ones or move them to a different queue for investigation.
- D is incorrect because FIFO queues do not automatically move failed messages to a separate queue. FIFO queues provide ordering and exactly-once processing, but dead-letter queue functionality must be configured separately on any queue type (Standard or FIFO).

Reference: [Using Dead-Letter Queues in Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html)

---

### Question 25

**Correct Answers: A, C, E**

The SNS + SQS fan-out pattern (A) delivers each order event to three independent SQS queues, one per service. Each service polls its own queue independently (E), processing at its own speed without blocking the others. Dead-letter queues on each SQS queue (C) capture messages that fail processing after multiple attempts, providing graceful failure handling. Together, these three components create a fully decoupled, fault-tolerant, event-driven architecture.

- B is incorrect because synchronous HTTP calls create tight coupling between the application and each service. If one service is slow or unavailable, the application blocks or fails. Synchronous calls do not provide independent processing, buffering, or fault isolation.
- D is incorrect because a single shared SQS queue delivers each message to only one consumer. The three services would compete for messages, and each order event would be processed by only one service instead of all three.
- F is incorrect because sending events directly to HTTP endpoints without buffering creates the same tight coupling problem as option B. If a service is temporarily unavailable, the event is lost (or requires complex retry logic). SQS queues provide the buffering needed for independent, fault-tolerant processing.

Reference: [Subscribing an SQS Queue to an SNS Topic](https://docs.aws.amazon.com/sns/latest/dg/subscribe-sqs-queue-to-sns-topic.html)

</details>

---

## Study Guide

If you scored below 70%, review the following topics organized by module before retaking the exam.

### Module 04: Compute with Amazon EC2

- EC2 instance type naming convention: family, generation, and size (for example, `t3.micro`, `m6i.large`, `c6g.xlarge`)
- Instance families and when to use each: general purpose (T, M), compute optimized (C), memory optimized (R), storage optimized (I), accelerated computing (G, P)
- Amazon Machine Images (AMIs): AWS-provided vs. custom AMIs, and the AMI lifecycle (launch, configure, create, reuse)
- Amazon EBS volume types: gp3 (general purpose SSD), io2 (provisioned IOPS SSD), st1 (throughput optimized HDD), sc1 (cold HDD), and when to use each
- EBS snapshots for backup, AMI creation, and cross-Region replication
- User data scripts for bootstrapping instances at launch
- Auto Scaling groups: minimum, desired, and maximum capacity settings; launch templates; scaling policies (target tracking, step, scheduled)
- EC2 pricing models: On-Demand, Reserved Instances, Savings Plans, and Spot Instances, and when to use each
- Reference: [Amazon EC2 Instance Types](https://docs.aws.amazon.com/ec2/latest/instancetypes/instance-type-names.html)

### Module 05: Storage with Amazon S3

- S3 storage classes: Standard, Intelligent-Tiering, Standard-IA, One Zone-IA, Glacier Instant Retrieval, Glacier Flexible Retrieval, Glacier Deep Archive
- Choosing a storage class based on access frequency, retrieval time requirements, and cost
- S3 Lifecycle policies: transition actions (moving objects between storage classes) and expiration actions (deleting objects)
- S3 Versioning: how it protects against accidental deletion, delete markers, and recovering previous versions
- MFA Delete for additional protection on versioned buckets
- S3 static website hosting: configuration, index documents, error documents, and bucket policies for public access
- S3 encryption options: SSE-S3, SSE-KMS, and SSE-C, and when to use each
- Access control: bucket policies vs. IAM policies, Block Public Access settings
- Reference: [S3 Storage Classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html)

### Module 06: Databases with Amazon RDS and DynamoDB

- Managed vs. self-managed databases: what AWS manages vs. what you manage
- Relational (SQL) vs. NoSQL: data models, query capabilities, scaling approaches, and when to use each
- RDS Multi-AZ deployments: synchronous replication, automatic failover, and when failover occurs
- RDS read replicas: asynchronous replication, read scaling, cross-Region replicas, and the difference between read replicas and Multi-AZ
- RDS automated backups: retention period, point-in-time recovery, and manual snapshots
- DynamoDB primary keys: partition key (simple) vs. composite key (partition key + sort key)
- DynamoDB Global Secondary Indexes (GSIs): alternate query patterns, attribute projections, and cost considerations
- DynamoDB capacity modes: on-demand vs. provisioned, and when to use each
- Database security: VPC placement, encryption at rest and in transit, IAM database authentication
- Reference: [Amazon RDS User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)

### Module 07: Load Balancing and DNS

- Elastic Load Balancing types: ALB (Layer 7, HTTP/HTTPS), NLB (Layer 4, TCP/UDP), GLB (Layer 3, network appliances)
- ALB features: listeners, rules, path-based routing, host-based routing, target groups, and SSL/TLS termination
- Health checks: configurable parameters (path, port, interval, thresholds), unhealthy target behavior, and the difference between ALB health checks and Route 53 health checks
- Route 53 record types: A, AAAA, CNAME, and Alias records, and when to use Alias vs. CNAME
- Route 53 routing policies: simple, weighted, latency-based, failover, geolocation, and multivalue answer
- Route 53 health checks: endpoint health checks, calculated health checks, and CloudWatch alarm health checks
- DNS-based disaster recovery: failover routing with health checks for active-passive architectures
- Reference: [Elastic Load Balancing Overview](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/what-is-load-balancing.html)

### Module 08: Messaging and Integration

- Tight coupling vs. loose coupling: benefits of asynchronous communication and messaging layers
- Amazon SQS: Standard vs. FIFO queues, visibility timeout, message retention, long polling vs. short polling
- Amazon SNS: publish/subscribe model, topics, subscriptions, supported protocols, and message filtering
- Amazon EventBridge: event buses, rules, event patterns, targets, and when to use EventBridge vs. SNS
- SNS + SQS fan-out pattern: how it works, when to use it, and how to configure queue access policies
- Dead-letter queues (DLQs): redrive policy, maximum receive count, monitoring DLQs, and redriving messages
- AWS Step Functions: state machines, state types, Standard vs. Express workflows
- Choosing the right integration service: SQS for point-to-point queuing, SNS for fan-out notifications, EventBridge for content-based event routing
- Reference: [Amazon SQS Developer Guide](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)
