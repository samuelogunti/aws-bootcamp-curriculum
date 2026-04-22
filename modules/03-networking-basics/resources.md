# Module 03: Resources

## Official Documentation

### VPC Overview and Fundamentals

- [What Is Amazon VPC?](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)
- [How Amazon VPC Works](https://docs.aws.amazon.com/vpc/latest/userguide/how-it-works.html)
- [Create a VPC](https://docs.aws.amazon.com/vpc/latest/userguide/create-vpc.html)
- [Default VPC Components](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc-components.html)

### CIDR Blocks and IP Addressing

- [VPC CIDR Blocks](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-cidr-blocks.html)

### Subnets

- [Subnets for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html)
- [Subnet CIDR Blocks (Subnet Sizing)](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html)

### Internet Gateways

- [Internet Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)
- [Add Internet Access to a Subnet (Working with Internet Gateways)](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-igw.html)

### NAT Gateways

- [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)
- [NAT Gateway Use Cases](https://docs.aws.amazon.com/vpc/latest/userguide/nat-gateway-scenarios.html)

### Route Tables

- [Configure Route Tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)
- [Create a Route Table for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/create-vpc-route-table.html)

### Security Groups

- [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)
- [Default Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/default-security-group.html)

### Network Access Control Lists (NACLs)

- [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html)

### VPC Quotas

- [Amazon VPC Quotas](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html)

### Elastic IP Addresses

- [Elastic IP Addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)

### EC2 Instance Connectivity (Used in Lab)

- [Connect to a Linux Instance Using EC2 Instance Connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-eic.html)
- [EC2 Instance Connect Connection Methods](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-connect-methods.html)
- [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)

### AWS Global Infrastructure (Referenced from Module 01)

- [AWS Regions and Availability Zones](https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions-availability-zones.html)
- [Regions and Zones (Amazon EC2 User Guide)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)

### IAM (Referenced from Module 02)

- [Policies and Permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)
- [Security Best Practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

## AWS Whitepapers

Module 03 focuses on VPC networking fundamentals using the Amazon VPC User Guide as the primary reference. There are no AWS whitepapers dedicated specifically to introductory VPC networking. For broader networking architecture guidance, see the following:

- [Building a Scalable and Secure Multi-VPC AWS Network Infrastructure](https://docs.aws.amazon.com/whitepapers/latest/building-scalable-secure-multi-vpc-network-infrastructure/welcome.html): Covers advanced multi-VPC networking patterns including centralized egress, transit gateways, and VPC peering. This whitepaper goes beyond Module 03 scope but provides useful context for students who want to explore VPC networking in depth.

## AWS FAQs

- [Amazon VPC FAQ](https://aws.amazon.com/vpc/faqs/)

## AWS Architecture References

No specific architecture references for this module. The two-tier and three-tier VPC architecture patterns covered in the lesson content are foundational patterns that appear throughout the AWS Architecture Center. Students will encounter these patterns in practice during later modules when deploying multi-tier applications with load balancers (Module 07), databases (Module 06), and containers (Module 10).

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
