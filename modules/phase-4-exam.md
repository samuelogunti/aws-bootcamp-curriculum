# Phase 4 Exam: Production Readiness

## Exam Information

| Field | Details |
|-------|---------|
| Phase | Phase 4: Production Readiness |
| Modules Covered | Module 13 (Security in Depth), Module 14 (Monitoring and Observability), Module 15 (Cost Optimization), Module 16 (Reliability and Disaster Recovery) |
| Estimated Duration | 60 to 90 minutes |
| Passing Score | 70% |
| Total Questions | 25 |
| Question Types | Multiple choice (single correct), multiple choice (multiple correct), scenario-based, ordering/sequencing |

> **Tip:** Read each question carefully. For questions that say "select TWO" or "select THREE," you must choose the exact number of answers specified. Partial credit is not awarded.

---

## Questions

**Question 1**

A security audit reveals that several S3 buckets in a production account do not have default encryption enabled. The security team wants to detect this misconfiguration automatically and receive notifications when any S3 bucket is created without encryption. Which AWS service should the team use to continuously evaluate S3 bucket configurations against an encryption requirement?

A. Amazon GuardDuty, because it detects security threats across AWS services.

B. AWS Config with the `s3-bucket-server-side-encryption-enabled` managed rule, because Config continuously evaluates resource configurations against defined rules and reports non-compliant resources.

C. AWS CloudTrail, because it records all S3 API calls including bucket creation.

D. AWS WAF, because it inspects requests to S3 buckets and can block unencrypted uploads.

---

**Question 2**

A company runs a microservices application with API Gateway, Lambda, DynamoDB, and SQS. Users report intermittent slow responses on one API endpoint, but CloudWatch metrics show that overall Lambda error rates and DynamoDB latency are normal. The team needs to identify which specific service in the request chain is causing the delay for the affected endpoint. Which approach is most effective?

A. Review CloudWatch Logs for each Lambda function and manually correlate timestamps to find the slow component.

B. Enable AWS X-Ray tracing on API Gateway and Lambda, then analyze the service map and individual traces to identify the specific subsegment (DynamoDB call, SQS publish, or Lambda code) causing the latency.

C. Create CloudWatch alarms on every metric for every service and wait for an alarm to trigger.

D. Increase the Lambda function memory for all functions to eliminate potential CPU bottlenecks.

---

**Question 3**

A company stores 100 TB of log data in S3 Standard. The data is accessed daily for the first 7 days, occasionally for the next 90 days, and must be retained for 7 years for compliance but is almost never accessed after 90 days. The company wants to minimize storage costs. Which S3 lifecycle configuration is most cost-effective?

A. Keep all data in S3 Standard for 7 years.

B. Transition to S3 Standard-IA after 7 days, transition to S3 Glacier Flexible Retrieval after 90 days, and expire after 7 years.

C. Transition to S3 Glacier Deep Archive after 7 days and expire after 7 years.

D. Transition to S3 One Zone-IA after 7 days, transition to S3 Glacier Deep Archive after 90 days, and expire after 7 years.

---

**Question 4**

A solutions architect is designing a disaster recovery strategy for a financial trading application. The application must have near-zero downtime and near-zero data loss if an entire AWS Region becomes unavailable. Which DR strategy should the architect recommend?

A. Backup and restore, because it is the most cost-effective strategy.

B. Pilot light, because it keeps core infrastructure running in the recovery Region.

C. Warm standby, because it maintains a scaled-down environment that can be scaled up quickly.

D. Multi-site active-active, because it runs the application simultaneously in multiple Regions with near-synchronous data replication, providing near-zero RTO and RPO.

---

**Question 5**

A development team receives 60 CloudWatch alarm notifications per day. Most are for brief CPU spikes on EC2 instances that resolve within 2 minutes. The team has started ignoring all notifications, including legitimate alerts. Which TWO changes should the team make to improve their alerting strategy? (Select TWO.)

A. Replace static CPU threshold alarms with anomaly detection alarms that learn normal patterns and alert only on true deviations.

B. Create composite alarms that require both high CPU AND high error rate before triggering, so brief CPU spikes alone do not generate notifications.

C. Increase the CPU alarm threshold from 80% to 99% to reduce the number of alerts.

D. Send all alerts to a Slack channel instead of email for faster visibility.

E. Disable all CPU alarms and rely on manual dashboard checks during business hours.

---

**Question 6**

A company uses AWS KMS to encrypt data in S3 and RDS. A compliance requirement mandates that the company must control its own encryption keys, rotate them annually, and be able to audit every use of the keys. Which KMS key type meets all three requirements?

A. AWS owned keys, because AWS manages them with no customer overhead.

B. AWS managed keys, because they rotate automatically every year.

C. Customer managed keys, because they provide full control over key policies, configurable rotation schedules, and CloudTrail logging of all key usage.

D. S3-managed keys (SSE-S3), because S3 handles encryption transparently.

---

**Question 7**

A team is evaluating whether to use AWS Secrets Manager or AWS Systems Manager Parameter Store for storing database credentials. The credentials must be rotated automatically every 30 days, and the rotation must update both the secret value and the database password without application downtime. Which service should the team choose, and why?

A. Parameter Store with a SecureString parameter, because it supports automatic rotation for all parameter types.

B. Secrets Manager, because it provides built-in automatic rotation for supported databases (RDS, Redshift, DocumentDB) that updates both the secret and the database credential seamlessly.

C. Parameter Store, because it is free and supports cross-account sharing.

D. Secrets Manager, because it is the only service that can store credentials (Parameter Store cannot store sensitive data).

---

