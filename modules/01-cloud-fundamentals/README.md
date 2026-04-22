# Module 01: Cloud Fundamentals

## Learning Objectives

By the end of this module, you will be able to:

- Define cloud computing and summarize how it differs from traditional on-premises infrastructure
- List the five essential characteristics of cloud computing as defined by the National Institute of Standards and Technology (NIST)
- Distinguish between Capital Expenditure (CapEx) and Operational Expenditure (OpEx) in the context of IT spending
- Describe the three cloud service models: Infrastructure as a Service (IaaS), Platform as a Service (PaaS), and Software as a Service (SaaS)
- Identify the three cloud deployment models: public cloud, private cloud, and hybrid cloud
- Explain the structure of the Amazon Web Services (AWS) global infrastructure, including Regions, Availability Zones (AZs), and edge locations
- Summarize the AWS Shared Responsibility Model and distinguish between AWS responsibilities and customer responsibilities

## Prerequisites

- Basic programming knowledge in any language
- An AWS account (free tier is sufficient)
- A modern web browser (Chrome, Firefox, Safari, or Edge)

## Concepts

### What Is Cloud Computing?

[Cloud computing](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/what-is-cloud-computing.html) lets you access servers, databases, storage, and other IT resources over the internet, paying only for what you consume. Rather than buying and maintaining physical hardware in your own facility, you provision what you need from a provider and release it when you are done.

#### On-Premises vs. Cloud

In a traditional on-premises environment, your organization owns and operates all the hardware, networking equipment, and software required to run applications. You are responsible for purchasing servers, provisioning storage, managing cooling systems, and handling hardware failures. This approach requires significant upfront investment and ongoing maintenance.

With cloud computing, a provider such as [AWS](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/introduction.html) owns and maintains the physical infrastructure. You select the resources you need through a web console or Application Programming Interface (API), and the provider takes care of the underlying hardware. This frees you to focus on building applications rather than racking servers.

| Aspect | On-Premises | Cloud |
|--------|-------------|-------|
| Upfront cost | High (servers, networking, facility) | Low (pay-as-you-go) |
| Scaling | Manual; weeks to months to procure hardware | Automatic or on-demand; minutes to scale |
| Maintenance | Your team handles all hardware and software | Provider handles physical infrastructure |
| Capacity planning | Must estimate peak demand in advance | Scale up or down based on actual usage |
| Global reach | Requires building or leasing data centers | Deploy to multiple regions with a few clicks |

#### The Five Essential Characteristics of Cloud Computing (NIST)

The [NIST definition of cloud computing](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/what-is-cloud-computing.html) identifies five essential characteristics. These matter because they distinguish true cloud services from traditional hosting:

1. **On-demand self-service.** You spin up servers, storage, or networking whenever you need them, without filing a ticket or waiting for a human to approve the request.
2. **Broad network access.** You reach your resources over the internet using standard tools: web browsers, APIs, or Command Line Interfaces (CLIs).
3. **Resource pooling.** The provider serves many customers from shared physical hardware, dynamically assigning capacity as demand shifts. You never see the multi-tenant layer underneath.
4. **Rapid elasticity.** Capacity grows and shrinks with your workload. From your perspective, available resources appear virtually limitless.
5. **Measured service.** Usage is metered and reported, so you see exactly what you consumed and what it cost.

#### CapEx vs. OpEx

Understanding the financial shift from on-premises to cloud is essential for evaluating cloud adoption.

- **Capital Expenditure (CapEx):** Upfront spending on physical infrastructure such as servers, networking equipment, and data center facilities. These are long-term investments that depreciate over time. With on-premises infrastructure, you pay a large sum before you use any resources.
- **Operational Expenditure (OpEx):** Ongoing spending for services consumed on a pay-as-you-go basis. Cloud computing follows the OpEx model. You pay for compute, storage, and networking as you use them, and you can stop paying when you stop using them.

