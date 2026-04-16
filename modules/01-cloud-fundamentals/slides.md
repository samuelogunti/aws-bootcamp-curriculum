---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 01: Cloud Fundamentals'
---

# Module 01: Cloud Fundamentals

**Phase 1: Cloud Foundations**
Estimated lecture time: 60 minutes

<!-- Speaker notes: Welcome students to the first module of the AWS Bootcamp. This module covers the foundational concepts that every subsequent module builds on. Total lecture time is approximately 60 minutes: 10 min cloud computing basics, 10 min service/deployment models, 15 min AWS global infrastructure, 10 min shared responsibility, 15 min discussion and Q&A. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Define cloud computing and summarize how it differs from on-premises
- List the five NIST characteristics of cloud computing
- Distinguish between CapEx and OpEx
- Describe IaaS, PaaS, and SaaS
- Identify public, private, and hybrid cloud models
- Explain AWS Regions, Availability Zones, and edge locations
- Summarize the AWS Shared Responsibility Model

---

## Prerequisites and agenda

**Prerequisites:** Basic programming knowledge, an AWS account (free tier), a modern web browser

**Agenda:**
1. What is cloud computing?
2. Cloud service models (IaaS, PaaS, SaaS)
3. Cloud deployment models
4. Why AWS?
5. AWS global infrastructure
6. Shared responsibility model

---

# What is cloud computing?

<!-- Speaker notes: This section takes approximately 10 minutes. Start by asking students how they think traditional IT infrastructure works, then contrast it with cloud computing. -->

---

## Cloud computing defined

- On-demand delivery of IT resources over the internet
- Pay only for what you use (pay-as-you-go)
- No upfront hardware purchases required
- Provider manages physical infrastructure
- You focus on building applications, not managing servers

---

## On-premises vs. cloud

| Aspect | On-Premises | Cloud |
|--------|-------------|-------|
| Upfront cost | High (servers, facility) | Low (pay-as-you-go) |
| Scaling | Weeks to months | Minutes |
| Maintenance | Your team | Provider |
| Capacity planning | Estimate peak demand | Scale on actual usage |

---

## Five NIST characteristics of cloud computing

1. **On-demand self-service:** Provision resources without human interaction
2. **Broad network access:** Access from any device over the network
3. **Resource pooling:** Provider shares resources across customers
4. **Rapid elasticity:** Scale up or down quickly
5. **Measured service:** Pay only for what you consume

---

## CapEx vs. OpEx

| | Capital Expenditure (CapEx) | Operational Expenditure (OpEx) |
|---|---|---|
| **What** | Upfront hardware purchases | Ongoing service payments |
| **When** | Before you use it | As you use it |
| **Risk** | Over/under-provisioning | Pay for actual usage |
| **Example** | Buy 10 servers | Rent EC2 instances hourly |

> Cloud computing shifts IT spending from CapEx to OpEx.

---

## Discussion: why does the CapEx-to-OpEx shift matter?

A startup has $50,000 to build its first product. Under the CapEx model, they would spend $30,000 on servers before writing any code. Under the OpEx model, they start building immediately and pay $500/month for cloud resources.

**Which approach lets the startup iterate faster, and why?**

<!-- Speaker notes: Expected answer: OpEx (cloud) lets them start immediately, adjust resources as they learn what they need, and avoid the risk of buying the wrong hardware. The $30,000 CapEx investment is a sunk cost even if the product pivots. -->

---

# Cloud service models

<!-- Speaker notes: This section takes approximately 5 minutes. Use the table on the next slide to show the spectrum from IaaS (you manage most) to SaaS (provider manages most). -->

---

## IaaS, PaaS, and SaaS

| Model | You Manage | Provider Manages | AWS Example |
|-------|-----------|-----------------|-------------|
| **IaaS** | OS, runtime, app, data | Hardware, networking | Amazon EC2 |
| **PaaS** | App code and data | OS, runtime, scaling | AWS Elastic Beanstalk |
| **SaaS** | Configuration only | Everything | Amazon Connect |

> As you move from IaaS to SaaS, you manage less and the provider manages more.

---

# Cloud deployment models

<!-- Speaker notes: This section takes approximately 5 minutes. Emphasize that most bootcamp labs use the public cloud model. -->

---

## Public, private, and hybrid cloud

| Model | Infrastructure Location | Best For |
|-------|------------------------|----------|
| **Public cloud** | Provider's data centers | Most workloads; no upfront cost |
| **Private cloud** | Your data centers | Strict regulatory requirements |
| **Hybrid cloud** | Both | Gradual migration; data sovereignty |

- This bootcamp uses the **public cloud** (AWS)
- Hybrid is common in enterprises migrating to the cloud

---

## Quick check: which deployment model?

A hospital must keep patient records on-premises due to regulations, but wants to use AWS for its public website and analytics workloads.

**Which deployment model is this?**

A) Public cloud
B) Private cloud
C) Hybrid cloud

<!-- Speaker notes: Answer: C) Hybrid cloud. The hospital uses on-premises for regulated data and public cloud for non-regulated workloads. This is a common pattern in healthcare and financial services. -->

