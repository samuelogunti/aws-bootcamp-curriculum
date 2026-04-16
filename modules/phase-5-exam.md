# Phase 5 Exam: Architecting

## Exam Information

| Field | Details |
|-------|---------|
| Phase | Phase 5: Architecting |
| Modules Covered | Module 17 (The AWS Well-Architected Framework), Module 18 (Architecture Patterns on AWS), Module 19 (Advanced Topics) |
| Estimated Duration | 60 to 90 minutes |
| Passing Score | 70% |
| Total Questions | 25 |
| Question Types | Multiple choice (single correct), multiple choice (multiple correct), scenario-based, ordering/sequencing |

> **Tip:** Read each question carefully. For questions that say "select TWO" or "select THREE," you must choose the exact number of answers specified. Partial credit is not awarded.

---

## Questions

**Question 1**

A solutions architect is conducting a Well-Architected review of a production web application. The review identifies the following: the application runs on a single EC2 instance with no Auto Scaling, the RDS database is Single-AZ, there are no CloudWatch alarms configured, and IAM policies use `Action: "*"` on several roles. Which TWO pillars have the most critical gaps? (Select TWO.)

A. Reliability, because the single EC2 instance and Single-AZ database are single points of failure with no automatic recovery.

B. Security, because IAM policies with `Action: "*"` violate the principle of least privilege and grant unrestricted access to all AWS services.

C. Performance Efficiency, because a single EC2 instance cannot handle traffic spikes.

D. Sustainability, because the application does not use Graviton instances.

E. Cost Optimization, because the application does not use Savings Plans.

---

**Question 2**

A company is designing a global e-commerce platform that must serve users in North America, Europe, and Asia with sub-100ms latency for static content. Product images and JavaScript bundles are stored in an S3 bucket in us-east-1. Which service should the company add to reduce latency for global users?

A. Deploy additional S3 buckets in eu-west-1 and ap-southeast-1 with Cross-Region Replication.

B. Add Amazon CloudFront with the S3 bucket as the origin and Origin Access Control (OAC) configured, so content is cached at edge locations worldwide and served from the location closest to each user.

C. Add an Application Load Balancer in each Region to distribute traffic.

D. Enable S3 Transfer Acceleration to speed up downloads.

---

**Question 3**

A startup is building its first product with a team of 4 developers. The product is a task management application with simple CRUD operations and uncertain traffic patterns. The team is debating between a microservices architecture and a monolithic architecture. Which approach should the team choose, and which Well-Architected pillar most strongly supports this recommendation?

A. Microservices, supported by the Performance Efficiency pillar, because microservices scale individual components independently.

B. A monolith (or serverless monolith), supported by the Operational Excellence pillar, because a small team can iterate faster with a single deployable unit, and the operational complexity of microservices is not justified for a simple CRUD application with 4 developers.

C. Microservices, supported by the Reliability pillar, because microservices provide better fault isolation.

D. A monolith, supported by the Cost Optimization pillar, because monoliths are always cheaper than microservices.

---

**Question 4**

A solutions architect is designing a workflow for processing loan applications. The workflow involves: (1) validating the application data, (2) running a credit check (which takes 10 seconds), (3) if the credit score is above 700, auto-approving the loan; if below 700, routing to a human underwriter who may take hours to review, (4) sending a notification to the applicant with the decision. Which AWS service is best suited for orchestrating this workflow?

A. Amazon SQS, because it can queue loan applications for processing.

B. AWS Step Functions, because it supports branching logic (Choice state for credit score threshold), long-running human review steps (Wait for Callback pattern), parallel execution, and built-in error handling.

C. Amazon EventBridge, because it can route events based on content.

D. Direct Lambda-to-Lambda invocation, because it is the simplest approach for multi-step processing.

---

**Question 5**

A company runs all workloads in a single AWS account. A developer accidentally deletes a production RDS database while testing in the development environment. The security team cannot attribute costs to specific projects because all resources share the same billing. Which multi-account change addresses both the isolation and cost attribution concerns?

A. Create separate VPCs for production and development within the same account.

B. Create separate AWS accounts for production and development under AWS Organizations. Apply SCPs to restrict destructive actions in the production account. Use consolidated billing with cost allocation tags for per-project cost attribution.

C. Create separate IAM users for production and development work.

D. Enable AWS Config rules to prevent accidental deletions.

---

**Question 6**

A data engineering team stores 2 TB of application logs in S3 as CSV files. They run daily Athena queries that filter by date and aggregate error counts. Each query scans the entire 2 TB and costs approximately $10. The team wants to reduce query costs by at least 80%. Which TWO optimizations should the team implement? (Select TWO.)

A. Convert the CSV files to Parquet format, which is columnar and allows Athena to read only the columns needed, reducing data scanned by 60% to 90%.

B. Partition the data by date (for example, `year=2026/month=04/day=16/`), so Athena skips partitions that do not match the date filter.

C. Increase the Athena query timeout to allow more processing time.

