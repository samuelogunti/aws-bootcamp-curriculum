# Module 04: Compute with Amazon EC2

## Learning Objectives

By the end of this module, you will be able to:

- Deploy Amazon Elastic Compute Cloud (Amazon EC2) instances into a Virtual Private Cloud (VPC) with appropriate subnet and security group configurations
- Demonstrate how to select an instance type based on workload requirements using the EC2 instance type naming convention
- Configure an Amazon Machine Image (AMI) for launching instances, and distinguish between AWS-provided and custom AMIs
- Set up secure access to EC2 instances using key pairs, EC2 Instance Connect, and AWS Systems Manager Session Manager
- Implement Amazon Elastic Block Store (Amazon EBS) volumes with appropriate volume types, snapshots, and encryption for persistent storage
- Use user data scripts to bootstrap EC2 instances at launch with automated software installation and configuration
- Configure Auto Scaling groups with launch templates to maintain application availability and scale capacity based on demand
- Demonstrate the differences between EC2 pricing models and identify when to use On-Demand, Reserved Instances, Savings Plans, and Spot Instances

## Prerequisites

- Completion of [Module 02: Identity and Access Management (IAM) and Security](../02-iam-and-security/README.md) (IAM roles and instance profiles for granting EC2 instances access to AWS services)
- Completion of [Module 03: Networking Basics (VPC)](../03-networking-basics/README.md) (VPCs, subnets, security groups, and route tables for network placement of EC2 instances)
- An AWS account with console access (free tier is sufficient)

## Concepts

### EC2 Overview: Virtual Servers in the Cloud

[Amazon Elastic Compute Cloud (Amazon EC2)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) gives you virtual servers (called instances) that you can launch, configure, and terminate on demand. You pick the operating system, CPU/memory combination, and storage, then connect remotely to install software and run applications.

EC2 is an Infrastructure as a Service (IaaS) offering. As you learned in Module 01, IaaS gives you the highest level of control: AWS manages the physical hardware and virtualization layer, while you manage everything from the operating system up.

Common use cases for EC2 include:

- **Web and application servers.** Host websites, APIs, and backend services on instances sized to match your traffic patterns.
- **Development and test environments.** Spin up instances for development, run tests, and terminate them when finished. You pay only for the time the instances run.
- **Batch processing.** Launch a fleet of instances to process large datasets, then terminate them when the job completes.
- **High-performance computing (HPC).** Use compute-optimized or GPU instances for scientific simulations, machine learning training, and video encoding.

In Module 03, you learned how to create [VPCs, subnets, and security groups](../03-networking-basics/README.md). Every EC2 instance launches into a subnet within a VPC. The subnet determines the instance's Availability Zone and network routing, while the security group controls which traffic can reach the instance. In Module 02, you learned about [IAM roles](../02-iam-and-security/README.md). You attach an IAM role to an EC2 instance through an instance profile, which grants the instance temporary credentials to access other AWS services without storing access keys on the instance.

> **Tip:** EC2 instances in the `t3.micro` size are eligible for the [AWS Free Tier](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier.html) (750 hours per month for 12 months). Use this instance type for labs and experimentation to avoid charges.

### Instance Types and Sizing