AWS describes this shift as one of the [six advantages of cloud computing](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/six-advantages-of-cloud-computing.html): trading fixed expense for variable expense. You stop guessing capacity years in advance and instead pay for resources as you consume them.

> **Tip:** The CapEx-to-OpEx shift is a frequent topic on AWS certification exams. Remember that cloud computing converts large upfront costs into smaller, ongoing variable costs.

### Cloud Service Models

AWS and other cloud providers offer services at different levels of abstraction. The [three main service models](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/types-of-cloud-computing.html) determine how much of the technology stack you manage versus how much the provider manages.

#### Infrastructure as a Service (IaaS)

IaaS provides the fundamental building blocks of cloud IT: networking, virtual or dedicated hardware, and storage. You get maximum flexibility because you control everything above the hypervisor layer.

AWS example: [Amazon Elastic Compute Cloud (Amazon EC2)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) gives you virtual servers that you size, configure, and manage. You pick the operating system, install your own software, and handle patching. AWS takes care of the physical machines and the virtualization layer underneath.

#### Platform as a Service (PaaS)

PaaS removes the burden of managing the underlying infrastructure (hardware and operating systems). You focus on your application code and data while the provider handles provisioning, patching, and scaling of the platform underneath.

AWS example: [AWS Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html) deploys and scales web applications for you. Upload your code, and Beanstalk configures capacity, load balancing, auto-scaling, and health monitoring on your behalf.

#### Software as a Service (SaaS)

SaaS delivers a complete, ready-to-use product that the provider runs and manages. You simply use the software through a web browser without worrying about infrastructure, platform, or application code.

