---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 17: Well-Architected Framework'
---

# Module 17: The AWS Well-Architected Framework

**Phase 5: Architecting**
Estimated lecture time: 90 to 105 minutes

<!-- Speaker notes: Welcome to Module 17, the first module in Phase 5. This module synthesizes everything from Modules 01-16 into a structured evaluation framework. Start by asking students to describe the architecture they built in previous modules, then evaluate it against each pillar. Breakdown: 10 min framework overview, 10 min per pillar (60 min total), 10 min trade-offs, 10 min WA Tool, 5 min wrap-up. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Critique an architecture against each of the six Well-Architected pillars
- Design improvement plans prioritized by business impact and effort
- Evaluate trade-offs between pillars and defend balanced decisions
- Propose changes that satisfy multiple pillars simultaneously
- Architect a Well-Architected review process for an organization
- Critique the use of Well-Architected lenses for specialized workloads
- Design a remediation roadmap sequenced by risk and dependencies

---

## Prerequisites and agenda

**Prerequisites:** All modules from Phase 1 through Phase 4 (Modules 01-16)

**Agenda:**
1. What is the Well-Architected Framework?
2. Pillar 1: Operational Excellence
3. Pillar 2: Security
4. Pillar 3: Reliability
5. Pillar 4: Performance Efficiency
6. Pillar 5: Cost Optimization
7. Pillar 6: Sustainability
8. Trade-offs between pillars
9. The AWS Well-Architected Tool

---

# What is the Well-Architected Framework?

<!-- Speaker notes: This section takes about 10 minutes. Emphasize that the Framework is a conversation tool, not a compliance checklist. The value comes from the discussion it generates. -->

---

## Six pillars of the Framework

| Pillar | Focus | Key Question |
|--------|-------|-------------|
| Operational Excellence | Running and monitoring systems | "How do you evolve operations?" |
| Security | Protecting information and systems | "How do you protect data?" |
| Reliability | Recovering from failures | "Does the workload perform its function?" |
| Performance Efficiency | Using resources efficiently | "How do you select the right resources?" |
| Cost Optimization | Avoiding unnecessary costs | "How do you manage costs?" |
| Sustainability | Minimizing environmental impact | "How do you reduce environmental impact?" |

> The Framework is most valuable as a regular practice, not a one-time assessment.

---

# Pillar 1: Operational Excellence

<!-- Speaker notes: This section takes about 10 minutes. Connect to Module 11 (IaC) and Module 12 (CI/CD). -->

---

## Operational Excellence design principles

- **Perform operations as code:** IaC templates (Module 11), CI/CD pipelines (Module 12)
- **Make frequent, small, reversible changes:** Deploy through pipelines with automated rollback
- **Refine operations procedures frequently:** Update runbooks after every incident
- **Anticipate failure:** Pre-mortems and chaos engineering (Module 16)
- **Learn from all operational failures:** Blameless post-incident reviews

Key services: CloudFormation, CDK, CodePipeline, CloudWatch, Systems Manager, Config

---

# Pillar 2: Security

<!-- Speaker notes: This section takes about 10 minutes. Connect to Module 02 (IAM) and Module 13 (Security in Depth). -->

---

## Security design principles

- **Strong identity foundation:** Least privilege, IAM roles, MFA (Module 02)
- **Maintain traceability:** CloudTrail, Config, VPC Flow Logs (Module 13)
- **Apply security at all layers:** Defense in depth across edge, network, compute, data
- **Automate security best practices:** Config rules, Security Hub, automated remediation
- **Protect data in transit and at rest:** KMS encryption, TLS, bucket policies
- **Prepare for security events:** Incident response procedures, GuardDuty automation

Key services: IAM, KMS, WAF, Shield, GuardDuty, Security Hub, Config, CloudTrail

---

# Pillar 3: Reliability

<!-- Speaker notes: This section takes about 10 minutes. Connect to Module 16 (Reliability and DR). -->

