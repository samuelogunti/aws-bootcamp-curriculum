# Lab 03: VPC Setup with Public and Private Subnets

## Objective

Create a custom Virtual Private Cloud (VPC) with public and private subnets across two Availability Zones, configure internet and NAT gateways, launch EC2 instances, and verify network connectivity using security groups and route tables.

## Architecture Diagram

This lab builds a multi-AZ VPC with public and private subnets, internet access through an internet gateway, and outbound-only internet access for private subnets through a NAT gateway:

```
Internet
    |
Internet Gateway (bootcamp-igw)
    |
    ├── Public Route Table (bootcamp-public-rt)
    |       Route: 0.0.0.0/0 -> Internet Gateway
    |       Associated: Public Subnet AZ-a, Public Subnet AZ-b
    |
VPC: bootcamp-vpc (10.0.0.0/16)
    |
    ├── AZ: us-east-1a
    |   ├── Public Subnet:  10.0.1.0/24  (bootcamp-public-1a)
    |   |   ├── EC2 Instance: bootcamp-web-server (Web Server SG)
    |   |   └── NAT Gateway: bootcamp-nat-gw (Elastic IP)
    |   └── Private Subnet: 10.0.2.0/24  (bootcamp-private-1a)
    |       └── EC2 Instance: bootcamp-private-instance (Private SG)
    |
    ├── AZ: us-east-1b
    |   ├── Public Subnet:  10.0.3.0/24  (bootcamp-public-1b)
    |   └── Private Subnet: 10.0.4.0/24  (bootcamp-private-1b)
    |
    └── Private Route Table (bootcamp-private-rt)
            Route: 0.0.0.0/0 -> NAT Gateway
            Associated: Private Subnet AZ-a, Private Subnet AZ-b

Security Groups:
    ├── bootcamp-web-sg: Allows HTTP (80), HTTPS (443) from 0.0.0.0/0; SSH (22) from your IP
    └── bootcamp-private-sg: Allows all traffic from bootcamp-web-sg only
```

The public subnets route internet-bound traffic through the internet gateway. The private subnets route outbound internet traffic through the NAT gateway in the public subnet. Security groups enforce that only the web server accepts inbound HTTP/HTTPS traffic, and the private instance accepts traffic only from the web server's security group.

## Prerequisites

