---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 13: Security in Depth'
---

# Module 13: Security in Depth

**Phase 4: Production Readiness**
Estimated lecture time: 90 to 105 minutes

<!-- Speaker notes: Welcome to Module 13, the first module in Phase 4. This module builds on the IAM foundation from Module 02 and adds encryption, secrets management, web application protection, threat detection, and compliance monitoring. Breakdown: 10 min defense in depth, 15 min KMS, 10 min secrets management, 15 min WAF/Shield, 15 min CloudTrail/GuardDuty, 15 min Config/Security Hub, 10 min wrap-up. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Analyze a defense-in-depth strategy and evaluate gaps at each layer
- Evaluate KMS encryption strategies (customer managed, AWS managed, AWS owned keys)
- Assess when to use Secrets Manager vs. Parameter Store
- Recommend AWS WAF rule configurations for common web threats
- Evaluate security posture using GuardDuty, Security Hub, and CloudTrail
- Analyze CloudTrail logs to identify unauthorized API calls
- Assess resource compliance using AWS Config rules
- Justify Shield Standard vs. Shield Advanced selection

---

## Prerequisites and agenda

**Prerequisites:** Modules 02 (IAM), 03 (VPC), 05 (S3), 07 (ALB/Route 53), 11 (IaC)

**Agenda:**
1. Defense in depth: layered security
2. AWS KMS: managing encryption keys
3. Secrets Manager and Parameter Store
4. AWS WAF: protecting web applications
5. AWS Shield: DDoS protection
6. AWS CloudTrail: auditing API activity
7. Amazon GuardDuty: threat detection
8. AWS Config and Security Hub

---

# Defense in depth

<!-- Speaker notes: This section takes about 10 minutes. Ask students to list all security controls they configured in previous modules (IAM policies, security groups, NACLs, S3 Block Public Access). Map them to the defense-in-depth layers. -->

---

## Layered security on AWS

| Layer | AWS Controls | Purpose |
|-------|-------------|---------|
| Edge | Shield, WAF, CloudFront | DDoS protection, malicious traffic filtering |
| Network | VPC, security groups, NACLs | Isolate resources, control traffic flow |
| Compute | IAM roles, Inspector, Patch Manager | Control permissions, scan vulnerabilities |
| Application | WAF rules, input validation, API throttling | Protect against injection and abuse |
| Data | KMS, S3 Block Public Access, encryption | Protect data at rest and in transit |

---

## Defense in depth (continued)

| Layer | AWS Controls | Purpose |
|-------|-------------|---------|
| Identity | IAM policies, MFA, IAM Identity Center | Control who can access what |
| Monitoring | CloudTrail, GuardDuty, Security Hub, Config | Detect threats, audit, assess compliance |

> No single service protects against all threats. Combine services so each compensates for the limitations of others.

---

# AWS KMS: managing encryption keys

<!-- Speaker notes: This section takes about 15 minutes. Draw the envelope encryption flow on the whiteboard. The concept of "encrypting the key that encrypts the data" is initially confusing. -->

---

## KMS key types

| Key Type | Who Creates | Who Manages | Rotation | Cost |
|----------|------------|-------------|----------|------|
| Customer managed | You | You | Configurable | Monthly + per-request |
| AWS managed | AWS (on your behalf) | AWS | Automatic (yearly) | Per-request only |
| AWS owned | AWS | AWS | Varies | No charge |

- Customer managed keys give full control over policy and lifecycle
- AWS managed keys are convenient but offer less control

---

## Envelope encryption

```
1. Request a data key from KMS
2. KMS returns plaintext key + encrypted copy
3. Encrypt your data with the plaintext key
4. Store encrypted data + encrypted key together
5. Discard the plaintext key from memory
```

- Your data never leaves your application unencrypted
- KMS never sees your data; only the small data key travels to KMS

> Deleting a KMS key is irreversible. All data encrypted with it becomes permanently unrecoverable.

---

## Discussion: which KMS key type for a healthcare app?