An [EC2 instance type](https://docs.aws.amazon.com/ec2/latest/instancetypes/instance-type-names.html) defines the combination of CPU, memory, storage, and networking capacity available to your instance. AWS offers hundreds of instance types organized into families, each optimized for different workload categories.

#### Instance Type Naming Convention

Every instance type name follows a structured format:

```
[family][generation].[size]
```

For example, `t3.micro`:

- **t** is the instance family (burstable general purpose)
- **3** is the generation (third generation of the T family)
- **micro** is the size (defines the amount of CPU and memory)

Some instance types include additional letters after the generation number to indicate specific features. For example, `m6i.large` uses Intel processors, while `m6g.large` uses AWS Graviton (ARM-based) processors.

#### Instance Families

| Family | Category | Optimized For | Example Use Cases |
|--------|----------|---------------|-------------------|
| T | General purpose (burstable) | Variable workloads with occasional CPU spikes | Development environments, small web servers, microservices |
| M | General purpose | Balanced compute, memory, and networking | Application servers, mid-size databases, backend services |
| C | Compute optimized | CPU-intensive tasks | Batch processing, scientific modeling, video encoding, gaming servers |
| R | Memory optimized | Large in-memory datasets | In-memory caches, real-time analytics, large relational databases |
| I | Storage optimized | High sequential read/write to local storage | Data warehousing, distributed file systems, NoSQL databases |
| G, P | Accelerated computing | GPU-intensive tasks | Machine learning training, 3D rendering, video transcoding |

#### Choosing the Right Instance Type

Start by identifying your workload's primary bottleneck:

- **CPU-bound** (batch processing, encoding): choose C family
- **Memory-bound** (caching, in-memory databases): choose R family
- **Balanced** (web servers, application servers): choose M or T family
- **Storage-bound** (data warehousing, log processing): choose I family

For development and testing, the `t3.micro` or `t3.small` instances provide a cost-effective starting point. You can resize instances later by stopping the instance, changing the instance type, and starting it again.

> **Tip:** AWS [Compute Optimizer](https://docs.aws.amazon.com/compute-optimizer/latest/ug/what-is-compute-optimizer.html) analyzes your instance usage patterns and recommends right-sized instance types. Use it after your workload has been running for at least 14 days to get accurate recommendations.

### Amazon Machine Images (AMIs)

An [Amazon Machine Image (AMI)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) is a template containing the software configuration (operating system, application server, applications) needed to launch an EC2 instance. Every launch requires an AMI, so choosing the right one is your first decision when spinning up compute.

#### What an AMI Contains

An AMI includes:

- **A root volume template.** This defines the operating system and any pre-installed software. For example, an Amazon Linux 2023 AMI includes the Linux kernel, package manager, and AWS CLI pre-installed.
- **Launch permissions.** These control which AWS accounts can use the AMI to launch instances.
- **Block device mapping.** This specifies the Amazon EBS volumes to attach to the instance at launch.

#### AMI Sources

You can obtain AMIs from three sources:

| Source | Description | Examples |
|--------|-------------|---------|
| AWS-provided AMIs | Maintained by AWS with regular security patches | Amazon Linux 2023, Ubuntu Server, Windows Server, Red Hat Enterprise Linux |
| AWS Marketplace AMIs | Published by third-party vendors, often with pre-installed software | WordPress on Amazon Linux, NGINX Plus, custom database appliances |
| Custom AMIs | Created by you from an existing instance | Your application pre-installed on Amazon Linux with all dependencies configured |

#### AMI Lifecycle

The typical AMI workflow is:

1. **Launch** an instance from an existing AMI (AWS-provided or Marketplace).
2. **Configure** the instance by installing your application, libraries, and settings.
3. **Create** a custom AMI from the configured instance. This captures the root volume and configuration as a reusable template.
4. **Launch** new instances from your custom AMI. Each new instance starts with the same software and configuration.

Custom AMIs reduce deployment time because you do not need to install and configure software on every new instance. They also ensure consistency across your fleet, since every instance launched from the same AMI starts in an identical state.

> **Tip:** AMIs are Region-specific. If you create an AMI in `us-east-1` and need to launch instances in `eu-west-1`, you must [copy the AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) to the target Region first.

### Key Pairs and Connecting to Instances

To connect to an EC2 instance, you need a way to authenticate. AWS provides several methods, each suited to different use cases.

#### SSH Key Pairs

An [EC2 key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) consists of a public key (stored by AWS on the instance) and a private key (downloaded by you). When you connect to a Linux instance using Secure Shell (SSH), the SSH client uses your private key to authenticate. You must keep the private key file secure; anyone with access to it can connect to your instances.

```bash
ssh -i /path/to/my-key-pair.pem ec2-user@<public-ip-address>
```

> **Warning:** If you lose your private key file, you cannot recover it. AWS does not store a copy of the private key. Store it in a secure location and set restrictive file permissions (`chmod 400`).

#### EC2 Instance Connect

[EC2 Instance Connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-connect-methods.html) provides a browser-based SSH connection directly from the Amazon EC2 console. It pushes a temporary SSH public key to the instance metadata, so you do not need to manage long-lived key pairs. EC2 Instance Connect requires the instance to have a public IP address and the security group to allow inbound SSH traffic on port 22.

#### Systems Manager Session Manager

[Session Manager](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-with-systems-manager-session-manager.html) is a capability of AWS Systems Manager that lets you connect to instances through a browser-based shell or the AWS CLI without opening inbound ports, managing SSH keys, or using bastion hosts. Session Manager requires the SSM Agent to be installed on the instance (it is pre-installed on Amazon Linux 2023 and many other AMIs) and an IAM role with the `AmazonSSMManagedInstanceCore` policy attached to the instance.

In Module 02, you learned about [IAM roles for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html). The IAM role attached to the instance through an [instance profile](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html) grants the SSM Agent permission to communicate with the Systems Manager service.

| Connection Method | Requires Public IP | Requires Open SSH Port | Requires Key Pair | Best For |
|-------------------|--------------------|------------------------|--------------------|----------|
| SSH with key pair | Yes | Yes (port 22) | Yes | Direct access from a known IP range |
| EC2 Instance Connect | Yes | Yes (port 22) | No (temporary key) | Quick browser-based access |
| Session Manager | No | No | No | Secure access without open ports; audit logging |

> **Tip:** Prefer Session Manager for production instances. It eliminates the need to open port 22, provides centralized access control through IAM policies, and logs every session for auditing.

### Amazon Elastic Block Store (Amazon EBS) Volumes

[Amazon Elastic Block Store (Amazon EBS)](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volume-types.html) provides persistent block storage for EC2 instances. An EBS volume behaves like a raw, unformatted external drive that you attach to a single instance. The key difference from instance store (ephemeral) storage: data on an EBS volume survives instance stops and terminations (unless you configure the volume to delete on termination).

#### EBS Volume Types

EBS offers four current-generation volume types, divided into two categories: Solid State Drive (SSD) volumes for transactional workloads and Hard Disk Drive (HDD) volumes for throughput-intensive workloads.

**SSD Volume Types**

| Volume Type | API Name | Max IOPS | Max Throughput | Use Cases |
|-------------|----------|----------|----------------|-----------|
| General Purpose SSD | [gp3](https://docs.aws.amazon.com/ebs/latest/userguide/general-purpose.html) | 16,000 | 1,000 MiB/s | Boot volumes, development environments, small to medium databases |
| Provisioned IOPS SSD | [io2 Block Express](https://docs.aws.amazon.com/ebs/latest/userguide/provisioned-iops.html) | 256,000 | 4,000 MiB/s | Large relational databases, latency-sensitive transactional workloads |

**HDD Volume Types**

| Volume Type | API Name | Max IOPS | Max Throughput | Use Cases |
|-------------|----------|----------|----------------|-----------|
| Throughput Optimized HDD | [st1](https://docs.aws.amazon.com/ebs/latest/userguide/hdd-vols.html) | 500 | 500 MiB/s | Big data, data warehouses, log processing |
| Cold HDD | [sc1](https://docs.aws.amazon.com/ebs/latest/userguide/hdd-vols.html) | 250 | 250 MiB/s | Infrequently accessed data, lowest storage cost |

> **Tip:** For most workloads, `gp3` is the best starting point. It provides a baseline of 3,000 IOPS and 125 MiB/s throughput at no additional cost, and you can independently scale IOPS and throughput as needed.

#### EBS Snapshots

An [EBS snapshot](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-snapshots.html) captures the state of an EBS volume at a specific point in time. Snapshots are stored in Amazon S3 (managed by AWS; you do not see them in your S3 buckets) and are incremental: only blocks that changed since the last snapshot are saved. This keeps snapshots storage-efficient and cost-effective.

Common uses for snapshots:

- **Backup and recovery.** Create regular snapshots to protect against data loss. Restore a volume from a snapshot to recover data.
- **AMI creation.** When you create a custom AMI, AWS creates snapshots of the instance's EBS volumes as part of the AMI.
- **Cross-Region replication.** Copy snapshots to another Region for disaster recovery.

#### EBS Encryption

[Amazon EBS encryption](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-encryption.html) uses AWS Key Management Service (AWS KMS) keys to encrypt data at rest on the volume, data in transit between the volume and the instance, and all snapshots created from the volume. Encryption is transparent to the instance operating system and applications; there is no performance penalty for encrypted volumes on current-generation instance types.

You can enable [encryption by default](https://docs.aws.amazon.com/ebs/latest/userguide/encryption-by-default.html) for your AWS account so that all new EBS volumes and snapshots are automatically encrypted.

> **Warning:** You cannot change an unencrypted volume to encrypted directly. To encrypt an existing unencrypted volume, create a snapshot, copy the snapshot with encryption enabled, and then create a new volume from the encrypted snapshot.

#### Attaching and Detaching Volumes

You can attach additional EBS volumes to a running instance to add storage capacity. Each volume appears as a block device (for example, `/dev/xvdf` on Linux) that you format and mount. You can detach a volume from one instance and attach it to another instance in the same Availability Zone.

```bash
# List attached block devices on a Linux instance
lsblk
```

Expected output:

```
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
xvda    202:0    0    8G  0 disk
├─xvda1 202:1    0    8G  0 part /
xvdf    202:80   0   20G  0 disk
```

> **Warning:** EBS volumes exist within a single Availability Zone. You cannot attach a volume in `us-east-1a` to an instance in `us-east-1b`. To move data between AZs, create a snapshot and restore it in the target AZ.

### User Data Scripts: Bootstrapping Instances at Launch

[User data](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html) lets you pass a script to an EC2 instance at launch time. The instance runs the script automatically during first boot, a process called bootstrapping. This means you can automate software installation and configuration without ever SSH-ing into the machine manually.

On Linux instances, user data scripts run as the `root` user. You can provide a shell script (starting with `#!/bin/bash`) or a cloud-init directive.

Here is an example user data script that installs and starts the Apache web server on Amazon Linux 2023:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from EC2</h1>" > /var/www/html/index.html
```

When you launch an instance with this user data, the instance automatically updates its packages, installs Apache, starts the web server, and creates a simple HTML page. You can then access the page by navigating to the instance's public IP address in a browser (assuming the security group allows inbound HTTP traffic on port 80).

Key points about user data:

- User data runs only on the first boot of the instance by default. If you stop and start the instance, the script does not run again.
- User data is limited to 16 KB. For larger configurations, download a script from Amazon S3 in your user data and execute it.
- You can view the user data of a running instance by querying the instance metadata service.

```bash
# View user data from within the instance
curl http://169.254.169.254/latest/user-data
```

> **Tip:** Combine user data scripts with custom AMIs for faster deployments. Bake common software into the AMI, then use user data for instance-specific configuration (such as setting environment variables or joining a cluster).

### Auto Scaling Groups and Launch Templates

As your application grows, you need a way to automatically add or remove EC2 instances based on demand. [Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html) provides this capability through Auto Scaling groups and launch templates.

#### Launch Templates

A [launch template](https://docs.aws.amazon.com/autoscaling/ec2/userguide/launch-templates.html) captures the full configuration for launching instances: AMI ID, instance type, key pair, security groups, EBS volumes, user data, and IAM instance profile. Templates support versioning, so you can update the configuration and roll back if something breaks.

Launch templates replace the older launch configurations. AWS recommends templates for all new Auto Scaling groups because they support additional features such as mixed instance types and purchase options.

#### Auto Scaling Groups

An [Auto Scaling group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-groups.html) manages a fleet of EC2 instances as a single logical unit. You define three capacity boundaries:

| Setting | Description |
|---------|-------------|
| Minimum capacity | The lowest number of instances the group maintains. The group never scales below this number. |
| Desired capacity | The number of instances the group tries to maintain at any given time. Auto Scaling launches or terminates instances to match this number. |
| Maximum capacity | The highest number of instances the group can scale to. The group never exceeds this number. |

For example, if you set minimum = 2, desired = 2, and maximum = 6, the group starts with 2 instances. If demand increases, Auto Scaling can add up to 4 more instances (for a total of 6). When demand decreases, it removes instances back down to 2.

#### Scaling Policies

Scaling policies define when and how the Auto Scaling group adjusts its capacity. The most common types are:

- **[Target tracking](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html).** You specify a target value for a metric (for example, average CPU utilization at 50%), and Auto Scaling adjusts the number of instances to maintain that target. This is the simplest and most commonly used policy type.
- **Step scaling.** You define scaling adjustments based on CloudWatch alarm thresholds. For example, add 2 instances when CPU exceeds 70%, add 4 instances when CPU exceeds 90%.
- **Scheduled scaling.** You define a schedule for scaling actions. For example, scale to 10 instances every weekday at 8:00 AM and scale down to 2 instances at 8:00 PM.

#### Health Checks

Auto Scaling performs [health checks](https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-health-checks.html) to detect unhealthy instances and replace them automatically. By default, it uses EC2 status checks. You can also configure Elastic Load Balancing (ELB) health checks, which verify that the instance is responding to traffic on a specific port and path.

When Auto Scaling detects an unhealthy instance, it terminates the instance and launches a replacement. This self-healing behavior improves application availability without manual intervention.

In Module 03, you learned about deploying resources across multiple [Availability Zones](../03-networking-basics/README.md) for high availability. Auto Scaling groups distribute instances across the AZs you specify. If one AZ experiences an outage, the group launches replacement instances in the remaining healthy AZs.

> **Tip:** Always configure your Auto Scaling group to span at least two Availability Zones. This ensures your application remains available even if one AZ has an issue.

### EC2 Pricing Models

EC2 offers several [pricing models](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) to help you optimize costs based on your workload characteristics. Understanding these models is essential for controlling your AWS spending.

#### Pricing Model Comparison

| Pricing Model | Commitment | Discount vs. On-Demand | Best For |
|---------------|------------|------------------------|----------|
| [On-Demand](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) | None | 0% (baseline price) | Short-term, unpredictable workloads; development and testing |
| [Reserved Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-reserved-instances.html) | 1 or 3 years | Up to 72% | Steady-state workloads with predictable usage |
| [Savings Plans](https://docs.aws.amazon.com/savingsplans/latest/userguide/what-is-savings-plans.html) | 1 or 3 years | Up to 72% | Flexible commitment across instance families, Regions, and compute services |
| [Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html) | None | Up to 90% | Fault-tolerant, flexible workloads; batch processing; CI/CD builds |

#### On-Demand Instances

On-Demand Instances let you pay for compute capacity by the second (Linux) or by the hour (Windows) with no long-term commitment. This is the default pricing model. You pay the full listed price, but you have complete flexibility to start and stop instances at any time.

Use On-Demand for workloads with unpredictable usage patterns, short-term projects, or when you are testing a new application and do not yet know your steady-state requirements.

#### Reserved Instances

[Reserved Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-reserved-instances.html) provide a significant discount in exchange for a one-year or three-year commitment to a specific instance type in a specific Region. You can choose between Standard Reserved Instances (higher discount, less flexibility) and Convertible Reserved Instances (lower discount, ability to change instance families).

Payment options include All Upfront (largest discount), Partial Upfront, and No Upfront (smallest discount).

#### Savings Plans

[Savings Plans](https://docs.aws.amazon.com/savingsplans/latest/userguide/what-is-savings-plans.html) offer the same discounts as Reserved Instances but with more flexibility. Instead of committing to a specific instance type, you commit to a consistent amount of compute usage (measured in dollars per hour) for one or three years. Savings Plans automatically apply to any EC2 instance usage, regardless of instance family, size, operating system, or Region. They also apply to AWS Fargate and AWS Lambda usage.

#### Spot Instances

[Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html) let you tap into spare EC2 capacity at discounts of up to 90% compared to On-Demand prices. The trade-off: AWS can reclaim your instance with a two-minute warning when it needs the capacity back.

Spot Instances are ideal for workloads that are:

- **Fault-tolerant.** The application can handle interruptions gracefully (for example, by checkpointing progress).
- **Flexible.** The workload can run on multiple instance types or in multiple AZs.
- **Stateless.** The instance does not store critical data locally.

Common Spot Instance use cases include batch processing, data analysis, CI/CD build agents, and containerized microservices behind a load balancer.

> **Warning:** Do not use Spot Instances for workloads that cannot tolerate interruptions, such as databases or single-instance applications without failover. Use On-Demand or Reserved Instances for these workloads.

## Instructor Notes

**Estimated lecture time:** 90 minutes

**Common student questions:**

- Q: How do I choose between a `t3` and an `m6i` instance?
  A: The T family uses a burstable CPU credit model, which is cost-effective for workloads that are mostly idle but occasionally spike. If your workload sustains high CPU usage continuously, the M family provides consistent performance without the credit model. Start with `t3` for development and testing, and switch to `m6i` if you observe sustained CPU usage above the baseline. See the [instance type naming conventions](https://docs.aws.amazon.com/ec2/latest/instancetypes/instance-type-names.html) for details on how to read instance type names.

- Q: What happens to my data when I stop or terminate an instance?
  A: When you stop an instance, the root EBS volume is preserved and the instance can be started again later. When you terminate an instance, the root EBS volume is deleted by default (though you can change this setting). Additional EBS volumes are not deleted by default when the instance is terminated. Instance store volumes (ephemeral storage) are always lost when the instance stops or terminates. See the [EC2 instance lifecycle](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html) documentation for details.

- Q: Why should I use Session Manager instead of SSH?
  A: Session Manager eliminates the need to open port 22 in your security group, which reduces your attack surface. It also provides centralized access control through IAM policies and logs every session to CloudWatch Logs or S3 for auditing. With SSH, you must manage key pairs, distribute them securely, and rotate them periodically. Session Manager handles authentication through IAM, which integrates with your existing identity management. See the [Session Manager documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-with-systems-manager-session-manager.html) for setup instructions.

- Q: When should I create a custom AMI versus using user data?
  A: Use a custom AMI when you have a stable base configuration that rarely changes (operating system, common libraries, monitoring agents). Use user data for instance-specific configuration that varies between launches (environment variables, cluster membership, application version). Combining both approaches gives you fast launch times (from the AMI) with per-instance customization (from user data).

**Teaching tips:**

- Start by connecting EC2 to the VPC concepts from Module 03. Draw a VPC with public and private subnets on the whiteboard, then place EC2 instances in each subnet. Show how the security group and route table determine whether the instance can receive internet traffic. This reinforces the networking foundation and shows students where EC2 fits in the architecture.
- When explaining instance types, write the naming convention on the board and decode two or three examples together (for example, `t3.micro`, `m6i.large`, `c6g.xlarge`). Ask students to predict what `r5.2xlarge` means before you explain it.
- Use the apartment analogy for pricing models: On-Demand is a hotel room (pay per night, no commitment), Reserved Instances are a long-term lease (lower monthly rent, locked in for a year), and Spot Instances are a last-minute hotel deal (great price, but you might get bumped).
- When demonstrating user data, launch an instance with a simple web server script and show the result in a browser. This gives students an immediate, visible outcome that reinforces the concept.

**Pause points:**

- After instance types: ask students to recommend an instance family for a machine learning training job (answer: P or G family for GPU acceleration) and for a web application with variable traffic (answer: T family for burstable performance or M family for consistent performance).
- After AMIs: ask students why creating a custom AMI is faster than installing software via user data on every launch (answer: the AMI already contains the installed software, so the instance is ready immediately after boot).
- After EBS volume types: present a scenario where a student needs storage for a PostgreSQL database with high IOPS requirements. Ask which volume type they would choose (answer: io2 Block Express for the highest IOPS, or gp3 if the IOPS requirement is under 16,000).
- After pricing models: give students a scenario with a web application that runs 24/7 and a nightly batch job that runs for 2 hours. Ask them to recommend a pricing model for each (answer: Reserved Instances or Savings Plans for the web application, Spot Instances for the batch job).

## Key Takeaways

- Amazon EC2 provides virtual servers that you launch into VPC subnets with security groups controlling network access and IAM roles (via instance profiles) granting permissions to other AWS services.
- Instance types follow a naming convention (family, generation, size) that maps to workload categories: general purpose (T, M), compute optimized (C), memory optimized (R), storage optimized (I), and accelerated computing (G, P).
- Amazon EBS provides persistent block storage with four volume types (gp3, io2, st1, sc1) suited to different performance and cost requirements. Use snapshots for backups and encryption for data protection.
- Auto Scaling groups with launch templates automatically maintain your desired instance count, replace unhealthy instances, and scale capacity based on demand across multiple Availability Zones.
- EC2 pricing models (On-Demand, Reserved Instances, Savings Plans, Spot Instances) offer trade-offs between flexibility and cost savings. Match the pricing model to your workload's commitment level and fault tolerance.
