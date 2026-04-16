# Module 17: Quiz

1. How many pillars does the AWS Well-Architected Framework have, and what are they?

2. A solutions architect is reviewing a production web application. The application runs on a single large EC2 instance with no Auto Scaling, uses a Single-AZ RDS database, has no CloudWatch alarms configured, and stores secrets as environment variables in the Lambda function configuration. Which TWO pillars have the most critical gaps in this architecture? (Select TWO.)

   A) Reliability, because the single EC2 instance and Single-AZ database are single points of failure with no automatic recovery.
   B) Security, because storing secrets as plaintext environment variables exposes them to anyone with access to the Lambda configuration.
   C) Performance Efficiency, because a single large instance is always less efficient than multiple small instances.
   D) Sustainability, because the application does not use Graviton instances.
   E) Cost Optimization, because the application does not use Savings Plans.

3. True or False: The AWS Well-Architected Framework requires you to implement every best practice in every pillar before launching a workload.

4. A startup has a limited budget and must choose between two architectural improvements: (1) enabling RDS Multi-AZ for automatic database failover ($200/month additional cost), or (2) adding CloudFront for faster content delivery ($150/month additional cost). The application is a customer-facing e-commerce platform where database downtime directly causes lost revenue. Which improvement should the startup prioritize, and which pillar does this decision primarily address?

   A) CloudFront (Performance Efficiency), because faster page loads increase customer satisfaction and revenue.
   B) RDS Multi-AZ (Reliability), because database downtime directly causes lost revenue, and Multi-AZ provides automatic failover that eliminates a critical single point of failure.
   C) Neither; the startup should invest in Savings Plans (Cost Optimization) to reduce the overall bill first.
   D) Both simultaneously; the startup should increase its budget to implement both improvements.

5. Which AWS service provides a structured, question-based review of your workload against the Well-Architected Framework pillars and generates a report of high-risk and medium-risk issues?

   A) AWS Trusted Advisor
   B) AWS Well-Architected Tool
   C) AWS Config
   D) AWS Security Hub

6. A team is reviewing their architecture and identifies a trade-off: enabling encryption on all DynamoDB tables (Security Pillar) adds a small amount of latency to every read and write operation (Performance Efficiency Pillar). The tables store customer personal data. How should the team resolve this trade-off?

   A) Disable encryption to maximize performance, because latency is the most important metric.
   B) Enable encryption, because the latency added by DynamoDB encryption at rest is negligible (single-digit milliseconds) and the security risk of storing unencrypted personal data far outweighs the minimal performance impact.
   C) Encrypt only the tables that store personal data and leave other tables unencrypted.
   D) Use client-side encryption instead of server-side encryption to avoid any DynamoDB latency impact.

7. What is the difference between a high-risk issue (HRI) and a medium-risk issue (MRI) in the AWS Well-Architected Tool?

8. A company runs a SaaS application on AWS. The architecture team wants to evaluate the application against both the general Well-Architected Framework and the specific best practices for multi-tenant SaaS applications. How should the team configure their Well-Architected review?

   A) Run two separate reviews: one with the general Framework lens and one with the SaaS Lens.
   B) Apply both the general Framework lens and the SaaS Lens to the same workload in the Well-Architected Tool, so the review covers both general and SaaS-specific best practices in a single assessment.
   C) Use only the SaaS Lens, because it replaces the general Framework for SaaS workloads.
   D) Use AWS Config rules instead of the Well-Architected Tool, because Config provides more detailed compliance checks.

9. A solutions architect is designing an improvement plan after a Well-Architected review. The review identified the following issues:
   - HRI: No automated backups for the RDS database (Reliability)
   - HRI: Root account has no MFA enabled (Security)
   - MRI: CloudWatch Logs have no retention policy (Cost Optimization)
   - MRI: EC2 instances use gp2 volumes instead of gp3 (Cost Optimization)
   - MRI: No structured logging in Lambda functions (Operational Excellence)

   Which issue should the architect address first, and why?

   A) The CloudWatch Logs retention policy, because it has the largest immediate cost impact.
   B) The root account MFA, because it is a high-risk security issue that can be remediated in minutes with no infrastructure changes, and a compromised root account could affect the entire AWS account.
   C) The RDS automated backups, because database data loss is the most severe business impact.
   D) The gp2 to gp3 migration, because it provides immediate cost savings.

10. A company conducts a Well-Architected review and discovers that their architecture scores well on Security and Reliability but poorly on Cost Optimization and Sustainability. The architecture uses over-provisioned EC2 instances (m5.4xlarge running at 10% CPU), stores all S3 data in Standard storage class regardless of access patterns, and runs development environments 24/7. Which single change would improve both the Cost Optimization and Sustainability pillars simultaneously?

    A) Enable MFA on all IAM users to improve the Security Pillar score.
    B) Right-size the EC2 instances from m5.4xlarge to m5.large based on actual utilization, reducing both cost (lower hourly rate) and environmental impact (less wasted compute capacity).
    C) Add CloudFront to improve Performance Efficiency.
    D) Enable RDS Multi-AZ to improve Reliability.

---

<details>
<summary>Answer Key</summary>

1. **The AWS Well-Architected Framework has six pillars: Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, and Sustainability.** Each pillar represents a fundamental aspect of a well-designed cloud workload. The Framework provides design principles and best practices for each pillar, and the AWS Well-Architected Tool helps you evaluate your architecture against these pillars.
   Further reading: [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)

