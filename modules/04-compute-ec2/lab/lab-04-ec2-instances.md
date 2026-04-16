# Lab 04: Launching and Managing EC2 Instances

## Objective

Launch an Amazon EC2 instance with a user data script that installs a web server, configure security groups, connect using EC2 Instance Connect, attach and snapshot an EBS volume, and create a launch template with an Auto Scaling group.

## Architecture Diagram

This lab builds a compute environment in the default VPC using EC2 instances, EBS volumes, a launch template, and an Auto Scaling group:

```
Internet
    |
    v
Default VPC (us-east-1)
    |
    ├── Security Group: lab04-web-sg
    |       Inbound: HTTP (80) from 0.0.0.0/0, SSH (22) from 0.0.0.0/0
    |       Outbound: All traffic
    |
    ├── EC2 Instance: lab04-web-server (t2.micro, Amazon Linux 2023)
    |       ├── Root Volume: gp3, 8 GiB
    |       ├── Additional Volume: lab04-data-volume (gp3, 20 GiB)
    |       |       └── EBS Snapshot: lab04-data-snapshot
    |       └── User Data: Apache httpd installed and running
    |
    ├── Launch Template: lab04-launch-template
    |       ├── AMI: Amazon Linux 2023
    |       ├── Instance type: t2.micro
    |       ├── Security group: lab04-web-sg
    |       └── User data: Apache httpd
    |
    └── Auto Scaling Group: lab04-asg
            ├── Min: 1, Desired: 2, Max: 4
            ├── AZ: us-east-1a, us-east-1b
            └── Launch Template: lab04-launch-template
```

You will use the [default VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) for this lab. The default VPC comes pre-configured with public subnets, an internet gateway, and a route table, so you can focus on compute concepts without additional networking setup.

## Prerequisites