A healthcare application stores patient records in S3 and RDS. Regulations require that the organization controls encryption keys and can audit all key usage.

**Which KMS key type should you use, and why?**

<!-- Speaker notes: Expected answer: Customer managed keys. They provide full control over key policies, rotation schedules, and lifecycle. You can audit all key usage via CloudTrail. AWS managed keys do not allow custom key policies. Compliance requirements typically mandate customer managed keys for regulated data. -->

---

# Secrets Manager and Parameter Store

<!-- Speaker notes: This section takes about 10 minutes. Emphasize: never hardcode credentials in source code. -->

---

## Choosing between Secrets Manager and Parameter Store

| Feature | Secrets Manager | Parameter Store |
|---------|----------------|-----------------|
| Automatic rotation | Built-in for supported databases | Requires custom implementation |
| Cost | Per-secret monthly fee + API calls | Free (standard) or low cost (advanced) |
| Cross-account sharing | Supported | Not natively supported |
| Max size | 64 KB | 4 KB (standard), 8 KB (advanced) |
| Best for | Database credentials, API keys needing rotation | Config values, feature flags, non-rotating secrets |

---

# AWS WAF: protecting web applications

<!-- Speaker notes: This section takes about 10 minutes. Start with managed rule groups, then cover rate-based rules. -->

---

## WAF managed rule groups

| Managed Rule Group | Protects Against |
|-------------------|-----------------|
| AWSManagedRulesCommonRuleSet | Common web exploits (OWASP Top 10) |
| AWSManagedRulesSQLiRuleSet | SQL injection attacks |
| AWSManagedRulesKnownBadInputsRuleSet | Known malicious input patterns |
| AWSManagedRulesAmazonIpReputationList | IPs with poor reputation |
| AWSManagedRulesBotControlRuleSet | Bot traffic (scrapers, crawlers) |

- WAF protects CloudFront, ALB, API Gateway, and AppSync
- Rate-based rules block IPs exceeding a request threshold

---

## Quick check: WAF vs. security groups

Your web application receives SQL injection attempts in HTTP request bodies. You have security groups configured on your ALB.

**Will security groups block these attacks? Why or why not?**

<!-- Speaker notes: Answer: No. Security groups operate at Layer 3/4 (IP, port, protocol) and cannot inspect HTTP request content. WAF operates at Layer 7 and inspects headers, body, URI, and query strings. You need WAF for SQL injection protection and security groups for network-level access control. Both are needed for defense in depth. -->

---

# AWS Shield: DDoS protection

<!-- Speaker notes: This section takes about 5 minutes. Most students will use Shield Standard (free). Cover when Shield Advanced is justified. -->

---

## Shield Standard vs. Shield Advanced

| Feature | Shield Standard | Shield Advanced |
|---------|----------------|-----------------|
| Cost | Free | $3,000/month |
| Protection | Layer 3/4 | Layer 3/4 and Layer 7 |
| DDoS Response Team | Not included | 24/7 access |
| Cost protection | Not included | Credits for DDoS scaling charges |
| WAF included | No | Yes, at no extra cost |

> Shield Standard is sufficient for most workloads. Consider Advanced for high-value targets or when you need DDoS cost protection.

---

# AWS CloudTrail: auditing API activity

<!-- Speaker notes: This section takes about 10 minutes. Emphasize that CloudTrail records who did what, when, and from where. -->

---

## CloudTrail event types

| Event Type | What It Records | Examples |
|-----------|----------------|---------|
| Management events | Control plane operations | `CreateBucket`, `RunInstances`, `AttachRolePolicy` |
| Data events | Data plane operations | S3 `GetObject`/`PutObject`, Lambda `Invoke` |
| Insights events | Unusual API activity patterns | Spike in `RunInstances`, unusual `DeleteBucket` |

- Management events logged by default (90-day retention, free)
- Data events require a trail (can generate high volume and cost)

> Enable data events selectively for sensitive buckets and functions, not for all resources.

---

