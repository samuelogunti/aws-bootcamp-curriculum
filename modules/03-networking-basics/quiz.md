# Module 03: Quiz

1. Which of the following best describes an Amazon Virtual Private Cloud (VPC)?

   A) A physical data center that AWS provisions for a single customer
   B) A logically isolated virtual network that you define within your AWS account
   C) A global network that spans all AWS Regions automatically
   D) A DNS service that routes traffic to AWS resources

2. True or False: The default VPC that AWS creates in each Region comes with an internet gateway already attached and a default subnet in each Availability Zone.

3. A VPC has the CIDR block `10.0.0.0/16`. How many total IP addresses does this CIDR block provide?

   A) 256
   B) 4,096
   C) 65,536
   D) 16,777,216

4. What makes a subnet a "public" subnet in a VPC?

   A) The subnet has a larger CIDR block than other subnets
   B) The subnet's route table includes a route to an internet gateway
   C) The subnet is located in the first Availability Zone of the Region
   D) The subnet has a network ACL that allows all inbound traffic

5. AWS reserves five IP addresses in every subnet. In a `/24` subnet with 256 total addresses, how many addresses are available for your resources?

   A) 254
   B) 253
   C) 251
   D) 250

6. Which of the following correctly describes the difference between an internet gateway and a NAT gateway?

   A) An internet gateway supports outbound traffic only; a NAT gateway supports both inbound and outbound traffic
   B) An internet gateway is attached to a VPC and supports both inbound and outbound traffic; a NAT gateway is placed in a public subnet and supports outbound traffic only from private subnets
   C) An internet gateway is placed in a private subnet; a NAT gateway is placed in a public subnet
   D) An internet gateway and a NAT gateway serve the same purpose but differ only in pricing

7. A VPC route table contains the following two routes:

   | Destination | Target |
   |-------------|--------|
   | `10.0.0.0/16` | local |
   | `0.0.0.0/0` | igw-xxxxxxxx |

   A resource in this VPC sends traffic to the IP address `10.0.3.25`. Which route does the VPC router select, and why?

8. Which of the following are characteristics of security groups? (Select TWO.)

   A) They are stateless, so you must explicitly allow return traffic
   B) They operate at the resource level (such as an EC2 instance)
   C) They support both allow and deny rules
   D) They are stateful, so return traffic is automatically allowed
   E) They are associated with subnets, not individual resources

9. True or False: Network access control lists (NACLs) evaluate all rules at once before making a decision, just like security groups.

10. In a three-tier VPC architecture, which tier is typically placed in public subnets?

    A) The data tier (databases)
    B) The application tier (application servers)
    C) The presentation tier (load balancers)
    D) All three tiers are placed in public subnets

---

<details>
<summary>Answer Key</summary>

1. **B) A logically isolated virtual network that you define within your AWS account**
   A VPC is a virtual network dedicated to your AWS account. You control the IP address range, subnets, route tables, and gateways. Option A is incorrect because a VPC is virtual, not physical. Option C is incorrect because a VPC exists within a single Region (though it can span multiple Availability Zones within that Region). Option D describes Amazon Route 53, not a VPC.
   Further reading: [What is Amazon VPC?](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)

2. **True.**
   AWS automatically creates a default VPC in each Region with a `/16` CIDR block (`172.31.0.0/16`), an internet gateway attached, a default subnet in each Availability Zone, a default route table with a route to the internet gateway, a default security group, and a default network ACL. The default VPC is designed for convenience so you can launch instances immediately.
   Further reading: [Default VPC components](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc-components.html)

3. **C) 65,536**
   A `/16` prefix means the first 16 bits of the address are fixed, leaving 16 bits for host addresses. The formula is 2^(32 - prefix length), so 2^(32 - 16) = 2^16 = 65,536. Option A (256) corresponds to a `/24`. Option B (4,096) corresponds to a `/20`. Option D (16,777,216) corresponds to a `/8`.
   Further reading: [VPC CIDR blocks](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-cidr-blocks.html)

4. **B) The subnet's route table includes a route to an internet gateway**
   A subnet is considered public when its associated route table has a route directing internet-bound traffic (typically `0.0.0.0/0`) to an internet gateway. The CIDR block size (A) does not determine whether a subnet is public or private. The Availability Zone placement (C) is unrelated to public or private designation. While NACLs (D) filter traffic, they do not define whether a subnet is public.
   Further reading: [Subnets for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html)

5. **C) 251**
   AWS reserves five IP addresses in every subnet: the network address (first), the VPC router (second), DNS (third), future use (fourth), and the broadcast address (last). For a `/24` subnet: 256 - 5 = 251 usable addresses. Option A (254) is the standard calculation without AWS reservations. Option B (253) accounts for only the network and broadcast addresses plus one reservation. Option D (250) over-counts the reservations.
   Further reading: [Subnet CIDR blocks](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html)

6. **B) An internet gateway is attached to a VPC and supports both inbound and outbound traffic; a NAT gateway is placed in a public subnet and supports outbound traffic only from private subnets**
   An internet gateway enables resources in public subnets to communicate with the internet in both directions. A NAT gateway allows instances in private subnets to initiate outbound connections to the internet while preventing the internet from initiating inbound connections to those instances. Option A reverses the directionality. Option C incorrectly places the internet gateway in a private subnet. Option D is incorrect because they serve different purposes.
   Further reading: [Internet gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) and [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)

7. **The VPC router selects the `10.0.0.0/16 -> local` route.** The destination `10.0.3.25` matches both routes: it falls within the `10.0.0.0/16` range and also within the `0.0.0.0/0` (all traffic) range. The VPC router uses longest prefix match, selecting the most specific route. The `/16` prefix is more specific than `/0`, so the traffic stays within the VPC via the local route. The `/0` route would only apply to traffic destined for addresses outside the `10.0.0.0/16` range.
   Further reading: [Configure route tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)

8. **B and D**
   Security groups operate at the resource level (B), meaning they are attached to individual resources such as EC2 instances or Elastic Network Interfaces, not to subnets. Security groups are stateful (D), so if you allow an inbound request, the response is automatically allowed regardless of outbound rules. Option A describes NACLs, which are stateless. Option C is incorrect because security groups support allow rules only; any traffic not explicitly allowed is denied. Option E describes NACLs, which are associated with subnets.
   Further reading: [Security groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)

9. **False.**
   NACLs evaluate rules in number order, starting with the lowest numbered rule. As soon as a rule matches the traffic, it is applied and no further rules are evaluated (first match wins). Security groups, by contrast, evaluate all rules before making a decision. This difference in rule evaluation is one of the key distinctions between the two.
   Further reading: [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html)

10. **C) The presentation tier (load balancers)**
    In a three-tier architecture, the presentation tier (load balancers) is placed in public subnets to accept incoming traffic from the internet. The application tier (B) runs in private subnets and receives traffic only from the load balancers. The data tier (A) also runs in private subnets and accepts connections only from the application tier. Placing all tiers in public subnets (D) would expose databases and application servers to the internet, violating the principle of least privilege for network access.
    Further reading: [How Amazon VPC works](https://docs.aws.amazon.com/vpc/latest/userguide/how-it-works.html)

</details>