D. Move the data to DynamoDB and use the Query API instead of Athena.

E. Enable S3 Versioning on the log bucket.

---

**Question 7**

A solutions architect is evaluating trade-offs for a customer-facing API. The team must choose between two architectures:

Option A: API Gateway + Lambda + DynamoDB. Estimated cost: $500/month. Latency: 50ms to 300ms (variable due to cold starts). Scales to zero during idle periods.

Option B: ALB + ECS Fargate + RDS. Estimated cost: $1,800/month. Latency: 20ms to 50ms (consistent). Minimum 2 tasks running at all times.

The API serves a financial trading platform where consistent sub-100ms latency is a hard requirement and the platform operates 24/7. Which option should the architect recommend?

A. Option A, because it is cheaper and the latency is acceptable for most requests.

B. Option B, because the financial trading platform requires consistent sub-100ms latency, which Option A cannot guarantee due to Lambda cold starts. The $1,300/month cost difference is justified by the latency requirement.

C. Option A with provisioned concurrency to eliminate cold starts, making it equivalent to Option B at lower cost.

D. Neither; the architect should propose EC2 instances for the lowest possible latency.

---

**Question 8**

A company wants to protect its S3 bucket so that content is accessible only through CloudFront, not by accessing the S3 URL directly. Which CloudFront feature should the company configure?

A. CloudFront signed URLs, which require users to authenticate before accessing content.

B. Origin Access Control (OAC), which restricts S3 access to requests signed by the CloudFront distribution, keeping the S3 bucket private.

C. AWS WAF on the CloudFront distribution, which blocks direct S3 access.

D. S3 Block Public Access, which prevents all access to the bucket including from CloudFront.

---

**Question 9**

A solutions architect is designing an event-driven order processing system. When a customer places an order, the following must happen independently: the inventory service deducts stock, the payment service charges the customer, and the notification service sends a confirmation email. Each service processes at different speeds, and a failure in one must not block the others. Which architecture pattern should the architect use?

A. The order service calls each downstream service synchronously via HTTP in sequence.

B. The order service publishes an "OrderPlaced" event to an SNS topic that fans out to three separate SQS queues (one per service). Each service polls its own queue independently, with dead-letter queues for failed messages.

C. The order service writes to a shared database table, and each downstream service polls the table for new orders.

D. The order service invokes three Lambda functions in parallel using direct invocation.

---

**Question 10**

A team is migrating a monolithic Java application to microservices on AWS. The application has 6 major features. The team wants to minimize risk and maintain the application's availability throughout the migration. Which migration strategy should the team use?

A. Rewrite the entire application as microservices and deploy all 6 services simultaneously.

B. Use the strangler fig pattern: extract one feature at a time into a microservice, route traffic for that feature to the new service using ALB path-based routing, and keep the monolith running for remaining features until all are migrated.

C. Deploy the monolith on ECS and rename it "microservices."

D. Split the monolith into 6 services simultaneously and deploy them in a single release.

---

**Question 11**

A solutions architect is reviewing a Well-Architected self-assessment for a capstone project. The student claims the architecture is "fully Well-Architected" because it uses Multi-AZ deployment, encryption, and CloudWatch alarms. However, the architecture has no CI/CD pipeline (deployments are manual), no IaC templates (resources were created through the console), and no cost allocation tags. Which pillar has the most significant gap?

A. Security, because the architecture lacks encryption.

B. Reliability, because the architecture lacks Multi-AZ deployment.

C. Operational Excellence, because the architecture lacks automated deployments (CI/CD), infrastructure as code, and the ability to reproduce the environment consistently. Manual deployments and console-created resources violate the "perform operations as code" design principle.

D. Cost Optimization, because the architecture lacks Savings Plans.

---

**Question 12**

A company uses Amazon ElastiCache (Redis) to cache product catalog data for an e-commerce application. The catalog is updated once per day at 2:00 AM. During the day, the application reads the catalog 50,000 times per hour. The team wants to minimize DynamoDB read costs while ensuring users see the updated catalog within 1 hour of the daily update. Which caching strategy is most appropriate?

A. Write-through caching, which updates the cache on every write to DynamoDB.

B. Cache-aside (lazy loading) with a 1-hour TTL. The first request after the TTL expires fetches from DynamoDB and repopulates the cache. All subsequent requests within the TTL are served from Redis with microsecond latency.

C. No caching; increase DynamoDB read capacity to handle 50,000 reads per hour.

D. Write-behind caching, which writes to the cache first and asynchronously updates DynamoDB.

---

**Question 13**

Place the following steps in the correct order for conducting a Well-Architected review using the AWS Well-Architected Tool.

1. Create an improvement plan that prioritizes high-risk issues by business impact and implementation effort.
2. Define the workload in the tool (name, description, environment, Regions).
3. Save a milestone to establish a baseline for tracking progress over time.
4. Answer the pillar questions, selecting which best practices are implemented.

A. 2, 4, 1, 3