- Completed [Lab 01: AWS Account Setup and Console Tour](../../01-cloud-fundamentals/lab/lab-01-aws-account-setup.md)
- Completed [Module 02: Identity and Access Management (IAM) and Security](../../02-iam-and-security/README.md) (understanding of IAM roles and security)
- Completed [Module 03: Networking Basics (VPC)](../../03-networking-basics/README.md) (understanding of VPCs, subnets, and security groups)
- Completed [Module 04: Compute with Amazon EC2](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

90 minutes

## Instructions

### Step 1: Identify the Default VPC and Subnets

Before launching an EC2 instance, confirm that your account has a [default VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) in `us-east-1` and identify its subnets.

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

6. In the left navigation pane, choose **Subnets**. Filter by the default VPC. Note the subnet IDs for at least two subnets in different Availability Zones (for example, `us-east-1a` and `us-east-1b`). You will need these for the Auto Scaling group later.

```bash
aws ec2 describe-subnets \
  --filters "Name=vpc-id,Values=$DEFAULT_VPC_ID" \
  --query "Subnets[].{SubnetId:SubnetId,AZ:AvailabilityZone,CidrBlock:CidrBlock}" \
  --output table \
  --region us-east-1
```

7. Store two subnet IDs for later use:

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

> **Tip:** If your account does not have a default VPC (for example, if it was deleted), you can recreate it by running `aws ec2 create-default-vpc --region us-east-1`. This creates a new default VPC with the standard configuration.

### Step 2: Create a Security Group for the Web Server

In this step, you create a [security group](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html) that allows inbound HTTP traffic on port 80 (for the web server) and SSH traffic on port 22 (for EC2 Instance Connect).

**Console:**

1. In the VPC console left navigation pane, choose **Security groups**.
2. Choose **Create security group**.
3. Configure the following settings:
   - **Security group name:** `lab04-web-sg`
   - **Description:** `Allow HTTP and SSH inbound for Lab 04 web server`
   - **VPC:** Select the default VPC
4. Under **Inbound rules**, choose **Add rule** twice and configure:

   | Type | Protocol | Port range | Source | Description |
   |------|----------|------------|--------|-------------|
   | HTTP | TCP | 80 | 0.0.0.0/0 | Allow HTTP from anywhere |
   | SSH | TCP | 22 | 0.0.0.0/0 | Allow SSH for EC2 Instance Connect |

5. Leave the **Outbound rules** as the default (all traffic allowed to 0.0.0.0/0).
6. Choose **Create security group**.

**Expected result:** The Security groups page lists `lab04-web-sg` with 2 inbound rules.

> **Warning:** Allowing SSH from `0.0.0.0/0` is acceptable for this lab. In production, restrict SSH access to your specific IP address or use [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) to avoid opening port 22 entirely.

**CLI equivalent:**

```bash
WEB_SG_ID=$(aws ec2 create-security-group \
  --group-name lab04-web-sg \
  --description "Allow HTTP and SSH inbound for Lab 04 web server" \
  --vpc-id $DEFAULT_VPC_ID \
  --query "GroupId" \
  --output text \
  --region us-east-1)
echo "Security Group: $WEB_SG_ID"
```

```bash
aws ec2 authorize-security-group-ingress \
  --group-id $WEB_SG_ID \
  --ip-permissions \
    'IpProtocol=tcp,FromPort=80,ToPort=80,IpRanges=[{CidrIp=0.0.0.0/0,Description="Allow HTTP from anywhere"}]' \
    'IpProtocol=tcp,FromPort=22,ToPort=22,IpRanges=[{CidrIp=0.0.0.0/0,Description="Allow SSH for EC2 Instance Connect"}]' \
  --region us-east-1
```

### Step 3: Launch an EC2 Instance with a User Data Script

In this step, you launch a [t2.micro EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) running Amazon Linux 2023 with a [user data](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html) script that installs and starts the Apache web server.

**Console:**

1. In the console search bar, type `EC2` and select **EC2** from the results.
2. In the left navigation pane, choose **Instances**.
3. Choose **Launch instances**.
4. Configure the instance:
   - **Name:** `lab04-web-server`
   - **Application and OS Images:** Select **Amazon Linux 2023 AMI** (Free tier eligible). Confirm the architecture is **64-bit (x86)**.
   - **Instance type:** `t2.micro` (Free tier eligible)
   - **Key pair:** Choose **Proceed without a key pair** (you will use EC2 Instance Connect)
5. Under **Network settings**, choose **Edit** and configure:
   - **VPC:** Select the default VPC
   - **Subnet:** Select a subnet in `us-east-1a`
   - **Auto-assign public IP:** Enable
   - **Firewall (security groups):** Select **Select existing security group**
   - **Common security groups:** Select `lab04-web-sg`
6. Leave **Configure storage** at the default (8 GiB gp3 root volume).
7. Expand **Advanced details**. Scroll down to the **User data** field and paste the following script:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
INSTANCE_ID=$(ec2-metadata --instance-id | cut -d' ' -f2)
AVAILABILITY_ZONE=$(ec2-metadata --availability-zone | cut -d' ' -f2)
cat <<EOF > /var/www/html/index.html
<h1>Hello from EC2</h1>
<p>Instance ID: ${INSTANCE_ID}</p>
<p>Availability Zone: ${AVAILABILITY_ZONE}</p>
EOF
```

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

Then launch the instance:

```bash
WEB_INSTANCE_ID=$(aws ec2 run-instances \
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
INSTANCE_ID=$(ec2-metadata --instance-id | cut -d'"'"' '"'"' -f2)
AVAILABILITY_ZONE=$(ec2-metadata --availability-zone | cut -d'"'"' '"'"' -f2)
cat <<EOF > /var/www/html/index.html
<h1>Hello from EC2</h1>
<p>Instance ID: ${INSTANCE_ID}</p>
<p>Availability Zone: ${AVAILABILITY_ZONE}</p>
EOF' \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=lab04-web-server}]' \
  --query "Instances[0].InstanceId" \
  --output text \
  --region us-east-1)
echo "Instance: $WEB_INSTANCE_ID"
```

9. Wait for the instance to reach the `running` state:

```bash
aws ec2 wait instance-running --instance-ids $WEB_INSTANCE_ID --region us-east-1
echo "Instance is running"
```

10. Retrieve the public IP address:

```bash
WEB_PUBLIC_IP=$(aws ec2 describe-instances \
  --instance-ids $WEB_INSTANCE_ID \
  --query "Reservations[0].Instances[0].PublicIpAddress" \
  --output text \
  --region us-east-1)
echo "Public IP: $WEB_PUBLIC_IP"
```

### Step 4: Verify the Web Server Is Accessible

The user data script takes 1 to 2 minutes to complete after the instance reaches the `running` state. In this step, you verify that the Apache web server is serving your custom page.

1. Wait 2 to 3 minutes after the instance reaches the `running` state.
2. Open a new browser tab and navigate to `http://<PUBLIC_IP>` (replace `<PUBLIC_IP>` with the public IP address from Step 3).

**Expected result:** The browser displays a page with the heading "Hello from EC2" along with the instance ID and Availability Zone.

3. Verify from the CLI:

```bash
curl http://$WEB_PUBLIC_IP
```

Expected output:

```html
<h1>Hello from EC2</h1>
<p>Instance ID: i-0abc123def456789</p>
<p>Availability Zone: us-east-1a</p>
```

> **Tip:** If the page does not load, verify the following: (1) the instance state is `running`, (2) the security group `lab04-web-sg` has an inbound rule for HTTP on port 80, (3) the instance has a public IP address, and (4) enough time has passed for the user data script to finish. You can check the user data script log on the instance at `/var/log/cloud-init-output.log`.

### Step 5: Connect to the Instance Using EC2 Instance Connect

[EC2 Instance Connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-connect-methods.html) provides a browser-based SSH connection without requiring you to manage SSH key pairs.

**Console:**

1. In the EC2 console, choose **Instances** in the left navigation pane.
2. Select the checkbox next to `lab04-web-server`.
3. Choose **Connect**.
4. On the **EC2 Instance Connect** tab, verify the username is `ec2-user`.
5. Choose **Connect**.

**Expected result:** A new browser tab opens with a terminal session connected to the instance.

6. In the terminal, verify the web server is running:

```bash
systemctl status httpd
```

Expected output (partial):

```
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
     Active: active (running)
```

7. Verify the user data script output:

```bash
cat /var/www/html/index.html
```

Expected output:

```html
<h1>Hello from EC2</h1>
<p>Instance ID: i-0abc123def456789</p>
<p>Availability Zone: us-east-1a</p>
```

8. Check the instance metadata to see the instance type:

```bash
ec2-metadata --instance-type
```

Expected output:

```
instance-type: t2.micro
```

9. View the block devices attached to the instance:

```bash
lsblk
```

Expected output:

```
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
xvda    202:0    0   8G  0 disk
├─xvda1 202:1    0   8G  0 part /
```

This shows the 8 GiB root volume. You will attach an additional volume in the next step.

10. Keep this terminal session open. You will use it in the next step.


### Step 6: Create and Attach an Additional EBS Volume

[Amazon Elastic Block Store (Amazon EBS)](https://docs.aws.amazon.com/ebs/latest/userguide/what-is-ebs.html) provides persistent block storage for EC2 instances. In this step, you create a 20 GiB [gp3 volume](https://docs.aws.amazon.com/ebs/latest/userguide/general-purpose.html), attach it to your instance, format it, and mount it.

**Console:**

1. In the EC2 console left navigation pane, under **Elastic Block Store**, choose **Volumes**.
2. Choose **Create volume**.
3. Configure the following settings:
   - **Volume type:** General Purpose SSD (gp3)
   - **Size (GiB):** `20`
   - **Availability Zone:** Select the same AZ as your instance (for example, `us-east-1a`)
   - **Encryption:** Leave unchecked for this lab

> **Warning:** The EBS volume must be in the same Availability Zone as the EC2 instance. You cannot attach a volume in `us-east-1a` to an instance in `us-east-1b`.

4. Under **Tags**, choose **Add tag**:
   - **Key:** `Name`
   - **Value:** `lab04-data-volume`
5. Choose **Create volume**.

**Expected result:** The Volumes page shows `lab04-data-volume` with state `available` and size 20 GiB.

**CLI equivalent:**

```bash
INSTANCE_AZ=$(aws ec2 describe-instances \
  --instance-ids $WEB_INSTANCE_ID \
  --query "Reservations[0].Instances[0].Placement.AvailabilityZone" \
  --output text \
  --region us-east-1)
echo "Instance AZ: $INSTANCE_AZ"
```

```bash
VOLUME_ID=$(aws ec2 create-volume \
  --volume-type gp3 \
  --size 20 \
  --availability-zone $INSTANCE_AZ \
  --tag-specifications 'ResourceType=volume,Tags=[{Key=Name,Value=lab04-data-volume}]' \
  --query "VolumeId" \
  --output text \
  --region us-east-1)
echo "Volume: $VOLUME_ID"
```

6. Wait for the volume to become available:

```bash
aws ec2 wait volume-available --volume-ids $VOLUME_ID --region us-east-1
echo "Volume is available"
```

7. Attach the volume to your instance:

**Console:**

   - Select `lab04-data-volume` in the Volumes list.
   - Choose **Actions**, then **Attach volume**.
   - In the **Instance** field, select `lab04-web-server`.
   - The **Device name** field auto-populates (for example, `/dev/sdf`). Leave the default.
   - Choose **Attach volume**.

**Expected result:** The volume state changes from `available` to `in-use`.

**CLI equivalent:**

```bash
aws ec2 attach-volume \
  --volume-id $VOLUME_ID \
  --instance-id $WEB_INSTANCE_ID \
  --device /dev/sdf \
  --region us-east-1
```

Expected output:

```json
{
    "AttachTime": "2024-01-15T10:30:00.000Z",
    "Device": "/dev/sdf",
    "InstanceId": "i-0abc123def456789",
    "State": "attaching",
    "VolumeId": "vol-0abc123def456789"
}
```

8. Wait for the volume to attach:

```bash
aws ec2 wait volume-in-use --volume-ids $VOLUME_ID --region us-east-1
echo "Volume attached"
```

9. Switch to your EC2 Instance Connect terminal session (from Step 5). Verify the new volume is visible:

```bash
lsblk
```

Expected output:

```
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
xvda    202:0    0   8G  0 disk
├─xvda1 202:1    0   8G  0 part /
xvdf    202:80   0  20G  0 disk
```

The `xvdf` device is your new 20 GiB volume. It has no filesystem yet.

10. Create a filesystem on the new volume:

```bash
sudo mkfs -t xfs /dev/xvdf
```

Expected output:

```
meta-data=/dev/xvdf              isize=512    agcount=4, agsize=1310720 blks
         =                       sectsz=512   attr=2, projid32bit=1
data     =                       bsize=4096   blocks=5242880, imaxpct=25
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

11. Create a mount point and mount the volume:

```bash
sudo mkdir /data
sudo mount /dev/xvdf /data
```

12. Verify the volume is mounted:

```bash
df -h /data
```

Expected output:

```
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvdf        20G  175M   20G   1% /data
```

13. Write a test file to the new volume:

```bash
sudo sh -c 'echo "This file is on the additional EBS volume" > /data/test-file.txt'
cat /data/test-file.txt
```

Expected output:

```
This file is on the additional EBS volume
```

> **Tip:** This mount is temporary. If you stop and start the instance, you need to mount the volume again. To make the mount persistent across reboots, add an entry to `/etc/fstab`. For this lab, the temporary mount is sufficient.

### Step 7: Create an EBS Snapshot

An [EBS snapshot](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-snapshots.html) is a point-in-time backup of an EBS volume. Snapshots are incremental, meaning only the blocks that have changed since the last snapshot are saved. In this step, you create a snapshot of the additional volume.

**Console:**

1. In the EC2 console left navigation pane, under **Elastic Block Store**, choose **Volumes**.
2. Select `lab04-data-volume`.
3. Choose **Actions**, then **Create snapshot**.
4. Configure the following settings:
   - **Description:** `Snapshot of lab04-data-volume`
5. Under **Tags**, choose **Add tag**:
   - **Key:** `Name`
   - **Value:** `lab04-data-snapshot`
6. Choose **Create snapshot**.

**Expected result:** A success banner displays the snapshot ID. The snapshot state starts as `pending` and transitions to `completed` within a few minutes.

**CLI equivalent:**

```bash
SNAPSHOT_ID=$(aws ec2 create-snapshot \
  --volume-id $VOLUME_ID \
  --description "Snapshot of lab04-data-volume" \
  --tag-specifications 'ResourceType=snapshot,Tags=[{Key=Name,Value=lab04-data-snapshot}]' \
  --query "SnapshotId" \
  --output text \
  --region us-east-1)
echo "Snapshot: $SNAPSHOT_ID"
```

7. Wait for the snapshot to complete:

```bash
aws ec2 wait snapshot-completed --snapshot-ids $SNAPSHOT_ID --region us-east-1
echo "Snapshot completed"
```

8. Verify the snapshot details:

```bash
aws ec2 describe-snapshots \
  --snapshot-ids $SNAPSHOT_ID \
  --query "Snapshots[0].{SnapshotId:SnapshotId,VolumeId:VolumeId,State:State,VolumeSize:VolumeSize,Description:Description}" \
  --region us-east-1
```

Expected output:

```json
{
    "SnapshotId": "snap-0abc123def456789",
    "VolumeId": "vol-0abc123def456789",
    "State": "completed",
    "VolumeSize": 20,
    "Description": "Snapshot of lab04-data-volume"
}
```

> **Tip:** You can create a new EBS volume from this snapshot at any time, even in a different Availability Zone within the same Region. This is useful for migrating data between AZs or restoring from a backup.

### Step 8: Create a Launch Template

A [launch template](https://docs.aws.amazon.com/autoscaling/ec2/userguide/launch-templates.html) captures the configuration needed to launch EC2 instances. Auto Scaling groups use launch templates to launch new instances automatically. In this step, you create a launch template based on the configuration of your running instance.

**Console:**

1. In the EC2 console left navigation pane, choose **Launch Templates**.
2. Choose **Create launch template**.
3. Configure the following settings:
   - **Launch template name:** `lab04-launch-template`
   - **Template version description:** `Lab 04 web server template`
   - **Auto Scaling guidance:** Select the checkbox **Provide guidance to help me set up a template that I can use with EC2 Auto Scaling**
4. Under **Application and OS Images (Amazon Machine Image)**, choose **Quick Start**, then select **Amazon Linux 2023 AMI** (Free tier eligible, 64-bit x86).
5. Under **Instance type**, select `t2.micro`.
6. Under **Key pair (login)**, select **Don't include in launch template**.
7. Under **Network settings**:
   - **Firewall (security groups):** Select **Select existing security group**
   - **Security groups:** Select `lab04-web-sg`
8. Leave **Storage (volumes)** at the default (8 GiB gp3 root volume).
9. Expand **Advanced details**. Scroll down to the **User data** field and paste the following script:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
INSTANCE_ID=$(ec2-metadata --instance-id | cut -d' ' -f2)
AVAILABILITY_ZONE=$(ec2-metadata --availability-zone | cut -d' ' -f2)
cat <<EOF > /var/www/html/index.html
<h1>Hello from EC2 Auto Scaling</h1>
<p>Instance ID: ${INSTANCE_ID}</p>
<p>Availability Zone: ${AVAILABILITY_ZONE}</p>
EOF
```

10. Choose **Create launch template**.

**Expected result:** A success message confirms the launch template was created. The Launch Templates page lists `lab04-launch-template` with version 1.

**CLI equivalent:**

First, find the latest Amazon Linux 2023 AMI (if you have not already):

```bash
AMI_ID=$(aws ec2 describe-images \
  --owners amazon \
  --filters "Name=name,Values=al2023-ami-2023*-x86_64" "Name=state,Values=available" \
  --query "sort_by(Images, &CreationDate)[-1].ImageId" \
  --output text \
  --region us-east-1)
echo "AMI: $AMI_ID"
```

Then create the launch template:

```bash
aws ec2 create-launch-template \
  --launch-template-name lab04-launch-template \
  --version-description "Lab 04 web server template" \
  --launch-template-data "{
    \"ImageId\": \"$AMI_ID\",
    \"InstanceType\": \"t2.micro\",
    \"SecurityGroupIds\": [\"$WEB_SG_ID\"],
    \"UserData\": \"$(echo '#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
INSTANCE_ID=$(ec2-metadata --instance-id | cut -d'"'"' '"'"' -f2)
AVAILABILITY_ZONE=$(ec2-metadata --availability-zone | cut -d'"'"' '"'"' -f2)
cat <<EOF > /var/www/html/index.html
<h1>Hello from EC2 Auto Scaling</h1>
<p>Instance ID: \${INSTANCE_ID}</p>
<p>Availability Zone: \${AVAILABILITY_ZONE}</p>
EOF' | base64 -w 0)\"
  }" \
  --region us-east-1
```

Expected output (partial):

```json
{
    "LaunchTemplate": {
        "LaunchTemplateId": "lt-0abc123def456789",
        "LaunchTemplateName": "lab04-launch-template",
        "DefaultVersionNumber": 1,
        "LatestVersionNumber": 1
    }
}
```

### Step 9: Create an Auto Scaling Group

An [Auto Scaling group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-groups.html) manages a fleet of EC2 instances, automatically launching and terminating instances to maintain your desired capacity. In this step, you create an Auto Scaling group that spans two Availability Zones with a minimum of 1, desired of 2, and maximum of 4 instances.

**Console:**

1. In the EC2 console left navigation pane, choose **Auto Scaling Groups**.
2. Choose **Create Auto Scaling group**.
3. On the **Choose launch template or configuration** page:
   - **Auto Scaling group name:** `lab04-asg`
   - **Launch template:** Select `lab04-launch-template`
   - **Version:** Select **Latest**
4. Choose **Next**.
5. On the **Choose instance launch options** page:
   - **VPC:** Select the default VPC
   - **Availability Zones and subnets:** Select subnets in `us-east-1a` and `us-east-1b`
6. Choose **Next**.
7. On the **Configure advanced options** page:
   - **Load balancing:** Select **No load balancer** (you will add one in Module 07)
   - **Health checks:** Leave the default (EC2 health checks)
8. Choose **Next**.
9. On the **Configure group size and scaling** page:
   - **Desired capacity:** `2`
   - **Minimum capacity:** `1`
   - **Maximum capacity:** `4`
   - **Scaling policies:** Select **None** (you will configure scaling policies in Module 07)
10. Choose **Next**.
11. On the **Add notifications** page, choose **Next** (skip notifications for this lab).
12. On the **Add tags** page, choose **Add tag**:
    - **Key:** `Name`
    - **Value:** `lab04-asg-instance`
    - Verify **Tag new instances** is selected
13. Choose **Next**.
14. Review the configuration and choose **Create Auto Scaling group**.

**Expected result:** The Auto Scaling Groups page lists `lab04-asg`. After 1 to 2 minutes, the group launches 2 instances (the desired capacity) across the two selected Availability Zones.

**CLI equivalent:**

```bash
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name lab04-asg \
  --launch-template LaunchTemplateName=lab04-launch-template,Version='$Latest' \
  --min-size 1 \
  --desired-capacity 2 \
  --max-size 4 \
  --vpc-zone-identifier "$SUBNET_1A,$SUBNET_1B" \
  --tags "Key=Name,Value=lab04-asg-instance,PropagateAtLaunch=true" \
  --region us-east-1
```

15. Verify the Auto Scaling group status:

```bash
aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names lab04-asg \
  --query "AutoScalingGroups[0].{Name:AutoScalingGroupName,Min:MinSize,Desired:DesiredCapacity,Max:MaxSize,Instances:Instances[].{Id:InstanceId,AZ:AvailabilityZone,Health:HealthStatus}}" \
  --region us-east-1
```

Expected output (after instances launch):

```json
{
    "Name": "lab04-asg",
    "Min": 1,
    "Desired": 2,
    "Max": 4,
    "Instances": [
        {
            "Id": "i-0abc123def456789",
            "AZ": "us-east-1a",
            "Health": "Healthy"
        },
        {
            "Id": "i-0def456abc789012",
            "AZ": "us-east-1b",
            "Health": "Healthy"
        }
    ]
}
```

16. Verify that the Auto Scaling instances are running web servers. Retrieve their public IP addresses:

```bash
ASG_INSTANCE_IDS=$(aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names lab04-asg \
  --query "AutoScalingGroups[0].Instances[].InstanceId" \
  --output text \
  --region us-east-1)

for INSTANCE_ID in $ASG_INSTANCE_IDS; do
  PUBLIC_IP=$(aws ec2 describe-instances \
    --instance-ids $INSTANCE_ID \
    --query "Reservations[0].Instances[0].PublicIpAddress" \
    --output text \
    --region us-east-1)
  echo "Instance $INSTANCE_ID: http://$PUBLIC_IP"
done
```

17. Open each URL in a browser tab. Each instance displays a different instance ID and Availability Zone, confirming that the Auto Scaling group distributed instances across two AZs.

> **Tip:** The Auto Scaling group tries to balance instances evenly across the selected Availability Zones. With a desired capacity of 2 and two AZs, you should see one instance in each AZ.

## Validation

Confirm the following:

- [ ] A security group named `lab04-web-sg` exists with inbound rules for HTTP (port 80) and SSH (port 22)
- [ ] An EC2 instance named `lab04-web-server` is running with a public IP address
- [ ] Navigating to `http://<PUBLIC_IP>` of `lab04-web-server` displays "Hello from EC2" with the instance ID and AZ
- [ ] You can connect to `lab04-web-server` using EC2 Instance Connect from the console
- [ ] An EBS volume named `lab04-data-volume` (20 GiB, gp3) is attached to `lab04-web-server`
- [ ] The additional volume is formatted (xfs) and mounted at `/data` with a test file present
- [ ] A snapshot named `lab04-data-snapshot` exists with state `completed`
- [ ] A launch template named `lab04-launch-template` exists with the correct AMI, instance type, security group, and user data
- [ ] An Auto Scaling group named `lab04-asg` exists with min=1, desired=2, max=4
- [ ] The Auto Scaling group has 2 running instances distributed across `us-east-1a` and `us-east-1b`
- [ ] Each Auto Scaling instance serves a web page at its public IP address

You can verify the Auto Scaling group configuration:

```bash
aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names lab04-asg \
  --query "AutoScalingGroups[0].{Min:MinSize,Desired:DesiredCapacity,Max:MaxSize,AZs:AvailabilityZones,InstanceCount:length(Instances)}" \
  --region us-east-1
```

## Cleanup

Delete all resources created in this lab to avoid unexpected charges. Follow this order to avoid dependency errors.

> **Warning:** The Auto Scaling group will continue launching instances if you do not delete it first. Always delete the Auto Scaling group before deleting the launch template or security group.

**1. Delete the Auto Scaling group:**

The Auto Scaling group must be deleted first. This terminates all instances managed by the group.

**Console:** In the EC2 console, choose **Auto Scaling Groups** in the left navigation pane. Select `lab04-asg`, choose **Delete**, type `delete` to confirm, and choose **Delete**.

```bash
aws autoscaling delete-auto-scaling-group \
  --auto-scaling-group-name lab04-asg \
  --force-delete \
  --region us-east-1
```

> **Tip:** The `--force-delete` flag terminates all instances in the group and deletes the group in one step. Without this flag, you would need to set the desired capacity to 0 first and wait for instances to terminate.

Wait for the Auto Scaling instances to terminate (1 to 2 minutes):

```bash
echo "Waiting for ASG instances to terminate..."
sleep 60
```

**2. Delete the launch template:**

```bash
aws ec2 delete-launch-template \
  --launch-template-name lab04-launch-template \
  --region us-east-1
```

**Console:** In the EC2 console, choose **Launch Templates**. Select `lab04-launch-template`, choose **Actions**, then **Delete template**. Type `Delete` to confirm.

**3. Terminate the original EC2 instance:**

```bash
aws ec2 terminate-instances \
  --instance-ids $WEB_INSTANCE_ID \
  --region us-east-1
```

Wait for the instance to terminate:

```bash
aws ec2 wait instance-terminated --instance-ids $WEB_INSTANCE_ID --region us-east-1
echo "Instance terminated"
```

**Console:** In the EC2 console, choose **Instances**. Select `lab04-web-server`, choose **Instance state**, then **Terminate instance**. Confirm the termination.

**4. Delete the EBS snapshot:**

```bash
aws ec2 delete-snapshot --snapshot-id $SNAPSHOT_ID --region us-east-1
```

**Console:** In the EC2 console, under **Elastic Block Store**, choose **Snapshots**. Select `lab04-data-snapshot`, choose **Actions**, then **Delete snapshot**. Confirm the deletion.

**5. Delete the additional EBS volume:**

The volume must be detached before it can be deleted. Terminating the instance in step 3 automatically detaches the volume.

```bash
aws ec2 wait volume-available --volume-ids $VOLUME_ID --region us-east-1
aws ec2 delete-volume --volume-id $VOLUME_ID --region us-east-1
```

**Console:** In the EC2 console, under **Elastic Block Store**, choose **Volumes**. Select `lab04-data-volume` (its state should be `available` after the instance was terminated), choose **Actions**, then **Delete volume**. Confirm the deletion.

> **Tip:** If the volume state is still `in-use`, wait a minute for the instance termination to complete and the volume to detach automatically.

**6. Delete the security group:**

```bash
aws ec2 delete-security-group --group-id $WEB_SG_ID --region us-east-1
```

**Console:** In the VPC console, choose **Security groups**. Select `lab04-web-sg`, choose **Actions**, then **Delete security groups**. Confirm the deletion.

> **Warning:** If the security group deletion fails with a dependency error, verify that all instances using the security group have been terminated and that the Auto Scaling group has been fully deleted. Wait a minute and try again.

**7. Verify cleanup is complete:**

```bash
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=lab04-*" "Name=instance-state-name,Values=running,pending,stopping,stopped" \
  --query "Reservations[].Instances[].{Id:InstanceId,Name:Tags[?Key=='Name'].Value|[0],State:State.Name}" \
  --region us-east-1
```

Expected output:

```json
[]
```

```bash
aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names lab04-asg \
  --query "AutoScalingGroups" \
  --region us-east-1
```

Expected output:

```json
[]
```

## Challenge (Optional)

Using only concepts from Modules 01 through 04, complete the following:

1. Create a new EBS volume from the snapshot `lab04-data-snapshot` you created in Step 7. Attach it to a new EC2 instance and mount it. Verify that the test file (`test-file.txt`) you wrote in Step 6 is present on the restored volume. This demonstrates how snapshots preserve data and can be used for backup and recovery.

2. Modify the Auto Scaling group to use a [target tracking scaling policy](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html) that maintains average CPU utilization at 50%. Then use the `stress` utility on one of the instances to generate CPU load and observe the Auto Scaling group launching additional instances. Install the stress utility with `sudo yum install -y stress` and run `stress --cpu 4 --timeout 300` to generate load for 5 minutes.

3. Create a second version of the launch template with a different user data script (for example, one that displays a different message). Update the Auto Scaling group to use the new template version and perform an [instance refresh](https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-instance-refresh.html) to roll out the change. Observe how Auto Scaling replaces instances one at a time.

> **Tip:** Remember to delete all resources created during the challenge (new volumes, scaling policies, additional template versions) to avoid charges.
