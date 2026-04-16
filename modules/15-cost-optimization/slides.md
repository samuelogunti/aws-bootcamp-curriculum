---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 15: Cost Optimization'
---

# Module 15: Cost Optimization

**Phase 4: Production Readiness**
Estimated lecture time: 75 to 90 minutes

<!-- Speaker notes: Welcome to Module 15. Start by asking students to estimate how much their bootcamp lab resources would cost if left running for a month. Use the AWS Pricing Calculator to show the actual cost. Breakdown: 10 min pricing models, 10 min Cost Explorer, 10 min Budgets, 15 min right-sizing, 15 min Savings Plans/RIs, 10 min cost traps, 10 min storage optimization, 5 min wrap-up. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Analyze AWS pricing models and evaluate cost-effectiveness for given workloads
- Assess spending using Cost Explorer to identify trends and anomalies
- Recommend a tagging strategy for cost allocation
- Evaluate right-sizing recommendations from Compute Optimizer
- Analyze common cost traps and recommend remediation
- Assess trade-offs between Savings Plans and Reserved Instances
- Optimize storage costs with lifecycle policies and class transitions
- Justify budget alert configurations for early warning of overruns

---

## Prerequisites and agenda

**Prerequisites:** Modules 01 (Free Tier), 04 (EC2), 05 (S3), 06 (Databases), 09 (Lambda), 14 (Monitoring)

**Agenda:**
1. How AWS pricing works
2. AWS Cost Explorer
3. AWS Budgets
4. Right-sizing with Compute Optimizer
5. Savings Plans and Reserved Instances
6. Common cost traps and remediation
7. Storage cost optimization

---

# How AWS pricing works

<!-- Speaker notes: This section takes about 10 minutes. Emphasize that data transfer is the most overlooked cost driver. -->

---

## Common pricing dimensions

| Dimension | Services | Example |
|-----------|----------|---------|
| Compute time | EC2, Lambda, Fargate, RDS | Per hour, per millisecond, per vCPU-hour |
| Storage volume | S3, EBS, RDS, DynamoDB | Per GB-month |
| Data transfer | All services | Per GB out to internet or across Regions |
| API requests | S3, DynamoDB, API Gateway, KMS | Per 1,000 or 1,000,000 requests |
| Provisioned capacity | RDS, DynamoDB, ElastiCache | Per unit-hour |

> Data transfer within the same AZ is free. Between AZs costs a small fee. Between Regions or to the internet costs more.

---

# AWS Cost Explorer

<!-- Speaker notes: This section takes about 10 minutes. Show Cost Explorer's interface and how to filter by service, tag, and time range. -->

---

## Cost Explorer capabilities

- View spending by service, account, Region, or tag
- Compare spending across months and identify trends
- Forecast future spending based on historical patterns
- Get Savings Plans and Reserved Instance recommendations

---

## Cost allocation tags

| Tag Key | Purpose | Example Values |
|---------|---------|----------------|
| `Project` | Which project owns the resource | `bootcamp`, `customer-portal` |
| `Environment` | Distinguish environments | `production`, `staging`, `dev` |
| `Owner` | Who is responsible | `team-backend`, `jsmith` |
| `CostCenter` | Map to organizational cost centers | `engineering`, `marketing` |

> Enforce tagging from day one using Organizations tag policies or IAM conditions that deny untagged resource creation.

---

## Discussion: diagnosing a cost spike

Your team's AWS bill jumped from $2,000 to $5,000 last month. Cost Explorer shows that S3 costs tripled and data transfer costs doubled.

**What questions would you ask, and which Cost Explorer filters would you use to investigate?**

<!-- Speaker notes: Expected answer: Filter by S3 service, then by tag (which project?), then by usage type (storage vs. requests vs. data transfer). Check if a new data pipeline is writing large volumes. Check if cross-Region replication was enabled. Check if a public bucket is being scraped. Data transfer costs often indicate unexpected external access or cross-Region traffic. -->

---

# AWS Budgets

<!-- Speaker notes: This section takes about 5 minutes. Emphasize that budget alerts are reactive, not preventive. -->

---

## Budget types

| Budget Type | What It Tracks | Use Case |
|-------------|---------------|----------|
| Cost budget | Dollar amount spent | "Alert at $500 monthly spend" |
| Usage budget | Resource consumption | "Alert at 1,000 EC2 hours" |
| RI utilization | Reserved Instance usage | "Alert if RI utilization drops below 80%" |
| Savings Plans coverage | SP coverage percentage | "Alert if coverage drops below 70%" |

> Budget alerts notify you after the threshold is reached. For hard limits, use budget actions that apply restrictive IAM policies automatically.

---

# Right-sizing with Compute Optimizer

<!-- Speaker notes: This section takes about 10 minutes. Emphasize: right-size before committing to discounts. -->

---

## Compute Optimizer findings

| Finding | Meaning | Action |
|---------|---------|--------|
| Over-provisioned | More CPU/memory than needed | Downsize to a smaller instance type |
| Under-provisioned | Not enough CPU/memory | Upsize to a larger instance type |
| Optimized | Current type matches workload | No action needed |

- Analyzes CloudWatch metrics over 14 to 93 days
- Covers EC2, EBS, Lambda, and ECS
- Each recommendation includes estimated monthly savings

> Right-size first, then commit. Buying a discount on an over-provisioned instance locks in waste.

---

# Savings Plans and Reserved Instances