B. 4, 2, 3, 1

C. 2, 1, 4, 3

D. 1, 2, 4, 3

---

**Question 14**

A solutions architect is designing a static website with a dynamic API backend for a media company. The website serves 500,000 page views per day (mostly static articles and images) and has a comments API that handles 10,000 requests per day. Which architecture provides the best combination of performance, cost, and scalability?

A. EC2 instances behind an ALB serving both static content and API requests.

B. S3 + CloudFront for static content delivery, with API Gateway + Lambda + DynamoDB for the comments API. CloudFront routes `/api/*` requests to API Gateway and all other requests to S3.

C. ECS Fargate serving both static content and API requests behind an ALB.

D. A single Lambda function that renders HTML pages and handles API requests.

---

**Question 15**

A company currently uses AWS Control Tower to manage a multi-account environment. The security team wants to prevent any account in the "Workloads" OU from launching EC2 instances in Regions outside of us-east-1 and eu-west-1. Which mechanism should the security team use?

A. IAM policies attached to every IAM user in every account in the Workloads OU.

B. A Service Control Policy (SCP) attached to the Workloads OU that denies `ec2:RunInstances` for all Regions except us-east-1 and eu-west-1.

C. AWS Config rules that detect EC2 instances in unauthorized Regions and automatically terminate them.

D. CloudTrail alerts that notify the security team when instances are launched in unauthorized Regions.

---

**Question 16**

A solutions architect is choosing between AWS Step Functions Standard Workflows and Express Workflows for a data transformation pipeline. The pipeline processes 200,000 events per hour, each event requires a single Lambda invocation with no branching or parallel execution, and each event completes in under 30 seconds. Which workflow type is more appropriate, and why?

A. Standard Workflows, because they provide better execution history and auditing.

B. Express Workflows, because they are designed for high-volume, short-duration workloads and charge per execution plus duration rather than per state transition, making them significantly more cost-effective at 200,000 events per hour.

C. Neither; SQS + Lambda is more appropriate for this simple, high-volume pattern because it avoids the overhead of Step Functions entirely.

D. Standard Workflows, because Express Workflows do not support Lambda invocations.

---

**Question 17**

A solutions architect is presenting a capstone architecture to stakeholders. The architecture uses API Gateway + Lambda + DynamoDB for the backend, S3 + CloudFront for the frontend, and CodePipeline for CI/CD. A stakeholder asks: "What happens if the us-east-1 Region goes down?" The architect's current design has no multi-Region capability. How should the architect respond?

A. "The application will be unavailable until the Region recovers. This is acceptable because AWS Regions rarely experience full outages."

B. "The application will be unavailable until the Region recovers. I made a conscious trade-off: the business requirements specify 99.9% availability, which a single-Region multi-AZ deployment achieves. Adding multi-Region would increase cost by approximately 2x and add significant complexity (DynamoDB Global Tables, cross-Region replication, Route 53 failover). If the availability requirement increases to 99.99% or higher, I would recommend adding a warm standby in a second Region."

C. "Lambda and DynamoDB are global services, so the application will continue working in another Region automatically."

D. "I will add multi-Region immediately to address this concern."

---

**Question 18**

A team is designing a data processing pipeline. Raw JSON files (100 MB each, 500 files per day) are uploaded to S3. Each file must be validated, transformed, and stored in Parquet format for Athena queries. Which pipeline architecture is most appropriate for this scale?

A. S3 event notification triggers a Lambda function that validates and transforms each file, then writes the Parquet output to an analytics S3 bucket.

B. Amazon Kinesis Data Streams for real-time ingestion, followed by Kinesis Data Analytics for transformation.

C. Amazon EMR cluster running 24/7 to process files as they arrive.

D. EC2 instances polling the S3 bucket every minute for new files.

---

**Question 19**

A solutions architect is evaluating whether to apply the CQRS pattern to a new application. The application is a simple task management tool with basic CRUD operations. Read and write traffic are roughly equal, and all queries are simple key-value lookups by task ID. Should the architect apply CQRS?

A. Yes, because CQRS is a best practice for all applications.

B. No, because CQRS adds complexity (separate read and write models, eventual consistency, synchronization logic) that is not justified for a simple CRUD application with equal read/write traffic and simple key-value access patterns. A single DynamoDB table handles both reads and writes efficiently.

C. Yes, because CQRS improves performance for all access patterns.

D. No, because CQRS only works with relational databases, not DynamoDB.

---

**Question 20**

A company runs a three-tier web application (ALB + EC2 Auto Scaling + RDS Multi-AZ). The Well-Architected review identifies that the application scores well on Reliability and Security but poorly on Cost Optimization. The EC2 instances are `m5.4xlarge` (16 vCPUs, 64 GB RAM) running at 12% average CPU utilization, all S3 data is in Standard storage class regardless of access frequency, and there are no commitment-based pricing plans. Which THREE changes would improve the Cost Optimization pillar? (Select THREE.)

