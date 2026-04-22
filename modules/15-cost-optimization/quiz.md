# Module 15: Quiz

1. Which AWS tool analyzes your historical usage patterns and recommends Savings Plans or Reserved Instances that would reduce your compute costs?

   A) AWS Budgets
   B) AWS Cost Explorer
   C) AWS Compute Optimizer
   D) AWS Pricing Calculator

2. True or False: Data transfer between two EC2 instances in the same Availability Zone using private IP addresses is free.

3. A company runs 10 EC2 instances of type `m5.xlarge` 24/7 for a production workload. The workload is stable and predictable, and the company plans to run it for at least the next three years. Which pricing approach would provide the largest cost savings compared to on-demand pricing?

   A) Spot Instances, because they offer up to 90% savings.
   B) On-demand pricing with a monthly budget alert to track spending.
   C) EC2 Instance Savings Plans with a three-year All Upfront commitment, because the workload is stable, predictable, and locked to a specific instance family.
   D) Compute Savings Plans with a one-year No Upfront commitment, because flexibility is more important than maximum savings.

4. What is the primary purpose of cost allocation tags in AWS?

5. A startup's AWS bill has increased 40% over the past three months. The team does not know which services or projects are driving the increase. Which TWO actions should the team take first to diagnose the cost increase? (Select TWO.)

   A) Use AWS Cost Explorer to view spending by service and identify which services have the largest cost increases.
   B) Purchase Reserved Instances immediately to reduce the overall bill.
   C) Activate cost allocation tags and tag all resources with `Project` and `Environment` to enable cost attribution in future reports.
   D) Delete all non-production resources to reduce costs immediately.
   E) Switch all EC2 instances to Spot Instances to reduce compute costs.

6. AWS Compute Optimizer reports that an EC2 instance of type `m5.2xlarge` (8 vCPUs, 32 GB RAM) is "over-provisioned." CloudWatch metrics show the instance averages 12% CPU utilization and 8 GB memory usage over the past 30 days. Which action should the team take?

   A) Terminate the instance, because 12% CPU utilization means it is not needed.
   B) Downsize the instance to `m5.large` (2 vCPUs, 8 GB RAM), which matches the actual CPU and memory usage while providing headroom for occasional spikes.
   C) Keep the current instance type but purchase a Reserved Instance to reduce the hourly cost.
   D) Switch to a Spot Instance to save up to 90% on the compute cost.

7. Which S3 storage class is the most cost-effective for data that is accessed approximately once per quarter and must be retrievable within 12 hours?

   A) S3 Standard
   B) S3 Standard-IA
   C) S3 Glacier Flexible Retrieval
   D) S3 One Zone-IA

8. A company has 50 unattached EBS volumes in their account, totaling 5 TB of provisioned storage. The volumes were left behind after EC2 instances were terminated. What is the cost impact, and what should the team do?

9. A solutions architect is choosing between Compute Savings Plans and EC2 Instance Savings Plans for a company that runs a mix of EC2, Fargate, and Lambda workloads. The company expects to change instance types as new generations become available and may shift some workloads between Regions. Which plan type should the architect recommend, and why?

   A) EC2 Instance Savings Plans, because they offer the highest discount rate.
   B) Compute Savings Plans, because they apply across EC2, Fargate, and Lambda regardless of instance family, size, or Region, providing the flexibility the company needs.
   C) Neither; the company should use on-demand pricing until workload patterns stabilize.
   D) Reserved Instances, because they cover all three services (EC2, Fargate, Lambda).

10. A team sets up an AWS Budget with a $1,000 monthly threshold and email alerts at 80% and 100%. On the 15th of the month, they receive the 80% alert ($800 spent). By the time they investigate, spending has already reached $1,100. What should the team do differently to prevent budget overruns in the future?

    A) Increase the budget threshold to $1,500 so the alerts trigger less frequently.
    B) Add an earlier alert threshold (for example, 50%) and configure a budget action that automatically applies a restrictive IAM policy when the 80% threshold is crossed, preventing new resource creation.
    C) Switch from email alerts to SMS alerts for faster notification delivery.
    D) Remove the budget entirely, because budget alerts are always too late to prevent overruns.

---

<details>
<summary>Answer Key</summary>

1. **B) AWS Cost Explorer**
   Cost Explorer analyzes your historical usage and provides recommendations for Savings Plans and Reserved Instances based on your actual consumption patterns. AWS Budgets tracks spending against thresholds but does not recommend pricing commitments. Compute Optimizer recommends instance types based on utilization, not pricing commitments. The Pricing Calculator estimates costs for new deployments but does not analyze historical usage.
   Further reading: [AWS Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html)

2. **True.**
   Data transfer between EC2 instances in the same Availability Zone using private IP addresses is free. Data transfer between AZs incurs a charge (currently $0.01/GB in each direction). Data transfer out to the internet incurs a higher charge that varies by Region and volume.
   Further reading: [EC2 Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)

