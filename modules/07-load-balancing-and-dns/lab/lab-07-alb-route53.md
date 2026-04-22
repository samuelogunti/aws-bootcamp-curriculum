# Lab 07: Application Load Balancer with Health Checks and Route 53

## Objective

Create an Application Load Balancer (ALB) that distributes HTTP traffic across two EC2 instances in different Availability Zones, configure health checks to route traffic only to healthy targets, and optionally create a Route 53 Alias record pointing to the ALB.

## Architecture Diagram

This lab builds a load-balanced web application in the default VPC using an ALB, a target group with health checks, and two EC2 instances:

```
Internet
    |
    v
(Optional) Route 53 Alias Record: app.example.com --> ALB DNS name
    |
    v
Application Load Balancer: lab07-alb
    |   Listener: HTTP port 80 --> Forward to target group
    |   Security Group: lab07-alb-sg (HTTP 80 from 0.0.0.0/0)
    |
    v
Target Group: lab07-target-group
    |   Health check: HTTP, port 80, path /
    |
    ├── EC2 Instance: lab07-web-1 (us-east-1a)
    |       Security Group: lab07-web-sg (HTTP 80 from lab07-alb-sg)
    |       User Data: Apache httpd displaying instance ID and AZ
    |
    └── EC2 Instance: lab07-web-2 (us-east-1b)
            Security Group: lab07-web-sg (HTTP 80 from lab07-alb-sg)
            User Data: Apache httpd displaying instance ID and AZ
```

The ALB receives all incoming HTTP requests and distributes them across the two instances using a round-robin algorithm. Health checks monitor each instance every 30 seconds. If an instance fails its health check, the ALB stops sending traffic to it until it recovers.

You will use the [default VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) for this lab. The default VPC provides public subnets across multiple Availability Zones, which is required for the ALB.

## Prerequisites