A. Right-size the EC2 instances from `m5.4xlarge` to `m5.large` based on actual utilization.

B. Apply S3 lifecycle policies to transition infrequently accessed data to S3 Standard-IA or Glacier.

C. Purchase Compute Savings Plans for the right-sized baseline compute usage.

D. Disable RDS Multi-AZ to reduce database costs.

E. Remove the ALB and have users connect directly to EC2 instance IP addresses.

F. Increase the EC2 instance size to `m5.8xlarge` for better performance headroom.

---

**Question 21**

A solutions architect needs to choose between Amazon ElastiCache Redis and Memcached for caching session data in a web application. The application requires: session persistence (sessions must survive a cache node failure), pub/sub messaging for real-time notifications, and multi-AZ replication for high availability. Which option should the architect choose?

A. Memcached, because it provides multi-threaded performance for session caching.

B. Redis, because it supports persistence (snapshots and AOF), pub/sub messaging, and multi-AZ replication with automatic failover. Memcached does not support any of these features.

C. Either Redis or Memcached, because both support persistence and replication.

D. Neither; use DynamoDB for session storage instead of a cache.

---

**Question 22**

A solutions architect is designing an architecture for a SaaS application. The architect wants to evaluate the application against both the general Well-Architected Framework and SaaS-specific best practices (tenant isolation, metering, onboarding). How should the architect configure the Well-Architected review?

A. Run two separate reviews: one with the general Framework lens and one with the SaaS Lens.

B. Apply both the general Framework lens and the SaaS Lens to the same workload in the Well-Architected Tool, covering both general and SaaS-specific best practices in a single assessment.

C. Use only the SaaS Lens, because it replaces the general Framework for SaaS workloads.

D. Skip the Well-Architected Tool and use AWS Config rules for compliance checking instead.

---

**Question 23**

A company is designing a notification system for an e-commerce platform. When an order is placed, the system must: send a confirmation email to the customer, update the inventory database, and trigger a fraud detection workflow. The fraud detection workflow involves multiple steps with branching logic (flag for review if the order amount exceeds $1,000). Which combination of services should the architect use?

A. SNS topic for fan-out to email (SES subscription), SQS queue for inventory updates (Lambda consumer), and Step Functions for the fraud detection workflow (Choice state for amount threshold).

B. A single Lambda function that handles all three tasks sequentially.

C. Three separate EventBridge rules, each invoking a different Lambda function.

D. SQS FIFO queue with three consumer groups.

---

**Question 24**

A solutions architect is writing an Architecture Decision Record (ADR) for choosing DynamoDB over RDS for a session storage use case. Which section of the ADR is most important for future team members who question the decision?

A. The Status section, which records whether the decision is accepted or superseded.

B. The Alternatives Considered section, which documents why RDS was evaluated and rejected (for example, connection management complexity with Lambda, higher cost for simple key-value access, unnecessary relational features for session data).

C. The Context section, which describes the business requirements.

D. The Consequences section, which lists the trade-offs of the decision.

---

**Question 25**

A solutions architect is designing the final architecture for a capstone project. The application is a URL shortener that must handle 1,000 redirects per second at peak, store URL mappings with automatic expiration, serve a web dashboard for analytics, and be deployed entirely through IaC with a CI/CD pipeline. The architect must justify every service choice. Which architecture best meets all requirements while following Well-Architected best practices?

A. EC2 instances behind an ALB for the redirect service, RDS for URL storage, and a separate EC2 instance for the analytics dashboard.

B. API Gateway + Lambda for the redirect API (scales automatically to handle 1,000 req/s), DynamoDB with TTL for URL mappings (automatic expiration, single-digit millisecond reads), S3 + CloudFront for the analytics dashboard (static frontend), CloudWatch for redirect metrics, all defined in SAM templates and deployed through CodePipeline.

C. A single ECS Fargate task handling redirects, storage, and the dashboard, with an RDS database for URL mappings.

D. CloudFront Functions for redirects (edge-based, sub-millisecond latency), with DynamoDB for URL storage and S3 for the dashboard.

---

<details>
<summary>Answer Key</summary>

### Question 1

**Correct Answers: A, B**

Reliability (A) has critical gaps: a single EC2 instance is a single point of failure, and a Single-AZ RDS database cannot survive an AZ failure. Both violate the Reliability pillar's design principle of automatically recovering from failure. Security (B) has a critical gap: IAM policies with `Action: "*"` grant unrestricted access to all AWS services, violating the principle of least privilege. A compromised credential with these permissions could access, modify, or delete any resource in the account.

- C is incorrect because Performance Efficiency is a concern (no Auto Scaling) but is less critical than the reliability and security gaps. The application may perform adequately on a single instance for current traffic.
- D and E are lower-priority concerns compared to the reliability and security risks.

Reference: [Reliability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html), [Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html)

---

### Question 2

**Correct Answer: B**

