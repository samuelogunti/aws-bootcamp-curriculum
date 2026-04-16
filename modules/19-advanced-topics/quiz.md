# Module 19: Quiz

1. What is the primary purpose of AWS Organizations, and how do Service Control Policies (SCPs) differ from IAM policies?

2. A company serves a global user base from a single AWS Region (us-east-1). Users in Asia and Europe report slow page load times for the company's static website hosted on S3. Which service should the company add to reduce latency for global users, and how does it work?

   A) Add an Application Load Balancer in each Region to distribute traffic globally.
   B) Add Amazon CloudFront, which caches static content at edge locations worldwide and serves it from the location closest to each user, reducing latency from hundreds of milliseconds to single-digit milliseconds for cached content.
   C) Add Amazon ElastiCache in each Region to cache the website content.
   D) Enable S3 Transfer Acceleration to speed up downloads from S3.

3. True or False: Amazon Athena requires you to load data into a database before you can query it with SQL.

4. A development team builds a Lambda function that reads a product catalog from DynamoDB. The catalog changes once per day, but the function is invoked 10,000 times per hour. Each invocation reads the same catalog data. The team wants to reduce DynamoDB read costs and improve response latency. Which caching strategy should the team implement?

   A) Write-through caching with ElastiCache, because it ensures the cache always has the latest data.
   B) Cache-aside (lazy loading) with ElastiCache Redis, because the catalog data changes infrequently and can tolerate a short period of staleness. The first request after a cache miss populates the cache, and subsequent requests are served from memory with microsecond latency.
   C) Write-behind caching, because it reduces write latency to DynamoDB.
   D) No caching; increase the DynamoDB read capacity units to handle the load.

5. Which AWS service automates the setup of a multi-account landing zone with organizational units, centralized logging, guardrails, and a compliance dashboard?

   A) AWS Organizations
   B) AWS Control Tower
   C) AWS CloudFormation StackSets
   D) AWS Config

6. A solutions architect is designing a workflow for processing insurance claims. The workflow involves: (1) validating the claim, (2) checking for fraud (which takes 30 seconds), (3) if fraud is detected, routing to a human reviewer who may take hours to respond, (4) if no fraud, automatically approving the claim and notifying the customer. Which AWS service is best suited for orchestrating this workflow, and why?

   A) Amazon SQS, because it can queue claims for processing.
   B) AWS Step Functions, because it supports branching logic (Choice state for fraud/no-fraud), long-running human approval steps (Wait for Callback pattern), parallel execution, and built-in error handling with retries.
   C) Direct Lambda-to-Lambda invocation, because it is the simplest approach.
   D) Amazon EventBridge, because it can route events based on content.

7. What is Origin Access Control (OAC) in Amazon CloudFront, and why should you use it when serving content from an S3 bucket?

8. A data engineering team stores 500 GB of application logs in S3 as CSV files. They use Amazon Athena to run daily queries that filter by date and aggregate error counts. Each query scans the entire 500 GB dataset and costs approximately $2.50 per query. Which TWO optimizations would reduce the query cost most significantly? (Select TWO.)

   A) Convert the CSV files to Parquet format, which is columnar and allows Athena to read only the columns needed for the query, reducing data scanned by 60% to 90%.
   B) Partition the data by date (for example, `s3://bucket/logs/year=2026/month=04/day=15/`), so Athena skips partitions that do not match the date filter in the query.
   C) Increase the Athena query timeout to allow more time for processing.
   D) Move the data from S3 to DynamoDB and query it with the DynamoDB API instead.
   E) Enable S3 Versioning on the log bucket.

9. A company currently runs all workloads in a single AWS account. The security team is concerned that a compromised IAM credential in the development environment could affect production resources. The finance team cannot attribute costs to specific projects. Which multi-account change addresses both concerns?

   A) Create separate IAM users for development and production within the same account.
   B) Create separate AWS accounts for development and production under AWS Organizations. Use SCPs to restrict development account actions, and use cost allocation tags with separate billing for each account.
   C) Create separate VPCs for development and production within the same account.
   D) Enable AWS Config rules to detect unauthorized access in the single account.

10. A solutions architect is evaluating whether to use AWS Step Functions (Standard Workflow) or an SQS queue with Lambda for processing 100,000 short-lived events per hour. Each event requires a single Lambda function invocation with no branching, no parallel execution, and no human approval. Which approach is more appropriate, and why?

    A) Step Functions, because it provides better visibility into each execution.
    B) SQS + Lambda, because the processing is simple (single function, no branching), high-volume (100,000 events/hour), and does not require the orchestration features of Step Functions. SQS + Lambda is simpler and more cost-effective for this pattern.
    C) Step Functions Express Workflow, because Express Workflows are designed for high-volume processing.
    D) Neither; use EventBridge to invoke Lambda directly for each event.

---

<details>
<summary>Answer Key</summary>

1. **AWS Organizations lets you create and manage multiple AWS accounts under a single management account, providing centralized billing, account grouping into organizational units (OUs), and policy-based governance. SCPs differ from IAM policies in that SCPs set the maximum permissions boundary for an entire account or OU, while IAM policies grant specific permissions to individual users, roles, or groups within an account.** An SCP cannot grant permissions; it can only restrict them. Even if an IAM policy grants `AdministratorAccess`, an SCP can deny specific actions for the entire account.
   Further reading: [AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html)

