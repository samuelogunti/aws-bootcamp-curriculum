# Phase 1 Exam: Cloud Foundations

## Exam Information

| Field | Details |
|-------|---------|
| Phase | Phase 1: Cloud Foundations |
| Modules Covered | Module 01 (Cloud Fundamentals), Module 02 (IAM and Security), Module 03 (Networking Basics) |
| Estimated Duration | 60 to 90 minutes |
| Passing Score | 70% |
| Total Questions | 25 |
| Question Types | Multiple choice (single correct), multiple choice (multiple correct), scenario-based, ordering/sequencing |

> **Tip:** Read each question carefully. For questions that say "select TWO" or "select THREE," you must choose the exact number of answers specified. Partial credit is not awarded.

---

## Questions

**Question 1**

Which of the following best describes the difference between authentication and authorization in AWS Identity and Access Management (IAM)?

A. Authentication determines which AWS services a user can access; authorization verifies the user's identity.

B. Authentication verifies a user's identity through credentials; authorization determines what actions the authenticated user is allowed to perform.

C. Authentication and authorization are the same process in IAM; both verify the user's identity and permissions simultaneously.

D. Authentication applies only to the root user; authorization applies to all IAM users and roles.

---

**Question 2**

A company is evaluating whether to migrate from on-premises infrastructure to AWS. The Chief Financial Officer wants to understand the financial impact. Which statement accurately describes the spending model shift when moving to the cloud?

A. Cloud computing replaces Operational Expenditure (OpEx) with Capital Expenditure (CapEx) because you purchase reserved capacity upfront.

B. Cloud computing replaces Capital Expenditure (CapEx) with Operational Expenditure (OpEx) because you pay for resources on a pay-as-you-go basis.

C. Cloud computing eliminates all IT spending because AWS provides services at no cost through the Free Tier.

D. Cloud computing requires the same CapEx as on-premises because you must purchase dedicated hardware in AWS data centers.

---

**Question 3**

A solutions architect needs to ensure that an Amazon EC2 instance in a private subnet can download software updates from the internet, but the instance must not be directly reachable from the internet. Which combination of components enables this? (Select TWO.)

A. Attach an internet gateway to the VPC and add a route from the private subnet to the internet gateway.

B. Place a NAT gateway in a public subnet and assign it an Elastic IP address.

C. Add a route in the private subnet's route table that directs `0.0.0.0/0` traffic to the NAT gateway.

D. Assign a public IPv4 address to the EC2 instance in the private subnet.

E. Create a security group that denies all outbound traffic from the private subnet.

---

**Question 4**

Which of the following are essential characteristics of cloud computing as defined by the National Institute of Standards and Technology (NIST)? (Select THREE.)

A. On-demand self-service

B. Dedicated single-tenant hardware

C. Broad network access

D. Fixed pricing with annual contracts

E. Rapid elasticity

F. Manual capacity provisioning

---

**Question 5**

An organization uses AWS Organizations to manage multiple AWS accounts. The security team attaches a Service Control Policy (SCP) to the Production organizational unit (OU) that denies the `s3:DeleteBucket` action. A user in a Production account has an IAM policy that grants `s3:*` (full S3 access). What happens when the user attempts to delete an S3 bucket?

A. The request succeeds because the IAM policy explicitly allows `s3:*`, which overrides the SCP.

B. The request is denied because the SCP restricts the maximum permissions available, and the deny in the SCP takes precedence over the IAM allow.

C. The request succeeds because SCPs only affect the management account, not member accounts.

D. The request is denied initially, but the user can override the SCP by assuming an administrator role within the account.

---

**Question 6**

A startup is designing a web application on AWS. The application has a public-facing web tier and a backend database. The team wants to follow security best practices by limiting network exposure. Which VPC design pattern is most appropriate for this workload?

A. Place both the web servers and the database in public subnets so they can communicate over the internet.

B. Place the web servers in public subnets and the database in private subnets, with the database security group allowing inbound traffic only from the web server security group.

C. Place both the web servers and the database in private subnets and use a NAT gateway for all traffic.

D. Place the web servers in private subnets and the database in public subnets so the database can receive backups from the internet.

---

**Question 7**

What is the primary purpose of an AWS Availability Zone (AZ)?

A. To cache content closer to end users for low-latency delivery

B. To provide a separate geographic region for data sovereignty compliance

C. To provide one or more isolated data centers within a Region for fault tolerance and high availability

D. To host edge computing workloads for mobile applications

---

**Question 8**

A developer needs to grant an Amazon EC2 instance permission to read objects from an Amazon S3 bucket. Which approach follows IAM security best practices?

A. Create an IAM user with S3 read permissions and embed the user's access keys in the application code running on the EC2 instance.