---

## Reliability design principles

- **Automatically recover from failure:** Health checks, Auto Scaling, multi-AZ
- **Test recovery procedures:** Chaos engineering with AWS FIS (Module 16)
- **Scale horizontally:** Multiple small resources instead of one large resource
- **Stop guessing capacity:** Auto Scaling matches capacity to demand
- **Manage change through automation:** CI/CD with automated testing and rollback

Key services: Auto Scaling, ALB, Route 53, RDS Multi-AZ, DynamoDB Global Tables, AWS Backup, FIS

---

# Pillar 4: Performance Efficiency

<!-- Speaker notes: This section takes about 10 minutes. Emphasize "mechanical sympathy": use services the way they were designed. -->

---

## Performance Efficiency design principles

- **Democratize advanced technologies:** Use managed services (RDS, Lambda, Fargate)
- **Go global in minutes:** CloudFront, Route 53, DynamoDB Global Tables
- **Use serverless architectures:** Lambda, Fargate, API Gateway, DynamoDB
- **Experiment more often:** Test different instance types and configurations
- **Consider mechanical sympathy:** DynamoDB for key-value, RDS for relational queries

Key services: EC2 (Graviton), Lambda, Fargate, CloudFront, ElastiCache, DynamoDB

---

# Pillar 5: Cost Optimization

<!-- Speaker notes: This section takes about 10 minutes. Connect to Module 15 (Cost Optimization). -->

---

## Cost Optimization design principles

- **Implement cloud financial management:** Tags, Cost Explorer, Budgets (Module 15)
- **Adopt a consumption model:** Pay only for what you use; Auto Scaling, serverless
- **Measure overall efficiency:** Cost per transaction, not just total spend
- **Stop spending on undifferentiated heavy lifting:** Managed services over self-managed
- **Analyze and attribute expenditure:** Tag all resources, use Compute Optimizer

Key services: Cost Explorer, Budgets, Compute Optimizer, Savings Plans, Trusted Advisor

---

# Pillar 6: Sustainability

<!-- Speaker notes: This section takes about 5 minutes. Note that sustainability and cost optimization often align. -->

---

## Sustainability design principles

- **Understand your impact:** AWS Customer Carbon Footprint Tool
- **Maximize utilization:** Right-size, Auto Scaling, consolidate workloads
- **Adopt efficient hardware:** Graviton (ARM) instances, better performance per watt
- **Reduce downstream impact:** Minimize data transfer, compress data, efficient formats

> Sustainability and cost optimization often align. Right-sizing reduces both cost and energy consumption.

---

## Think about it: pillar trade-offs

Your startup has $5,000/month for AWS. You must choose between Multi-AZ RDS ($200/month extra for reliability) and a larger EC2 instance ($150/month extra for performance). You cannot afford both.

**Which do you choose, and how do you justify the trade-off?**

<!-- Speaker notes: Expected answer: Multi-AZ RDS. Database failure causes data loss and extended downtime, which is harder to recover from than slower performance. Performance can be improved later with caching (ElastiCache) or query optimization. Data loss from a single-AZ database failure may be unrecoverable. This illustrates the reliability vs. performance trade-off. -->

---

## Think about it: conflicting pillar recommendations

The Security team wants to encrypt all data with customer managed KMS keys (Security Pillar). The Development team says this adds latency to every read/write and slows their iteration speed (Performance Efficiency, Operational Excellence).

**How do you resolve this conflict?**

<!-- Speaker notes: Expected answer: Measure the actual latency impact of KMS encryption. For most services (S3, RDS, DynamoDB), the latency added by KMS is negligible (single-digit milliseconds). Use S3 Bucket Keys to reduce KMS API calls and cost. The security benefit far outweighs the minimal performance impact. If latency is measurable for high-throughput workloads, consider using AWS managed keys (slightly less control but same encryption strength with potentially better caching). Document the trade-off decision in an ADR. -->