# Amazon GuardDuty: threat detection

<!-- Speaker notes: This section takes about 10 minutes. If time permits, show the GuardDuty console and sample findings. -->

---

## How GuardDuty works

- Analyzes CloudTrail, VPC Flow Logs, DNS logs using ML and threat intelligence
- Generates findings with type, severity, affected resource, and action details
- No agents to install, no rules to write, no infrastructure to manage

| Finding Category | Examples |
|-----------------|---------|
| Unauthorized access | Compromised credentials from unusual location |
| Cryptocurrency mining | EC2 communicating with mining pools |
| Data exfiltration | Unusual data volume from S3 to external IP |

> Enable GuardDuty in every Region, even Regions where you do not run workloads.

---

## Discussion: assessing a GuardDuty finding

GuardDuty reports a High severity finding: `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.OutsideAWS`. An EC2 instance's IAM role credentials are being used from an IP address outside AWS.

**What steps would you take to investigate and remediate?**

<!-- Speaker notes: Expected answer: 1) Identify the EC2 instance and its IAM role. 2) Check CloudTrail for API calls made with those credentials from the external IP. 3) Revoke the role's active sessions by adding a deny policy with a date condition. 4) Investigate how the credentials were exfiltrated (compromised instance, exposed metadata endpoint). 5) Patch the vulnerability and rotate any other credentials on the instance. -->

---

# AWS Config and Security Hub

<!-- Speaker notes: This section takes about 10 minutes. Cover Config rules for compliance and Security Hub for aggregation. -->

---

## AWS Config: resource compliance

| Managed Rule | What It Checks |
|-------------|---------------|
| `s3-bucket-server-side-encryption-enabled` | S3 buckets have encryption |
| `ec2-instance-no-public-ip` | EC2 instances lack public IPs |
| `iam-root-access-key-check` | Root account has no active keys |
| `rds-instance-public-access-check` | RDS is not publicly accessible |
| `cloudtrail-enabled` | CloudTrail is enabled |

- Config records every configuration change for tracked resources
- Supports automatic remediation via Systems Manager Automation

---

## AWS Security Hub: centralized view

- Aggregates findings from GuardDuty, Inspector, Config, and more
- Normalizes findings into AWS Security Finding Format (ASFF)
- Runs automated compliance checks against standards:
  - AWS Foundational Security Best Practices
  - CIS AWS Foundations Benchmark
  - PCI DSS, NIST 800-53

> Start with the AWS Foundational Security Best Practices standard as your baseline.

---

## Key takeaways

- Defense in depth applies controls at every layer so no single failure compromises your environment
- AWS KMS manages encryption centrally; use customer managed keys for full control, AWS managed keys for convenience
- Store secrets in Secrets Manager (with rotation) or Parameter Store (for simpler config); never hardcode credentials
- WAF, Shield, CloudTrail, GuardDuty, Config, and Security Hub work together to protect, detect, audit, and assess continuously
- Enable CloudTrail and GuardDuty in all Regions; use Config rules to enforce compliance baselines

---

## Lab preview: security services configuration

**What you will do:**
- Create a KMS customer managed key and encrypt an S3 bucket
- Store a database password in Secrets Manager
- Create a CloudTrail trail delivering to S3 and CloudWatch Logs
- Enable GuardDuty and review sample findings
- Configure Config rules for encryption and public access checks

**Duration:** 60 minutes
**Key services:** KMS, Secrets Manager, CloudTrail, GuardDuty, Config

<!-- Speaker notes: The lab has 3 guided steps and 3 semi-guided steps. Remind students that GuardDuty generates sample findings they can explore. Emphasize cleanup: disable GuardDuty and delete the trail to avoid ongoing charges. -->

---

# Questions?

Review `modules/13-security-in-depth/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions: "GuardDuty vs. Security Hub?" (GuardDuty detects threats; Security Hub aggregates findings and runs compliance checks.) "CloudTrail vs. Config?" (CloudTrail records who did what; Config records the resulting resource state.) -->