2. **B) Amazon CloudFront**
   CloudFront is a CDN that caches content at over 400 edge locations worldwide. When a user in Asia requests a page, CloudFront serves it from the nearest edge location (for example, Tokyo) instead of fetching it from the S3 bucket in us-east-1. This reduces latency from hundreds of milliseconds (cross-Pacific round trip) to single-digit milliseconds (local edge cache). An ALB (A) operates within a single Region and does not provide global caching. ElastiCache (C) is an in-memory cache for application data, not a CDN for static content. S3 Transfer Acceleration (D) speeds up uploads to S3, not downloads.
   Further reading: [Amazon CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)

3. **False.**
   Athena uses a schema-on-read approach. Your data stays in S3 in its original format. You define a table schema in the AWS Glue Data Catalog that describes the column names, data types, and S3 location. When you run a query, Athena reads the data from S3, applies the schema, and returns results. No data loading, ETL, or database provisioning is required.
   Further reading: [Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/what-is.html)

4. **B) Cache-aside (lazy loading) with ElastiCache Redis**
   The catalog changes once per day but is read 10,000 times per hour. Cache-aside is ideal: the first request after a cache miss reads from DynamoDB and populates the cache. The remaining 9,999 requests in that hour are served from ElastiCache with microsecond latency, eliminating 99.99% of DynamoDB reads. A short TTL (for example, 1 hour) ensures the cache refreshes after the daily update. Write-through (A) is unnecessary because the catalog is read-heavy, not write-heavy. Write-behind (C) optimizes writes, not reads. Increasing DynamoDB capacity (D) is more expensive than caching.
   Further reading: [Caching Patterns](https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/caching-patterns.html)

5. **B) AWS Control Tower**
   Control Tower automates the setup of a multi-account landing zone following AWS best practices. It creates the organizational structure (OUs), configures centralized logging (CloudTrail, Config), sets up guardrails (preventive SCPs and detective Config rules), provisions new accounts through Account Factory, and provides a compliance dashboard. Organizations (A) provides the underlying multi-account structure but does not automate the landing zone setup. StackSets (C) deploy CloudFormation templates across accounts but do not provide the governance framework. Config (D) evaluates resource compliance but does not set up multi-account environments.
   Further reading: [AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/what-is-control-tower.html)

6. **B) AWS Step Functions**
   The insurance claim workflow requires branching logic (fraud detected vs. not detected), a long-running human approval step (hours), and different processing paths based on the fraud check result. Step Functions supports all of these natively: Choice states for branching, the Wait for Callback pattern for human approval (the workflow pauses until the reviewer sends a callback token), and built-in Retry/Catch for error handling. SQS (A) can queue claims but does not provide branching or human approval orchestration. Direct Lambda invocation (C) cannot handle the hours-long human approval step (Lambda timeout is 15 minutes). EventBridge (D) routes events but does not orchestrate multi-step workflows with branching and waiting.
   Further reading: [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html)

7. **Origin Access Control (OAC) is a CloudFront feature that restricts access to an S3 origin so that users can only access the content through CloudFront, not by accessing the S3 bucket URL directly.** When OAC is configured, CloudFront signs requests to S3 using a service principal. The S3 bucket policy allows access only from the CloudFront distribution's OAC, and Block Public Access remains enabled on the bucket. This ensures that all traffic goes through CloudFront (benefiting from caching, HTTPS, and edge security) and prevents users from bypassing CloudFront to access S3 directly.
   Further reading: [Restrict Access to an S3 Origin](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html)

8. **A, B**
   Converting to Parquet (A) is the highest-impact optimization. Parquet is a columnar format, so Athena reads only the columns referenced in the query instead of the entire row. For a query that selects 3 columns out of 20, this reduces data scanned by approximately 85%. Partitioning by date (B) allows Athena to skip entire directories of data that do not match the date filter. If the query filters for a single day, Athena scans only that day's partition (approximately 1.4 GB) instead of the full 500 GB. Together, these optimizations can reduce data scanned (and cost) by 95% or more. Increasing timeout (C) does not reduce cost. Moving to DynamoDB (D) is a major architectural change that does not support SQL analytics. Versioning (E) does not affect query performance.
   Further reading: [Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/what-is.html)

9. **B) Separate AWS accounts under Organizations**
   Separate accounts provide the strongest isolation. A compromised credential in the development account cannot access production resources because they are in a different account with separate IAM boundaries. SCPs on the development OU can restrict actions (for example, deny creating production-grade resources). Separate accounts also provide automatic cost separation in the consolidated billing view, addressing the finance team's concern. Separate IAM users (A) share the same account boundary, so a compromised credential could still access production resources if the IAM policy is misconfigured. Separate VPCs (C) provide network isolation but not IAM or billing isolation. Config rules (D) detect issues but do not prevent cross-environment access.
   Further reading: [Multi-Account Strategy](https://docs.aws.amazon.com/controltower/latest/userguide/aws-multi-account-landing-zone.html)

10. **B) SQS + Lambda**
    For simple, high-volume event processing with no branching, parallel execution, or human approval, SQS + Lambda is the appropriate choice. SQS handles buffering and retry, Lambda processes each message, and the architecture scales automatically. Step Functions Standard Workflows (A) charge per state transition, which would be expensive at 100,000 events per hour. Step Functions Express Workflows (C) are designed for high-volume processing but add unnecessary complexity for a single-function, no-branching pattern. EventBridge (D) can invoke Lambda but does not provide the buffering and retry capabilities of SQS for high-volume processing.
    Further reading: [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html)

</details>
