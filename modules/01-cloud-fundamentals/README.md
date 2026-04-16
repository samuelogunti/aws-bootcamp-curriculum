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

[Cloud computing](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/what-is-cloud-computing.html) is the on-demand delivery of compute power, database, storage, applications, and other IT resources through a cloud services platform via the internet with pay-as-you-go pricing. Instead of purchasing and maintaining physical servers in your own data center, you rent computing resources from a cloud provider and pay only for what you use.

#### On-Premises vs. Cloud

In a traditional on-premises environment, your organization owns and operates all the hardware, networking equipment, and software required to run applications. You are responsible for purchasing servers, provisioning storage, managing cooling systems, and handling hardware failures. This approach requires significant upfront investment and ongoing maintenance.

With cloud computing, a provider such as [AWS](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/introduction.html) owns and maintains the physical infrastructure. You provision the resources you need through a web console or Application Programming Interface (API), and the provider handles the underlying hardware. This shifts your focus from managing infrastructure to building applications.

| Aspect | On-Premises | Cloud |
|--------|-------------|-------|
| Upfront cost | High (servers, networking, facility) | Low (pay-as-you-go) |
| Scaling | Manual; weeks to months to procure hardware | Automatic or on-demand; minutes to scale |
| Maintenance | Your team handles all hardware and software | Provider handles physical infrastructure |
| Capacity planning | Must estimate peak demand in advance | Scale up or down based on actual usage |
| Global reach | Requires building or leasing data centers | Deploy to multiple regions with a few clicks |

#### The Five Essential Characteristics of Cloud Computing (NIST)

The [NIST definition of cloud computing](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/what-is-cloud-computing.html) identifies five essential characteristics:

1. **On-demand self-service.** You can provision computing resources (servers, storage, networking) as needed, without requiring human interaction with the service provider.
2. **Broad network access.** Resources are available over the network and accessed through standard mechanisms such as web browsers, APIs, or Command Line Interfaces (CLIs).
3. **Resource pooling.** The provider's computing resources are pooled to serve multiple customers using a multi-tenant model. Physical and virtual resources are dynamically assigned and reassigned according to demand.
4. **Rapid elasticity.** Resources can be elastically provisioned and released to scale outward and inward with demand. To the consumer, the resources available for provisioning often appear unlimited.
5. **Measured service.** Cloud systems automatically control and optimize resource use by leveraging a metering capability. Resource usage is monitored, controlled, and reported, providing transparency for both the provider and the consumer.

#### CapEx vs. OpEx

Understanding the financial shift from on-premises to cloud is essential for evaluating cloud adoption.

- **Capital Expenditure (CapEx):** Upfront spending on physical infrastructure such as servers, networking equipment, and data center facilities. These are long-term investments that depreciate over time. With on-premises infrastructure, you pay a large sum before you use any resources.
- **Operational Expenditure (OpEx):** Ongoing spending for services consumed on a pay-as-you-go basis. Cloud computing follows the OpEx model. You pay for compute, storage, and networking as you use them, and you can stop paying when you stop using them.

AWS describes this shift as one of the [six advantages of cloud computing](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/six-advantages-of-cloud-computing.html): "Trade fixed expense for variable expense." Instead of investing heavily in data centers before you know how you will use them, you pay only when you consume computing resources.

> **Tip:** The CapEx-to-OpEx shift is a frequent topic on AWS certification exams. Remember that cloud computing converts large upfront costs into smaller, ongoing variable costs.

### Cloud Service Models

AWS and other cloud providers offer services at different levels of abstraction. The [three main service models](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/types-of-cloud-computing.html) determine how much of the technology stack you manage versus how much the provider manages.

#### Infrastructure as a Service (IaaS)

IaaS provides the fundamental building blocks of cloud IT. You get access to networking features, virtual or dedicated hardware, and storage. IaaS gives you the highest level of flexibility and management control over your IT resources.

AWS example: [Amazon Elastic Compute Cloud (Amazon EC2)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) provides resizable virtual servers. You choose the operating system, configure networking, and install your own software. You manage the operating system, runtime, and application; AWS manages the physical hardware and virtualization layer.

#### Platform as a Service (PaaS)

PaaS removes the need for you to manage the underlying infrastructure (hardware and operating systems). You focus on deploying and managing your applications and data. The provider handles provisioning, patching, and scaling of the platform.

AWS example: [AWS Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html) deploys and scales web applications automatically. You upload your code, and Elastic Beanstalk handles capacity provisioning, load balancing, auto-scaling, and application health monitoring.

#### Software as a Service (SaaS)