3. **C) EC2 Instance Savings Plans with a three-year All Upfront commitment**
   For a stable, predictable workload running 24/7 for three years on a specific instance family, EC2 Instance Savings Plans provide the highest discount (up to 72%). The workload characteristics (stable, predictable, long-term) match the commitment requirements perfectly. Spot Instances (A) can be interrupted and are unsuitable for production workloads that must run continuously. On-demand with budget alerts (B) provides no discount. Compute Savings Plans (D) with one-year No Upfront offer a smaller discount than three-year All Upfront EC2 Instance Savings Plans.
   Further reading: [Savings Plans](https://docs.aws.amazon.com/savingsplans/latest/userguide/what-is-savings-plans.html)

4. **Cost allocation tags are key-value pairs attached to AWS resources that enable you to categorize and track costs by project, environment, owner, or any other dimension in AWS Cost Explorer and billing reports.** After activating cost allocation tags in the Billing console, they appear as filterable dimensions in Cost Explorer, allowing you to answer questions like "how much did the production environment cost last month?" or "which team is responsible for the highest S3 spending?"
   Further reading: [Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html)

5. **A, C**
   Cost Explorer (A) provides immediate visibility into which services are driving the cost increase by showing spending trends over time, grouped by service. Activating cost allocation tags (C) enables future cost attribution by project and environment, which is essential for ongoing cost management. Purchasing Reserved Instances (B) without understanding the cost drivers risks committing to resources that may not be needed. Deleting all non-production resources (D) is drastic and may disrupt development workflows. Switching to Spot Instances (E) risks interruptions for production workloads.
   Further reading: [AWS Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html), [Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html)

6. **B) Downsize the instance to `m5.large`**
   The instance is using only 12% of its CPU (approximately 1 vCPU out of 8) and 8 GB of its 32 GB memory. Downsizing to `m5.large` (2 vCPUs, 8 GB RAM) matches the actual usage while providing headroom for occasional spikes. This reduces the hourly cost by approximately 75%. Terminating the instance (A) is incorrect because the instance is being used (12% CPU is not zero). Purchasing a Reserved Instance (C) locks in the cost of an over-provisioned instance. Spot Instances (D) can be interrupted and may not be suitable for the workload.
   Further reading: [AWS Compute Optimizer](https://docs.aws.amazon.com/compute-optimizer/latest/ug/what-is-compute-optimizer.html)

7. **C) S3 Glacier Flexible Retrieval**
   Glacier Flexible Retrieval is designed for data accessed once or twice per quarter with retrieval times ranging from minutes to hours (expedited: 1 to 5 minutes, standard: 3 to 5 hours, bulk: 5 to 12 hours). The 12-hour retrieval requirement is well within the standard retrieval time. S3 Standard (A) is the most expensive option for infrequently accessed data. Standard-IA (B) is designed for monthly access, not quarterly. One Zone-IA (D) stores data in a single AZ, reducing durability, and is designed for more frequent access than quarterly.
   Further reading: [S3 Storage Classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html)

8. **Unattached EBS volumes incur storage charges based on their provisioned size, regardless of whether they are attached to an instance. At 5 TB of provisioned gp3 storage, the monthly cost is approximately $40 (at $0.08/GB-month). The team should verify that no data on the volumes is needed, snapshot any volumes that contain important data, and then delete the unattached volumes.** This is a common cost trap because EBS volumes persist independently of EC2 instances. When an instance is terminated, its root volume may be deleted automatically, but additional volumes often remain.
   Further reading: [EBS Pricing](https://aws.amazon.com/ebs/pricing/)

9. **B) Compute Savings Plans**
   Compute Savings Plans apply across EC2, Fargate, and Lambda regardless of instance family, size, OS, tenancy, or Region. This matches the company's requirements: they use all three compute services, expect to change instance types, and may shift workloads between Regions. EC2 Instance Savings Plans (A) offer a higher discount but are locked to a specific instance family and Region, which conflicts with the company's flexibility needs. Reserved Instances (D) do not cover Fargate or Lambda.
   Further reading: [Savings Plans vs Reserved Instances](https://docs.aws.amazon.com/savingsplans/latest/userguide/sp-ris.html)

10. **B) Add an earlier alert threshold and configure a budget action**
    Adding a 50% threshold provides earlier warning, and configuring a budget action at 80% automatically applies a restrictive IAM policy that prevents new resource creation. This combination provides both early visibility and automated enforcement. Increasing the threshold (A) hides the problem. SMS alerts (C) are marginally faster but do not prevent spending. Removing the budget (D) eliminates all visibility.
    Further reading: [AWS Budgets Actions](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-action-configure.html)

</details>

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