B. Create an IAM role with S3 read permissions and attach it to the EC2 instance as an instance profile. The instance receives temporary credentials automatically.

C. Use the root user's access keys on the EC2 instance because the root user has unrestricted access to all services.

D. Create an inline policy on the EC2 instance's operating system that grants S3 read access.

---

**Question 9**

A cloud engineer is reviewing the following IAM policy. What does this policy allow?

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::reports-bucket",
                "arn:aws:s3:::reports-bucket/*"
            ],
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": "10.0.0.0/16"
                }
            }
        }
    ]
}
```

A. It allows listing and reading objects in any S3 bucket, but only from the IP range `10.0.0.0/16`.

B. It allows listing the `reports-bucket` and reading objects within it, but only when the request originates from the IP range `10.0.0.0/16`.

C. It denies all S3 access unless the request comes from the IP range `10.0.0.0/16`.

D. It allows full S3 access (read, write, delete) to the `reports-bucket` from any IP address.

---

**Question 10**

Which of the following statements correctly describes the AWS Shared Responsibility Model? (Select TWO.)

A. AWS is responsible for patching the operating system on Amazon EC2 instances.

B. The customer is responsible for configuring security groups and network access control lists (NACLs) in a VPC.

C. AWS is responsible for the physical security of data centers, including power, cooling, and environmental controls.

D. The customer is responsible for maintaining the hypervisor and virtualization layer.

E. AWS is responsible for encrypting all customer data by default without any customer configuration.

---

**Question 11**

A network engineer is troubleshooting connectivity for an EC2 instance in a public subnet. The instance has a public IP address and the VPC has an internet gateway attached. The instance can send traffic to the internet, but external clients cannot reach the instance on port 443 (HTTPS). Which is the most likely cause?

A. The instance's security group does not have an inbound rule allowing TCP port 443.

B. The route table for the public subnet is missing a route to the internet gateway.

C. The NAT gateway is blocking inbound traffic to the instance.

D. The instance needs an Elastic IP address instead of an auto-assigned public IP address.

---

**Question 12**

Which of the following correctly describes the relationship between AWS Regions, Availability Zones, and subnets in a VPC?

A. A VPC spans multiple Regions, and each subnet spans multiple Availability Zones within those Regions.

B. A VPC exists within a single Region and can span multiple Availability Zones. Each subnet resides in a single Availability Zone.

C. A VPC exists within a single Availability Zone, and subnets are used to span the VPC across multiple Regions.

D. A VPC and its subnets are global resources that are not tied to any specific Region or Availability Zone.

---

**Question 13**

A security auditor is reviewing an AWS account and finds that the root user has active access keys and no Multi-Factor Authentication (MFA) enabled. Which actions should the auditor recommend? (Select TWO.)

A. Enable MFA on the root user immediately.

B. Delete the root user's access keys and use IAM users or roles for programmatic access.

C. Create a second root user account as a backup in case the first is compromised.

D. Attach an IAM policy to the root user to restrict its permissions to read-only access.

E. Share the root user credentials with the operations team so they can respond to emergencies.

---

**Question 14**

A company is deploying a three-tier web application on AWS. The architecture includes a load balancer, application servers, and a database. The security team requires that each tier can only communicate with the tier directly above or below it. How should the security groups be configured?

A. Create one security group for all three tiers and allow all traffic between resources in the same security group.

B. Create separate security groups for each tier. The load balancer security group allows inbound HTTP/HTTPS from the internet. The application tier security group allows inbound traffic only from the load balancer security group. The database security group allows inbound traffic only from the application tier security group.

C. Use network ACLs instead of security groups because NACLs support deny rules, which are required for tier isolation.

D. Place all three tiers in the same public subnet and use operating system firewalls to control traffic between tiers.

---

**Question 15**

What is the key difference between security groups and network access control lists (NACLs) in a VPC?

A. Security groups are stateless and operate at the subnet level; NACLs are stateful and operate at the instance level.

B. Security groups are stateful and support only allow rules; NACLs are stateless and support both allow and deny rules.

C. Security groups and NACLs are identical in functionality but differ only in name.

D. Security groups apply to traffic between Regions; NACLs apply to traffic within a single Availability Zone.

---

**Question 16**

A junior developer asks why they should use customer managed policies instead of inline policies for granting permissions to multiple IAM users. Which explanation is correct?

A. Inline policies can be attached to multiple users simultaneously, making them more efficient than customer managed policies.

B. Customer managed policies are standalone objects that can be attached to multiple users, groups, or roles, and they support versioning. Inline policies are embedded in a single identity and cannot be reused.

C. Customer managed policies are automatically updated by AWS when new services are released, while inline policies require manual updates.

D. Inline policies provide stronger security because they are encrypted at rest, while customer managed policies are stored in plain text.

---

**Question 17**

A company wants to deploy a production application that remains available even if a single data center experiences a failure. Which AWS infrastructure design best supports this requirement?

A. Deploy the application in a single Availability Zone within a Region and use Amazon CloudFront for redundancy.

B. Deploy the application across multiple Availability Zones within a single Region.

C. Deploy the application to a single edge location for low-latency access.

D. Deploy the application in a single Availability Zone and create daily backups to Amazon S3.

---

**Question 18**

An organization is planning its VPC network design. The VPC will use the CIDR block `10.0.0.0/16`. The team needs to create subnets for two Availability Zones, each with a public and a private subnet. Which of the following subnet CIDR allocations is valid for this VPC?

A. Public Subnet AZ-a: `10.0.1.0/24`, Private Subnet AZ-a: `10.0.2.0/24`, Public Subnet AZ-b: `10.0.3.0/24`, Private Subnet AZ-b: `10.0.4.0/24`

B. Public Subnet AZ-a: `172.16.1.0/24`, Private Subnet AZ-a: `172.16.2.0/24`, Public Subnet AZ-b: `172.16.3.0/24`, Private Subnet AZ-b: `172.16.4.0/24`

C. Public Subnet AZ-a: `10.0.0.0/8`, Private Subnet AZ-a: `10.0.1.0/8`, Public Subnet AZ-b: `10.0.2.0/8`, Private Subnet AZ-b: `10.0.3.0/8`

D. Public Subnet AZ-a: `192.168.1.0/24`, Private Subnet AZ-a: `192.168.2.0/24`, Public Subnet AZ-b: `10.0.3.0/24`, Private Subnet AZ-b: `10.0.4.0/24`

---

**Question 19**

In the IAM policy evaluation logic, what is the result when an IAM policy explicitly allows an action but a separate policy attached to the same user explicitly denies the same action?

A. The allow takes precedence because it was evaluated first.

B. The request is allowed because explicit allows always override explicit denies.

C. The request is denied because an explicit deny in any policy always overrides any allow.

D. The result depends on which policy was attached to the user most recently.

---

**Question 20**

A systems administrator needs to allow instances in a private subnet to access the internet for downloading patches, while preventing any inbound connections from the internet. The administrator also needs to ensure high availability. What is the recommended approach?

A. Attach an internet gateway directly to the private subnet and configure the route table to use it.

B. Create a single NAT gateway in one public subnet and route all private subnet traffic through it.

C. Create a NAT gateway in a public subnet in each Availability Zone and configure each private subnet to route through the NAT gateway in its own AZ.

D. Assign public IP addresses to all instances in the private subnet so they can access the internet directly.

---

**Question 21**

Which of the following is an example of a Platform as a Service (PaaS) offering on AWS?

A. Amazon EC2, where you manage the operating system, runtime, and application

B. AWS Elastic Beanstalk, where you upload your code and AWS handles capacity provisioning, load balancing, and scaling

C. Amazon Connect, where AWS manages the entire application and you only configure settings

D. Amazon S3, where you store and retrieve objects using API calls

---

**Question 22**

A company has a VPC with a public subnet and a private subnet. An EC2 instance in the private subnet needs to communicate with an EC2 instance in the public subnet within the same VPC. Which statement is correct about this communication?

A. The instances cannot communicate because they are in different subnet types (public and private).

B. The instances can communicate using their private IP addresses because the VPC route table includes a local route that enables traffic within the VPC.

C. The instances can communicate only if a NAT gateway is placed between the two subnets.

D. The instances can communicate only if an internet gateway route is added to the private subnet's route table.

---

**Question 23**

Place the following steps in the correct order for how IAM evaluates a request when multiple policies apply to a principal.

1. An explicit allow in an identity-based policy overrides the implicit deny.
2. All requests start with an implicit deny.
3. An explicit deny in any policy overrides any allow.

A. 1, 2, 3

B. 3, 1, 2

C. 2, 1, 3

D. 2, 3, 1

---

**Question 24**

A company is migrating to AWS and wants to understand the Shared Responsibility Model as it applies to IAM and VPC security. The security team asks: "Who is responsible for configuring IAM policies, and who is responsible for ensuring the physical network infrastructure is secure?" Which answer correctly assigns these responsibilities?

A. The customer is responsible for both configuring IAM policies and securing the physical network infrastructure.

B. AWS is responsible for both configuring IAM policies and securing the physical network infrastructure.

C. The customer is responsible for configuring IAM policies (security "in" the cloud). AWS is responsible for securing the physical network infrastructure (security "of" the cloud).

D. AWS is responsible for configuring IAM policies because IAM is an AWS managed service. The customer is responsible for physical network security because they choose the Region.

---

**Question 25**

A cloud architect is designing a secure architecture for a web application. The architect wants to implement layered security using both IAM and VPC controls. Which combination correctly describes how IAM policies and security groups work together to protect an EC2 instance running a web application? (Select TWO.)

A. IAM policies control which users and roles can perform API actions on the EC2 instance, such as starting, stopping, or terminating it.

B. Security groups control the network traffic that can reach the EC2 instance, such as allowing inbound HTTP traffic on port 80.

C. IAM policies replace security groups for controlling network traffic to EC2 instances.

D. Security groups determine which IAM users can log in to the EC2 instance's operating system.

E. IAM policies and security groups are mutually exclusive; you can use one or the other but not both.

---

<details>
<summary>Answer Key</summary>

### Question 1

**Correct Answer: B**

Authentication verifies a user's identity through credentials (such as a username and password or access keys). Authorization determines what actions the authenticated user is allowed to perform, based on the policies attached to that identity. A user can be authenticated (successfully signed in) but not authorized to perform a specific action if no policy grants permission.

- A is incorrect because it reverses the definitions. Authentication verifies identity; authorization determines access.
- C is incorrect because authentication and authorization are distinct processes in IAM. Authentication happens first, then authorization evaluates policies.
- D is incorrect because both authentication and authorization apply to all IAM identities (users, roles, federated users), not just the root user.

Reference: [IAM Introduction](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)

---

### Question 2

**Correct Answer: B**

Cloud computing shifts IT spending from Capital Expenditure (CapEx), which involves large upfront investments in physical infrastructure, to Operational Expenditure (OpEx), where you pay for resources as you consume them. AWS describes this as "trade fixed expense for variable expense," one of the six advantages of cloud computing.

- A is incorrect because it reverses the direction of the shift. Cloud computing moves from CapEx to OpEx, not the other way around.
- C is incorrect because the AWS Free Tier has usage limits and does not eliminate all costs. Many services incur charges beyond Free Tier limits.
- D is incorrect because AWS customers do not purchase dedicated hardware in AWS data centers under the standard cloud model. AWS owns and maintains the physical infrastructure.

Reference: [Six Advantages of Cloud Computing](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/six-advantages-of-cloud-computing.html)

---

### Question 3

**Correct Answers: B, C**

To allow a private subnet instance to reach the internet without being directly reachable, you need a NAT gateway in a public subnet with an Elastic IP (B) and a route in the private subnet's route table pointing `0.0.0.0/0` to that NAT gateway (C). The NAT gateway forwards outbound traffic to the internet gateway using its own public IP, and return traffic is routed back through the NAT gateway.

- A is incorrect because adding a route from the private subnet directly to the internet gateway would make it a public subnet, exposing instances to inbound internet traffic.
- D is incorrect because assigning a public IP to an instance in a private subnet does not enable internet access without a route to an internet gateway, and it contradicts the goal of keeping the instance unreachable from the internet.
- E is incorrect because denying all outbound traffic would prevent the instance from downloading updates, which is the opposite of the requirement.

Reference: [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)

---

### Question 4

**Correct Answers: A, C, E**

The NIST definition of cloud computing identifies five essential characteristics: on-demand self-service (A), broad network access (C), resource pooling, rapid elasticity (E), and measured service. These characteristics distinguish cloud computing from traditional IT infrastructure.

- B is incorrect because cloud computing uses a multi-tenant model with resource pooling, not dedicated single-tenant hardware.
- D is incorrect because cloud computing uses pay-as-you-go (measured service) pricing, not fixed annual contracts.
- F is incorrect because cloud computing provides rapid elasticity with automatic or on-demand provisioning, not manual capacity provisioning.

Reference: [What Is Cloud Computing?](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/what-is-cloud-computing.html)

---

### Question 5

**Correct Answer: B**

Service Control Policies (SCPs) set the maximum permissions boundary for all users and roles in member accounts. Even though the IAM policy grants `s3:*`, the SCP denying `s3:DeleteBucket` restricts what is possible. An action must be allowed by both the SCP and the IAM policy for the request to succeed. Since the SCP denies the action, the request is denied.

- A is incorrect because IAM policies cannot override SCPs. SCPs define the upper boundary of permissions, and IAM policies operate within that boundary.
- C is incorrect because SCPs affect all member accounts in the organization. It is the management account that is not affected by SCPs.
- D is incorrect because no IAM role within a member account can override an SCP. SCPs apply to all users and roles in the account, including administrators.

Reference: [Service Control Policies (SCPs)](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html)

---

### Question 6

**Correct Answer: B**

A two-tier architecture places web servers in public subnets (accessible from the internet) and databases in private subnets (no direct internet access). The database security group allows inbound traffic only from the web server security group, following the principle of least privilege for network access.

- A is incorrect because placing the database in a public subnet exposes it to the internet, increasing the attack surface. Databases should be in private subnets.
- C is incorrect because placing web servers in private subnets would prevent them from receiving inbound traffic from the internet, which is required for a public-facing web application.
- D is incorrect because placing the database in a public subnet and web servers in private subnets reverses the correct architecture. Databases do not need internet access for backups; AWS backup services operate within the AWS network.

Reference: [Amazon VPC: How It Works](https://docs.aws.amazon.com/vpc/latest/userguide/how-it-works.html)

---

### Question 7

**Correct Answer: C**

An Availability Zone (AZ) consists of one or more discrete data centers within a Region, each with redundant power, networking, and connectivity. AZs are physically separated within a Region to protect against localized failures, providing fault tolerance and high availability.

- A is incorrect because it describes edge locations, which are part of the Amazon CloudFront content delivery network.
- B is incorrect because it describes an AWS Region, not an Availability Zone. Regions are separate geographic areas; AZs are isolated data centers within a Region.
- D is incorrect because edge computing for mobile applications is associated with AWS Wavelength Zones, not Availability Zones.

Reference: [AWS Regions and Availability Zones](https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions-availability-zones.html)

---

### Question 8

**Correct Answer: B**

IAM roles provide temporary security credentials to AWS services without requiring long-term access keys. By attaching a role with S3 read permissions to the EC2 instance as an instance profile, the instance automatically receives temporary credentials that are rotated by AWS. This eliminates the risk of exposed or leaked access keys.

- A is incorrect because embedding access keys in application code is a security risk. If the code is committed to version control or the instance is compromised, the keys are exposed. IAM best practices recommend using roles instead of access keys.
- C is incorrect because using the root user's access keys violates IAM best practices. The root user should never have access keys, and its credentials should not be used for application access.
- D is incorrect because IAM policies are AWS constructs managed through the IAM service, not operating system-level configurations. You cannot create an IAM inline policy on an EC2 instance's operating system.

Reference: [IAM Security Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

---

### Question 9

**Correct Answer: B**

The policy allows two specific actions (`s3:GetObject` and `s3:ListBucket`) on two specific resources (the `reports-bucket` and all objects within it), but only when the request originates from the IP range `10.0.0.0/16`. The Condition element restricts when the policy statement is in effect.

- A is incorrect because the Resource element specifies only `reports-bucket` and its contents, not "any S3 bucket." The policy is scoped to a single bucket.
- C is incorrect because the policy uses `"Effect": "Allow"`, not `"Deny"`. It grants access under the specified conditions rather than denying access.
- D is incorrect because the Action element lists only `s3:GetObject` and `s3:ListBucket` (read operations), not full S3 access. Write and delete actions are not included.

Reference: [IAM JSON Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html)

---

### Question 10

**Correct Answers: B, C**

Under the Shared Responsibility Model, the customer is responsible for configuring security groups and NACLs (security "in" the cloud), and AWS is responsible for the physical security of data centers (security "of" the cloud). The customer controls how they configure AWS services; AWS secures the underlying infrastructure.

- A is incorrect because the customer is responsible for patching the operating system on EC2 instances. EC2 is an IaaS service, so the customer manages the OS. AWS patches the OS only for managed services like RDS or Lambda.
- D is incorrect because AWS is responsible for maintaining the hypervisor and virtualization layer. This falls under AWS's "security of the cloud" responsibility.
- E is incorrect because AWS does not encrypt all customer data by default. The customer is responsible for configuring encryption at rest and in transit for their data.

Reference: [Shared Responsibility Model](https://docs.aws.amazon.com/whitepapers/latest/aws-risk-and-compliance/shared-responsibility-model.html)

---

### Question 11

**Correct Answer: A**

Security groups act as a virtual firewall at the resource level. If the security group attached to the EC2 instance does not have an inbound rule allowing TCP port 443, external clients cannot reach the instance on that port, even though the instance has a public IP and the route table is correctly configured. Security groups deny all inbound traffic by default unless an allow rule is explicitly added.

- B is incorrect because the question states the instance can send traffic to the internet, which means the route table already has a route to the internet gateway. If the route were missing, outbound traffic would also fail.
- C is incorrect because NAT gateways are used for private subnet instances. The instance is in a public subnet and communicates through the internet gateway, not a NAT gateway.
- D is incorrect because both auto-assigned public IPs and Elastic IPs enable internet communication. The type of public IP does not affect whether inbound traffic is allowed; that is controlled by security groups.

Reference: [VPC Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)

---

### Question 12

**Correct Answer: B**

A VPC exists within a single AWS Region but can span multiple Availability Zones within that Region. Each subnet resides in exactly one Availability Zone and cannot span multiple AZs. This design allows you to deploy resources across AZs for high availability while keeping them within the same VPC.

- A is incorrect because a VPC cannot span multiple Regions. A VPC is a regional resource. To operate in multiple Regions, you create separate VPCs in each Region.
- C is incorrect because a VPC spans a Region (which contains multiple AZs), not a single AZ. Subnets are the components that reside in individual AZs.
- D is incorrect because VPCs and subnets are regional and AZ-specific resources, respectively. They are not global resources.

Reference: [Regions and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)

---

### Question 13

**Correct Answers: A, B**

Enabling MFA on the root user (A) adds a second authentication factor, protecting against password compromise. Deleting the root user's access keys (B) eliminates the risk of programmatic access through long-term credentials. IAM best practices state that the root user should have MFA enabled, should not have access keys, and should be used only for tasks that specifically require root access.

- C is incorrect because AWS accounts have exactly one root user. You cannot create a second root user. Instead, create IAM users or use IAM Identity Center for daily access.
- D is incorrect because you cannot attach IAM policies to the root user to restrict its permissions. The root user always has unrestricted access to the account. The only way to limit root user actions is through SCPs in AWS Organizations (which affect member accounts, not the management account's root user).
- E is incorrect because sharing root user credentials violates IAM best practices. Root credentials should be secured and used only for emergency tasks that require root access.

Reference: [Security Best Practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

---

### Question 14

**Correct Answer: B**

Creating separate security groups for each tier and configuring inbound rules to reference the security group of the tier above implements layered security. The load balancer accepts internet traffic, the application tier accepts traffic only from the load balancer, and the database accepts traffic only from the application tier. Security group referencing automatically adapts as instances are added or removed.

- A is incorrect because a single security group for all tiers would allow unrestricted communication between all resources, violating the requirement that each tier communicates only with adjacent tiers.
- C is incorrect because while NACLs support deny rules, security groups are the recommended primary firewall for tier isolation. Security groups operate at the resource level and are stateful, making them easier to manage for this use case. NACLs are a supplementary defense layer.
- D is incorrect because placing all tiers in a public subnet exposes the database and application servers to the internet. Operating system firewalls are not a substitute for VPC-level network controls.

Reference: [VPC Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)

---

### Question 15

**Correct Answer: B**

Security groups are stateful: if you allow an inbound request, the response is automatically allowed regardless of outbound rules. Security groups support only allow rules; any traffic not matching an allow rule is denied by default. NACLs are stateless: you must explicitly allow both inbound and outbound traffic, including return traffic on ephemeral ports. NACLs support both allow and deny rules, and rules are evaluated in number order.

- A is incorrect because it reverses the characteristics. Security groups are stateful (not stateless) and operate at the resource level (not subnet level). NACLs are stateless (not stateful) and operate at the subnet level (not instance level).
- C is incorrect because security groups and NACLs differ significantly in statefulness, rule types, scope, and evaluation method. They are not identical.
- D is incorrect because both security groups and NACLs operate within a VPC, not between Regions. Security groups apply at the resource level, and NACLs apply at the subnet level, both within the same VPC.

Reference: [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html)

---

### Question 16

**Correct Answer: B**

Customer managed policies are standalone JSON policy objects that can be attached to multiple users, groups, or roles. They support versioning (up to five versions), allowing you to roll back changes. Inline policies are embedded directly in a single identity and have a strict one-to-one relationship. When you need to apply the same permissions to multiple identities, customer managed policies are the appropriate choice.

- A is incorrect because inline policies cannot be attached to multiple users. Each inline policy is embedded in exactly one identity. This is the key limitation that makes customer managed policies preferable for shared permissions.
- C is incorrect because customer managed policies are not automatically updated by AWS. AWS managed policies are updated by AWS. Customer managed policies are created and maintained by the customer.
- D is incorrect because the security of policy storage does not differ between inline and customer managed policies. Both are stored securely by the IAM service. The distinction is about reusability and management, not encryption.

Reference: [Managed Policies and Inline Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html)

---

### Question 17

**Correct Answer: B**

Deploying across multiple Availability Zones within a Region provides fault tolerance against a single data center failure. Each AZ consists of one or more isolated data centers with independent power and networking. If one AZ experiences an outage, resources in other AZs continue to operate.

- A is incorrect because deploying in a single AZ means a data center failure in that AZ would cause downtime. CloudFront is a content delivery network for caching, not a redundancy mechanism for compute workloads.
- C is incorrect because edge locations are for content caching and delivery (CloudFront), not for hosting application workloads. They do not provide compute redundancy.
- D is incorrect because a single-AZ deployment with backups does not provide high availability. Backups help with data recovery but do not prevent downtime during an AZ failure. The application would be unavailable until restored.

Reference: [AWS Regions and Availability Zones](https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions-availability-zones.html)

---

### Question 18

**Correct Answer: A**

All four subnets in option A use CIDR blocks (`10.0.1.0/24` through `10.0.4.0/24`) that fall within the VPC's `10.0.0.0/16` range. Each `/24` subnet provides 256 IP addresses (251 usable after AWS reservations), and the subnets do not overlap.

- B is incorrect because the `172.16.x.x` addresses are outside the VPC's `10.0.0.0/16` CIDR block. Subnet CIDR blocks must be subsets of the VPC's CIDR block.
- C is incorrect because `/8` subnets contain 16,777,216 addresses each, which is far larger than the VPC's `/16` block (65,536 addresses). A subnet cannot be larger than its VPC, and these subnets would overlap.
- D is incorrect because the `192.168.x.x` subnets are outside the VPC's `10.0.0.0/16` CIDR block. While `192.168.0.0/16` is a valid private IP range, it does not fall within this VPC's address space.

Reference: [VPC CIDR Blocks](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-cidr-blocks.html)

---

### Question 19

**Correct Answer: C**

In IAM policy evaluation, an explicit deny always takes precedence over any allow. The evaluation order does not matter. If any policy attached to the user (directly or through group membership) contains an explicit deny for an action, the request is denied regardless of any other policies that allow it.

- A is incorrect because IAM does not evaluate policies in a sequential order where the first match wins. All policies are evaluated, and the most restrictive result applies.
- B is incorrect because it reverses the precedence. Explicit denies always override explicit allows, not the other way around.
- D is incorrect because the order in which policies are attached has no effect on evaluation. IAM evaluates all applicable policies and applies the deny-overrides-allow rule regardless of attachment order.

Reference: [Policy Evaluation Logic](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html)

---

### Question 20

**Correct Answer: C**

Creating a NAT gateway in each Availability Zone and routing each private subnet through its local NAT gateway provides both outbound internet access and high availability. If one AZ's NAT gateway fails, private subnets in other AZs continue to have internet access through their own NAT gateways.

- A is incorrect because you cannot attach an internet gateway directly to a subnet. An internet gateway is attached to the VPC. Adding a route from a private subnet to the internet gateway would make it a public subnet, allowing inbound internet traffic.
- B is incorrect because a single NAT gateway in one AZ creates a single point of failure. If that AZ experiences an outage, all private subnets lose internet access. For high availability, you need a NAT gateway in each AZ.
- D is incorrect because assigning public IPs to instances in a private subnet does not provide internet access without a route to an internet gateway. It also contradicts the requirement to prevent inbound connections from the internet.

Reference: [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)

---

### Question 21

**Correct Answer: B**

AWS Elastic Beanstalk is a PaaS offering. You upload your application code, and Elastic Beanstalk handles capacity provisioning, load balancing, auto-scaling, and application health monitoring. You manage the application and data; AWS manages the underlying infrastructure, operating system, and platform.

- A is incorrect because Amazon EC2 is an Infrastructure as a Service (IaaS) offering. With EC2, you manage the operating system, runtime, and application, while AWS manages the hardware and virtualization layer.
- C is incorrect because Amazon Connect is a Software as a Service (SaaS) offering. AWS manages the entire application; you only configure settings through a web interface.
- D is incorrect because Amazon S3 is an object storage service. While S3 abstracts infrastructure management, it is a storage service rather than a platform for deploying and running application code, which is the defining characteristic of PaaS.

Reference: [Types of Cloud Computing](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/types-of-cloud-computing.html)

---

### Question 22

**Correct Answer: B**

Every VPC has a local route in its route tables that enables communication between all subnets within the VPC. Instances in different subnets (whether public or private) can communicate using their private IP addresses as long as the security groups and NACLs allow the traffic. The subnet type (public or private) affects internet access, not intra-VPC communication.

- A is incorrect because subnet type does not prevent communication within the VPC. The local route enables traffic between all subnets regardless of whether they are public or private.
- C is incorrect because NAT gateways are used for outbound internet access from private subnets, not for communication between subnets within the same VPC.
- D is incorrect because an internet gateway route is needed for internet access, not for intra-VPC communication. The local route handles all traffic within the VPC's CIDR block.

Reference: [Route Table Concepts](https://docs.aws.amazon.com/vpc/latest/userguide/RouteTables.html)

---

### Question 23

**Correct Answer: C**

The correct order of IAM policy evaluation is: (1) all requests start with an implicit deny (step 2), (2) an explicit allow in a policy overrides the implicit deny (step 1), (3) an explicit deny in any policy overrides any allow (step 3). This means the sequence is 2, 1, 3.

- A is incorrect because the process does not start with an explicit allow. It starts with an implicit deny as the default state.
- B is incorrect because the explicit deny is the final override, not the first step. The process begins with an implicit deny.
- D is incorrect because the explicit deny (step 3) is evaluated after the allow (step 1), not before it. The deny overrides the allow as the final determination, but the evaluation considers allows before applying the deny override.

Reference: [Policy Evaluation Logic](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html)

---

### Question 24

**Correct Answer: C**

Under the Shared Responsibility Model, the customer is responsible for security "in" the cloud, which includes configuring IAM policies, security groups, NACLs, and encryption. AWS is responsible for security "of" the cloud, which includes the physical infrastructure such as data centers, networking hardware, and the virtualization layer.

- A is incorrect because the customer is not responsible for physical network infrastructure security. AWS manages all physical infrastructure, including data center security, power, cooling, and networking hardware.
- B is incorrect because AWS does not configure IAM policies for customers. IAM policy configuration is the customer's responsibility. AWS provides the IAM service, but the customer decides what permissions to grant.
- D is incorrect because IAM is a customer-configured service, not an AWS-managed configuration. While AWS manages the IAM service infrastructure, the customer is responsible for creating and managing IAM policies, users, and roles. Region selection does not make the customer responsible for physical network security.

Reference: [Shared Responsibility Model](https://docs.aws.amazon.com/whitepapers/latest/aws-risk-and-compliance/shared-responsibility-model.html)

---

### Question 25

**Correct Answers: A, B**

IAM policies and security groups serve complementary but distinct purposes. IAM policies control API-level access, determining which users and roles can perform actions like `ec2:StartInstances` or `ec2:TerminateInstances` (A). Security groups control network-level access, determining which traffic can reach the instance on specific ports and protocols (B). Both layers work together to provide defense in depth.

- C is incorrect because IAM policies do not replace security groups. IAM controls API actions (who can manage the instance), while security groups control network traffic (what traffic can reach the instance). They operate at different layers.
- D is incorrect because security groups do not control IAM user authentication or operating system login. Security groups filter network traffic by IP address, port, and protocol. OS-level access is managed through SSH keys, passwords, or AWS Systems Manager.
- E is incorrect because IAM policies and security groups are not mutually exclusive. They are designed to work together as complementary security layers. Using both is a best practice for defense in depth.

Reference: [IAM Introduction](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)

</details>

---

## Study Guide

If you scored below 70%, review the following topics organized by module before retaking the exam.

### Module 01: Cloud Fundamentals

- Cloud computing definition and the five NIST essential characteristics (on-demand self-service, broad network access, resource pooling, rapid elasticity, measured service)
- Capital Expenditure (CapEx) vs. Operational Expenditure (OpEx) and how cloud computing shifts spending from CapEx to OpEx
- Cloud service models: IaaS, PaaS, and SaaS, and what you manage vs. what the provider manages in each model
- AWS global infrastructure: Regions, Availability Zones, and edge locations, and the purpose of each
- The Shared Responsibility Model: security "of" the cloud (AWS) vs. security "in" the cloud (customer)
- Reference: [Overview of Amazon Web Services](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/what-is-cloud-computing.html)

### Module 02: IAM and Security

- Authentication (verifying identity) vs. authorization (granting permissions) and how IAM handles both
- IAM identities: users, user groups, and roles, and when to use each
- Managed policies vs. inline policies: reusability, versioning, and use cases
- IAM policy JSON structure: Effect, Action, Resource, and Condition elements
- Policy evaluation logic: implicit deny, explicit allow, explicit deny (deny always wins)
- Service Control Policies (SCPs) in AWS Organizations: how they set maximum permission boundaries
- IAM best practices: enable MFA, protect the root user, use roles instead of access keys, apply least privilege
- Reference: [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)

### Module 03: Networking Basics

- VPC fundamentals: what a VPC is, default VPC vs. custom VPC, and how VPCs relate to Regions and Availability Zones
- CIDR blocks: how prefix length determines network size, valid private IP ranges, and subnet sizing (including the five AWS-reserved addresses per subnet)
- Public subnets vs. private subnets: what makes a subnet public (route to internet gateway) and what belongs in each type
- Internet gateway vs. NAT gateway: direction of traffic, placement, and use cases
- Route tables: main vs. custom route tables, how route evaluation works (longest prefix match)
- Security groups vs. NACLs: stateful vs. stateless, allow-only vs. allow-and-deny, resource-level vs. subnet-level
- VPC design patterns: two-tier and three-tier architectures, and how security groups enforce tier isolation
- Reference: [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