CloudFront caches content at over 400 edge locations worldwide. When configured with an S3 origin and OAC, CloudFront serves cached content from the edge location closest to each user (Tokyo for Asian users, Frankfurt for European users) while keeping the S3 bucket private. This reduces latency from hundreds of milliseconds (cross-ocean round trip to us-east-1) to single-digit milliseconds for cached content.

- A is incorrect because deploying S3 buckets in multiple Regions with CRR adds complexity and cost without the edge caching benefit that CloudFront provides. Users still connect to a specific Region, not the nearest edge location.
- C is incorrect because ALBs operate within a single Region and do not cache static content.
- D is incorrect because S3 Transfer Acceleration speeds up uploads to S3, not downloads from S3.

Reference: [Amazon CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)

---

### Question 3

**Correct Answer: B**

The Operational Excellence pillar emphasizes performing operations as code, making frequent small changes, and refining procedures. A small team (4 developers) with a simple CRUD application benefits from the operational simplicity of a monolith: single deployment unit, single codebase to debug, no inter-service communication complexity. Microservices add operational overhead (service discovery, distributed tracing, independent deployments, network latency between services) that is not justified for this use case.

- A is incorrect because while microservices can scale independently, the Performance Efficiency benefit does not outweigh the operational complexity for a simple application with uncertain traffic.
- C is incorrect because fault isolation is a Reliability benefit of microservices, but a 4-person team building a simple CRUD app does not need this level of isolation.
- D is incorrect because monoliths are not always cheaper; the recommendation is based on operational simplicity, not cost alone.

Reference: [Operational Excellence Pillar](https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/welcome.html)

---

### Question 4

**Correct Answer: B**

Step Functions supports all the requirements: Choice states for branching based on credit score, the Wait for Callback pattern for long-running human review (the workflow pauses until the underwriter sends a callback token), Lambda tasks for each processing step, and built-in Retry/Catch for error handling. The workflow can run for up to 1 year (Standard Workflow), accommodating the potentially hours-long human review.

- A is incorrect because SQS queues messages but does not provide branching logic or human approval orchestration.
- C is incorrect because EventBridge routes events but does not orchestrate multi-step workflows with branching and waiting.
- D is incorrect because direct Lambda invocation cannot handle the hours-long human review step (Lambda timeout is 15 minutes).

Reference: [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html)

---

### Question 5

**Correct Answer: B**

Separate AWS accounts provide the strongest isolation. A developer in the development account cannot accidentally delete production resources because they are in a different account with separate IAM boundaries. SCPs on the production OU can deny destructive actions (for example, deny `rds:DeleteDBInstance`). Consolidated billing with cost allocation tags provides per-project cost attribution across accounts.

- A is incorrect because separate VPCs provide network isolation but not IAM or billing isolation. A developer with IAM permissions can still access production resources in the same account.
- C is incorrect because separate IAM users share the same account boundary. A misconfigured policy could still grant cross-environment access.
- D is incorrect because Config rules detect issues after they occur but do not prevent accidental deletions.

Reference: [AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html)

---

### Question 6

**Correct Answers: A, B**

Converting to Parquet (A) reduces data scanned by 60% to 90% because Athena reads only the columns referenced in the query instead of entire rows. Partitioning by date (B) allows Athena to skip partitions that do not match the date filter. If the query filters for a single day out of 365, Athena scans approximately 0.3% of the data instead of 100%. Together, these optimizations can reduce costs by 95% or more (from $10 to under $0.50 per query).

- C is incorrect because increasing timeout does not reduce data scanned or cost.
- D is incorrect because DynamoDB is not designed for SQL analytics queries.
- E is incorrect because versioning does not affect query performance or cost.

Reference: [Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/what-is.html)

---

### Question 7

**Correct Answer: B**

The financial trading platform has a hard requirement for consistent sub-100ms latency. Option A (Lambda) has variable latency of 50ms to 300ms due to cold starts, which violates the requirement. Option B (ECS Fargate) provides consistent 20ms to 50ms latency because containers are always running (no cold starts). The $1,300/month cost difference is justified by the latency requirement.

- A is incorrect because the variable latency (up to 300ms) violates the sub-100ms hard requirement.
- C is a reasonable consideration, but provisioned concurrency adds cost that narrows the gap with Option B, and Lambda still has per-invocation overhead that may not match the consistency of always-running containers for a 24/7 trading platform.
- D is incorrect because EC2 instances add operational overhead (patching, scaling) without providing better latency than Fargate for this use case.

Reference: [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)

---

### Question 8

**Correct Answer: B**

Origin Access Control (OAC) configures CloudFront to sign requests to S3 using a service principal. The S3 bucket policy allows access only from the CloudFront distribution's OAC, and Block Public Access remains enabled. Users can access content only through the CloudFront URL; direct S3 URL access is denied.

- A is incorrect because signed URLs control user-level access to specific content (time-limited, authenticated), not origin-level access restriction.
- C is incorrect because WAF inspects HTTP requests for threats (SQL injection, XSS) but does not restrict S3 origin access.
- D is incorrect because Block Public Access alone would block CloudFront as well (unless OAC is configured to use IAM-based access).