- Completed [Lab 01: AWS Account Setup and Console Tour](../../01-cloud-fundamentals/lab/lab-01-aws-account-setup.md)
- Completed [Lab 02: IAM Users, Groups, Policies, and Roles](../../02-iam-and-security/lab/lab-02-iam-users-groups-roles.md)
- Completed [Module 03: Networking Basics (VPC)](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

75 minutes

## Instructions

### Step 1: Create a Custom VPC

In this step, you create a [custom VPC](https://docs.aws.amazon.com/vpc/latest/userguide/create-vpc.html) with the CIDR block `10.0.0.0/16`, which provides 65,536 private IP addresses.

**Console:**

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as `bootcamp-admin`.
2. Verify that the Region selector in the top-right corner displays **US East (N. Virginia) us-east-1**.
3. In the search bar at the top, type `VPC` and select **VPC** from the results.
4. In the left navigation pane, choose **Your VPCs**.
5. Choose **Create VPC**.
6. Under **VPC settings**, select **VPC only**.
7. Configure the following settings:
   - **Name tag:** `bootcamp-vpc`
   - **IPv4 CIDR block:** Select **IPv4 CIDR manual input**
   - **IPv4 CIDR:** `10.0.0.0/16`
   - **IPv6 CIDR block:** Select **No IPv6 CIDR block**
   - **Tenancy:** Default
8. Choose **Create VPC**.

**Expected result:** A success banner displays the VPC ID. The Your VPCs page lists `bootcamp-vpc` with CIDR `10.0.0.0/16` and state `available`.

**CLI equivalent:**

```bash
aws ec2 create-vpc \
  --cidr-block 10.0.0.0/16 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=bootcamp-vpc}]' \
  --region us-east-1
```

Expected output:

```json
{
    "Vpc": {
        "CidrBlock": "10.0.0.0/16",
        "VpcId": "vpc-0abc123def456789",
        "State": "available",
        "Tags": [
            {
                "Key": "Name",
                "Value": "bootcamp-vpc"
            }
        ]
    }
}
```

9. Note the `VpcId` value. You will use it throughout this lab. Store it in a shell variable for convenience:

```bash
VPC_ID=$(aws ec2 describe-vpcs \
  --filters "Name=tag:Name,Values=bootcamp-vpc" \
  --query "Vpcs[0].VpcId" \
  --output text \
  --region us-east-1)
echo $VPC_ID
```

10. Enable DNS hostnames for the VPC. This is required for instances to receive public DNS names:

```bash
aws ec2 modify-vpc-attribute \
  --vpc-id $VPC_ID \
  --enable-dns-hostnames '{"Value": true}' \
  --region us-east-1
```

> **Tip:** DNS hostnames are disabled by default on custom VPCs. Enabling them allows EC2 instances with public IPs to receive public DNS names, which is useful for testing connectivity.


### Step 2: Create Public and Private Subnets

In this step, you create four [subnets](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html): one public and one private in each of two Availability Zones. This layout supports high availability by distributing resources across AZs.

**Console:**

1. In the VPC console left navigation pane, choose **Subnets**.
2. Choose **Create subnet**.
3. Under **VPC ID**, select `bootcamp-vpc`.
4. Under **Subnet settings**, configure the first subnet:
   - **Subnet name:** `bootcamp-public-1a`
   - **Availability Zone:** `us-east-1a`
   - **IPv4 subnet CIDR block:** `10.0.1.0/24`
5. Choose **Add new subnet** to add a second subnet in the same form:
   - **Subnet name:** `bootcamp-private-1a`
   - **Availability Zone:** `us-east-1a`
   - **IPv4 subnet CIDR block:** `10.0.2.0/24`
6. Choose **Add new subnet** again for the third subnet:
   - **Subnet name:** `bootcamp-public-1b`
   - **Availability Zone:** `us-east-1b`
   - **IPv4 subnet CIDR block:** `10.0.3.0/24`
7. Choose **Add new subnet** again for the fourth subnet:
   - **Subnet name:** `bootcamp-private-1b`
   - **Availability Zone:** `us-east-1b`
   - **IPv4 subnet CIDR block:** `10.0.4.0/24`
8. Choose **Create subnet**.

**Expected result:** The Subnets page lists all four subnets with state `available`, each associated with `bootcamp-vpc`.

**CLI equivalent:**

```bash
aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.1.0/24 \
  --availability-zone us-east-1a \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=bootcamp-public-1a}]' \
  --region us-east-1
```

```bash
aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.2.0/24 \
  --availability-zone us-east-1a \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=bootcamp-private-1a}]' \
  --region us-east-1
```

```bash
aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.3.0/24 \
  --availability-zone us-east-1b \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=bootcamp-public-1b}]' \
  --region us-east-1
```

```bash
aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.4.0/24 \
  --availability-zone us-east-1b \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=bootcamp-private-1b}]' \
  --region us-east-1
```

9. Store the subnet IDs in shell variables for later use:

```bash
PUBLIC_SUBNET_1A=$(aws ec2 describe-subnets \
  --filters "Name=tag:Name,Values=bootcamp-public-1a" \
  --query "Subnets[0].SubnetId" --output text --region us-east-1)

PRIVATE_SUBNET_1A=$(aws ec2 describe-subnets \
  --filters "Name=tag:Name,Values=bootcamp-private-1a" \
  --query "Subnets[0].SubnetId" --output text --region us-east-1)

PUBLIC_SUBNET_1B=$(aws ec2 describe-subnets \
  --filters "Name=tag:Name,Values=bootcamp-public-1b" \
  --query "Subnets[0].SubnetId" --output text --region us-east-1)

PRIVATE_SUBNET_1B=$(aws ec2 describe-subnets \
  --filters "Name=tag:Name,Values=bootcamp-private-1b" \
  --query "Subnets[0].SubnetId" --output text --region us-east-1)

echo "Public 1a: $PUBLIC_SUBNET_1A"
echo "Private 1a: $PRIVATE_SUBNET_1A"
echo "Public 1b: $PUBLIC_SUBNET_1B"
echo "Private 1b: $PRIVATE_SUBNET_1B"
```

10. Enable auto-assign public IPv4 addresses on the public subnets. Instances launched in these subnets will automatically receive a public IP:

```bash
aws ec2 modify-subnet-attribute \
  --subnet-id $PUBLIC_SUBNET_1A \
  --map-public-ip-on-launch \
  --region us-east-1
```

```bash
aws ec2 modify-subnet-attribute \
  --subnet-id $PUBLIC_SUBNET_1B \
  --map-public-ip-on-launch \
  --region us-east-1
```

> **Tip:** Auto-assigning public IPs is what makes a subnet behave as "public" from an instance perspective. Without a public IP, an instance cannot communicate directly with the internet even if the route table points to an internet gateway.

### Step 3: Create and Attach an Internet Gateway

An [internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) enables communication between resources in your VPC and the internet. In this step, you create an internet gateway and attach it to your VPC.

**Console:**

1. In the VPC console left navigation pane, choose **Internet gateways**.
2. Choose **Create internet gateway**.
3. In the **Name tag** field, enter `bootcamp-igw`.
4. Choose **Create internet gateway**.
5. On the internet gateway details page, choose **Actions**, then **Attach to VPC**.
6. In the **Available VPCs** dropdown, select `bootcamp-vpc`.
7. Choose **Attach internet gateway**.

**Expected result:** The internet gateway state changes to `Attached`. The details page shows `bootcamp-vpc` under **VPC ID**.

**CLI equivalent:**

```bash
IGW_ID=$(aws ec2 create-internet-gateway \
  --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=bootcamp-igw}]' \
  --query "InternetGateway.InternetGatewayId" \
  --output text \
  --region us-east-1)
echo "Internet Gateway: $IGW_ID"
```

```bash
aws ec2 attach-internet-gateway \
  --internet-gateway-id $IGW_ID \
  --vpc-id $VPC_ID \
  --region us-east-1
```

> **Tip:** A VPC can have only one internet gateway attached at a time. The internet gateway is horizontally scaled, redundant, and highly available. You do not need to worry about its capacity or availability.

### Step 4: Create a Public Route Table and Associate with Public Subnets

A [route table](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html) controls where network traffic is directed. In this step, you create a route table for the public subnets with a route to the internet gateway.

**Console:**

1. In the VPC console left navigation pane, choose **Route tables**.
2. Choose **Create route table**.
3. Configure the following settings:
   - **Name:** `bootcamp-public-rt`
   - **VPC:** Select `bootcamp-vpc`
4. Choose **Create route table**.
5. On the route table details page, choose the **Routes** tab.
6. Choose **Edit routes**.
7. Choose **Add route**.
8. Configure the new route:
   - **Destination:** `0.0.0.0/0`
   - **Target:** Select **Internet Gateway**, then select `bootcamp-igw`
9. Choose **Save changes**.

**Expected result:** The Routes tab shows two routes:

| Destination | Target | Status |
|-------------|--------|--------|
| 10.0.0.0/16 | local | active |
| 0.0.0.0/0 | igw-xxxxxxxx | active |

10. Choose the **Subnet associations** tab.
11. Choose **Edit subnet associations**.
12. Select the checkboxes next to `bootcamp-public-1a` and `bootcamp-public-1b`.
13. Choose **Save associations**.

**Expected result:** The Subnet associations tab shows both public subnets as explicitly associated.

**CLI equivalent:**

```bash
PUBLIC_RT_ID=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=bootcamp-public-rt}]' \
  --query "RouteTable.RouteTableId" \
  --output text \
  --region us-east-1)
echo "Public Route Table: $PUBLIC_RT_ID"
```

```bash
aws ec2 create-route \
  --route-table-id $PUBLIC_RT_ID \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id $IGW_ID \
  --region us-east-1
```

Expected output:

```json
{
    "Return": true
}
```

```bash
aws ec2 associate-route-table \
  --route-table-id $PUBLIC_RT_ID \
  --subnet-id $PUBLIC_SUBNET_1A \
  --region us-east-1
```

```bash
aws ec2 associate-route-table \
  --route-table-id $PUBLIC_RT_ID \
  --subnet-id $PUBLIC_SUBNET_1B \
  --region us-east-1
```

### Step 5: Create Security Groups

[Security groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html) act as virtual firewalls for your instances. In this step, you create two security groups: one for the web server (allowing HTTP, HTTPS, and SSH inbound) and one for the private instance (allowing traffic only from the web server security group).

**Console:**

1. In the VPC console left navigation pane, choose **Security groups**.
2. Choose **Create security group**.
3. Configure the web server security group:
   - **Security group name:** `bootcamp-web-sg`
   - **Description:** `Allow HTTP, HTTPS, and SSH inbound for web servers`
   - **VPC:** Select `bootcamp-vpc`
4. Under **Inbound rules**, choose **Add rule** three times and configure:

   | Type | Protocol | Port range | Source | Description |
   |------|----------|------------|--------|-------------|
   | HTTP | TCP | 80 | 0.0.0.0/0 | Allow HTTP from anywhere |
   | HTTPS | TCP | 443 | 0.0.0.0/0 | Allow HTTPS from anywhere |
   | SSH | TCP | 22 | 0.0.0.0/0 | Allow SSH (restrict to your IP in production) |

5. Leave the **Outbound rules** as the default (all traffic allowed to 0.0.0.0/0).
6. Choose **Create security group**.

**Expected result:** The Security groups page lists `bootcamp-web-sg` with 3 inbound rules.

> **Warning:** Allowing SSH from `0.0.0.0/0` is acceptable for this lab environment. In production, restrict SSH access to your specific IP address or use [EC2 Instance Connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-eic.html) or [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) instead.

7. Note the security group ID for `bootcamp-web-sg`. You will need it for the next security group.
8. Choose **Create security group** again to create the private instance security group:
   - **Security group name:** `bootcamp-private-sg`
   - **Description:** `Allow all traffic from web server security group only`
   - **VPC:** Select `bootcamp-vpc`
9. Under **Inbound rules**, choose **Add rule** and configure:

   | Type | Protocol | Port range | Source | Description |
   |------|----------|------------|--------|-------------|
   | All traffic | All | All | Custom: select `bootcamp-web-sg` | Allow all traffic from web server SG |

10. Leave the **Outbound rules** as the default.
11. Choose **Create security group**.

**Expected result:** The Security groups page lists `bootcamp-private-sg` with 1 inbound rule referencing `bootcamp-web-sg`.

**CLI equivalent:**

```bash
WEB_SG_ID=$(aws ec2 create-security-group \
  --group-name bootcamp-web-sg \
  --description "Allow HTTP, HTTPS, and SSH inbound for web servers" \
  --vpc-id $VPC_ID \
  --query "GroupId" \
  --output text \
  --region us-east-1)
echo "Web SG: $WEB_SG_ID"
```

```bash
aws ec2 authorize-security-group-ingress \
  --group-id $WEB_SG_ID \
  --ip-permissions \
    'IpProtocol=tcp,FromPort=80,ToPort=80,IpRanges=[{CidrIp=0.0.0.0/0,Description="Allow HTTP from anywhere"}]' \
    'IpProtocol=tcp,FromPort=443,ToPort=443,IpRanges=[{CidrIp=0.0.0.0/0,Description="Allow HTTPS from anywhere"}]' \
    'IpProtocol=tcp,FromPort=22,ToPort=22,IpRanges=[{CidrIp=0.0.0.0/0,Description="Allow SSH"}]' \
  --region us-east-1
```

```bash
PRIVATE_SG_ID=$(aws ec2 create-security-group \
  --group-name bootcamp-private-sg \
  --description "Allow all traffic from web server security group only" \
  --vpc-id $VPC_ID \
  --query "GroupId" \
  --output text \
  --region us-east-1)
echo "Private SG: $PRIVATE_SG_ID"
```

```bash
aws ec2 authorize-security-group-ingress \
  --group-id $PRIVATE_SG_ID \
  --ip-permissions \
    "IpProtocol=-1,UserIdGroupPairs=[{GroupId=$WEB_SG_ID,Description=\"Allow all traffic from web server SG\"}]" \
  --region us-east-1
```

> **Tip:** Referencing a security group as the source (instead of an IP range) is a best practice. When you add or remove instances from the source security group, the rules automatically apply to the new set of instances without any changes to the security group rules themselves.

### Step 6: Launch an EC2 Instance in the Public Subnet

In this step, you launch an [EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html) in the public subnet and verify that it has internet access. You will use Amazon Linux 2023, which is included in the [AWS Free Tier](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-free-tier.html).

**Console:**

1. In the console search bar, type `EC2` and select **EC2** from the results.
2. In the left navigation pane, choose **Instances**.
3. Choose **Launch instances**.
4. Configure the instance:
   - **Name:** `bootcamp-web-server`
   - **Application and OS Images:** Select **Amazon Linux 2023 AMI** (Free tier eligible)
   - **Instance type:** `t2.micro` (Free tier eligible)
   - **Key pair:** Choose **Proceed without a key pair** (you will use EC2 Instance Connect for SSH)
5. Under **Network settings**, choose **Edit** and configure:
   - **VPC:** Select `bootcamp-vpc`
   - **Subnet:** Select `bootcamp-public-1a`
   - **Auto-assign public IP:** Enable
   - **Firewall (security groups):** Select **Select existing security group**
   - **Common security groups:** Select `bootcamp-web-sg`
6. Under **Advanced details**, in the **User data** field, paste the following script. This installs and starts a simple web server:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from bootcamp-web-server</h1><p>AZ: $(ec2-metadata --availability-zone | cut -d' ' -f2)</p>" > /var/www/html/index.html
```

7. Choose **Launch instance**.

**Expected result:** A success message displays the instance ID. The instance state transitions from `pending` to `running` within 1 to 2 minutes.

**CLI equivalent:**

First, find the latest Amazon Linux 2023 AMI:

```bash
AMI_ID=$(aws ec2 describe-images \
  --owners amazon \
  --filters "Name=name,Values=al2023-ami-2023*-x86_64" "Name=state,Values=available" \
  --query "sort_by(Images, &CreationDate)[-1].ImageId" \
  --output text \
  --region us-east-1)
echo "AMI: $AMI_ID"
```

Then launch the instance:

```bash
WEB_INSTANCE_ID=$(aws ec2 run-instances \
  --image-id $AMI_ID \
  --instance-type t2.micro \
  --subnet-id $PUBLIC_SUBNET_1A \
  --security-group-ids $WEB_SG_ID \
  --associate-public-ip-address \
  --user-data '#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from bootcamp-web-server</h1>" > /var/www/html/index.html' \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=bootcamp-web-server}]' \
  --query "Instances[0].InstanceId" \
  --output text \
  --region us-east-1)
echo "Web Instance: $WEB_INSTANCE_ID"
```

8. Wait for the instance to reach the `running` state:

```bash
aws ec2 wait instance-running --instance-ids $WEB_INSTANCE_ID --region us-east-1
echo "Instance is running"
```

9. Retrieve the public IP address:

```bash
WEB_PUBLIC_IP=$(aws ec2 describe-instances \
  --instance-ids $WEB_INSTANCE_ID \
  --query "Reservations[0].Instances[0].PublicIpAddress" \
  --output text \
  --region us-east-1)
echo "Public IP: $WEB_PUBLIC_IP"
```

10. Wait 2 to 3 minutes for the user data script to complete, then verify internet access by testing the web server. Open a new browser tab and navigate to `http://<PUBLIC_IP>` (replace `<PUBLIC_IP>` with the IP from step 9).

**Expected result:** The browser displays "Hello from bootcamp-web-server".

11. Verify from the CLI using `curl`:

```bash
curl http://$WEB_PUBLIC_IP
```

Expected output:

```html
<h1>Hello from bootcamp-web-server</h1>
```

12. Connect to the instance using [EC2 Instance Connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-eic.html) to verify outbound internet access:
    - In the EC2 console, select `bootcamp-web-server`.
    - Choose **Connect**.
    - On the **EC2 Instance Connect** tab, choose **Connect**.
    - In the terminal, run:

```bash
ping -c 3 amazon.com
```

Expected output:

```
PING amazon.com (205.251.242.103) 56(84) bytes of data.
64 bytes from 205.251.242.103: icmp_seq=1 ttl=234 time=1.23 ms
64 bytes from 205.251.242.103: icmp_seq=2 ttl=234 time=1.15 ms
64 bytes from 205.251.242.103: icmp_seq=3 ttl=234 time=1.18 ms

--- amazon.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss
```

This confirms that the public subnet instance has full internet connectivity through the internet gateway.

### Step 7: Create a NAT Gateway

A [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) allows instances in private subnets to initiate outbound connections to the internet while preventing inbound connections from the internet. The NAT gateway requires an [Elastic IP address](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html) and must be placed in a public subnet.

**Console:**

1. In the VPC console left navigation pane, choose **NAT gateways**.
2. Choose **Create NAT gateway**.
3. Configure the following settings:
   - **Name:** `bootcamp-nat-gw`
   - **Subnet:** Select `bootcamp-public-1a`
   - **Connectivity type:** Public
   - **Elastic IP allocation ID:** Choose **Allocate Elastic IP**. This creates a new Elastic IP and assigns it to the NAT gateway.
4. Choose **Create NAT gateway**.

**Expected result:** The NAT gateways page shows `bootcamp-nat-gw` with status `pending`. The status changes to `available` within 1 to 2 minutes.

> **Warning:** NAT gateways incur hourly charges and data processing charges even when idle. You will delete this NAT gateway in the Cleanup section. Do not leave it running after completing the lab.

**CLI equivalent:**

```bash
EIP_ALLOC_ID=$(aws ec2 allocate-address \
  --domain vpc \
  --tag-specifications 'ResourceType=elastic-ip,Tags=[{Key=Name,Value=bootcamp-nat-eip}]' \
  --query "AllocationId" \
  --output text \
  --region us-east-1)
echo "Elastic IP Allocation: $EIP_ALLOC_ID"
```

```bash
NAT_GW_ID=$(aws ec2 create-nat-gateway \
  --subnet-id $PUBLIC_SUBNET_1A \
  --allocation-id $EIP_ALLOC_ID \
  --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=bootcamp-nat-gw}]' \
  --query "NatGateway.NatGatewayId" \
  --output text \
  --region us-east-1)
echo "NAT Gateway: $NAT_GW_ID"
```

5. Wait for the NAT gateway to become available:

```bash
aws ec2 wait nat-gateway-available --nat-gateway-ids $NAT_GW_ID --region us-east-1
echo "NAT Gateway is available"
```

> **Tip:** The NAT gateway can take 1 to 2 minutes to reach the `available` state. The `wait` command blocks until the NAT gateway is ready.

### Step 8: Create a Private Route Table and Associate with Private Subnets

In this step, you create a route table for the private subnets that routes internet-bound traffic through the NAT gateway.

**Console:**

1. In the VPC console left navigation pane, choose **Route tables**.
2. Choose **Create route table**.
3. Configure the following settings:
   - **Name:** `bootcamp-private-rt`
   - **VPC:** Select `bootcamp-vpc`
4. Choose **Create route table**.
5. On the route table details page, choose the **Routes** tab.
6. Choose **Edit routes**.
7. Choose **Add route**.
8. Configure the new route:
   - **Destination:** `0.0.0.0/0`
   - **Target:** Select **NAT Gateway**, then select `bootcamp-nat-gw`
9. Choose **Save changes**.

**Expected result:** The Routes tab shows two routes:

| Destination | Target | Status |
|-------------|--------|--------|
| 10.0.0.0/16 | local | active |
| 0.0.0.0/0 | nat-xxxxxxxx | active |

10. Choose the **Subnet associations** tab.
11. Choose **Edit subnet associations**.
12. Select the checkboxes next to `bootcamp-private-1a` and `bootcamp-private-1b`.
13. Choose **Save associations**.

**Expected result:** The Subnet associations tab shows both private subnets as explicitly associated.

**CLI equivalent:**

```bash
PRIVATE_RT_ID=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=bootcamp-private-rt}]' \
  --query "RouteTable.RouteTableId" \
  --output text \
  --region us-east-1)
echo "Private Route Table: $PRIVATE_RT_ID"
```

```bash
aws ec2 create-route \
  --route-table-id $PRIVATE_RT_ID \
  --destination-cidr-block 0.0.0.0/0 \
  --nat-gateway-id $NAT_GW_ID \
  --region us-east-1
```

Expected output:

```json
{
    "Return": true
}
```

```bash
aws ec2 associate-route-table \
  --route-table-id $PRIVATE_RT_ID \
  --subnet-id $PRIVATE_SUBNET_1A \
  --region us-east-1
```

```bash
aws ec2 associate-route-table \
  --route-table-id $PRIVATE_RT_ID \
  --subnet-id $PRIVATE_SUBNET_1B \
  --region us-east-1
```

### Step 9: Launch an EC2 Instance in the Private Subnet

In this step, you launch an EC2 instance in the private subnet and verify that it can reach the internet through the NAT gateway for outbound traffic, but cannot be reached directly from the internet.

**Console:**

1. In the EC2 console, choose **Instances** in the left navigation pane.
2. Choose **Launch instances**.
3. Configure the instance:
   - **Name:** `bootcamp-private-instance`
   - **Application and OS Images:** Select **Amazon Linux 2023 AMI** (Free tier eligible)
   - **Instance type:** `t2.micro` (Free tier eligible)
   - **Key pair:** Choose **Proceed without a key pair**
4. Under **Network settings**, choose **Edit** and configure:
   - **VPC:** Select `bootcamp-vpc`
   - **Subnet:** Select `bootcamp-private-1a`
   - **Auto-assign public IP:** Disable
   - **Firewall (security groups):** Select **Select existing security group**
   - **Common security groups:** Select `bootcamp-private-sg`
5. Choose **Launch instance**.

**Expected result:** A success message displays the instance ID. The instance state transitions to `running` within 1 to 2 minutes. The instance has a private IP address (in the 10.0.2.x range) but no public IP address.

**CLI equivalent:**

```bash
PRIVATE_INSTANCE_ID=$(aws ec2 run-instances \
  --image-id $AMI_ID \
  --instance-type t2.micro \
  --subnet-id $PRIVATE_SUBNET_1A \
  --security-group-ids $PRIVATE_SG_ID \
  --no-associate-public-ip-address \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=bootcamp-private-instance}]' \
  --query "Instances[0].InstanceId" \
  --output text \
  --region us-east-1)
echo "Private Instance: $PRIVATE_INSTANCE_ID"
```

```bash
aws ec2 wait instance-running --instance-ids $PRIVATE_INSTANCE_ID --region us-east-1
echo "Private instance is running"
```

6. Retrieve the private IP address of the instance:

```bash
PRIVATE_INSTANCE_IP=$(aws ec2 describe-instances \
  --instance-ids $PRIVATE_INSTANCE_ID \
  --query "Reservations[0].Instances[0].PrivateIpAddress" \
  --output text \
  --region us-east-1)
echo "Private IP: $PRIVATE_INSTANCE_IP"
```

### Step 10: Verify Private Instance Connectivity Through the NAT Gateway

The private instance has no public IP and no direct internet access. To verify its outbound connectivity through the NAT gateway, you connect to it from the web server instance (which is in the same VPC and allowed by the private security group).

1. In the EC2 console, select `bootcamp-web-server`.
2. Choose **Connect**.
3. On the **EC2 Instance Connect** tab, choose **Connect**.
4. From the web server terminal, use SSH to connect to the private instance. Replace `PRIVATE_IP` with the private IP address from Step 9 (for example, `10.0.2.x`):

> **Tip:** EC2 Instance Connect does not work directly with private instances because they have no public IP. Instead, you connect to the public instance first and then SSH to the private instance from there. This pattern is called a "jump box" or "bastion host."

Since you launched without a key pair, use [EC2 Instance Connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-connect-methods.html) to push a temporary SSH key to the private instance. From the web server terminal, run:

```bash
# Install the EC2 Instance Connect CLI (if not already available)
pip3 install ec2instanceconnectcli 2>/dev/null || true
```

Alternatively, you can use [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) to connect to the private instance. From CloudShell, run:

```bash
aws ssm start-session --target $PRIVATE_INSTANCE_ID --region us-east-1
```

> **Tip:** If Session Manager is not available, you can use the following approach. From the web server terminal, generate a temporary key pair and push it to the private instance using the AWS CLI:

```bash
# Generate a temporary key pair on the web server
ssh-keygen -t rsa -f /tmp/temp-key -N "" -q

# Push the public key to the private instance using EC2 Instance Connect
aws ec2-instance-connect send-ssh-public-key \
  --instance-id PRIVATE_INSTANCE_ID \
  --availability-zone us-east-1a \
  --instance-os-user ec2-user \
  --ssh-public-key file:///tmp/temp-key.pub \
  --region us-east-1

# SSH to the private instance
ssh -i /tmp/temp-key -o StrictHostKeyChecking=no ec2-user@PRIVATE_IP
```

Replace `PRIVATE_INSTANCE_ID` with the actual instance ID and `PRIVATE_IP` with the private IP address.

5. Once connected to the private instance, verify outbound internet access through the NAT gateway:

```bash
curl -s https://checkip.amazonaws.com
```

**Expected result:** The command returns an IP address. This IP address is the Elastic IP of the NAT gateway, not the private instance's IP. This confirms that outbound traffic from the private subnet is routed through the NAT gateway.

6. Verify that the instance can download packages from the internet:

```bash
ping -c 3 amazon.com
```

Expected output:

```
PING amazon.com (205.251.242.103) 56(84) bytes of data.
64 bytes from 205.251.242.103: icmp_seq=1 ttl=234 time=1.45 ms
64 bytes from 205.251.242.103: icmp_seq=2 ttl=234 time=1.38 ms
64 bytes from 205.251.242.103: icmp_seq=3 ttl=234 time=1.41 ms

--- amazon.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss
```

7. Verify that the private instance cannot be reached from the internet. Open a new CloudShell tab and try to ping the private IP from outside the VPC:

```bash
# This will NOT work because the private instance has no public IP
# and is not reachable from the internet
curl --connect-timeout 5 http://$PRIVATE_INSTANCE_IP
```

**Expected result:** The connection times out. The private instance is not reachable from the internet because it has no public IP address and no route from the internet gateway to the private subnet.

8. Exit the SSH session on the private instance:

```bash
exit
```

> **Tip:** The NAT gateway provides outbound-only internet access. Private instances can download updates and call external APIs, but the internet cannot initiate connections to them. This is the core security benefit of the public/private subnet architecture.

## Validation

Confirm the following:

- [ ] A custom VPC named `bootcamp-vpc` exists with CIDR block `10.0.0.0/16`
- [ ] Four subnets exist: `bootcamp-public-1a` (10.0.1.0/24), `bootcamp-private-1a` (10.0.2.0/24), `bootcamp-public-1b` (10.0.3.0/24), `bootcamp-private-1b` (10.0.4.0/24)
- [ ] An internet gateway named `bootcamp-igw` is attached to `bootcamp-vpc`
- [ ] The public route table `bootcamp-public-rt` has a route `0.0.0.0/0` pointing to the internet gateway and is associated with both public subnets
- [ ] The private route table `bootcamp-private-rt` has a route `0.0.0.0/0` pointing to the NAT gateway and is associated with both private subnets
- [ ] The `bootcamp-web-sg` security group allows inbound HTTP (80), HTTPS (443), and SSH (22)
- [ ] The `bootcamp-private-sg` security group allows inbound traffic only from `bootcamp-web-sg`
- [ ] The `bootcamp-web-server` instance in the public subnet responds to HTTP requests from the internet
- [ ] The `bootcamp-private-instance` in the private subnet can reach the internet through the NAT gateway (verified with `curl` or `ping`)
- [ ] The `bootcamp-private-instance` has no public IP address and is not directly reachable from the internet

You can verify the VPC configuration using the CLI:

```bash
aws ec2 describe-vpcs \
  --filters "Name=tag:Name,Values=bootcamp-vpc" \
  --query "Vpcs[0].{VpcId:VpcId,CidrBlock:CidrBlock,State:State}" \
  --region us-east-1
```

```bash
aws ec2 describe-subnets \
  --filters "Name=vpc-id,Values=$VPC_ID" \
  --query "Subnets[].{Name:Tags[?Key=='Name'].Value|[0],SubnetId:SubnetId,CidrBlock:CidrBlock,AZ:AvailabilityZone}" \
  --output table \
  --region us-east-1
```

## Cleanup

Delete all resources created in this lab to avoid unexpected charges. Follow this order to avoid dependency errors.

> **Warning:** The NAT gateway incurs hourly charges. Complete the cleanup promptly after finishing the lab. The order below is important because some resources depend on others. Deleting them out of order will produce dependency errors.

**1. Terminate the EC2 instances:**

```bash
aws ec2 terminate-instances \
  --instance-ids $WEB_INSTANCE_ID $PRIVATE_INSTANCE_ID \
  --region us-east-1
```

Wait for both instances to reach the `terminated` state:

```bash
aws ec2 wait instance-terminated \
  --instance-ids $WEB_INSTANCE_ID $PRIVATE_INSTANCE_ID \
  --region us-east-1
echo "Instances terminated"
```

**Console:** In the EC2 console, select both instances, choose **Instance state**, then **Terminate instance**. Confirm the termination.

**2. Delete the NAT gateway:**

```bash
aws ec2 delete-nat-gateway --nat-gateway-id $NAT_GW_ID --region us-east-1
```

Wait for the NAT gateway to be deleted (this can take 1 to 2 minutes):

```bash
echo "Waiting for NAT gateway to delete (1-2 minutes)..."
aws ec2 wait nat-gateway-available --nat-gateway-ids $NAT_GW_ID --region us-east-1 2>/dev/null || true
sleep 60
```

**Console:** In the VPC console, choose **NAT gateways**, select `bootcamp-nat-gw`, choose **Actions**, then **Delete NAT gateway**. Type `delete` to confirm.

**3. Release the Elastic IP address:**

The Elastic IP cannot be released until the NAT gateway is fully deleted.

```bash
aws ec2 release-address --allocation-id $EIP_ALLOC_ID --region us-east-1
```

**Console:** In the VPC console, choose **Elastic IP addresses**, select the Elastic IP tagged `bootcamp-nat-eip`, choose **Actions**, then **Release Elastic IP addresses**. Confirm the release.

> **Warning:** Elastic IP addresses that are allocated but not associated with a running resource incur charges. Always release unused Elastic IPs.

**4. Delete the security groups:**

```bash
aws ec2 delete-security-group --group-id $PRIVATE_SG_ID --region us-east-1
aws ec2 delete-security-group --group-id $WEB_SG_ID --region us-east-1
```

**Console:** In the VPC console, choose **Security groups**. Select `bootcamp-private-sg` first (it references `bootcamp-web-sg`, so delete it first), choose **Actions**, then **Delete security groups**. Repeat for `bootcamp-web-sg`.

> **Tip:** Delete `bootcamp-private-sg` before `bootcamp-web-sg`. The private security group references the web security group, so the web security group cannot be deleted while it is still referenced.

**5. Delete the subnet associations and route tables:**

Disassociate subnets from the route tables, then delete the route tables:

```bash
# Get association IDs for the public route table
PUBLIC_ASSOC_IDS=$(aws ec2 describe-route-tables \
  --route-table-ids $PUBLIC_RT_ID \
  --query "RouteTables[0].Associations[?!Main].RouteTableAssociationId" \
  --output text \
  --region us-east-1)

for ASSOC_ID in $PUBLIC_ASSOC_IDS; do
  aws ec2 disassociate-route-table --association-id $ASSOC_ID --region us-east-1
done

# Get association IDs for the private route table
PRIVATE_ASSOC_IDS=$(aws ec2 describe-route-tables \
  --route-table-ids $PRIVATE_RT_ID \
  --query "RouteTables[0].Associations[?!Main].RouteTableAssociationId" \
  --output text \
  --region us-east-1)

for ASSOC_ID in $PRIVATE_ASSOC_IDS; do
  aws ec2 disassociate-route-table --association-id $ASSOC_ID --region us-east-1
done
```

```bash
aws ec2 delete-route-table --route-table-id $PUBLIC_RT_ID --region us-east-1
aws ec2 delete-route-table --route-table-id $PRIVATE_RT_ID --region us-east-1
```

**Console:** In the VPC console, choose **Route tables**. For each custom route table (`bootcamp-public-rt` and `bootcamp-private-rt`): select the route table, choose the **Subnet associations** tab, choose **Edit subnet associations**, deselect all subnets, and choose **Save associations**. Then select the route table, choose **Actions**, and **Delete route table**.

**6. Delete the subnets:**

```bash
aws ec2 delete-subnet --subnet-id $PUBLIC_SUBNET_1A --region us-east-1
aws ec2 delete-subnet --subnet-id $PRIVATE_SUBNET_1A --region us-east-1
aws ec2 delete-subnet --subnet-id $PUBLIC_SUBNET_1B --region us-east-1
aws ec2 delete-subnet --subnet-id $PRIVATE_SUBNET_1B --region us-east-1
```

**Console:** In the VPC console, choose **Subnets**. Select all four bootcamp subnets, choose **Actions**, then **Delete subnet**. Confirm the deletion.

**7. Detach and delete the internet gateway:**

```bash
aws ec2 detach-internet-gateway \
  --internet-gateway-id $IGW_ID \
  --vpc-id $VPC_ID \
  --region us-east-1
```

```bash
aws ec2 delete-internet-gateway --internet-gateway-id $IGW_ID --region us-east-1
```

**Console:** In the VPC console, choose **Internet gateways**, select `bootcamp-igw`, choose **Actions**, then **Detach from VPC**. Confirm. Then choose **Actions** again and **Delete internet gateway**. Confirm.

**8. Delete the VPC:**

```bash
aws ec2 delete-vpc --vpc-id $VPC_ID --region us-east-1
```

**Console:** In the VPC console, choose **Your VPCs**, select `bootcamp-vpc`, choose **Actions**, then **Delete VPC**. Type `delete` to confirm.

**Expected result:** All resources are deleted. The VPC console shows no resources with the `bootcamp-` prefix.

9. Verify that all resources have been cleaned up:

```bash
aws ec2 describe-vpcs \
  --filters "Name=tag:Name,Values=bootcamp-vpc" \
  --query "Vpcs" \
  --region us-east-1
```

Expected output:

```json
[]
```

## Challenge (Optional)

Using only concepts from Modules 01 through 03, complete the following:

1. Create a second NAT gateway in `bootcamp-public-1b` (the public subnet in AZ-b) with its own Elastic IP. Update the private route table so that `bootcamp-private-1b` uses the NAT gateway in AZ-b while `bootcamp-private-1a` continues to use the NAT gateway in AZ-a. This requires creating a second private route table.

   This configuration provides high availability for NAT. If AZ-a experiences an outage, private instances in AZ-b still have outbound internet access through their own NAT gateway.

2. Create a [network ACL](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) for the public subnets that explicitly denies all inbound traffic on port 23 (Telnet) and port 3389 (RDP). Allow all other traffic. Associate this NACL with both public subnets.

   This exercise demonstrates the difference between security groups (which only support allow rules) and NACLs (which support both allow and deny rules). The NACL provides a subnet-level defense layer that blocks unwanted protocols before traffic reaches individual instances.

3. Verify your NACL rules by checking the rule evaluation order. Remember that NACL rules are evaluated in number order, and the first matching rule is applied.

> **Tip:** Remember to delete the second NAT gateway, its Elastic IP, the additional route table, and the custom NACL after completing the challenge to avoid charges.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