AWS example: [Amazon Connect](https://docs.aws.amazon.com/connect/latest/adminguide/what-is-amazon-connect.html) is a cloud-based contact center service. You configure and use it through a web interface without managing any underlying servers or software.

| Service Model | You Manage | Provider Manages | AWS Example |
|---------------|-----------|------------------|-------------|
| IaaS | OS, runtime, application, data | Hardware, networking, virtualization | Amazon EC2 |
| PaaS | Application, data | Hardware, OS, runtime, scaling | AWS Elastic Beanstalk |
| SaaS | Configuration only | Everything | Amazon Connect |

### Cloud Deployment Models

The [deployment model](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/types-of-cloud-computing.html) you choose determines where your infrastructure lives and how much control you have over it. There are three primary deployment models.

#### Public Cloud

In a public cloud deployment, all parts of the application run on the provider's infrastructure. You share the underlying physical hardware with other customers (multi-tenant), but your data and applications are logically isolated. Applications are either built natively in the cloud or migrated from existing on-premises infrastructure.

Benefits of public cloud include low upfront cost, elastic scaling, and global reach.

#### Private Cloud (On-Premises)

A private cloud deploys resources on-premises using virtualization and resource management tools. The organization owns and operates the hardware in its own data center. While this model does not provide many of the benefits of cloud computing (such as elastic scaling and pay-as-you-go pricing), some organizations choose it for regulatory compliance or data sovereignty requirements.

#### Hybrid Cloud

A [hybrid deployment](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/types-of-cloud-computing.html) connects cloud-based resources with existing on-premises infrastructure. This model allows organizations to extend their data center into the cloud, keeping sensitive workloads on-premises while using the cloud for scalable or burst workloads. AWS supports hybrid deployments through services such as [AWS Outposts](https://docs.aws.amazon.com/outposts/latest/userguide/what-is-outposts.html), which extends AWS infrastructure and services to your on-premises facility.

| Deployment Model | Infrastructure Location | Use Case |
|------------------|------------------------|----------|
| Public cloud | Provider's data centers | Most workloads; startups; variable demand |
| Private cloud | Your own data centers | Strict regulatory or compliance requirements |
| Hybrid cloud | Both provider and your data centers | Gradual migration; data sovereignty with cloud burst |

### Why AWS?

AWS is the world's largest and most broadly adopted cloud platform. The [Overview of Amazon Web Services](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/introduction.html) whitepaper describes its catalog of over 200 services spanning compute, storage, databases, machine learning, and more.

#### Market Position

AWS was the first major cloud provider, launching Amazon Simple Storage Service (Amazon S3) in 2006 and Amazon EC2 shortly after. Today, AWS serves millions of customers across virtually every industry, including startups, enterprises, and public sector organizations.

#### Global Infrastructure: Regions, Availability Zones, and Edge Locations

The [AWS global infrastructure](https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions-availability-zones.html) is built around three layers of physical separation, each serving a distinct purpose for availability and performance.

**Regions.** An [AWS Region](https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions.html) is a geographic area (for example, `us-east-1` for Northern Virginia, `eu-west-1` for Ireland) containing multiple data center clusters. Regions are fully isolated from each other, giving you fault isolation and data sovereignty.

When choosing a Region, consider these factors:

- **Compliance:** Some regulations require data to stay within a specific geographic boundary.
- **Latency:** Choose a Region close to your users for lower latency.
- **Service availability:** Not all AWS services are available in every Region.
- **Pricing:** Costs vary by Region.

**Availability Zones (AZs).** Each Region consists of multiple [Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html). An AZ is one or more discrete data centers with independent power, cooling, and networking. AZs within a Region connect through low-latency links but sit far enough apart physically to survive localized disasters (fires, floods, power outages).

> **Tip:** Deploying your application across multiple AZs within a Region is a foundational best practice for high availability on AWS.

**Edge Locations.** [Amazon CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html) uses a global network of [edge locations](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/LocationsOfEdgeServers.html) to cache content closer to end users. Edge locations are separate from Regions and AZs. They cut latency by serving cached copies of your data from a point geographically near the requester.

| Infrastructure Component | What It Is | Purpose |
|--------------------------|-----------|---------|
| Region | A cluster of data centers in a geographic area | Fault isolation, data sovereignty, compliance |
| Availability Zone (AZ) | One or more data centers within a Region | High availability, fault tolerance |
| Edge Location | A CloudFront cache point of presence | Low-latency content delivery |

#### AWS Free Tier

The [AWS Free Tier](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier.html) lets you experiment with AWS services at no cost. It includes three categories of offers:

- **Always Free:** Offers that never expire (for example, 1 million AWS Lambda requests per month).
- **12 Months Free:** Offers available for the first year after sign-up (for example, 750 hours per month of Amazon EC2 `t2.micro` or `t3.micro` instances).
- **Trials:** Short-term offers that begin when you activate a particular service.

> **Warning:** Some AWS services are not covered by the Free Tier. Always check the [Free Tier usage tracking page](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/tracking-free-tier-usage.html) in the AWS Billing console to monitor your usage and avoid unexpected charges.

You can verify your current Region and Free Tier eligibility using the AWS CLI:

```bash
aws sts get-caller-identity
```

Expected output:

```json
{
    "UserId": "AIDEXAMPLE123456",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/your-username"
}
```

### Shared Responsibility Model

The [AWS Shared Responsibility Model](https://docs.aws.amazon.com/whitepapers/latest/aws-risk-and-compliance/shared-responsibility-model.html) draws a clear line between what AWS secures and what you must secure. Getting this boundary wrong is one of the most common causes of cloud security incidents, so understanding it now will save you trouble in every module that follows.

The [Security Pillar of the AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/shared-responsibility.html) frames the split as: AWS handles security "of" the cloud, while you handle security "in" the cloud.

#### AWS Responsibility: Security "of" the Cloud

AWS protects the infrastructure that runs every service in the AWS Cloud. This covers:

- Physical security of data centers (facilities, power, cooling, environmental controls)
- Hardware and networking infrastructure (servers, storage devices, networking equipment)
- The virtualization layer (hypervisor)
- Managed service software (for services such as Amazon S3, Amazon DynamoDB, and AWS Lambda, AWS also manages the operating system and platform)

#### Customer Responsibility: Security "in" the Cloud

You are responsible for the security of everything you put in the cloud and how you configure AWS services. This includes:

- Data encryption (at rest and in transit)
- Identity and Access Management (IAM) configuration (users, roles, policies, Multi-Factor Authentication)
- Operating system patches and updates (for Amazon EC2 instances)
- Network configuration (security groups, network access control lists, routing)
- Application-level security (input validation, authentication, authorization)

The exact split of responsibilities varies by service type:

| Service Type | AWS Manages | You Manage |
|-------------|-------------|------------|
| IaaS (e.g., Amazon EC2) | Hardware, networking, virtualization | OS, patches, firewall, data, IAM |
| PaaS (e.g., Elastic Beanstalk) | Hardware, OS, platform | Application code, data, IAM |
| SaaS (e.g., Amazon Connect) | Everything except configuration | Access controls, data classification |

> **Tip:** A helpful way to remember the model: if you can configure it in the AWS console, you are responsible for configuring it securely.

## Instructor Notes

**Estimated lecture time:** 60 minutes

**Common student questions:**

- Q: Is the cloud just "someone else's computer"?
  A: Partially, but that oversimplifies it. Cloud computing includes on-demand self-service, rapid elasticity, measured service, and resource pooling, which go far beyond renting a single server. The [NIST definition](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/what-is-cloud-computing.html) captures these essential characteristics.

- Q: How do I know which AWS Region to choose?
  A: Consider four factors: compliance requirements (data residency laws), latency to your users, service availability in the Region, and pricing. For this bootcamp, we default to `us-east-1` (Northern Virginia) because it has the broadest service availability and is typically the first Region to receive new features.

- Q: What happens if an entire Availability Zone goes down?
  A: If your application is deployed across multiple AZs (a best practice), traffic automatically shifts to the healthy AZs. This is why AWS recommends [multi-AZ deployments](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) for production workloads. A single-AZ deployment would experience downtime.

- Q: Is the Free Tier really free?
  A: Yes, within the published limits. However, if you exceed those limits or use services not covered by the Free Tier, you will incur charges. Always set up a [billing alarm](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/tracking-free-tier-usage.html) to monitor your usage.

**Teaching tips:**

- Start with the on-premises vs. cloud comparison table. Ask students to estimate how long it takes to provision a physical server (weeks) versus a cloud instance (minutes). This makes the value proposition concrete.
- When explaining the Shared Responsibility Model, draw a vertical line on the whiteboard. Label the left side "AWS manages" and the right side "You manage." Walk through each service type (IaaS, PaaS, SaaS) and show how the line shifts.
- Use the analogy of renting an apartment (cloud) versus owning a house (on-premises) to explain the CapEx vs. OpEx distinction. In an apartment, the landlord handles structural maintenance; in a house, you handle everything.

**Pause points:**

- After the NIST five characteristics: ask students to identify which characteristic explains why you only pay for what you use (measured service).
- After the service models table: ask students to classify a few AWS services as IaaS, PaaS, or SaaS.
- After the Shared Responsibility Model: ask students who is responsible for patching the operating system on an EC2 instance (the customer) versus an RDS database (AWS).

## Key Takeaways

- Cloud computing is the on-demand delivery of IT resources over the internet with pay-as-you-go pricing, shifting spending from CapEx to OpEx.
- AWS organizes its global infrastructure into Regions, Availability Zones, and edge locations to provide high availability, fault tolerance, and low-latency content delivery.
- The three service models (IaaS, PaaS, SaaS) determine how much of the technology stack you manage versus how much AWS manages.
- Security on AWS is a shared responsibility: AWS secures the cloud infrastructure, and you secure your data, configurations, and applications within the cloud.
- The AWS Free Tier provides a cost-free way to explore services, but you must monitor usage to avoid unexpected charges.