- Completed [Lab 01: AWS Account Setup and Console Tour](../../01-cloud-fundamentals/lab/lab-01-aws-account-setup.md)
- Completed [Module 03: Networking Basics (VPC)](../../03-networking-basics/README.md) (understanding of VPCs, subnets, security groups, and Availability Zones)
- Completed [Module 04: Compute with Amazon EC2](../../04-compute-ec2/README.md) (understanding of EC2 instances, user data scripts, and security groups)
- Completed [Module 07: Load Balancing and DNS](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

75 minutes

## Instructions

### Step 1: Identify the Default VPC and Subnets

Before creating any resources, confirm that your account has a [default VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) in `us-east-1` and identify subnets in two different Availability Zones.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as `bootcamp-admin`.
2. Verify that the Region selector in the top-right corner displays **US East (N. Virginia) us-east-1**.
3. In the search bar at the top, type `VPC` and select **VPC** from the results.
4. In the left navigation pane, choose **Your VPCs**.
5. Locate the VPC with **Default VPC** set to **Yes**. Note the VPC ID.

**Expected result:** You see a VPC marked as the default VPC with a CIDR block of `172.31.0.0/16` (or similar).

**CLI equivalent:**

```bash
DEFAULT_VPC_ID=$(aws ec2 describe-vpcs \
  --filters "Name=is-default,Values=true" \
  --query "Vpcs[0].VpcId" \
  --output text \
  --region us-east-1)
echo "Default VPC: $DEFAULT_VPC_ID"
```

6. In the left navigation pane, choose **Subnets**. Filter by the default VPC. Note the subnet IDs for subnets in `us-east-1a` and `us-east-1b`.

```bash
aws ec2 describe-subnets \
  --filters "Name=vpc-id,Values=$DEFAULT_VPC_ID" \
  --query "Subnets[].{SubnetId:SubnetId,AZ:AvailabilityZone,CidrBlock:CidrBlock}" \
  --output table \
  --region us-east-1
```

7. Store the subnet IDs for later use:

```bash
SUBNET_1A=$(aws ec2 describe-subnets \
  --filters "Name=vpc-id,Values=$DEFAULT_VPC_ID" "Name=availability-zone,Values=us-east-1a" \
  --query "Subnets[0].SubnetId" \
  --output text \
  --region us-east-1)

SUBNET_1B=$(aws ec2 describe-subnets \
  --filters "Name=vpc-id,Values=$DEFAULT_VPC_ID" "Name=availability-zone,Values=us-east-1b" \
  --query "Subnets[0].SubnetId" \
  --output text \
  --region us-east-1)

echo "Subnet 1a: $SUBNET_1A"
echo "Subnet 1b: $SUBNET_1B"
```

> **Tip:** If your account does not have a default VPC, you can recreate it by running `aws ec2 create-default-vpc --region us-east-1`.

### Step 2: Create Security Groups

You need two [security groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html): one for the ALB (allowing HTTP from the internet) and one for the EC2 instances (allowing HTTP only from the ALB security group). This follows the security best practice of restricting instance access to traffic that passes through the load balancer.

#### 2a: Create the ALB Security Group

**Console:**

1. In the VPC console left navigation pane, choose **Security groups**.
2. Choose **Create security group**.
3. Configure the following settings:
   - **Security group name:** `lab07-alb-sg`
   - **Description:** `Allow HTTP inbound from anywhere for ALB`
   - **VPC:** Select the default VPC
4. Under **Inbound rules**, choose **Add rule** and configure:

   | Type | Protocol | Port range | Source | Description |
   |------|----------|------------|--------|-------------|
   | HTTP | TCP | 80 | 0.0.0.0/0 | Allow HTTP from anywhere |

5. Leave the **Outbound rules** as the default (all traffic allowed to 0.0.0.0/0).
6. Choose **Create security group**.
7. Note the security group ID for `lab07-alb-sg`.

**Expected result:** The Security groups page lists `lab07-alb-sg` with 1 inbound rule.

**CLI equivalent:**

```bash
ALB_SG_ID=$(aws ec2 create-security-group \
  --group-name lab07-alb-sg \
  --description "Allow HTTP inbound from anywhere for ALB" \
  --vpc-id $DEFAULT_VPC_ID \
  --query "GroupId" \
  --output text \
  --region us-east-1)
echo "ALB Security Group: $ALB_SG_ID"
```

```bash
aws ec2 authorize-security-group-ingress \
  --group-id $ALB_SG_ID \
  --ip-permissions \
    'IpProtocol=tcp,FromPort=80,ToPort=80,IpRanges=[{CidrIp=0.0.0.0/0,Description="Allow HTTP from anywhere"}]' \
  --region us-east-1
```

#### 2b: Create the Web Server Security Group

This security group allows HTTP traffic only from the ALB security group, not from the entire internet. This ensures that users access your instances through the load balancer.

**Console:**

1. Choose **Create security group** again.
2. Configure the following settings:
   - **Security group name:** `lab07-web-sg`
   - **Description:** `Allow HTTP from ALB security group only`
   - **VPC:** Select the default VPC
3. Under **Inbound rules**, choose **Add rule** and configure:

   | Type | Protocol | Port range | Source | Description |
   |------|----------|------------|--------|-------------|
   | HTTP | TCP | 80 | Select `lab07-alb-sg` (type the name to search) | Allow HTTP from ALB |

4. Leave the **Outbound rules** as the default.
5. Choose **Create security group**.

**Expected result:** The Security groups page lists `lab07-web-sg` with 1 inbound rule referencing `lab07-alb-sg`.

**CLI equivalent:**

```bash
WEB_SG_ID=$(aws ec2 create-security-group \
  --group-name lab07-web-sg \
  --description "Allow HTTP from ALB security group only" \
  --vpc-id $DEFAULT_VPC_ID \
  --query "GroupId" \
  --output text \
  --region us-east-1)
echo "Web Security Group: $WEB_SG_ID"
```

```bash
aws ec2 authorize-security-group-ingress \
  --group-id $WEB_SG_ID \
  --ip-permissions \
    "IpProtocol=tcp,FromPort=80,ToPort=80,UserIdGroupPairs=[{GroupId=$ALB_SG_ID,Description=Allow HTTP from ALB}]" \
  --region us-east-1
```

> **Tip:** Referencing a security group as the source (instead of an IP range) means that any resource associated with that security group is allowed. When the ALB sends health check requests or forwards client traffic, those requests originate from the ALB's network interfaces, which are associated with `lab07-alb-sg`.

### Step 3: Launch Two EC2 Instances in Different Availability Zones

In this step, you launch two [t2.micro EC2 instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) with [user data](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html) scripts that install Apache and display the instance ID and Availability Zone on the web page. Placing the instances in different AZs demonstrates how the ALB distributes traffic across zones.

#### User Data Script

Both instances use the following user data script. The script installs Apache, retrieves the instance metadata, and creates a custom HTML page:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
INSTANCE_ID=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/instance-id)
AVAILABILITY_ZONE=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/placement/availability-zone)
cat <<EOF > /var/www/html/index.html
<h1>Hello from the ALB Lab</h1>
<p><strong>Instance ID:</strong> ${INSTANCE_ID}</p>
<p><strong>Availability Zone:</strong> ${AVAILABILITY_ZONE}</p>
EOF
```

> **Tip:** This script uses [Instance Metadata Service Version 2 (IMDSv2)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html), which requires a session token. IMDSv2 is the recommended approach because it provides additional protection against certain types of attacks compared to IMDSv1.

#### 3a: Launch Instance 1 in us-east-1a

**Console:**

1. In the console search bar, type `EC2` and select **EC2** from the results.
2. In the left navigation pane, choose **Instances**.
3. Choose **Launch instances**.
4. Configure the instance:
   - **Name:** `lab07-web-1`
   - **Application and OS Images:** Select **Amazon Linux 2023 AMI** (Free tier eligible). Confirm the architecture is **64-bit (x86)**.
   - **Instance type:** `t2.micro` (Free tier eligible)
   - **Key pair:** Choose **Proceed without a key pair**
5. Under **Network settings**, choose **Edit** and configure:
   - **VPC:** Select the default VPC
   - **Subnet:** Select the subnet in `us-east-1a`
   - **Auto-assign public IP:** Enable
   - **Firewall (security groups):** Select **Select existing security group**
   - **Common security groups:** Select `lab07-web-sg`
6. Leave **Configure storage** at the default (8 GiB gp3 root volume).
7. Expand **Advanced details**. Scroll down to the **User data** field and paste the user data script from above.
8. Choose **Launch instance**.

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


Then launch Instance 1:

```bash
INSTANCE_1_ID=$(aws ec2 run-instances \
  --image-id $AMI_ID \
  --instance-type t2.micro \
  --subnet-id $SUBNET_1A \
  --security-group-ids $WEB_SG_ID \
  --associate-public-ip-address \
  --user-data '#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
INSTANCE_ID=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/instance-id)
AVAILABILITY_ZONE=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone)
cat <<EOF > /var/www/html/index.html
<h1>Hello from the ALB Lab</h1>
<p><strong>Instance ID:</strong> ${INSTANCE_ID}</p>
<p><strong>Availability Zone:</strong> ${AVAILABILITY_ZONE}</p>
EOF' \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=lab07-web-1}]' \
  --query "Instances[0].InstanceId" \
  --output text \
  --region us-east-1)