**Question 8**

A company runs 20 EC2 instances of type `m5.2xlarge` (8 vCPUs, 32 GB RAM) for a production workload. AWS Compute Optimizer reports that all instances average 15% CPU utilization and 10 GB memory usage. The company also has no commitment-based pricing in place. Which TWO actions should the company take to optimize costs? (Select TWO.)

A. Right-size the instances to `m5.large` (2 vCPUs, 8 GB RAM) based on actual utilization, reducing compute costs by approximately 75%.

B. Purchase EC2 Instance Savings Plans for the right-sized instances to lock in additional discounts for the predictable baseline usage.

C. Switch all instances to Spot Instances to save up to 90%.

D. Keep the current instance type but purchase Reserved Instances to reduce the hourly cost.

E. Terminate all 20 instances and migrate the workload to Lambda.

---

**Question 9**

A solutions architect is reviewing a web application's monitoring setup. The application runs on ECS Fargate behind an ALB. The current dashboard shows only ECS CPU utilization and memory utilization. The architect wants the dashboard to follow the four golden signals framework. Which metrics should the architect add to the dashboard to cover all four signals?

A. Add ECS task count and ALB rule count.

B. Add ALB `TargetResponseTime` (latency), ALB `RequestCount` (traffic), and ALB `HTTPCode_Target_5XX_Count` (errors). CPU and memory utilization already cover saturation.

C. Add CloudTrail event count and Config compliance percentage.

D. Add Lambda invocation count and DynamoDB read capacity units.

---

**Question 10**

A company's RDS PostgreSQL database runs in a Single-AZ deployment. The database stores order data that the business cannot afford to lose. The operations team wants to protect against AZ-level failures with automatic failover and also needs the ability to restore the database to any point within the last 7 days. Which TWO configurations should the team implement? (Select TWO.)

A. Enable RDS Multi-AZ deployment, which maintains a synchronous standby replica in a different AZ with automatic failover.

B. Enable RDS automated backups with a 7-day retention period, which provides point-in-time recovery to any second within the retention window.

C. Create a read replica in the same AZ for redundancy.

D. Take manual snapshots once per week and store them in S3.

E. Enable DynamoDB Global Tables for cross-Region replication.

---

**Question 11**

A Lambda function processes payment transactions. The function calls a third-party payment API that occasionally experiences outages lasting 5 to 10 minutes. During these outages, the Lambda function retries the API call repeatedly, consuming concurrency and causing other Lambda functions in the account to be throttled. Which resilience pattern should the team implement to prevent this cascading failure?

A. Increase the Lambda function's reserved concurrency to handle more retries.

B. Implement a circuit breaker pattern that stops calling the payment API after repeated failures, returning a graceful error to the caller and allowing other functions to use the available concurrency.

C. Increase the Lambda function timeout to 15 minutes so retries have more time to succeed.

D. Remove all retry logic and fail immediately on the first error.

---

**Question 12**

A security team wants a single dashboard that aggregates findings from GuardDuty, Inspector, Config, and Macie across multiple AWS accounts. The dashboard should normalize findings into a standard format and run automated compliance checks against the CIS AWS Foundations Benchmark. Which service provides this capability?

A. Amazon CloudWatch Dashboards, because they can display metrics from multiple accounts.

B. AWS CloudTrail Lake, because it aggregates API call data across accounts.

C. AWS Security Hub, because it aggregates findings from multiple security services, normalizes them into the AWS Security Finding Format (ASFF), and runs automated compliance checks against industry standards.

D. Amazon Detective, because it investigates security findings across accounts.

---

**Question 13**

A company runs a web application behind an ALB. The application has been targeted by SQL injection attacks and HTTP flood attacks (thousands of requests per second from distributed IP addresses). Which TWO AWS services should the company use together to protect against both types of attacks? (Select TWO.)

A. AWS WAF with the SQL injection managed rule group to inspect HTTP request content and block SQL injection patterns.

B. AWS Shield Standard (automatic, free) combined with AWS WAF rate-based rules to detect and block IP addresses that exceed a request threshold, mitigating the HTTP flood.

C. Security groups on the ALB to block SQL injection patterns.

D. AWS CloudTrail to log all HTTP requests and manually identify attackers.

E. Amazon GuardDuty to block malicious HTTP requests in real time.

---

**Question 14**

A team is configuring CloudWatch Logs for a production Lambda function. The function generates 20 GB of log data per month, most of which is DEBUG-level output. The team wants to reduce logging costs while retaining the ability to troubleshoot production issues. Which TWO changes should the team make? (Select TWO.)

A. Set the Lambda function's log level to WARN or ERROR in production, eliminating verbose DEBUG output.

B. Set the CloudWatch Logs retention period to 30 days instead of "Never expire," and archive older logs to S3 if long-term retention is needed.

C. Disable CloudWatch Logs entirely for the function.

D. Increase the Lambda function memory to reduce execution time and therefore reduce log volume.

E. Switch from JSON structured logging to plain text logging to reduce log entry size.

---

**Question 15**

Place the following steps in the correct order for responding to a GuardDuty finding that indicates an EC2 instance is communicating with a known cryptocurrency mining pool.

1. Isolate the compromised instance by modifying its security group to deny all inbound and outbound traffic.
2. Review the GuardDuty finding details to identify the affected instance, the malicious IP address, and the time of first communication.
3. Investigate the root cause by examining CloudTrail logs for unauthorized access and checking the instance for malware.
4. Remediate by terminating the compromised instance and launching a clean replacement from a known-good AMI.

A. 2, 1, 3, 4

B. 1, 2, 4, 3

