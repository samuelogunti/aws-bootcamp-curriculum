# Lab 15: Cost Optimization Strategy for a Multi-Tier Application

## Objective

Design and implement a cost optimization strategy for the AWS resources built in previous modules, reducing estimated monthly costs while maintaining application functionality and availability.

## Architecture Diagram

This lab does not prescribe a specific architecture. Instead, you analyze the resources you have created throughout the bootcamp and design optimizations. The resources you may evaluate include:

```
Resources from previous labs (examples):
    ├── EC2 instances (Module 04): instance types, utilization
    ├── S3 buckets (Module 05): storage classes, lifecycle policies
    ├── RDS instances (Module 06): instance class, Multi-AZ, storage
    ├── ALB (Module 07): idle load balancers, target group health
    ├── SQS/SNS (Module 08): message retention, delivery retries
    ├── Lambda functions (Module 09): memory allocation, timeout
    ├── ECS services (Module 10): Fargate task sizing, desired count
    ├── NAT Gateways (Module 03): data transfer through NAT
    ├── EBS volumes: unattached volumes, snapshot retention
    ├── Elastic IPs: unassociated addresses
    └── CloudWatch Logs: retention policies
```

Your deliverable is a cost optimization report and the implementation of at least three optimizations.

## Prerequisites

- Completed all labs from Modules 01 through 14 (or have a clear understanding of the resources created in each)
- Completed [Module 15: Cost Optimization](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- Access to AWS Cost Explorer (may require 24 hours after account creation to populate data)
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

90 minutes

## Goal

Analyze the AWS resources in your bootcamp account, identify at least five cost optimization opportunities, implement at least three of them, and document the estimated savings for each. Your optimizations must not degrade the functionality of any resources you are still using for the bootcamp.

## Constraints

- You must use AWS Cost Explorer or the AWS CLI to gather current cost and utilization data before making changes.
- You must implement at least three different types of optimizations (for example, right-sizing, storage class changes, and resource cleanup count as three types; deleting three idle EC2 instances counts as one type).
- All changes must be documented in a cost optimization report that includes: the resource, the current configuration, the proposed change, and the estimated monthly savings.
- You must not delete resources that are prerequisites for upcoming modules (Modules 16 through 20). If in doubt, document the optimization as a recommendation rather than implementing it.
- You must create or update at least one AWS Budget to monitor spending going forward.

## Reference Links

- [AWS Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html)
- [AWS Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html)
- [AWS Compute Optimizer](https://docs.aws.amazon.com/compute-optimizer/latest/ug/what-is-compute-optimizer.html)
- [AWS Pricing Calculator](https://calculator.aws/)
- [S3 Storage Classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html)
- [EBS Volume Types](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volume-types.html)
- [Cost Optimization Pillar: AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html)
- [Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html)
- [Savings Plans](https://docs.aws.amazon.com/savingsplans/latest/userguide/what-is-savings-plans.html)

## Deliverables

1. **Cost optimization report** (Markdown document or text file) containing:
   - A summary of your current estimated monthly costs (from Cost Explorer or the Pricing Calculator)
   - At least five identified optimization opportunities, each with: resource name, current configuration, proposed change, estimated monthly savings, and risk assessment
   - At least three implemented optimizations with before/after evidence (screenshots, CLI output, or configuration changes)
   - A total estimated savings percentage

2. **Updated AWS Budget** with:
   - A monthly cost budget set to your post-optimization expected spending
   - Alert thresholds at 50%, 80%, and 100% of the budgeted amount
   - Email notification configured for each threshold

3. **Tagging audit** showing:
   - A list of resources that are missing required tags (`Project`, `Environment`, `Owner`)
   - Evidence that you have tagged at least five previously untagged resources

## Validation

Confirm the following:

- [ ] You have analyzed your account's spending using Cost Explorer or the CLI
- [ ] Your cost optimization report identifies at least five optimization opportunities
- [ ] You have implemented at least three optimizations from different categories
- [ ] Each implemented optimization is documented with before/after evidence
- [ ] An AWS Budget exists with alert thresholds at 50%, 80%, and 100%
- [ ] At least five resources have been tagged with `Project`, `Environment`, and `Owner` tags

## Cleanup

This lab modifies existing resources rather than creating new ones. The primary cleanup actions are:

1. If you created any test resources during your analysis, delete them.
2. Keep the AWS Budget you created; it will continue to monitor your spending for the remainder of the bootcamp.
3. Keep the tags you applied; they will be useful for cost tracking in future modules.

> **Warning:** Do not delete resources that are prerequisites for Modules 16 through 20. If you are unsure whether a resource is needed, document the optimization as a recommendation in your report rather than implementing it.

## Challenge (Optional)

Extend this lab with the following advanced optimizations:

1. Use the [AWS Pricing Calculator](https://calculator.aws/) to create a detailed cost estimate for your bootcamp environment. Compare the estimate to your actual Cost Explorer data and explain any differences.

2. Write a CloudFormation template (or CDK construct) that creates a Lambda function triggered by a daily CloudWatch Events schedule. The function uses the AWS SDK to identify and report unattached EBS volumes, unused Elastic IPs, and EC2 instances with average CPU utilization below 5% over the past 7 days. Send the report to an SNS topic.

3. Analyze your DynamoDB tables' capacity mode. If any tables use on-demand mode with predictable traffic, calculate the cost difference between on-demand and provisioned mode (with auto scaling) and document your recommendation.

These challenges combine cost optimization with Lambda, CloudFormation, and CloudWatch concepts from previous modules.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