echo "Instance 1: $INSTANCE_1_ID"
```

#### 3b: Launch Instance 2 in us-east-1b

Repeat the same process for the second instance, but select the subnet in `us-east-1b`.

**Console:**

1. Choose **Launch instances** again.
2. Configure the instance with the same settings as Instance 1, except:
   - **Name:** `lab07-web-2`
   - **Subnet:** Select the subnet in `us-east-1b`
3. Paste the same user data script.
4. Choose **Launch instance**.

**CLI equivalent:**

```bash
INSTANCE_2_ID=$(aws ec2 run-instances \
  --image-id $AMI_ID \
  --instance-type t2.micro \
  --subnet-id $SUBNET_1B \
  --security-group-ids $WEB_SG_ID \
  --associate-public-ip-address \
  --user-data '#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
INSTANCE_ID=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/instance-id)
AVAILABILITY_ZONE=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone)
cat <<EOF > /var/www/html/index.html
<h1>Hello from the ALB Lab</h1>
<p><strong>Instance ID:</strong> ${INSTANCE_ID}</p>
<p><strong>Availability Zone:</strong> ${AVAILABILITY_ZONE}</p>
EOF' \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=lab07-web-2}]' \
  --query "Instances[0].InstanceId" \
  --output text \
  --region us-east-1)
echo "Instance 2: $INSTANCE_2_ID"
```

5. Wait for both instances to reach the `running` state:

```bash
aws ec2 wait instance-running \
  --instance-ids $INSTANCE_1_ID $INSTANCE_2_ID \
  --region us-east-1
echo "Both instances are running"
```

6. Verify both instances are running:

```bash
aws ec2 describe-instances \
  --instance-ids $INSTANCE_1_ID $INSTANCE_2_ID \
  --query "Reservations[].Instances[].{Name:Tags[?Key=='Name']|[0].Value,InstanceId:InstanceId,AZ:Placement.AvailabilityZone,State:State.Name,PublicIP:PublicIpAddress}" \
  --output table \
  --region us-east-1