Reference: [CloudFront OAC](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html)

---

### Question 9

**Correct Answer: B**

SNS fan-out to SQS queues provides independent, decoupled processing for each service. The order service publishes once to the SNS topic. Each downstream service has its own SQS queue subscribed to the topic, processes messages at its own pace, and handles failures independently (with dead-letter queues). A failure in the payment service does not block inventory or notification processing.

- A is incorrect because synchronous HTTP calls create tight coupling. If the payment service is slow or down, the order service blocks.
- C is incorrect because polling a shared database creates tight coupling, is inefficient, and does not provide failure isolation.
- D is incorrect because direct Lambda invocation means the order service must know about and call each downstream service. Adding a new consumer requires changing the order service code.

Reference: [SQS, SNS, or EventBridge Decision Guide](https://docs.aws.amazon.com/decision-guides/latest/sns-or-sqs-or-eventbridge/sns-or-sqs-or-eventbridge.html)

---

### Question 10

**Correct Answer: B**

The strangler fig pattern extracts features incrementally while the monolith continues running. ALB path-based routing directs traffic for extracted features to new microservices and everything else to the monolith. This minimizes risk: you can stop at any point, roll back a single extraction, and validate each microservice in production before extracting the next.

- A is incorrect because a full rewrite is high-risk and time-consuming. The application is unavailable or unstable during the transition.
- C is incorrect because deploying a monolith on ECS does not decompose it into microservices.
- D is incorrect because splitting all features simultaneously is as risky as a full rewrite.

Reference: [Strangler Fig Pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/strangler-fig.html)

---

### Question 11

**Correct Answer: C**

Operational Excellence is the most significant gap. The pillar's core design principle is "perform operations as code." Without IaC, the environment cannot be reproduced consistently. Without CI/CD, deployments are manual and error-prone. These gaps undermine the ability to evolve, recover, and maintain the application reliably. The student has addressed some aspects of other pillars (Multi-AZ for Reliability, encryption for Security, alarms for monitoring) but has neglected the foundational operational practices.

- A is incorrect because the architecture does have encryption (the question states it).
- B is incorrect because the architecture does have Multi-AZ deployment.
- D is incorrect because while cost allocation tags are missing, the lack of IaC and CI/CD is a more fundamental gap.

Reference: [Operational Excellence Pillar](https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/welcome.html)

---

### Question 12

**Correct Answer: B**

Cache-aside with a 1-hour TTL matches the access pattern perfectly. The catalog updates once daily, and a 1-hour TTL ensures users see the updated catalog within 1 hour. During the hour, all 50,000 reads are served from Redis (microsecond latency) instead of DynamoDB, eliminating virtually all DynamoDB read costs. Write-through (A) is unnecessary because the catalog updates only once per day; the overhead of updating the cache on every write is not justified. No caching (C) means paying for 50,000 DynamoDB reads per hour. Write-behind (D) optimizes writes, not reads.

Reference: [Caching Patterns](https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/caching-patterns.html)

---

### Question 13

**Correct Answer: A**

The correct order is: (2) Define the workload in the tool, (4) Answer the pillar questions, (1) Create an improvement plan based on the identified HRIs and MRIs, (3) Save a milestone to establish the baseline. You must define the workload before answering questions, answer questions before creating an improvement plan (the plan is based on the findings), and save the milestone after the review is complete.

Reference: [AWS Well-Architected Tool](https://docs.aws.amazon.com/wellarchitected/latest/userguide/intro.html)

---

### Question 14

**Correct Answer: B**

S3 + CloudFront serves the 500,000 daily page views of static content from edge caches at minimal cost and maximum performance. The comments API (10,000 requests/day) is a low-volume workload perfectly suited for the serverless pattern (API Gateway + Lambda + DynamoDB), which scales to zero during idle periods. CloudFront's path-based routing sends `/api/*` to API Gateway and everything else to S3.

- A is incorrect because EC2 instances serving static content is more expensive and slower than S3 + CloudFront.
- C is incorrect because ECS Fargate for static content adds unnecessary compute cost.
- D is incorrect because rendering HTML in Lambda for every page view incurs per-invocation costs for content that does not change.

Reference: [Serverless Multi-Tier Architectures](https://docs.aws.amazon.com/whitepapers/latest/serverless-multi-tier-architectures-api-gateway-lambda/sample-architecture-patterns.html)

---

### Question 15

**Correct Answer: B**

SCPs attached to an OU set the maximum permissions boundary for all accounts in that OU. An SCP that denies `ec2:RunInstances` for all Regions except us-east-1 and eu-west-1 prevents any account in the Workloads OU from launching instances in unauthorized Regions, regardless of the IAM policies within those accounts.

- A is incorrect because attaching IAM policies to every user in every account is operationally impractical and does not cover service roles or future users.
- C is incorrect because Config rules detect non-compliance after the fact. The instances would already be running before Config detects and terminates them.
- D is incorrect because CloudTrail alerts notify after the fact but do not prevent the action.

Reference: [Service Control Policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html)

---

### Question 16

**Correct Answer: C**

For a simple, high-volume pattern (200,000 events/hour, single Lambda invocation, no branching), SQS + Lambda is the most appropriate choice. It provides buffering, retry, and automatic scaling without the overhead of Step Functions. Step Functions (Standard or Express) add orchestration capabilities that are unnecessary for this pattern and increase cost.

- A is incorrect because Standard Workflows charge per state transition, which is expensive at 200,000 events/hour.
- B is a reasonable option for high-volume processing, but the question specifies no branching or parallel execution, making SQS + Lambda simpler and cheaper.
- D is incorrect because Express Workflows do support Lambda invocations, but the statement about SQS + Lambda being more appropriate for this simple pattern is correct.

Reference: [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html)

---

### Question 17

**Correct Answer: B**

This response demonstrates architectural maturity: acknowledging the limitation, explaining the trade-off reasoning (cost and complexity vs. availability), referencing the specific availability requirement that the current design meets, and proposing a path forward if requirements change. This is how a Well-Architected review conversation should work.

- A is incorrect because dismissing the concern without explaining the trade-off reasoning is unprofessional and does not demonstrate architectural thinking.
- C is incorrect because Lambda and DynamoDB are Regional services, not global. They do not automatically fail over to another Region.
- D is incorrect because adding multi-Region without evaluating the cost and complexity trade-off against the actual availability requirement is not a well-reasoned response.

Reference: [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)

---

### Question 18

**Correct Answer: A**

For 500 files per day at 100 MB each (50 GB daily), Lambda is well within its limits. S3 event notifications trigger Lambda automatically on each upload. Lambda can validate and transform the JSON to Parquet using libraries like `pyarrow`. This is the simplest and most cost-effective architecture for this scale.

- B is incorrect because Kinesis is designed for real-time streaming, which is unnecessary for batch file processing.
- C is incorrect because EMR running 24/7 is over-provisioned for 50 GB of daily batch data.
- D is incorrect because EC2 polling adds unnecessary infrastructure management.

Reference: [Using Lambda with S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html)

---

### Question 19

**Correct Answer: B**

CQRS adds complexity (separate read and write models, eventual consistency between them, synchronization logic) that is not justified for a simple CRUD application with equal read/write traffic and simple key-value access patterns. A single DynamoDB table handles both reads and writes efficiently for this use case. CQRS is appropriate when read and write patterns differ significantly or when different query patterns require different data models.

- A is incorrect because CQRS is not a universal best practice; it is a pattern for specific situations.
- C is incorrect because CQRS does not improve performance for simple key-value access patterns.
- D is incorrect because CQRS works with any database, including DynamoDB.

Reference: [Cloud Design Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/introduction.html)

---

### Question 20

**Correct Answers: A, B, C**

Right-sizing (A) addresses the most significant waste: instances using 12% of 16 vCPUs can be downsized to `m5.large` (2 vCPUs), reducing compute costs by approximately 87%. S3 lifecycle policies (B) transition infrequently accessed data to cheaper storage classes, reducing storage costs. Savings Plans (C) lock in discounts (up to 72%) for the right-sized baseline compute usage.

- D is incorrect because disabling Multi-AZ degrades the Reliability pillar. Cost optimization should not come at the expense of availability for a production workload.
- E is incorrect because removing the ALB eliminates load balancing, health checks, and SSL termination, degrading both Reliability and Security.
- F is incorrect because increasing instance size increases cost, the opposite of optimization.

Reference: [Cost Optimization Pillar](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html)

---

### Question 21

**Correct Answer: B**

Redis supports all three requirements: persistence (RDB snapshots and AOF for data durability), pub/sub messaging (for real-time notifications), and multi-AZ replication with automatic failover (for high availability). Memcached does not support persistence, pub/sub, or replication.

- A is incorrect because Memcached does not support persistence, pub/sub, or multi-AZ replication.
- C is incorrect because only Redis supports these features; Memcached does not.
- D is incorrect because while DynamoDB is a valid session store, the question specifically asks about ElastiCache options, and Redis meets all stated requirements.

Reference: [Amazon ElastiCache](https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/WhatIs.html)

---

### Question 22

**Correct Answer: B**

The Well-Architected Tool supports applying multiple lenses to a single workload. The general Framework lens covers the six pillars, and the SaaS Lens adds questions specific to multi-tenant architecture. Applying both in a single review provides comprehensive coverage without duplicating effort.

- A is incorrect because running two separate reviews creates unnecessary overhead and makes it harder to see the complete picture.
- C is incorrect because the SaaS Lens supplements the general Framework; it does not replace it.
- D is incorrect because Config rules evaluate resource configurations, not architectural best practices.

Reference: [Well-Architected Lenses](https://docs.aws.amazon.com/wellarchitected/latest/userguide/lenses.html)

---

### Question 23

**Correct Answer: A**

This combination uses the right service for each task: SNS for fan-out (email via SES subscription, SQS queue for inventory), SQS for buffered, independent processing (inventory Lambda consumer), and Step Functions for the complex fraud detection workflow (Choice state for amount threshold, branching to human review or auto-approval). Each service is used for its primary strength.

- B is incorrect because a single Lambda function handling all three tasks has no failure isolation and cannot handle the branching fraud detection logic elegantly.
- C is incorrect because EventBridge rules can invoke Lambda functions, but this approach does not provide the buffering (SQS) for inventory updates or the workflow orchestration (Step Functions) for fraud detection.
- D is incorrect because SQS FIFO does not support consumer groups in the way described.

Reference: [SQS, SNS, or EventBridge Decision Guide](https://docs.aws.amazon.com/decision-guides/latest/sns-or-sqs-or-eventbridge/sns-or-sqs-or-eventbridge.html)

---

### Question 24

**Correct Answer: B**

The Alternatives Considered section is most valuable for future team members who question the decision. It documents why RDS was evaluated and rejected, providing the reasoning that prevents the team from re-evaluating the same options. Without this section, a new team member might propose switching to RDS without understanding why DynamoDB was chosen, wasting time on analysis that was already done.

- A is incorrect because the Status section is important for tracking but does not explain the reasoning.
- C is incorrect because the Context section describes the requirements but not the evaluation of alternatives.
- D is incorrect because the Consequences section lists trade-offs but does not explain why alternatives were rejected.

Reference: [Cloud Design Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/introduction.html)

---

### Question 25

**Correct Answer: B**

This architecture uses the serverless API pattern (API Gateway + Lambda + DynamoDB) for the redirect service, which scales automatically to handle 1,000 requests per second without provisioning servers. DynamoDB with TTL provides automatic URL expiration. S3 + CloudFront serves the analytics dashboard as a static website. CloudWatch tracks redirect metrics. SAM templates define all resources as code, and CodePipeline automates deployment. Every service choice is justified by the requirements.

- A is incorrect because EC2 instances require manual scaling, patching, and capacity planning. RDS is over-engineered for simple key-value URL mappings.
- C is incorrect because a single ECS task cannot handle 1,000 requests per second without scaling configuration, and RDS adds unnecessary complexity for key-value lookups.
- D is incorrect because CloudFront Functions have limited execution time (1ms) and cannot perform DynamoDB lookups for URL resolution. They are designed for simple request/response transformations, not application logic.

Reference: [Serverless Multi-Tier Architectures](https://docs.aws.amazon.com/whitepapers/latest/serverless-multi-tier-architectures-api-gateway-lambda/sample-architecture-patterns.html)

---

</details>

---

## Study Guide

If you scored below 70%, review the following topics organized by module:

### Module 17: The AWS Well-Architected Framework
- The six pillars: Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, Sustainability
- Design principles for each pillar and the key AWS services that support them
- Trade-offs between pillars: cost vs. reliability, security vs. agility, performance vs. cost, reliability vs. cost
- The AWS Well-Architected Tool: defining workloads, answering pillar questions, identifying HRIs and MRIs, creating improvement plans, saving milestones
- Well-Architected Lenses: Serverless, SaaS, Data Analytics, and when to apply them
- The Framework as a conversation tool, not a compliance checklist
- Reference: [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)

### Module 18: Architecture Patterns on AWS
- Monolith vs. microservices: trade-offs, when to use each, team size and domain complexity as decision factors
- Three-tier web architecture: ALB + compute (EC2/ECS/Lambda) + database (RDS/DynamoDB), service selection criteria
- Serverless API pattern: API Gateway + Lambda + DynamoDB, optimizations (HTTP APIs, DAX, provisioned concurrency)
- Event-driven architecture: EventBridge for content-based routing, SQS for buffering, SNS for fan-out, Step Functions for orchestration
- Static website + dynamic API: S3 + CloudFront + API Gateway, OAC for secure S3 access
- Data processing pipeline: S3 + Lambda for small scale, Glue for medium, Kinesis for real-time
- CQRS: when to apply (different read/write patterns) and when to avoid (simple CRUD)
- Strangler fig pattern: incremental migration from monolith to microservices using ALB path-based routing
- Reference: [Cloud Design Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/introduction.html)

### Module 19: Advanced Topics
- Multi-account strategy: AWS Organizations, OUs, SCPs, Control Tower landing zones
- CloudFront: edge caching, origins, cache behaviors, OAC for S3, invalidation
- ElastiCache: Redis vs. Memcached, caching strategies (cache-aside, write-through, read-through, write-behind)
- Step Functions: Standard vs. Express Workflows, when to use Step Functions vs. SQS + Lambda
- Athena: schema-on-read, Parquet/ORC for cost optimization, partitioning, Glue Data Catalog
- Architecture Decision Records (ADRs): context, decision, alternatives considered, consequences
- Reference: [AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html)