C. 3, 2, 1, 4

D. 2, 3, 1, 4

---

**Question 16**

A startup wants to implement cost monitoring for their AWS account. They have a monthly budget of $2,000 and want to be alerted at 50%, 80%, and 100% of the budget. They also want to automatically prevent new resource creation if spending exceeds 90% of the budget. Which AWS Budgets configuration meets these requirements?

A. Create a cost budget of $2,000 with email alerts at 50%, 80%, and 100%. Create a separate budget action at 90% that applies a restrictive IAM policy denying resource creation.

B. Create a cost budget of $2,000 with email alerts at 50%, 80%, and 100%. No automated action is possible with AWS Budgets.

C. Create a usage budget that tracks EC2 hours and set alerts based on instance count.

D. Use AWS Cost Explorer forecasting to predict when spending will exceed $2,000 and manually stop resources.

---

**Question 17**

A solutions architect is comparing the four disaster recovery strategies for a SaaS application. The application has an RTO of 4 hours and an RPO of 1 hour. The company wants to minimize DR costs while meeting these objectives. Which strategy is the most cost-effective choice that meets the requirements?

A. Multi-site active-active, because it provides the best RTO and RPO.

B. Warm standby, because it provides recovery within minutes.

C. Pilot light, because it maintains core infrastructure (database replicas) in the recovery Region at low cost, and compute resources can be provisioned within the 4-hour RTO window. Continuous database replication meets the 1-hour RPO.

D. Backup and restore, because daily backups to another Region are the cheapest option.

---

**Question 18**

A company enables AWS CloudTrail in their production account. After one month, the security team wants to investigate whether any IAM user has called the `DeleteBucket` API in the past 30 days. Which approach allows the team to query CloudTrail events efficiently?

A. Download all CloudTrail log files from S3, decompress them, and search through the JSON files manually.

B. Use CloudTrail Lake to run a SQL query filtering for `eventName = 'DeleteBucket'` across the event data store.

C. Use CloudWatch Metrics to filter for `DeleteBucket` events.

D. Use AWS Config to check whether any S3 buckets have been deleted.

---

**Question 19**

A company runs an Auto Scaling group of EC2 instances behind an ALB in two Availability Zones. The team wants to validate that the architecture recovers correctly when an entire AZ becomes unavailable. They want to test this in a controlled manner during a maintenance window. Which approach should the team use?

A. Manually terminate all instances in one AZ and observe the Auto Scaling group's behavior.

B. Use AWS Fault Injection Service (FIS) to create an experiment that stops all instances in one AZ, then monitor the ALB health checks, Auto Scaling replacement activity, and application availability during the experiment.

C. Wait for a real AZ failure to occur and observe the recovery.

D. Disable the ALB health checks temporarily to simulate an AZ failure.

---

**Question 20**

A solutions architect is designing a monitoring and alerting strategy for a production web application. The application runs on ECS Fargate behind an ALB. The architect wants to create alarms that minimize false positives while catching real incidents. Which alarm configuration follows best practices?

A. Create a static threshold alarm that triggers when ALB 5XX error count exceeds 0 for 1 evaluation period of 1 minute.

B. Create a composite alarm that triggers only when BOTH the ALB 5XX error rate exceeds 5% for 3 consecutive 5-minute periods AND the ALB TargetResponseTime p99 exceeds 2 seconds for 3 consecutive 5-minute periods.

C. Create alarms on every available CloudWatch metric for every service to ensure complete coverage.

D. Create a single alarm on ECS CPU utilization that triggers when CPU exceeds 50%.

---

**Question 21**

A company stores database credentials in AWS Secrets Manager. A developer accidentally commits the secret ARN and a hardcoded copy of the password to a Git repository. The security team discovers the exposure 2 hours later. Which THREE actions should the security team take immediately? (Select THREE.)

A. Rotate the secret in Secrets Manager immediately to generate a new password, invalidating the exposed credential.

B. Review CloudTrail logs for any `GetSecretValue` API calls using the exposed credential during the 2-hour window to determine if the secret was accessed by unauthorized parties.

C. Delete the Git commit containing the secret and force-push to remove it from the repository history.

D. Disable Secrets Manager entirely until the investigation is complete.

E. Do nothing, because the secret ARN alone does not grant access to the secret value without proper IAM permissions.

F. Review and tighten the IAM policies and resource-based policies on the secret to ensure only authorized roles can access it.

---

**Question 22**

A company runs a production workload on 10 EC2 instances that run 24/7 with predictable, steady utilization. The company also runs a data analytics workload on Lambda that processes files uploaded to S3, with highly variable invocation patterns. The company wants to reduce costs using commitment-based pricing. Which combination of pricing models is most appropriate?

A. Purchase EC2 Instance Savings Plans for the EC2 workload and use on-demand pricing for Lambda (Lambda is already included in Compute Savings Plans if purchased).

B. Purchase Compute Savings Plans that cover both the EC2 instances and the Lambda invocations, providing flexibility across both compute services.

C. Purchase Reserved Instances for both EC2 and Lambda.

D. Use Spot Instances for EC2 and on-demand pricing for Lambda.

---

**Question 23**

A team enables Amazon GuardDuty in their AWS account. After one week, GuardDuty generates a High-severity finding: `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.OutsideAWS`. The finding indicates that EC2 instance credentials are being used from an IP address outside of AWS. What does this finding mean, and what should the team do?

A. The finding is a false positive because EC2 instance credentials are always used from within AWS. Suppress the finding.

