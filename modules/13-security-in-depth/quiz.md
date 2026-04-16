# Module 13: Quiz

1. Which AWS service creates and manages the cryptographic keys used to encrypt data across AWS services such as S3, EBS, and RDS?

   A) AWS Secrets Manager
   B) AWS Certificate Manager
   C) AWS Key Management Service (AWS KMS)
   D) AWS CloudHSM

2. True or False: AWS Shield Standard is a paid service that must be explicitly enabled for each AWS account.

3. What is the primary difference between AWS Secrets Manager and AWS Systems Manager Parameter Store for storing sensitive data?

4. A company stores customer financial records in S3 and must comply with a regulation that requires the company to control its own encryption keys, rotate them annually, and audit all key usage. Which KMS key type should the company use, and why?

   A) AWS owned keys, because they are managed entirely by AWS and require no configuration.
   B) AWS managed keys, because they rotate automatically every year and are free of charge.
   C) Customer managed keys, because they provide full control over key policies, configurable rotation schedules, and CloudTrail logging of all key usage.
   D) S3-managed keys (SSE-S3), because S3 handles all encryption automatically.

5. Which AWS service continuously monitors your AWS account for malicious activity by analyzing CloudTrail events, VPC Flow Logs, and DNS logs using machine learning and threat intelligence?

   A) AWS Config
   B) Amazon Inspector
   C) Amazon GuardDuty
   D) AWS Security Hub

6. A security team discovers that an S3 bucket containing sensitive data was made publicly accessible three days ago. They need to determine who changed the bucket policy and when. Which TWO AWS services should they use to investigate? (Select TWO.)

   A) AWS CloudTrail, to find the API call that modified the bucket policy, including the IAM principal, timestamp, and source IP address.
   B) AWS Config, to view the configuration timeline of the bucket and see the exact policy change and when it occurred.
   C) Amazon GuardDuty, to view the bucket policy change history.
   D) AWS WAF, to inspect the HTTP requests that modified the bucket.
   E) Amazon CloudWatch Logs, to view the S3 server access logs for the bucket.

7. An architect is designing a web application that will be exposed to the internet through an Application Load Balancer. The application handles user login forms and must be protected against SQL injection and cross-site scripting attacks. Which AWS service should the architect use to inspect and filter HTTP requests at the application layer?

   A) Security groups, because they filter traffic based on IP addresses and ports.
   B) Network ACLs, because they provide stateless filtering at the subnet level.
   C) AWS WAF, because it inspects HTTP request content and can block requests matching SQL injection and XSS patterns.
   D) AWS Shield Standard, because it protects against all types of web attacks.

8. What is envelope encryption, and why does AWS KMS use it instead of encrypting data directly?

9. A company runs a multi-account AWS environment. The security team wants a single dashboard that aggregates findings from GuardDuty, Inspector, and Config across all accounts, normalizes them into a standard format, and runs automated compliance checks against industry standards. Which service provides this capability?

   A) Amazon CloudWatch
   B) AWS CloudTrail
   C) AWS Security Hub
   D) Amazon Detective

10. A startup has a limited security budget but wants to protect its web application against common DDoS attacks. The application runs behind an Application Load Balancer. The team does not need 24/7 DDoS response support or cost protection credits. Which Shield tier should the team use, and what additional service should they consider for application-layer protection?

    A) Shield Advanced ($3,000/month), because it is the only option that provides DDoS protection.
    B) Shield Standard (free, automatic) for network-layer DDoS protection, combined with AWS WAF rate-based rules for application-layer protection against HTTP floods.
    C) Shield Standard only, because it protects against all DDoS attacks including application-layer attacks.
    D) Neither Shield tier; the ALB provides built-in DDoS protection that is sufficient for all attack types.

---

<details>
<summary>Answer Key</summary>

1. **C) AWS Key Management Service (AWS KMS)**
   AWS KMS is the managed service for creating and controlling encryption keys. It integrates with over 100 AWS services to encrypt data at rest. Secrets Manager stores secrets (not keys), Certificate Manager manages SSL/TLS certificates, and CloudHSM provides dedicated hardware security modules for customers with specific compliance requirements.
   Further reading: [AWS KMS Overview](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)