2. **A, B**
   Reliability (A) has critical gaps: a single EC2 instance with no Auto Scaling is a single point of failure (if it fails, the application is down), and a Single-AZ RDS database cannot survive an AZ failure. Security (B) has a critical gap: storing secrets as plaintext environment variables exposes them to anyone who can view the Lambda configuration (IAM users with `lambda:GetFunctionConfiguration` permission). Secrets should be stored in Secrets Manager or Parameter Store. Performance Efficiency (C) is not necessarily a critical gap; a single large instance may be appropriate for some workloads. Sustainability (D) and Cost Optimization (E) are lower-priority concerns compared to the reliability and security risks.
   Further reading: [Reliability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html), [Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html)

3. **False.**
   The Framework is aspirational, not prescriptive. It provides best practices that you should consider, but not every best practice applies to every workload. A development environment does not need the same level of reliability as a production financial system. The Framework helps you make conscious decisions about which best practices to implement based on your workload's requirements, risk tolerance, and budget.
   Further reading: [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)

4. **B) RDS Multi-AZ (Reliability)**
   For an e-commerce platform where database downtime directly causes lost revenue, eliminating the database as a single point of failure is the highest-priority improvement. RDS Multi-AZ provides automatic failover with 1 to 2 minute recovery time, protecting against AZ-level failures. CloudFront (A) improves performance but does not address the critical reliability risk. If the database goes down, faster content delivery does not help. Savings Plans (C) reduce cost but do not address the reliability or performance gaps. Implementing both (D) may not be feasible within the stated budget constraint.
   Further reading: [RDS Multi-AZ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html)

5. **B) AWS Well-Architected Tool**
   The Well-Architected Tool provides a structured, question-based review process aligned with the six pillars. It identifies HRIs and MRIs and generates improvement plans. Trusted Advisor (A) provides automated checks for cost, performance, security, and fault tolerance but does not follow the structured pillar-based review process. Config (C) evaluates resource configurations against rules but is not a review tool. Security Hub (D) aggregates security findings but does not cover all six pillars.
   Further reading: [AWS Well-Architected Tool](https://docs.aws.amazon.com/wellarchitected/latest/userguide/intro.html)

6. **B) Enable encryption**
   DynamoDB encryption at rest is transparent and adds negligible latency (the encryption and decryption happen at the storage layer, not the application layer). The security risk of storing unencrypted customer personal data (potential data breach, regulatory non-compliance) far outweighs the minimal performance impact. This is an example of a trade-off where one pillar (Security) clearly takes priority because the cost of not implementing the best practice (data breach) is orders of magnitude higher than the cost of implementing it (negligible latency). Option C (encrypting only some tables) creates inconsistency and increases the risk of accidentally storing personal data in an unencrypted table. Option D (client-side encryption) adds application complexity without meaningful performance benefit over server-side encryption.
   Further reading: [DynamoDB Encryption at Rest](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/EncryptionAtRest.html)

7. **A high-risk issue (HRI) is a best practice that is not implemented and poses significant risk to the workload. An MRI is a best practice that is partially implemented or poses moderate risk.** In the Well-Architected Tool, HRIs are flagged when you indicate that a critical best practice is not followed (for example, no automated backups, no encryption, no health checks). MRIs are flagged when a best practice is partially implemented (for example, backups exist but are not tested, encryption is enabled for some resources but not all). HRIs should be addressed before MRIs in the improvement plan.
   Further reading: [Well-Architected Tool: Workloads](https://docs.aws.amazon.com/wellarchitected/latest/userguide/workloads.html)

8. **B) Apply both lenses to the same workload**
   The Well-Architected Tool supports applying multiple lenses to a single workload. The general Framework lens covers the six pillars, and the SaaS Lens adds questions specific to multi-tenant architecture (tenant isolation, onboarding, metering, billing). Applying both lenses in a single review provides comprehensive coverage without duplicating effort. Running two separate reviews (A) creates unnecessary overhead. Using only the SaaS Lens (C) misses the general best practices. Config rules (D) evaluate resource configurations, not architectural best practices.
   Further reading: [Well-Architected Lenses](https://docs.aws.amazon.com/wellarchitected/latest/userguide/lenses.html)

9. **B) Root account MFA**
   The root account MFA is a high-risk security issue that can be remediated in minutes (enable MFA on the root account in the IAM console). A compromised root account has unrestricted access to the entire AWS account, including the ability to delete all resources, change billing, and close the account. This is the highest-impact risk with the lowest remediation effort. RDS automated backups (C) are also high-risk and should be addressed immediately after MFA, but they require more configuration time. The cost optimization issues (A, D) and operational excellence issue (E) are medium-risk and can be addressed after the high-risk items.
   Further reading: [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

10. **B) Right-size the EC2 instances**
    Right-sizing from m5.4xlarge (16 vCPUs, 64 GB RAM) to m5.large (2 vCPUs, 8 GB RAM) based on 10% CPU utilization reduces the hourly cost by approximately 87% (Cost Optimization) and eliminates wasted compute capacity that consumes energy without delivering value (Sustainability). This is an example of a change that improves multiple pillars simultaneously. MFA (A) improves Security, not Cost or Sustainability. CloudFront (C) improves Performance Efficiency. RDS Multi-AZ (D) improves Reliability.
    Further reading: [Cost Optimization Pillar](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html), [Sustainability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/sustainability-pillar.html)

</details>