```

**Expected result:** Both instances show state `running` with public IP addresses, one in `us-east-1a` and one in `us-east-1b`.

> **Tip:** Wait 2 to 3 minutes after the instances reach the `running` state for the user data script to finish installing and starting Apache. You can verify by navigating to `http://<PUBLIC_IP>` of either instance in your browser. Note that the instances accept HTTP traffic from the ALB security group, not from the internet directly. If you want to test the instances individually before creating the ALB, temporarily add an inbound rule to `lab07-web-sg` allowing HTTP from `0.0.0.0/0`, then remove it after testing.

### Step 4: Create a Target Group

A [target group](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html) tells the ALB where to send traffic. You register your two EC2 instances as targets and configure health checks so the ALB sends traffic only to healthy instances.

**Console:**

1. In the EC2 console left navigation pane, under **Load Balancing**, choose **Target Groups**.
2. Choose **Create target group**.
3. On the **Specify group details** page, configure:
   - **Choose a target type:** Select **Instances**
   - **Target group name:** `lab07-target-group`
   - **Protocol:** HTTP
   - **Port:** 80
   - **VPC:** Select the default VPC
   - **Protocol version:** HTTP1
4. Under **Health checks**, configure:
   - **Health check protocol:** HTTP
   - **Health check path:** `/`
5. Expand **Advanced health check settings** and review the defaults:
   - **Port:** Traffic port
   - **Healthy threshold:** 5
   - **Unhealthy threshold:** 2
   - **Timeout:** 5 seconds
   - **Interval:** 30 seconds
   - **Success codes:** 200
6. Choose **Next**.
7. On the **Register targets** page, select both instances (`lab07-web-1` and `lab07-web-2`) from the list.
8. Choose **Include as pending below**.
9. Verify both instances appear in the **Review targets** section with port 80.
10. Choose **Create target group**.

**Expected result:** The Target groups page lists `lab07-target-group` with 2 registered targets.

**CLI equivalent:**

```bash
TARGET_GROUP_ARN=$(aws elbv2 create-target-group \
  --name lab07-target-group \
  --protocol HTTP \
  --port 80 \
  --vpc-id $DEFAULT_VPC_ID \
  --health-check-protocol HTTP \
  --health-check-path "/" \
  --health-check-port "traffic-port" \
  --healthy-threshold-count 5 \
  --unhealthy-threshold-count 2 \
  --health-check-timeout-seconds 5 \
  --health-check-interval-seconds 30 \
  --matcher "HttpCode=200" \
  --target-type instance \
  --query "TargetGroups[0].TargetGroupArn" \
  --output text \
  --region us-east-1)
echo "Target Group ARN: $TARGET_GROUP_ARN"
```

Register both instances with the target group:

```bash
aws elbv2 register-targets \
  --target-group-arn $TARGET_GROUP_ARN \
  --targets Id=$INSTANCE_1_ID Id=$INSTANCE_2_ID \
  --region us-east-1
echo "Targets registered"
```

Verify the registered targets:

```bash
aws elbv2 describe-target-health \
  --target-group-arn $TARGET_GROUP_ARN \
  --query "TargetHealthDescriptions[].{InstanceId:Target.Id,Port:Target.Port,Health:TargetHealth.State}" \
  --output table \
  --region us-east-1
```

> **Tip:** The targets will show as `unused` until you attach the target group to a load balancer listener. After you create the ALB and listener in the next steps, the health checks begin and the targets transition to `initial`, then to `healthy` (if the web server is responding).

### Step 5: Create the Application Load Balancer

An [Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) distributes incoming HTTP traffic across the targets in your target group. The ALB must be deployed in at least two Availability Zones for high availability.

**Console:**

1. In the EC2 console left navigation pane, under **Load Balancing**, choose **Load Balancers**.
2. Choose **Create load balancer**.
3. Under **Application Load Balancer**, choose **Create**.
4. On the **Create Application Load Balancer** page, configure:
   - **Load balancer name:** `lab07-alb`
   - **Scheme:** Internet-facing
   - **IP address type:** IPv4
5. Under **Network mapping**:
   - **VPC:** Select the default VPC
   - **Mappings:** Select at least two Availability Zones: `us-east-1a` and `us-east-1b`. Select the corresponding default subnets.
6. Under **Security groups**:
   - Remove the default security group if pre-selected.
   - Select `lab07-alb-sg`.