B. The finding indicates that the temporary credentials assigned to the EC2 instance's IAM role have been stolen and are being used from outside AWS. The team should immediately revoke the role's active sessions, investigate how the credentials were exfiltrated (check for SSRF vulnerabilities, exposed metadata endpoints), and rotate any resources the role had access to.

C. The finding indicates that the EC2 instance is running in the wrong Region. Move the instance to the correct Region.

D. The finding indicates that the EC2 instance's security group allows outbound traffic to the internet. Restrict the security group to block all outbound traffic.

---

**Question 24**

A solutions architect is reviewing the cost of a production environment. The environment includes 5 unattached EBS volumes (500 GB total), 3 unused Elastic IP addresses, an idle NAT Gateway processing no traffic, and CloudWatch Logs with no retention policy (accumulating 50 GB per month indefinitely). Which optimization provides the largest immediate cost reduction?

A. Delete the 5 unattached EBS volumes.

B. Release the 3 unused Elastic IP addresses.

C. Delete the idle NAT Gateway.

D. Set a 30-day retention policy on CloudWatch Logs.

---

**Question 25**

A company is designing a highly available architecture for a customer-facing API. The API must remain available if a single AZ fails, must recover from a Region-level disaster within 30 minutes, and must lose no more than 5 minutes of data. The company wants to balance cost and reliability. Which architecture meets all requirements?

A. Deploy the API in a single AZ with daily backups to another Region. Restore from backup if the Region fails.

B. Deploy the API across two AZs with an ALB, use RDS Multi-AZ for the database, and implement a warm standby in a second Region with continuous database replication and Route 53 failover routing with health checks.

C. Deploy the API in a single AZ with an Auto Scaling group. Use RDS Single-AZ with manual snapshots every 6 hours.

D. Deploy the API across three Regions in an active-active configuration with DynamoDB Global Tables.

---

<details>
<summary>Answer Key</summary>

### Question 1

**Correct Answer: B**

AWS Config with the `s3-bucket-server-side-encryption-enabled` managed rule continuously evaluates S3 bucket configurations. When a bucket is created or modified without default encryption, Config marks it as non-compliant and can send an SNS notification. Config can also trigger automatic remediation to enable encryption on non-compliant buckets.

- A is incorrect because GuardDuty detects active threats (compromised credentials, malware, data exfiltration), not configuration compliance issues like missing encryption.
- C is incorrect because CloudTrail records API calls (including `CreateBucket`) but does not evaluate whether the bucket configuration meets a compliance rule. You would need to parse CloudTrail logs manually to check for encryption settings.
- D is incorrect because WAF inspects HTTP requests to protected web resources (ALB, API Gateway, CloudFront), not S3 bucket configurations.

Reference: [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html)

---

### Question 2

**Correct Answer: B**

AWS X-Ray traces individual requests across all services in the chain (API Gateway, Lambda, DynamoDB, SQS). The service map shows average latency at each node, and individual traces show the exact time spent in each subsegment for a specific request. This makes it straightforward to identify which service is causing intermittent latency on a specific endpoint.

- A is incorrect because manually correlating timestamps across multiple CloudWatch Logs log groups is time-consuming and error-prone, especially for intermittent issues that affect only some requests.
- C is incorrect because creating alarms on every metric is reactive and generates noise. It does not help trace a specific slow request through the service chain.
- D is incorrect because increasing memory for all functions is a guess that may not address the actual bottleneck (which could be in DynamoDB, SQS, or the Lambda code itself, not CPU).

Reference: [AWS X-Ray](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html)

---

### Question 3

**Correct Answer: B**

This lifecycle configuration matches the access pattern: S3 Standard for the first 7 days (daily access), S3 Standard-IA from 7 to 90 days (occasional access at lower storage cost), and S3 Glacier Flexible Retrieval from 90 days to 7 years (rare access at the lowest practical cost for data that must be retained). This provides the best balance of cost and accessibility.

- A is incorrect because keeping 100 TB in S3 Standard for 7 years is the most expensive option. S3 Standard has the highest per-GB storage cost.
- C is incorrect because transitioning to Glacier Deep Archive after only 7 days means the data accessed occasionally between 7 and 90 days would require 12 to 48 hour retrieval times, which is impractical for occasional access.
- D is incorrect because S3 One Zone-IA stores data in a single Availability Zone, reducing durability. For compliance data that must be retained for 7 years, the reduced durability is an unacceptable risk.

Reference: [S3 Storage Classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html)

---

### Question 4

**Correct Answer: D**

Multi-site active-active runs the application simultaneously in multiple Regions, each handling production traffic. If one Region fails, the remaining Regions absorb the traffic with near-zero downtime (RTO). Data is replicated near-synchronously across Regions, providing near-zero data loss (RPO). This is the only strategy that meets the "near-zero" requirements for both RTO and RPO.

- A is incorrect because backup and restore has an RTO of hours and an RPO of hours, far from near-zero.
- B is incorrect because pilot light has an RTO of minutes to hours (compute must be provisioned) and an RPO of minutes.
- C is incorrect because warm standby has an RTO of minutes (must scale up) and an RPO of seconds to minutes. While close, it does not achieve "near-zero" for both metrics.

Reference: [Disaster Recovery Options](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html)

---

### Question 5

**Correct Answers: A, B**

Anomaly detection alarms (A) learn normal CPU patterns (including expected spikes during batch jobs or peak hours) and alert only when CPU deviates from the learned baseline. This eliminates false positives from expected brief spikes. Composite alarms (B) require multiple conditions to be true simultaneously (high CPU AND high error rate), ensuring that a CPU spike alone does not trigger a notification unless it is actually causing user-facing problems.

