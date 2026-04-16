# Module 07: Resources

## Official Documentation

### Elastic Load Balancing Overview

- [What Is Elastic Load Balancing?](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/what-is-load-balancing.html)
- [How Elastic Load Balancing Works](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html)

### Application Load Balancer

- [What Is an Application Load Balancer?](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)
- [Create an HTTPS Listener for Your Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html)
- [Listener Rules for Your Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-rules.html)
- [Target Groups for Your Application Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)
- [Health Checks for Your Target Groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html)
- [SSL Certificates for Your Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/https-listener-certificates.html)

### Network Load Balancer

- [What Is a Network Load Balancer?](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html)

### Gateway Load Balancer

- [What Is a Gateway Load Balancer?](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html)

### AWS Certificate Manager (ACM)

- [What Is AWS Certificate Manager?](https://docs.aws.amazon.com/acm/latest/userguide/acm-overview.html)
- [DNS Validation](https://docs.aws.amazon.com/acm/latest/userguide/dns-validation.html)

### Amazon Route 53 Overview

- [What Is Amazon Route 53?](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html)
- [Working with Hosted Zones](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-working-with.html)
- [Working with Private Hosted Zones](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html)

### Route 53 Record Types and Routing

- [Supported DNS Record Types](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html)
- [Choosing Between Alias and Non-Alias Records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html)
- [Choosing a Routing Policy](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html)
- [Routing Traffic to an ELB Load Balancer](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-to-elb-load-balancer.html)

### Route 53 Health Checks and Failover

- [Creating Amazon Route 53 Health Checks](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover.html)
- [Configuring DNS Failover](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-configuring.html)

### Amazon EC2 Auto Scaling (Referenced from Module 04)

- [What Is Amazon EC2 Auto Scaling?](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
- [Use Elastic Load Balancing to Distribute Traffic in Your Auto Scaling Group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/autoscaling-load-balancer.html)

### Networking (Referenced from Module 03)

- [Default VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html)
- [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)

### Amazon EC2 (Referenced from Module 04)

- [Amazon EC2 Concepts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html)
- [User Data Scripts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)
- [Configuring the Instance Metadata Service](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html)

## AWS Whitepapers

- [Reliability Pillar, AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html): Covers best practices for building reliable architectures, including load balancing, health checks, and multi-AZ deployments. Students will explore this pillar in depth in Module 16 (Reliability and Disaster Recovery) and Module 17 (Well-Architected Framework).

## AWS FAQs

- [Elastic Load Balancing FAQ](https://aws.amazon.com/elasticloadbalancing/faqs/)
- [Amazon Route 53 FAQ](https://aws.amazon.com/route53/faqs/)

## AWS Architecture References

No specific architecture references for this module. Elastic Load Balancing and Amazon Route 53 are foundational networking services that appear in most AWS reference architectures. Students will work with load-balanced, DNS-routed architectures in later modules when they build serverless applications (Module 09), deploy containerized workloads (Module 10), and design multi-tier architecture patterns (Module 18).