7. Under **Listeners and routing**:
   - **Protocol:** HTTP
   - **Port:** 80
   - **Default action:** Forward to `lab07-target-group`
8. Review the summary and choose **Create load balancer**.

**Expected result:** A success message confirms the ALB was created. The load balancer state starts as `provisioning` and transitions to `active` within 2 to 5 minutes.

**CLI equivalent:**

```bash
ALB_ARN=$(aws elbv2 create-load-balancer \
  --name lab07-alb \
  --subnets $SUBNET_1A $SUBNET_1B \
  --security-groups $ALB_SG_ID \
  --scheme internet-facing \
  --type application \
  --ip-address-type ipv4 \
  --query "LoadBalancers[0].LoadBalancerArn" \
  --output text \
  --region us-east-1)
echo "ALB ARN: $ALB_ARN"
```

Create the HTTP listener on port 80 that forwards traffic to the target group:

```bash
aws elbv2 create-listener \
  --load-balancer-arn $ALB_ARN \
  --protocol HTTP \
  --port 80 \
  --default-actions Type=forward,TargetGroupArn=$TARGET_GROUP_ARN \
  --region us-east-1
```

Expected output (partial):

```json
{
    "Listeners": [
        {
            "ListenerArn": "arn:aws:elasticloadbalancing:us-east-1:123456789012:listener/app/lab07-alb/...",
            "LoadBalancerArn": "arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/app/lab07-alb/...",
            "Port": 80,
            "Protocol": "HTTP",
            "DefaultActions": [
                {
                    "Type": "forward",
                    "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/lab07-target-group/..."
                }
            ]
        }
    ]
}
```

Wait for the ALB to become active:

```bash
aws elbv2 wait load-balancer-available \
  --load-balancer-arns $ALB_ARN \
  --region us-east-1
echo "ALB is active"
```

Retrieve the ALB DNS name:

```bash
ALB_DNS=$(aws elbv2 describe-load-balancers \
  --load-balancer-arns $ALB_ARN \
  --query "LoadBalancers[0].DNSName" \
  --output text \
  --region us-east-1)
echo "ALB DNS: $ALB_DNS"
```

### Step 6: Test Load Balancing

Now that the ALB is active and the listener is forwarding traffic to the target group, you can test that traffic is distributed across both instances.

1. Wait for the ALB to reach the `active` state (2 to 5 minutes after creation).
2. Retrieve the ALB DNS name from the Load Balancers page in the console. It follows this format:

```
lab07-alb-1234567890.us-east-1.elb.amazonaws.com
```

3. Open a new browser tab and navigate to `http://<ALB_DNS_NAME>` (replace `<ALB_DNS_NAME>` with the actual DNS name).

**Expected result:** The browser displays a page with the heading "Hello from the ALB Lab" along with an instance ID and Availability Zone.

4. Refresh the page several times (press F5 or Ctrl+R). Observe that the instance ID and Availability Zone change between refreshes. This confirms the ALB is distributing traffic across both instances.

> **Tip:** If you do not see the instance ID changing, try opening the URL in a private/incognito browser window or clear your browser cache. Some browsers may cache the response. You can also test from the CLI, which does not cache responses.

5. Test from the CLI by running the `curl` command multiple times:

```bash
for i in 1 2 3 4 5 6; do
  echo "--- Request $i ---"
  curl -s http://$ALB_DNS
  echo ""
done
```

Expected output (instance IDs and AZs alternate):

```
--- Request 1 ---
<h1>Hello from the ALB Lab</h1>
<p><strong>Instance ID:</strong> i-0abc123def456789</p>
<p><strong>Availability Zone:</strong> us-east-1a</p>
--- Request 2 ---
<h1>Hello from the ALB Lab</h1>
<p><strong>Instance ID:</strong> i-0def456abc789012</p>
<p><strong>Availability Zone:</strong> us-east-1b</p>
--- Request 3 ---
<h1>Hello from the ALB Lab</h1>
<p><strong>Instance ID:</strong> i-0abc123def456789</p>
<p><strong>Availability Zone:</strong> us-east-1a</p>
...
```

6. Verify the target health status:

```bash
aws elbv2 describe-target-health \
  --target-group-arn $TARGET_GROUP_ARN \
  --query "TargetHealthDescriptions[].{InstanceId:Target.Id,Port:Target.Port,Health:TargetHealth.State}" \
  --output table \
  --region us-east-1
```

Expected output:

```
---------------------------------------------
|          DescribeTargetHealth             |
+----------+--------+---------------------+
| Health   | InstanceId          | Port    |
+----------+---------------------+---------+
| healthy  | i-0abc123def456789  | 80      |
| healthy  | i-0def456abc789012  | 80      |
+----------+---------------------+---------+
```

Both targets should show `healthy`. If a target shows `unhealthy` or `initial`, wait another minute for the health checks to complete and try again.

### Step 7: Observe Health Check Behavior

In this step, you stop the web server on one instance to observe how the ALB [health checks](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html) detect the failure and route traffic only to the healthy instance.

1. In the EC2 console, select `lab07-web-1` and choose **Connect**.
2. On the **EC2 Instance Connect** tab, choose **Connect**.

> **Tip:** To use EC2 Instance Connect, you need to temporarily add an SSH inbound rule to `lab07-web-sg`. In the VPC console, edit `lab07-web-sg` and add an inbound rule: Type SSH, Port 22, Source 0.0.0.0/0. You will remove this rule after testing.

3. In the terminal session, stop the Apache web server:

```bash
sudo systemctl stop httpd
```

4. Verify that Apache is stopped:

```bash
sudo systemctl status httpd
```

Expected output (partial):

```
● httpd.service - The Apache HTTP Server
     Active: inactive (dead)
```

5. Wait approximately 60 seconds. The ALB performs health checks every 30 seconds, and the unhealthy threshold is 2 consecutive failures. After two failed checks (approximately 60 seconds), the ALB marks the instance as unhealthy.

6. Check the target health status:

```bash
aws elbv2 describe-target-health \
  --target-group-arn $TARGET_GROUP_ARN \
  --query "TargetHealthDescriptions[].{InstanceId:Target.Id,Health:TargetHealth.State,Reason:TargetHealth.Reason}" \
  --output table \
  --region us-east-1
```

Expected output:

```
-----------------------------------------------------------------
|                    DescribeTargetHealth                        |
+---------------------+-----------+-----------------------------+
| Health              | InstanceId          | Reason            |
+---------------------+---------------------+-------------------+
| unhealthy           | i-0abc123def456789  | Target.Timeout    |
| healthy             | i-0def456abc789012  | None              |
+---------------------+---------------------+-------------------+
```

7. Test the ALB again. Run the `curl` command multiple times:

```bash
for i in 1 2 3 4; do
  echo "--- Request $i ---"
  curl -s http://$ALB_DNS
  echo ""
done
```

**Expected result:** All requests now return the instance ID and AZ of `lab07-web-2` only. The ALB has stopped sending traffic to the unhealthy instance.

8. Restart the web server on `lab07-web-1`:

```bash
sudo systemctl start httpd
```

9. Wait approximately 2 to 3 minutes. The ALB needs 5 consecutive successful health checks (at 30-second intervals) to mark the instance as healthy again.

10. Check the target health status again:

```bash
aws elbv2 describe-target-health \
  --target-group-arn $TARGET_GROUP_ARN \
  --query "TargetHealthDescriptions[].{InstanceId:Target.Id,Health:TargetHealth.State}" \
  --output table \
  --region us-east-1
```

**Expected result:** Both targets show `healthy` again. Traffic is once again distributed across both instances.

11. If you added an SSH rule to `lab07-web-sg` in step 2, remove it now. In the VPC console, edit `lab07-web-sg`, select the SSH inbound rule, and choose **Delete**.

> **Warning:** Always remove temporary SSH rules after testing. Leaving port 22 open to `0.0.0.0/0` is a security risk in any environment beyond a short-lived lab.

### Step 8: (Optional) Configure Route 53 with an Alias Record

This step is optional. If you own a domain name, you can create a [Route 53 hosted zone](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-working-with.html) and an [Alias record](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html) that points to your ALB. If you do not own a domain, read through the steps to understand the process conceptually.

#### Why Use an Alias Record?

An Alias record maps your domain name (for example, `app.example.com`) directly to the ALB DNS name. Unlike a CNAME record, an Alias record:

- Works at the zone apex (for example, `example.com` without a subdomain prefix)
- Incurs no Route 53 query charges for queries to AWS resources
- Automatically tracks changes to the ALB's IP addresses

#### If You Have a Domain