- C is incorrect because raising the threshold to 99% means the alarm would almost never trigger, potentially missing real issues where sustained 90% CPU causes performance degradation.
- D is incorrect because changing the notification channel does not reduce the number of false positive alerts. The team would still receive 60 notifications per day, just in a different channel.
- E is incorrect because disabling all CPU alarms eliminates monitoring for genuine CPU saturation issues.

Reference: [CloudWatch Anomaly Detection](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Anomaly_Detection.html), [Composite Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/alarm-combining.html)

---

### Question 6

**Correct Answer: C**

Customer managed keys provide full control over key policies (who can use the key), configurable rotation schedules (automatic annual rotation or custom), and CloudTrail logging of every API call that uses the key (`Encrypt`, `Decrypt`, `GenerateDataKey`). This meets all three compliance requirements.

- A is incorrect because AWS owned keys are managed entirely by AWS. Customers have no visibility into or control over these keys.
- B is incorrect because AWS managed keys rotate automatically but do not allow custom key policies. You cannot control who uses the key beyond the service that created it, and you cannot share them across accounts.
- D is incorrect because SSE-S3 uses keys managed entirely by S3. You have no control over key policies, rotation, or audit logging.

Reference: [AWS KMS Key Types](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html)

---

### Question 7

**Correct Answer: B**

Secrets Manager provides built-in automatic rotation for supported databases (RDS, Redshift, DocumentDB). When rotation is configured, Secrets Manager uses a Lambda function to generate a new password, update the database credential, and update the secret value. The rotation process uses staging labels (AWSCURRENT, AWSPENDING) to ensure the application always has a valid credential during the rotation.

- A is incorrect because Parameter Store does not provide built-in automatic rotation. You must implement rotation logic yourself using a Lambda function and a CloudWatch Events schedule.
- C is incorrect because while Parameter Store is free for standard parameters, it does not support automatic rotation, which is a stated requirement.
- D is incorrect because Parameter Store can store sensitive data using SecureString parameters (encrypted with KMS). The distinguishing factor is rotation capability, not the ability to store secrets.

Reference: [Secrets Manager Rotation](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html)

---

### Question 8

**Correct Answers: A, B**

Right-sizing (A) addresses the immediate waste: instances using 15% CPU and 10 GB of 32 GB memory are significantly over-provisioned. Downsizing from `m5.2xlarge` to `m5.large` matches actual utilization and reduces compute costs by approximately 75%. After right-sizing, purchasing Savings Plans (B) for the optimized instances locks in additional discounts (up to 72%) for the predictable baseline usage.

- C is incorrect because Spot Instances can be interrupted with 2 minutes notice. A production workload running 20 instances cannot tolerate sudden interruptions without a complex architecture to handle Spot terminations.
- D is incorrect because purchasing Reserved Instances for the current over-provisioned instance type locks in waste. Right-size first, then commit.
- E is incorrect because migrating a 20-instance workload to Lambda is a major architectural change that may not be feasible depending on the workload characteristics (long-running processes, stateful connections, etc.).

