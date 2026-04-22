# Lab 17: Conducting a Well-Architected Review

## Objective

Conduct a Well-Architected review of the multi-tier application built throughout the bootcamp, identify high-risk and medium-risk issues across all six pillars, and create a prioritized improvement plan.

## Architecture Diagram

This lab does not build new infrastructure. Instead, you review the architecture you have built across all previous modules:

```
Architecture under review (from previous modules):
    ├── VPC with public/private subnets across 2 AZs (Module 03)
    ├── EC2 instances in Auto Scaling group behind ALB (Modules 04, 07)
    ├── RDS PostgreSQL Multi-AZ (Module 06)
    ├── DynamoDB table (Module 06)
    ├── S3 buckets with encryption and lifecycle policies (Module 05)
    ├── Lambda functions with API Gateway (Module 09)
    ├── ECS Fargate services (Module 10)
    ├── CloudFormation/SAM templates (Module 11)
    ├── CI/CD pipeline with CodePipeline (Module 12)
    ├── KMS encryption, Secrets Manager, CloudTrail, GuardDuty, Config (Module 13)
    ├── CloudWatch dashboards, alarms, X-Ray tracing (Module 14)
    ├── Cost allocation tags, budgets (Module 15)
    └── AWS Backup, DR strategy (Module 16)

Your deliverable: a Well-Architected review report with findings and improvement plan.
```

## Prerequisites

- Completed all modules from Phase 1 through Phase 4 (Modules 01 through 16)
- Completed [Module 17: The AWS Well-Architected Framework](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- Access to the AWS Well-Architected Tool in the console

## Duration

90 minutes

## Goal

Conduct a structured Well-Architected review of the bootcamp architecture using the AWS Well-Architected Tool. Evaluate the architecture against all six pillars, identify high-risk and medium-risk issues, and create a prioritized improvement plan that addresses the most critical gaps.

## Constraints

- You must use the AWS Well-Architected Tool in the console to conduct the review (not a manual document-only assessment).
- You must evaluate at least three pillars in depth (answering all questions for those pillars in the tool).
- Your improvement plan must identify at least five high-risk or medium-risk issues across at least three different pillars.
- For each identified issue, you must propose a specific remediation action, estimate the effort (low, medium, high), and assess the business impact (low, medium, high).
- Your improvement plan must prioritize issues using a risk matrix (impact vs. effort) and justify the sequencing.
- You must save a milestone in the Well-Architected Tool to establish a baseline for future reviews.

## Reference Links

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [The Pillars of the Framework](https://docs.aws.amazon.com/wellarchitected/2025-02-25/framework/the-pillars-of-the-framework.html)
- [AWS Well-Architected Tool User Guide](https://docs.aws.amazon.com/wellarchitected/latest/userguide/intro.html)
- [Well-Architected Tool: Workloads](https://docs.aws.amazon.com/wellarchitected/latest/userguide/workloads.html)
- [Well-Architected Lenses](https://docs.aws.amazon.com/wellarchitected/latest/userguide/lenses.html)
- [Operational Excellence Pillar](https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/welcome.html)
- [Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html)
- [Reliability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html)
- [Performance Efficiency Pillar](https://docs.aws.amazon.com/wellarchitected/2025-02-25/framework/a-performance-efficiency.html)
- [Cost Optimization Pillar](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html)
- [Sustainability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/sustainability-pillar.html)

## Deliverables

1. **Well-Architected Tool workload** with:
   - A workload defined in the tool representing the bootcamp architecture
   - At least three pillars evaluated with all questions answered
   - A saved milestone establishing the baseline

2. **Review report** (Markdown document) containing:
   - An executive summary of the architecture's overall Well-Architected posture
   - A findings table listing each HRI and MRI identified, organized by pillar
   - For each finding: the pillar, the question, the risk level (HRI or MRI), a description of the gap, and the proposed remediation
   - A trade-off analysis section discussing at least two trade-offs you identified (for example, cost vs. reliability, security vs. agility)

3. **Improvement plan** containing:
   - A prioritized list of at least five improvements, ordered by a risk matrix (high impact + low effort first)
   - For each improvement: the pillar, the specific change, the estimated effort (low/medium/high), the business impact (low/medium/high), and any dependencies on other improvements
   - A recommended timeline for implementing the improvements (immediate, next sprint, next quarter)

## Validation

Confirm the following:

- [ ] A workload exists in the AWS Well-Architected Tool with at least three pillars evaluated
- [ ] A milestone is saved in the tool establishing the baseline
- [ ] Your review report identifies at least five HRI or MRI findings across at least three pillars
- [ ] Each finding includes a specific remediation action with effort and impact estimates
- [ ] Your improvement plan prioritizes findings using a risk matrix
- [ ] Your report includes a trade-off analysis discussing at least two cross-pillar trade-offs

## Cleanup

1. **Keep the Well-Architected Tool workload.** It serves as a baseline for future reviews and does not incur charges.
2. Do not delete any resources from previous modules. This lab is a review exercise, not an infrastructure change.

> **Tip:** The Well-Architected Tool is free to use. Workloads and milestones are stored indefinitely at no cost. Keep your workload and add new milestones as you make improvements in future modules.

## Challenge (Optional)

Extend this lab with the following advanced exercises:

1. Apply the Serverless Applications Lens to the Lambda and API Gateway components of your architecture (from [Module 09](../../09-serverless-lambda/README.md)). Compare the findings from the Serverless Lens to the general Framework findings. Identify any additional HRIs specific to serverless workloads.

2. Conduct a second review from the perspective of a different stakeholder. For example, review the architecture as a security auditor (focusing on the Security Pillar) or as a finance manager (focusing on the Cost Optimization Pillar). Document how the priorities differ based on the stakeholder's perspective.

3. Create a CloudFormation template that automates the remediation of one HRI you identified. For example, if the review found that S3 buckets lack versioning, create a Config rule with automatic remediation that enables versioning on non-compliant buckets.

These challenges combine Well-Architected concepts with serverless, security, and IaC practices from previous modules.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