1. In the console search bar, type `Route 53` and select **Route 53** from the results.
2. In the left navigation pane, choose **Hosted zones**.
3. If you already have a hosted zone for your domain, select it. Otherwise, choose **Create hosted zone**:
   - **Domain name:** Enter your domain (for example, `example.com`)
   - **Type:** Public hosted zone
   - Choose **Create hosted zone**.
4. If you created a new hosted zone, update the name server (NS) records at your domain registrar to point to the four Route 53 name servers listed in the NS record of your hosted zone.

> **Warning:** Changing name servers at your registrar affects all DNS resolution for your domain. If you are using the domain for other services, proceed with caution. NS propagation can take up to 48 hours.

5. In the hosted zone, choose **Create record**.
6. Configure the record:
   - **Record name:** Enter a subdomain (for example, `app`) or leave blank for the zone apex
   - **Record type:** A
   - **Alias:** Toggle on
   - **Route traffic to:** Select **Alias to Application and Classic Load Balancer**
   - **Region:** US East (N. Virginia) [us-east-1]
   - **Load balancer:** Select `lab07-alb`
   - **Routing policy:** Simple routing
7. Choose **Create records**.

**Expected result:** The hosted zone now contains an A record (Alias) pointing to the ALB.

**CLI equivalent:**

First, retrieve the ALB hosted zone ID (this is the Route 53 hosted zone ID for the ALB, not your domain's hosted zone):

```bash
ALB_HOSTED_ZONE_ID=$(aws elbv2 describe-load-balancers \
  --load-balancer-arns $ALB_ARN \
  --query "LoadBalancers[0].CanonicalHostedZoneId" \
  --output text \
  --region us-east-1)
echo "ALB Hosted Zone ID: $ALB_HOSTED_ZONE_ID"
```

Then create the Alias record (replace `HOSTED_ZONE_ID` with your domain's hosted zone ID and `app.example.com` with your actual record name):

```bash
aws route53 change-resource-record-sets \
  --hosted-zone-id HOSTED_ZONE_ID \
  --change-batch '{
    "Changes": [
      {
        "Action": "UPSERT",
        "ResourceRecordSet": {
          "Name": "app.example.com",
          "Type": "A",
          "AliasTarget": {
            "HostedZoneId": "'$ALB_HOSTED_ZONE_ID'",
            "DNSName": "'$ALB_DNS'",
            "EvaluateTargetHealth": true
          }
        }
      }
    ]
  }'
```

8. Wait 1 to 2 minutes for DNS propagation, then test:

```bash
curl http://app.example.com
```

**Expected result:** The response shows the same "Hello from the ALB Lab" page, served through your custom domain name.

#### If You Do Not Have a Domain

You can still understand the Route 53 workflow conceptually. The process involves three steps:

1. **Create a hosted zone** in Route 53 for your domain. Route 53 assigns four name servers to the hosted zone.
2. **Update name servers** at your domain registrar to point to the Route 53 name servers. This delegates DNS resolution for your domain to Route 53.
3. **Create an Alias record** in the hosted zone that maps your domain (or a subdomain) to the ALB DNS name. Route 53 resolves queries for your domain by returning the ALB's IP addresses.

The ALB DNS name (for example, `lab07-alb-1234567890.us-east-1.elb.amazonaws.com`) works without Route 53. Route 53 adds the ability to use a custom, human-readable domain name and enables advanced routing policies such as weighted, latency-based, and failover routing.

## Validation

Confirm the following:

- [ ] Two EC2 instances (`lab07-web-1` and `lab07-web-2`) are running in different Availability Zones (`us-east-1a` and `us-east-1b`)
- [ ] A security group named `lab07-alb-sg` exists with an inbound rule allowing HTTP (port 80) from `0.0.0.0/0`
- [ ] A security group named `lab07-web-sg` exists with an inbound rule allowing HTTP (port 80) from `lab07-alb-sg`
- [ ] A target group named `lab07-target-group` exists with both instances registered and showing `healthy`
- [ ] An Application Load Balancer named `lab07-alb` exists in `active` state with an HTTP listener on port 80
- [ ] Navigating to `http://<ALB_DNS_NAME>` displays the web page, and refreshing shows traffic alternating between the two instances (different instance IDs and AZs)
- [ ] Stopping the web server on one instance causes the ALB to route all traffic to the remaining healthy instance
- [ ] Restarting the web server causes the instance to return to `healthy` status and receive traffic again

You can verify the full setup with these commands:

```bash
# Check ALB status
aws elbv2 describe-load-balancers \
  --names lab07-alb \
  --query "LoadBalancers[0].{Name:LoadBalancerName,State:State.Code,DNS:DNSName}" \
  --output table \
  --region us-east-1

# Check target health
aws elbv2 describe-target-health \
  --target-group-arn $TARGET_GROUP_ARN \
  --query "TargetHealthDescriptions[].{InstanceId:Target.Id,Health:TargetHealth.State}" \
  --output table \
  --region us-east-1
```

## Cleanup

Delete all resources created in this lab to avoid unexpected charges. Follow this order to avoid dependency errors.

> **Warning:** The ALB incurs hourly charges while it exists, even if no traffic is flowing. Delete it promptly after completing the lab.

**1. Delete the load balancer:**

**Console:** In the EC2 console, under **Load Balancing**, choose **Load Balancers**. Select `lab07-alb`, choose **Actions**, then **Delete load balancer**. Type `confirm` and choose **Delete**.

```bash
aws elbv2 delete-load-balancer \
  --load-balancer-arn $ALB_ARN \
  --region us-east-1
echo "ALB deleted"
```

**2. Delete the target group:**

Wait about 30 seconds after deleting the ALB, then delete the target group.

**Console:** Under **Load Balancing**, choose **Target Groups**. Select `lab07-target-group`, choose **Actions**, then **Delete**. Confirm the deletion.

```bash
aws elbv2 delete-target-group \
  --target-group-arn $TARGET_GROUP_ARN \
  --region us-east-1
echo "Target group deleted"
```

**3. Terminate the EC2 instances:**

**Console:** In the EC2 console, choose **Instances**. Select both `lab07-web-1` and `lab07-web-2`. Choose **Instance state**, then **Terminate instance**. Confirm the termination.

```bash
aws ec2 terminate-instances \
  --instance-ids $INSTANCE_1_ID $INSTANCE_2_ID \
  --region us-east-1
echo "Instances terminating"
```

Wait for the instances to terminate:

```bash
aws ec2 wait instance-terminated \
  --instance-ids $INSTANCE_1_ID $INSTANCE_2_ID \
  --region us-east-1
echo "Instances terminated"
```

**4. Delete the security groups:**

You must delete `lab07-web-sg` first because `lab07-alb-sg` is referenced in its inbound rules.

```bash
aws ec2 delete-security-group \
  --group-id $WEB_SG_ID \
  --region us-east-1
echo "Web security group deleted"
```

```bash
aws ec2 delete-security-group \
  --group-id $ALB_SG_ID \
  --region us-east-1
echo "ALB security group deleted"
```

**Console:** In the VPC console, choose **Security groups**. Select `lab07-web-sg`, choose **Actions**, then **Delete security groups**. Confirm. Repeat for `lab07-alb-sg`.

**5. (Optional) Delete the Route 53 record and hosted zone:**

If you created a Route 53 Alias record in Step 8, delete it:

**Console:** In the Route 53 console, choose **Hosted zones**. Select your hosted zone. Select the A record you created, choose **Delete record**, and confirm.

If you created a new hosted zone specifically for this lab and no longer need it:

**Console:** Delete all records in the hosted zone (except the NS and SOA records, which are deleted automatically). Then select the hosted zone and choose **Delete hosted zone**.

> **Warning:** Do not delete a hosted zone that is actively serving DNS for other services. Deleting the hosted zone removes all DNS records and stops DNS resolution for the domain.

## Challenge (Optional)

Using only the AWS Management Console and concepts from Modules 01 through 07, complete the following:

1. **Path-based routing:** Create a second target group named `lab07-api-target-group` with the same two instances. Add a new listener rule on the ALB that routes requests with the path `/api/*` to this second target group. Modify the user data on one instance to serve a different page at `/api/index.html`. Test by navigating to `http://<ALB_DNS_NAME>/api/` and `http://<ALB_DNS_NAME>/` to confirm different routing behavior.

2. **Connection draining observation:** Set the deregistration delay on `lab07-target-group` to 30 seconds (instead of the default 300 seconds). Deregister one instance from the target group and observe how quickly it stops receiving new requests. Re-register the instance after testing.

3. **Health check tuning:** Modify the health check settings on `lab07-target-group` to use a custom path `/health` instead of `/`. SSH into both instances and create a `/var/www/html/health/index.html` file that returns a simple "OK" message. Observe the health check behavior with the new path.

> **Tip:** Remember to clean up any additional resources you create during the challenge (extra target groups, listener rules) to avoid charges.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