Reference: [AWS Compute Optimizer](https://docs.aws.amazon.com/compute-optimizer/latest/ug/what-is-compute-optimizer.html), [Savings Plans](https://docs.aws.amazon.com/savingsplans/latest/userguide/what-is-savings-plans.html)

---

### Question 9

**Correct Answer: B**

The four golden signals are latency, traffic, errors, and saturation. The current dashboard covers only saturation (CPU and memory utilization). Adding ALB `TargetResponseTime` covers latency, ALB `RequestCount` covers traffic, and ALB `HTTPCode_Target_5XX_Count` covers errors. Together with the existing CPU/memory metrics, all four signals are represented.

- A is incorrect because task count and rule count are configuration metrics, not operational health signals.
- C is incorrect because CloudTrail events and Config compliance are security and governance metrics, not the four golden signals.
- D is incorrect because Lambda and DynamoDB metrics are relevant for those specific services but do not cover the four golden signals for the ECS/ALB application being monitored.

Reference: [CloudWatch Dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)

---

### Question 10

**Correct Answers: A, B**

RDS Multi-AZ (A) maintains a synchronous standby replica in a different AZ. If the primary AZ fails, RDS automatically promotes the standby within 1 to 2 minutes. Automated backups with 7-day retention (B) provide point-in-time recovery to any second within the retention window, meeting the requirement to restore to any point in the last 7 days.

- C is incorrect because a read replica in the same AZ does not protect against AZ-level failures. Read replicas use asynchronous replication and do not provide automatic failover.
- D is incorrect because weekly manual snapshots provide a maximum RPO of 7 days (you could lose up to a week of data). Automated backups with continuous transaction log archiving provide a much finer RPO.
- E is incorrect because DynamoDB Global Tables are for DynamoDB, not RDS PostgreSQL.

Reference: [RDS Multi-AZ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html), [RDS Automated Backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)

---

### Question 11

**Correct Answer: B**

A circuit breaker monitors the failure rate of calls to the payment API. When failures exceed a threshold, the circuit opens and subsequent calls fail immediately without attempting the API call. This prevents the Lambda function from consuming concurrency on retries to a service that is clearly down, freeing concurrency for other functions. After a timeout period, the circuit allows test calls to check if the API has recovered.

- A is incorrect because increasing reserved concurrency allows more retries, which puts more load on the already-failing payment API and does not solve the cascading failure to other functions.
- C is incorrect because increasing the timeout allows each retry to wait longer, consuming concurrency for a longer period and worsening the problem.
- D is incorrect because removing all retry logic means transient failures (which are common and recoverable) are not handled. Retries are appropriate for transient failures; the circuit breaker handles sustained failures.

Reference: [Reliability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html)

---

### Question 12

**Correct Answer: C**

AWS Security Hub aggregates findings from GuardDuty, Inspector, Config, Macie, and third-party tools into a single dashboard. It normalizes findings into the AWS Security Finding Format (ASFF) and runs automated compliance checks against standards including CIS AWS Foundations Benchmark, AWS Foundational Security Best Practices, PCI DSS, and NIST 800-53.

- A is incorrect because CloudWatch Dashboards display metrics and alarms, not security findings from multiple services.
- B is incorrect because CloudTrail Lake stores and queries API call events, not aggregated security findings from multiple services.
- D is incorrect because Amazon Detective helps investigate individual security findings by analyzing related data, but it does not aggregate findings from multiple services or run compliance checks.

Reference: [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub-v2.html)

---

### Question 13

**Correct Answers: A, B**

AWS WAF with the SQL injection managed rule group (A) inspects HTTP request content (headers, body, query strings) and blocks requests that match SQL injection patterns. This protects against application-layer injection attacks. Shield Standard combined with WAF rate-based rules (B) provides two layers of protection against the HTTP flood: Shield Standard automatically mitigates network-layer DDoS attacks, and WAF rate-based rules block individual IP addresses that exceed a request threshold, mitigating the distributed HTTP flood.

- C is incorrect because security groups operate at the network layer (Layer 3/4) and filter traffic based on IP addresses and ports. They cannot inspect HTTP request content or detect SQL injection patterns.
- D is incorrect because CloudTrail logs API calls to AWS services, not HTTP requests to your application. It is an audit tool, not a real-time protection mechanism.
- E is incorrect because GuardDuty detects threats by analyzing CloudTrail, VPC Flow Logs, and DNS logs. It does not inspect or block HTTP requests in real time.

Reference: [AWS WAF](https://docs.aws.amazon.com/waf/latest/developerguide/what-is-aws-waf.html), [AWS Shield](https://docs.aws.amazon.com/waf/latest/developerguide/ddos-overview.html)

---

### Question 14

**Correct Answers: A, B**

Setting the log level to WARN or ERROR (A) eliminates the majority of log volume (DEBUG output) while retaining the logs needed for troubleshooting production issues. Setting a 30-day retention period (B) automatically deletes old logs, reducing storage costs. For compliance or long-term analysis, the team can archive logs to S3 (which is significantly cheaper than CloudWatch Logs storage).

- C is incorrect because disabling logs entirely eliminates the ability to troubleshoot production issues.
- D is incorrect because increasing memory may slightly reduce execution time but does not reduce the number of log statements generated per invocation.
- E is incorrect because plain text logging is harder to query and parse than JSON structured logging. The cost difference in log entry size is negligible compared to the volume reduction from changing the log level.

Reference: [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html)

---

### Question 15

**Correct Answer: A**

The correct order is: (2) Review the finding details to understand the scope (which instance, which IP, when it started), (1) Isolate the instance immediately to stop the malicious communication, (3) Investigate the root cause (how were the credentials compromised, what else was accessed), (4) Remediate by terminating the compromised instance and launching a clean replacement.

- B is incorrect because isolating before reviewing the finding means you do not know which instance to isolate or what you are dealing with.
- C is incorrect because investigating before isolating allows the malicious activity to continue during the investigation.
- D is incorrect because investigating before isolating leaves the compromised instance communicating with the mining pool during the investigation.

Reference: [Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)

---

### Question 16

**Correct Answer: A**

AWS Budgets supports both notification alerts (email/SNS at configurable thresholds) and budget actions (automated IAM policy application when thresholds are crossed). Creating a cost budget with alerts at 50%, 80%, and 100% provides visibility, and a budget action at 90% that applies a restrictive IAM policy provides automated enforcement to prevent new resource creation.

- B is incorrect because AWS Budgets does support automated actions (budget actions), not just notifications.
- C is incorrect because a usage budget tracking EC2 hours does not cover spending across all services.
- D is incorrect because Cost Explorer forecasting is informational and does not provide automated alerts or enforcement.

Reference: [AWS Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html), [Budget Actions](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-action-configure.html)

---

### Question 17

**Correct Answer: C**

Pilot light maintains core infrastructure (database replicas, AMIs, network configuration) in the recovery Region at low cost. When a disaster occurs, compute resources are provisioned from the pre-configured AMIs and templates. With a 4-hour RTO, there is ample time to provision compute resources (typically 15 to 30 minutes). Continuous database replication provides an RPO well within the 1-hour requirement.

- A is incorrect because multi-site active-active provides near-zero RTO/RPO, which far exceeds the requirements. The additional cost of running full production infrastructure in multiple Regions is not justified by a 4-hour RTO.
- B is incorrect because warm standby provides recovery within minutes, which exceeds the 4-hour RTO requirement. The ongoing cost of running a scaled-down environment is not justified when pilot light meets the requirements at lower cost.
- D is incorrect because backup and restore with daily backups has an RPO of up to 24 hours, which exceeds the 1-hour RPO requirement.

Reference: [Disaster Recovery Options](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html)

---

### Question 18

**Correct Answer: B**

CloudTrail Lake provides a managed data lake for CloudTrail events with SQL query capability. The team can run a query like `SELECT * FROM event_data_store WHERE eventName = 'DeleteBucket' AND eventTime > '2026-03-16'` to find all `DeleteBucket` calls in the past 30 days, including the IAM principal, source IP, and timestamp.

- A is incorrect because manually downloading, decompressing, and searching JSON log files is time-consuming and error-prone, especially across 30 days of data.
- C is incorrect because CloudWatch Metrics tracks numerical measurements (CPU, request count), not individual API call events.
- D is incorrect because Config tracks resource configuration state (whether a bucket exists and its settings), not the specific API calls that changed the configuration.

Reference: [CloudTrail Lake](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-lake.html)

---

### Question 19

**Correct Answer: B**

AWS Fault Injection Service (FIS) is a managed chaos engineering service designed for controlled failure injection. An FIS experiment can stop all instances in a specific AZ, allowing the team to observe Auto Scaling replacement behavior, ALB health check responses, and application availability in a controlled, repeatable manner. FIS experiments include safety controls (stop conditions) that halt the experiment if unexpected impact occurs.

- A is incorrect because manually terminating instances achieves a similar result but lacks the safety controls, repeatability, and observability that FIS provides. FIS experiments are documented, auditable, and can be stopped automatically if conditions deteriorate.
- C is incorrect because waiting for a real AZ failure is not a controlled test. Real failures happen at unpredictable times and may occur when the team is not prepared to observe and learn from the event.
- D is incorrect because disabling ALB health checks does not simulate an AZ failure. It would cause the ALB to send traffic to unhealthy instances, which is the opposite of what happens during a real AZ failure.

Reference: [AWS Fault Injection Service](https://docs.aws.amazon.com/fis/latest/userguide/what-is.html)

---

### Question 20

**Correct Answer: B**

A composite alarm requiring both high error rate AND high latency for 3 consecutive periods minimizes false positives while catching real incidents. Brief error spikes (a single 5XX response) or momentary latency increases do not trigger the alarm. Only sustained, correlated degradation across both metrics triggers a notification, which strongly indicates a real user-facing problem.

- A is incorrect because triggering on any single 5XX error in a 1-minute period generates excessive false positives. Occasional 5XX errors are normal in production (client disconnects, timeout retries).
- C is incorrect because creating alarms on every metric generates massive alert noise and alert fatigue, which is the opposite of the goal.
- D is incorrect because a single CPU alarm does not cover the four golden signals and does not directly indicate user-facing impact. High CPU during a batch job is expected; it should not trigger an alert.

Reference: [Composite Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/alarm-combining.html)

---

### Question 21

**Correct Answers: A, B, C**

Rotating the secret immediately (A) generates a new password and invalidates the exposed credential, preventing unauthorized access even if someone obtained the password from the Git repository. Reviewing CloudTrail logs (B) determines whether the secret was accessed during the 2-hour exposure window, which informs the scope of the incident response. Removing the commit from Git history (C) prevents future exposure of the credential from the repository.

- D is incorrect because disabling Secrets Manager would break all applications that depend on it, causing a production outage.
- E is incorrect because while the secret ARN alone does not grant access, the developer also committed a hardcoded copy of the password. The password itself is exposed, not just the ARN.
- F is a good long-term action but is not an immediate priority. The immediate actions are to rotate the credential (invalidate the exposed value), check for unauthorized access, and remove the exposure from Git.

Reference: [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)

---

### Question 22

**Correct Answer: B**

Compute Savings Plans apply across EC2, Fargate, and Lambda regardless of instance family, size, or Region. Since the company uses both EC2 (predictable) and Lambda (variable), a Compute Savings Plan covers the predictable baseline of both services with a single commitment. The variable portion of Lambda usage above the commitment is charged at on-demand rates.

- A is incorrect because EC2 Instance Savings Plans cover only EC2 (locked to a specific instance family and Region). Lambda usage would not be covered. A separate commitment would be needed for Lambda.
- C is incorrect because Reserved Instances are not available for Lambda. RIs cover EC2, RDS, ElastiCache, OpenSearch, and Redshift.
- D is incorrect because Spot Instances can be interrupted and are unsuitable for a production workload running 24/7.

Reference: [Savings Plans](https://docs.aws.amazon.com/savingsplans/latest/userguide/what-is-savings-plans.html)

---

### Question 23

**Correct Answer: B**

The `InstanceCredentialExfiltration.OutsideAWS` finding means that temporary credentials from an EC2 instance's IAM role are being used from an IP address outside of AWS. This indicates credential theft, typically through a Server-Side Request Forgery (SSRF) attack against the instance metadata service or through malware on the instance. The team should revoke active sessions (by updating the IAM role's trust policy or using STS to revoke sessions), investigate the exfiltration method, and rotate any resources the role had access to.

- A is incorrect because this is not a false positive. EC2 instance role credentials should only be used from within AWS (from the instance itself). Usage from an external IP is a strong indicator of credential theft.
- C is incorrect because the finding is about credential usage from outside AWS, not about the instance being in the wrong Region.
- D is incorrect because restricting outbound traffic does not address the fact that stolen credentials are being used externally. The credentials have already been exfiltrated.

Reference: [Amazon GuardDuty Findings](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)

---

### Question 24

**Correct Answer: D**

CloudWatch Logs accumulating 50 GB per month indefinitely is the largest ongoing cost. At approximately $0.03/GB-month for storage, after 12 months the accumulated 600 GB costs approximately $18/month in storage alone, and the ingestion cost is approximately $25/month (50 GB at $0.50/GB). Setting a 30-day retention policy immediately stops the accumulation and deletes old data, providing the largest ongoing cost reduction. The idle NAT Gateway (C) is also significant (approximately $32/month for the hourly charge alone), but the question asks about the largest immediate reduction, and the Logs retention change addresses both the accumulated storage and the ongoing growth.

- A is incorrect because 500 GB of gp3 EBS volumes costs approximately $40/month, which is significant but less than the ongoing Logs accumulation.
- B is incorrect because 3 unused Elastic IPs cost approximately $11/month ($3.65 each), which is the smallest cost item.
- C is a close second. The idle NAT Gateway costs approximately $32/month plus data processing charges. However, the CloudWatch Logs issue is both larger in accumulated cost and growing monthly.

Reference: [CloudWatch Logs Pricing](https://aws.amazon.com/cloudwatch/pricing/), [EBS Pricing](https://aws.amazon.com/ebs/pricing/)

---

### Question 25

**Correct Answer: B**

This architecture meets all three requirements. Multi-AZ deployment with an ALB provides AZ-level fault tolerance (the API remains available if one AZ fails). RDS Multi-AZ provides database high availability with automatic failover. A warm standby in a second Region with continuous database replication provides a 30-minute RTO (the environment is already running and can be scaled up quickly) and a sub-5-minute RPO (continuous replication). Route 53 failover routing with health checks automates the DNS switchover when the primary Region becomes unavailable.

- A is incorrect because a single-AZ deployment does not survive an AZ failure, and daily backups provide an RPO of up to 24 hours (exceeding the 5-minute requirement). Restoring from backup would also take hours, exceeding the 30-minute RTO.
- C is incorrect because a single-AZ deployment with manual snapshots every 6 hours provides an RPO of up to 6 hours (exceeding the 5-minute requirement) and no automatic failover.
- D is incorrect because active-active across three Regions far exceeds the requirements (30-minute RTO, 5-minute RPO) and costs significantly more than a warm standby approach. The question asks to balance cost and reliability.

Reference: [Disaster Recovery Options](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html)

---

</details>

---

## Study Guide

If you scored below 70%, review the following topics organized by module:

### Module 13: Security in Depth
- Defense in depth: security controls at each layer (edge, network, compute, application, data, identity, monitoring)
- AWS KMS: customer managed keys vs. AWS managed keys vs. AWS owned keys, envelope encryption, key rotation, key deletion
- Secrets Manager vs. Parameter Store: automatic rotation, cost, cross-account sharing, use cases for each
- AWS WAF: web ACLs, managed rule groups (SQL injection, XSS, bot control), rate-based rules, integration with ALB/CloudFront/API Gateway
- AWS Shield: Standard (free, Layer 3/4) vs. Advanced (paid, Layer 3/4/7, DDoS Response Team, cost protection)
- CloudTrail: management events, data events, Insights events, trails, CloudTrail Lake SQL queries
- GuardDuty: threat detection, finding types and severity, data sources analyzed, enabling in all Regions
- AWS Config: managed rules, compliance evaluation, configuration history, automatic remediation
- Security Hub: finding aggregation, ASFF format, compliance standards (CIS, PCI DSS, NIST)
- Reference: [Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html)

### Module 14: Monitoring and Observability
- Three pillars of observability: metrics (CloudWatch), logs (CloudWatch Logs), traces (X-Ray)
- CloudWatch metrics: built-in vs. custom, namespaces, dimensions, statistics, percentiles (p95, p99)
- CloudWatch alarms: static threshold, anomaly detection, composite alarms, alarm actions
- CloudWatch Logs: log groups, log streams, structured JSON logging, Logs Insights query syntax
- AWS X-Ray: traces, segments, subsegments, service map, sampling, enabling on Lambda/API Gateway/ECS
- Four golden signals: latency, traffic, errors, saturation
- Dashboard design: organizing by service, showing all four signals, alarm status widgets
- Alerting best practices: alert on symptoms not causes, every alert must be actionable, severity levels, avoiding alert fatigue
- Log retention and cost: retention policies, archiving to S3, log-level filtering
- Reference: [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)

### Module 15: Cost Optimization
- AWS pricing models: on-demand, reserved, savings plans, spot
- Cost Explorer: spending analysis, time-based comparison, forecasting, Savings Plans recommendations
- Cost allocation tags: tagging strategy (Project, Environment, Owner, CostCenter), activating tags in Billing
- AWS Budgets: cost budgets, usage budgets, alert thresholds, budget actions for automated enforcement
- Compute Optimizer: right-sizing recommendations, over-provisioned vs. under-provisioned findings
- Savings Plans vs. Reserved Instances: flexibility, services covered, payment options, when to use each
- Common cost traps: idle instances, unattached EBS volumes, old snapshots, unused Elastic IPs, NAT Gateway data transfer, CloudWatch Logs retention
- Storage optimization: S3 lifecycle policies, EBS volume type changes (gp2 to gp3), DynamoDB capacity modes
- Reference: [Cost Optimization Pillar](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html)

### Module 16: Reliability and Disaster Recovery
- Availability: uptime percentages, nines notation, cost of higher availability
- RTO and RPO: definitions, how they determine DR strategy selection
- Four DR strategies: backup/restore, pilot light, warm standby, multi-site active-active (RTO, RPO, cost, complexity for each)
- Multi-AZ: minimum for production, services that support Multi-AZ (EC2+ASG, RDS, ALB, ECS, ElastiCache)
- Multi-Region: Route 53 failover, S3 Cross-Region Replication, RDS cross-Region read replicas, DynamoDB Global Tables, Aurora Global Database
- Resilience patterns: retry with exponential backoff and jitter, circuit breaker, bulkhead, timeout, fallback
- AWS Backup: backup plans, vaults, rules, resource assignment, cross-Region copy
- Chaos engineering: AWS Fault Injection Service (FIS), experiment design, hypothesis-driven testing
- Reference: [Reliability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html)