---

# Why AWS?

<!-- Speaker notes: This section takes approximately 15 minutes. Cover the global infrastructure, then the shared responsibility model. Use the map analogy: Regions are cities, AZs are neighborhoods, edge locations are local post offices. -->

---

## AWS by the numbers

- 200+ fully featured services
- 30+ geographic Regions worldwide
- Millions of active customers
- Largest cloud provider by market share
- Extensive free tier for learning

---

## AWS global infrastructure

```
AWS Global Infrastructure
    |
    ├── Region (e.g., us-east-1: N. Virginia)
    |   ├── Availability Zone (us-east-1a)
    |   ├── Availability Zone (us-east-1b)
    |   └── Availability Zone (us-east-1c)
    |
    ├── Region (e.g., eu-west-1: Ireland)
    |   ├── AZ (eu-west-1a)
    |   ├── AZ (eu-west-1b)
    |   └── AZ (eu-west-1c)
    |
    └── Edge Locations (400+ worldwide)
        └── Cache content close to users
```

---

## Regions, AZs, and edge locations

| Component | What It Is | Why It Matters |
|-----------|-----------|----------------|
| **Region** | Geographic area with 2+ AZs | Data residency, latency |
| **Availability Zone** | One or more data centers | Fault isolation, high availability |
| **Edge location** | CDN cache point | Low-latency content delivery |

> **All bootcamp labs use `us-east-1` (N. Virginia).**

---

## How to choose a Region

Four factors to consider:

1. **Compliance:** Does regulation require data in a specific country?
2. **Latency:** Which Region is closest to your users?
3. **Service availability:** Not all services are in all Regions
4. **Cost:** Pricing varies by Region

---

## Discussion: Region selection

Your company is based in Germany and must comply with GDPR, which requires customer data to stay in the EU. Your primary users are in Europe, but you also have users in the US.

**Which Region(s) would you choose, and why?**

<!-- Speaker notes: Expected answer: eu-central-1 (Frankfurt) or eu-west-1 (Ireland) for the primary deployment to meet GDPR requirements. For US users, consider a second deployment in us-east-1 or use CloudFront edge locations to reduce latency. This is a multi-Region discussion that previews Module 16 (Reliability and DR). -->

---

# Shared Responsibility Model

<!-- Speaker notes: This section takes approximately 10 minutes. This is one of the most important concepts in the entire bootcamp. Draw the dividing line on the whiteboard: AWS below, customer above. -->

---

## AWS responsibilities vs. your responsibilities

| AWS Manages ("Security OF the Cloud") | You Manage ("Security IN the Cloud") |
|---------------------------------------|--------------------------------------|
| Physical data centers | Your data and encryption |
| Hardware and networking | IAM users, roles, and policies |
| Hypervisor and host OS | Operating system patches (EC2) |
| Managed service infrastructure | Security group and firewall rules |
| Global infrastructure | Application code and configuration |

---

## Shared responsibility varies by service

| Service Type | AWS Manages | You Manage |
|-------------|-------------|------------|
| **IaaS (EC2)** | Hardware, hypervisor | OS, patches, app, data |
| **Managed (RDS)** | Hardware, OS, patching | Data, access, backups config |
| **Serverless (Lambda)** | Everything below your code | Function code, IAM role |

> The more managed the service, the less you are responsible for.

---

## Quick check: who is responsible?

For each item, answer: **AWS** or **Customer**?

1. Patching the OS on an EC2 instance
2. Physical security of the data center
3. Configuring security group rules
4. Replacing a failed hard drive
5. Encrypting data stored in S3

<!-- Speaker notes: Answers: 1) Customer, 2) AWS, 3) Customer, 4) AWS, 5) Customer. Walk through each answer and explain why. Emphasize that if you can configure it in the console, you are responsible for configuring it securely. -->

---

## Instructor notes

**Common student questions:**
- "Is the cloud just someone else's computer?" (Technically yes, but with elasticity, global reach, and managed services that your own servers cannot match)
- "Is cloud computing always cheaper?" (Not always; poorly managed cloud resources can cost more than on-premises)
- "What happens if AWS goes down?" (AZs provide fault isolation; multi-AZ and multi-Region designs provide resilience)

---

## Key takeaways

- Cloud computing is the on-demand delivery of IT resources over the internet with pay-as-you-go pricing, shifting spending from CapEx to OpEx
- AWS global infrastructure is organized into Regions (geographic areas), Availability Zones (isolated data centers), and edge locations (CDN cache points)
- The Shared Responsibility Model divides security: AWS secures the cloud infrastructure; you secure your data, access, and configurations within the cloud
- Choose your AWS Region based on compliance, latency, service availability, and cost

---

## Lab preview: AWS account setup and console tour

**What you will do:**
- Create and secure an AWS account with MFA
- Create an IAM administrative user
- Navigate the AWS Management Console
- Set up a zero-spend budget alert

**Duration:** 45 minutes
**Region:** us-east-1 (N. Virginia)

---

# Questions?

Review `modules/01-cloud-fundamentals/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. If no questions, transition to the lab. Remind students to have their MFA app ready before starting the lab. -->