---

## Common trade-offs

| Trade-Off | Example | How to Decide |
|-----------|---------|---------------|
| Cost vs. Reliability | Multi-AZ costs more but provides fault tolerance | Compare downtime cost vs. infrastructure cost |
| Security vs. Agility | Strict approvals slow deployments | Automate security checks in CI/CD |
| Performance vs. Cost | Provisioned IOPS costs more | Measure actual I/O needs first |
| Reliability vs. Cost | Multi-Region doubles infrastructure | Match DR strategy to RTO/RPO |

---

# The AWS Well-Architected Tool

<!-- Speaker notes: This section takes about 10 minutes. If time permits, demo the tool in the console. Create a workload, answer a few questions, show the HRI/MRI output. -->

---

## How a review works

1. **Define the workload** in the WA Tool (name, environment, Regions)
2. **Select lenses** (default Framework, plus Serverless, SaaS, etc.)
3. **Answer questions** for each pillar about your architecture
4. **Review findings** (High-Risk Issues and Medium-Risk Issues)
5. **Create an improvement plan** prioritized by risk
6. **Track progress** with milestones over time

---

## Well-Architected lenses

| Lens | Focus |
|------|-------|
| Serverless Applications | Lambda, API Gateway, Step Functions, DynamoDB |
| SaaS | Multi-tenant application architecture |
| Data Analytics | Data lakes, ETL pipelines, analytics |
| Machine Learning | Model training, inference, MLOps |
| Container Build | Container images and CI/CD for containers |

> Use the WA Tool as a living document. Save milestones after each review to demonstrate continuous improvement.

---

## Design challenge: Well-Architected review

You are reviewing a web application: ALB, EC2 (single AZ, no Auto Scaling), RDS (single AZ, no backups), no monitoring dashboards, no IaC, manual deployments.

**Identify one high-risk issue per pillar and propose a fix.**

<!-- Speaker notes: Expected answers: Operational Excellence: no IaC or CI/CD (fix: CloudFormation + CodePipeline). Security: no mention of encryption or IAM review (fix: KMS encryption, least-privilege audit). Reliability: single AZ, no backups (fix: multi-AZ, enable RDS backups). Performance: no Auto Scaling (fix: ASG with target tracking). Cost: no tagging or budgets (fix: tagging strategy + budget alerts). Sustainability: no right-sizing data (fix: enable Compute Optimizer). -->

---

## Key takeaways

- The Well-Architected Framework provides six pillars for evaluating and improving cloud architectures
- Every architecture involves trade-offs between pillars; the Framework helps you make them consciously
- Use the AWS Well-Architected Tool for structured reviews, identifying high-risk issues, and tracking improvement
- The Framework is most valuable as a regular practice (quarterly reviews), not a one-time assessment
- Well-Architected lenses extend the Framework with specialized guidance for serverless, SaaS, and analytics workloads

---

## Lab preview: Well-Architected review

**What you will do:**
- Create a workload in the AWS Well-Architected Tool
- Answer questions for each of the six pillars
- Review the generated findings (HRIs and MRIs)
- Create an improvement plan with prioritized actions
- Save a milestone to track your baseline

**Duration:** 60 minutes (open-ended format)
**Key services:** AWS Well-Architected Tool

<!-- Speaker notes: This is an open-ended lab. Students evaluate the architecture they have built throughout the bootcamp. Deliverables include a review report, improvement plan, and milestone. Encourage students to be honest in their self-assessment; documenting a known gap with a plan is more valuable than claiming perfection. -->

---

# Questions?

Review `modules/17-well-architected-framework/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions: "Is this on the certification exam?" (Yes, heavily tested on SA Associate and Professional.) "Do I need to implement every best practice?" (No. Prioritize by risk and workload requirements. A dev environment does not need the same reliability as production.) -->