2. **False.**
   AWS Shield Standard is free and automatically enabled for all AWS accounts. It provides protection against common network-layer (Layer 3/4) DDoS attacks. Shield Advanced is the paid tier ($3,000/month) that provides additional application-layer protection, DDoS Response Team access, and cost protection.
   Further reading: [How AWS Shield Works](https://docs.aws.amazon.com/waf/latest/developerguide/ddos-overview.html)

3. **Secrets Manager provides built-in automatic rotation for supported databases and cross-account sharing, while Parameter Store is simpler and less expensive but does not include built-in rotation.**
   Secrets Manager is designed for credentials that need automatic rotation (database passwords, API keys). Parameter Store is better suited for configuration values that change infrequently and do not require rotation (feature flags, endpoint URLs). Secrets Manager charges per secret per month plus per API call; Parameter Store standard parameters are free.
   Further reading: [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html), [Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)

4. **C) Customer managed keys**
   Customer managed keys provide full control over key policies (who can use the key), configurable rotation schedules (automatic annual rotation or custom schedules), and CloudTrail logging of every API call that uses the key. This meets all three regulatory requirements: control, rotation, and audit. AWS managed keys rotate automatically but do not allow custom key policies. AWS owned keys provide no customer visibility or control.
   Further reading: [AWS KMS Key Types](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html)

5. **C) Amazon GuardDuty**
   GuardDuty is the threat detection service that uses machine learning, anomaly detection, and threat intelligence to identify malicious activity. Config evaluates resource compliance against rules. Inspector scans for software vulnerabilities. Security Hub aggregates findings from multiple services but does not perform its own threat detection from raw data sources.
   Further reading: [What Is Amazon GuardDuty?](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)

6. **A, B**
   CloudTrail (A) records the `PutBucketPolicy` or `PutBucketAcl` API call, showing exactly who made the change, when, and from which IP address. Config (B) records the configuration state of the bucket over time, showing the before and after states of the bucket policy. Together, they provide a complete picture: who changed it (CloudTrail) and what changed (Config). GuardDuty detects threats but does not track configuration changes. WAF inspects HTTP requests to protected resources, not AWS API calls. CloudWatch Logs can store S3 access logs, but access logs record object-level operations, not bucket policy changes.
   Further reading: [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html), [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html)

7. **C) AWS WAF**
   WAF operates at the application layer (Layer 7) and inspects HTTP/HTTPS request content. It can detect and block SQL injection patterns, cross-site scripting payloads, and other application-layer attacks using managed rule groups or custom rules. Security groups and NACLs operate at the network layer and cannot inspect request content. Shield Standard protects against network-layer DDoS attacks, not application-layer injection attacks.
   Further reading: [What Is AWS WAF?](https://docs.aws.amazon.com/waf/latest/developerguide/what-is-aws-waf.html)

8. **Envelope encryption is a technique where KMS generates a data key, the data key encrypts the data locally, and then KMS encrypts the data key with the KMS key. KMS uses this approach because sending large datasets to KMS for direct encryption would be slow and impractical.** With envelope encryption, only the small data key (typically 256 bits) travels to and from KMS. The actual data never leaves the application. This provides both security (the data key is encrypted at rest) and performance (encryption happens locally using the plaintext data key).
   Further reading: [AWS KMS Concepts](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html)

9. **C) AWS Security Hub**
   Security Hub aggregates findings from GuardDuty, Inspector, Config, Macie, Firewall Manager, and third-party tools into a single dashboard. It normalizes findings into the AWS Security Finding Format (ASFF) and runs automated compliance checks against standards such as AWS Foundational Security Best Practices, CIS Benchmarks, and PCI DSS. CloudWatch monitors metrics and logs. CloudTrail records API calls. Detective helps investigate security findings but does not aggregate or run compliance checks.
   Further reading: [What Is AWS Security Hub?](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub-v2.html)

10. **B) Shield Standard (free) combined with AWS WAF rate-based rules**
    Shield Standard is free and automatically protects against common network-layer DDoS attacks. For application-layer protection (HTTP floods), the team should add AWS WAF with rate-based rules that block IP addresses exceeding a request threshold. This combination provides effective DDoS protection at minimal cost. Shield Advanced is unnecessary for a startup that does not need 24/7 DDoS response support or cost protection credits. Shield Standard alone does not protect against application-layer attacks. The ALB does not provide comprehensive DDoS protection on its own.
    Further reading: [AWS Shield](https://docs.aws.amazon.com/waf/latest/developerguide/ddos-overview.html), [AWS WAF Rate-Based Rules](https://docs.aws.amazon.com/waf/latest/developerguide/waf-rule-statement-type-rate-based.html)

</details>
