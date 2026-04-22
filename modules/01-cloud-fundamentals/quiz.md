# Module 01: Quiz

1. Which of the following is NOT one of the five essential characteristics of cloud computing as defined by the National Institute of Standards and Technology (NIST)?

   A) On-demand self-service
   B) Broad network access
   C) Dedicated single-tenant hardware
   D) Rapid elasticity

2. True or False: In the cloud computing financial model, you replace large upfront Capital Expenditure (CapEx) with ongoing Operational Expenditure (OpEx), paying only for the resources you consume.

3. A company uploads its application code to a service that automatically handles capacity provisioning, load balancing, and auto-scaling. The company does not manage the operating system or runtime. Which cloud service model does this describe?

   A) Infrastructure as a Service (IaaS)
   B) Platform as a Service (PaaS)
   C) Software as a Service (SaaS)
   D) Function as a Service (FaaS)

4. In your own words, describe the difference between a public cloud deployment model and a hybrid cloud deployment model.

5. What is the primary purpose of AWS Availability Zones within a Region?

   A) To cache content closer to end users for lower latency
   B) To provide fault tolerance by isolating failures across physically separate data centers
   C) To enforce data sovereignty by keeping data within a country
   D) To reduce costs by sharing hardware across customers

6. True or False: In the AWS Shared Responsibility Model, AWS is responsible for patching the guest operating system on Amazon EC2 instances.

7. Which of the following are factors you should consider when choosing an AWS Region? (Select THREE.)

   A) Compliance and data residency requirements
   B) The programming language your application uses
   C) Latency to your end users
   D) Service availability in the Region
   E) The number of developers on your team

8. List the three types of offers included in the AWS Free Tier.

9. Under the AWS Shared Responsibility Model, which of the following is the customer's responsibility?

   A) Physical security of data center facilities
   B) Patching the hypervisor
   C) Configuring security group rules for Amazon EC2 instances
   D) Maintaining networking hardware in Availability Zones

10. An organization wants to keep sensitive workloads in its own data center for regulatory reasons but use AWS for scalable, burst-capacity workloads. Which cloud deployment model best fits this requirement?

    A) Public cloud
    B) Private cloud
    C) Hybrid cloud
    D) Multi-cloud

---

<details>
<summary>Answer Key</summary>

1. **C) Dedicated single-tenant hardware**
   The five NIST characteristics are: on-demand self-service, broad network access, resource pooling, rapid elasticity, and measured service. "Dedicated single-tenant hardware" is the opposite of resource pooling, which uses a multi-tenant model where the provider's resources are shared across multiple customers. Options A, B, and D are all genuine NIST characteristics.
   Further reading: [What is cloud computing? (AWS Overview)](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/what-is-cloud-computing.html)

2. **True.**
   Cloud computing shifts IT spending from CapEx (large upfront investments in physical infrastructure) to OpEx (pay-as-you-go variable costs). AWS describes this as one of the six advantages of cloud computing: "Trade fixed expense for variable expense."
   Further reading: [Six advantages of cloud computing (AWS Overview)](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/six-advantages-of-cloud-computing.html)

3. **B) Platform as a Service (PaaS)**
   PaaS removes the need to manage the underlying infrastructure, operating system, and runtime. You deploy your application code, and the platform handles provisioning and scaling. AWS Elastic Beanstalk is an example. IaaS (A) would require you to manage the OS and runtime. SaaS (C) is a fully managed application you use through a browser, not one you deploy code to. FaaS (D) is a subset of serverless computing, not one of the three primary service models.
   Further reading: [Types of cloud computing (AWS Overview)](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/types-of-cloud-computing.html)

4. **Sample answer:** In a public cloud deployment, all parts of the application run in the cloud provider's infrastructure, shared with other customers (multi-tenant) and accessed over the internet. In a hybrid cloud deployment, an organization connects cloud-based resources with its existing on-premises infrastructure, allowing sensitive workloads to remain on-premises while using the cloud for scalable or burst workloads.
   Further reading: [Types of cloud computing (AWS Overview)](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/types-of-cloud-computing.html)

5. **B) To provide fault tolerance by isolating failures across physically separate data centers**
   Each Availability Zone consists of one or more discrete data centers with redundant power, networking, and connectivity. AZs within a Region are physically separated to protect against localized failures such as fires, floods, or power outages. Option A describes edge locations (used by CloudFront). Option C describes Regions, not AZs. Option D describes resource pooling, a general cloud characteristic, not the specific purpose of AZs.
   Further reading: [AWS Regions and Availability Zones](https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions-availability-zones.html)

6. **False.**
   Under the Shared Responsibility Model, AWS is responsible for security "of" the cloud (physical infrastructure, hypervisor, networking hardware). The customer is responsible for security "in" the cloud, which includes patching the guest operating system on EC2 instances. For managed services like Amazon RDS, AWS handles OS patching, but for EC2, this is the customer's responsibility.
   Further reading: [Shared Responsibility Model (AWS Risk and Compliance)](https://docs.aws.amazon.com/whitepapers/latest/aws-risk-and-compliance/shared-responsibility-model.html)

7. **A, C, D**
   When choosing an AWS Region, you should consider: compliance and data residency requirements (A), latency to your end users (C), and service availability in the Region (D). Pricing also varies by Region. The programming language your application uses (B) does not affect Region selection, as AWS supports the same languages across Regions. The number of developers on your team (E) is an organizational factor, not a Region selection criterion.
   Further reading: [Regions and Zones (Amazon EC2 User Guide)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)

8. **Always Free** (offers that do not expire and are available to all AWS customers), **12 Months Free** (offers available for 12 months following your initial sign-up date), and **Trials** (short-term free trial offers that start from the date you activate a particular service).
   Further reading: [AWS Free Tier (AWS Billing)](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-free-tier.html)

9. **C) Configuring security group rules for Amazon EC2 instances**
   Security groups are network-level firewalls that you configure in the AWS console. Under the Shared Responsibility Model, anything you can configure is your responsibility to configure securely. Physical security of data centers (A), patching the hypervisor (B), and maintaining networking hardware (D) are all AWS responsibilities as part of security "of" the cloud.
   Further reading: [Shared Responsibility (Security Pillar, AWS Well-Architected Framework)](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/shared-responsibility.html)

10. **C) Hybrid cloud**
    A hybrid deployment connects cloud-based resources with existing on-premises infrastructure. This allows the organization to keep sensitive workloads on-premises for regulatory compliance while using AWS for scalable or burst workloads. Public cloud (A) would move everything to the cloud, which does not meet the regulatory requirement. Private cloud (B) keeps everything on-premises, missing the benefit of cloud scalability. Multi-cloud (D) refers to using multiple cloud providers, which does not address the on-premises requirement.
    Further reading: [Types of cloud computing (AWS Overview)](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/types-of-cloud-computing.html)

</details>

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