<!-- Speaker notes: This section takes about 15 minutes. Use the gym membership analogy: Savings Plans work at any gym in the chain (flexible); RIs are for a specific gym location (cheaper but less flexible). -->

---

## Savings Plans

| Plan Type | Flexibility | Discount |
|-----------|------------|----------|
| Compute Savings Plans | Across EC2, Fargate, Lambda; any family, size, Region | Up to 66% |
| EC2 Instance Savings Plans | Specific instance family in a specific Region | Up to 72% |

---

## Reserved Instances

| Payment Option | Upfront Cost | Discount Level |
|----------------|-------------|----------------|
| All Upfront | Full at purchase | Highest |
| Partial Upfront | Partial at purchase | Moderate |
| No Upfront | Monthly payments only | Lowest |

- Available for EC2, RDS, ElastiCache, OpenSearch, Redshift
- One-year or three-year commitment

---

## Choosing between Savings Plans and RIs

| Factor | Savings Plans | Reserved Instances |
|--------|--------------|-------------------|
| Flexibility | High (cross-service, cross-Region) | Low (specific instance and Region) |
| Services covered | EC2, Fargate, Lambda | EC2, RDS, ElastiCache, OpenSearch |
| Best for | Workloads that may change | Stable, predictable workloads |
| Recommendation | Start here for most workloads | Use for RDS and services not covered by SPs |

---

## Quick check: commitment strategy

Your company runs 20 m5.large EC2 instances 24/7 in us-east-1 for a stable production workload. They also run Lambda functions with variable traffic.

**What commitment strategy would you recommend for each?**

<!-- Speaker notes: Answer: EC2 Instance Savings Plan for the m5.large instances (highest discount for a stable, known instance family). Compute Savings Plan for Lambda (flexible, applies to variable serverless usage). Start with a conservative commitment covering 70% of baseline EC2 usage and increase over time. Do not use RIs for Lambda (not supported). -->

---

# Common cost traps

<!-- Speaker notes: This section takes about 10 minutes. Pause for a group exercise: give each team a mock Cost Explorer report and ask them to identify the top three optimization opportunities. -->

---

## Cost traps and remediation

| Cost Trap | How to Detect | Remediation |
|-----------|--------------|-------------|
| Idle EC2 instances | Near-zero CPU in Compute Optimizer | Stop or terminate |
| Unattached EBS volumes | EBS charges with no instance | Delete after verifying data |
| Old EBS snapshots | Snapshot storage growing | Lifecycle policies to delete old snapshots |
| Over-provisioned RDS | Low CPU and memory utilization | Downsize the instance class |
| Wrong S3 storage class | S3 analytics shows infrequent access | Lifecycle transition to Standard-IA or Glacier |

---

## Discussion: evaluating a cost optimization plan

Your team identified three optimizations: (1) downsize 10 EC2 instances (saves $500/month), (2) delete 500 old snapshots (saves $50/month), (3) buy Savings Plans (saves $2,000/month).

**In what order should you implement these, and why?**

<!-- Speaker notes: Expected answer: 1) Downsize EC2 first (right-size before committing). 2) Buy Savings Plans based on the optimized instance sizes. 3) Delete old snapshots (lowest impact, do anytime). The key insight is that committing to discounts before right-sizing locks in waste. Always right-size first. -->

---

## Storage cost optimization

| Access Pattern | Recommended S3 Class | Relative Cost |
|---------------|---------------------|---------------|
| Frequently accessed (daily) | S3 Standard | Highest |
| Infrequently accessed (monthly) | S3 Standard-IA | ~45% less |
| Rarely accessed (quarterly) | Glacier Flexible Retrieval | ~70% less |
| Archive (yearly or less) | Glacier Deep Archive | ~95% less |

- Switch gp2 EBS volumes to gp3 (same performance, lower price)
- Switch DynamoDB from on-demand to provisioned for steady traffic

---

## Key takeaways

- AWS pricing is pay-as-you-go with multiple dimensions; understanding each service's pricing model is the foundation of cost optimization
- Use Cost Explorer for spending trends, cost allocation tags for attribution, and Budgets for alerts before spending exceeds expectations
- Right-size resources first using Compute Optimizer, then commit to Savings Plans or RIs for predictable baseline usage
- Eliminate waste regularly: idle instances, unattached volumes, old snapshots, missing log retention policies, unnecessary NAT Gateway traffic
- Cost optimization is continuous; schedule monthly reviews and treat cost efficiency as a non-functional requirement

---

## Lab preview: cost optimization audit

**What you will do:**
- Explore Cost Explorer and identify top spending services
- Create a cost allocation tagging strategy and apply tags
- Set up an AWS Budget with alert thresholds
- Review Compute Optimizer recommendations
- Write a cost optimization report with findings and actions

**Duration:** 60 minutes (open-ended format)
**Key services:** Cost Explorer, Budgets, Compute Optimizer, S3

<!-- Speaker notes: This is the first open-ended lab in the curriculum. Students receive goals and constraints, not step-by-step instructions. Deliverables include a cost optimization report, budget setup, and tagging audit. Encourage students to use the Pricing Calculator to estimate monthly costs. -->

---

# Questions?

Review `modules/15-cost-optimization/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions: "Is the Free Tier really free?" (Yes, within limits. Exceeding limits incurs standard charges. Common gotchas: running instances 24/7, creating resources in multiple Regions, forgetting cleanup.) "How much can I realistically save?" (20-40% for most organizations through right-sizing, commitments, and waste elimination.) -->