SaaS provides a complete product that the provider runs and manages. You simply use the software, typically through a web browser. You do not manage the infrastructure, platform, or application code.

AWS example: [Amazon Connect](https://docs.aws.amazon.com/connect/latest/adminguide/what-is-amazon-connect.html) is a cloud-based contact center service. You configure and use it through a web interface without managing any underlying servers or software.

| Service Model | You Manage | Provider Manages | AWS Example |
|---------------|-----------|------------------|-------------|
| IaaS | OS, runtime, application, data | Hardware, networking, virtualization | Amazon EC2 |
| PaaS | Application, data | Hardware, OS, runtime, scaling | AWS Elastic Beanstalk |
| SaaS | Configuration only | Everything | Amazon Connect |

### Cloud Deployment Models

The [deployment model](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/types-of-cloud-computing.html) you choose determines where your infrastructure lives and how much control you have over it. There are three primary deployment models.

#### Public Cloud

In a public cloud deployment, all parts of the application run in the cloud. Applications are either built natively in the cloud or migrated from existing on-premises infrastructure. Public cloud providers such as AWS make resources available to the general public over the internet. You share the underlying physical infrastructure with other customers (multi-tenant), but your data and applications are logically isolated.

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

AWS is the world's most comprehensive and broadly adopted cloud platform. The [Overview of Amazon Web Services](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/introduction.html) whitepaper describes how AWS offers over 200 fully featured services from data centers globally.

#### Market Position

AWS was the first major cloud provider, launching Amazon Simple Storage Service (Amazon S3) in 2006 and Amazon EC2 shortly after. Today, AWS serves millions of customers across virtually every industry, including startups, enterprises, and public sector organizations.

#### Global Infrastructure: Regions, Availability Zones, and Edge Locations

The [AWS global infrastructure](https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions-availability-zones.html) is designed for high availability, fault tolerance, and low latency.

**Regions.** An [AWS Region](https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions.html) is a physical location in the world where AWS clusters data centers. Each Region is a separate geographic area (for example, `us-east-1` for Northern Virginia, `eu-west-1` for Ireland). Regions are fully isolated from each other, which provides fault isolation and data sovereignty.

When choosing a Region, consider these factors:

- **Compliance:** Some regulations require data to stay within a specific geographic boundary.
- **Latency:** Choose a Region close to your users for lower latency.
- **Service availability:** Not all AWS services are available in every Region.
- **Pricing:** Costs vary by Region.

**Availability Zones (AZs).** Each Region consists of multiple [Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html). An AZ is one or more discrete data centers with redundant power, networking, and connectivity. AZs within a Region are connected through low-latency links but are physically separated to protect against localized failures such as fires, floods, or power outages.

> **Tip:** Deploying your application across multiple AZs within a Region is a foundational best practice for high availability on AWS.

**Edge Locations.** [Amazon CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html) uses a global network of [edge locations](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/LocationsOfEdgeServers.html) to cache and deliver content closer to end users. Edge locations are separate from Regions and AZs. They reduce latency for content delivery by serving cached copies of your data from a location geographically close to the requester.

| Infrastructure Component | What It Is | Purpose |
|--------------------------|-----------|---------|
| Region | A cluster of data centers in a geographic area | Fault isolation, data sovereignty, compliance |
| Availability Zone (AZ) | One or more data centers within a Region | High availability, fault tolerance |
| Edge Location | A CloudFront cache point of presence | Low-latency content delivery |

#### AWS Free Tier

The [AWS Free Tier](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier.html) lets you explore and try AWS services without cost. It includes three types of offers:

- **Always Free:** Offers that do not expire and are available to all AWS customers (for example, 1 million AWS Lambda requests per month).
- **12 Months Free:** Offers available for 12 months following your initial sign-up date (for example, 750 hours per month of Amazon EC2 `t2.micro` or `t3.micro` instances).
- **Trials:** Short-term free trial offers that start from the date you activate a particular service.

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

The [AWS Shared Responsibility Model](https://docs.aws.amazon.com/whitepapers/latest/aws-risk-and-compliance/shared-responsibility-model.html) defines the division of security and compliance responsibilities between AWS and the customer. Understanding this model is critical for securing your workloads on AWS.

The [Security Pillar of the AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/shared-responsibility.html) summarizes the model as: security "of" the cloud versus security "in" the cloud.

#### AWS Responsibility: Security "of" the Cloud

AWS is responsible for protecting the infrastructure that runs all of the services offered in the AWS Cloud. This includes:

- Physical security of data centers (facilities, power, cooling, environmental controls)
- Hardware and networking infrastructure (servers, storage devices, networking equipment)
- Virtualization layer (hypervisor)
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
