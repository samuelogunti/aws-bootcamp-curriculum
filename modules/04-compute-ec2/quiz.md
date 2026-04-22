# Module 04: Quiz

1. Which EC2 instance family is best suited for a workload that requires high sequential read and write access to large datasets stored on local disks, such as a distributed file system?

   A) T (General purpose, burstable)
   B) C (Compute optimized)
   C) R (Memory optimized)
   D) I (Storage optimized)

2. True or False: An Amazon Machine Image (AMI) created in `us-east-1` can be used directly to launch instances in `eu-west-1` without any additional steps.

3. In the EC2 instance type name `m6i.large`, what does each component represent? Identify the instance family, generation, additional capability indicator, and size.

4. Which connection method allows you to access an EC2 instance in a private subnet without opening inbound ports or managing SSH key pairs?

   A) SSH with a key pair
   B) EC2 Instance Connect
   C) AWS Systems Manager Session Manager
   D) Remote Desktop Protocol (RDP)

5. Which of the following are characteristics of Amazon EBS `gp3` volumes? (Select TWO.)

   A) They provide a baseline of 3,000 IOPS at no additional cost
   B) They can serve as boot volumes for EC2 instances
   C) They are optimized for large, sequential throughput workloads such as log processing
   D) They support a maximum of 500 IOPS
   E) They use Hard Disk Drive (HDD) storage media

6. A development team launches an EC2 instance with a user data script that installs and configures a monitoring agent. After the instance is running, the team stops and then starts the instance. What happens to the user data script?

   A) The script runs again automatically each time the instance starts
   B) The script does not run again because user data runs only on the first boot by default
   C) The script is deleted from the instance after the first execution
   D) The script runs again only if the instance is rebooted, not stopped and started

7. True or False: EBS snapshots are incremental, meaning that only the blocks that have changed since the last snapshot are saved.

8. An Auto Scaling group is configured with a minimum capacity of 2, a desired capacity of 4, and a maximum capacity of 8. If a target tracking scaling policy detects that average CPU utilization has dropped well below the target, what is the lowest number of instances the group can scale down to?

   A) 0
   B) 2
   C) 4
   D) 8

9. Which EC2 pricing model provides discounts of up to 90% compared to On-Demand prices but allows AWS to reclaim the instance with a two-minute warning when capacity is needed?

   A) Reserved Instances
   B) Savings Plans
   C) Spot Instances
   D) Dedicated Hosts

10. List three pieces of information that an Amazon Machine Image (AMI) contains.

---

<details>
<summary>Answer Key</summary>

1. **D) I (Storage optimized)**
   The I family is optimized for workloads that require high sequential read and write access to large datasets on local storage, such as distributed file systems, data warehousing, and NoSQL databases. The T family (A) is for burstable, variable workloads. The C family (B) is for CPU-intensive tasks like batch processing and encoding. The R family (C) is for memory-intensive workloads like in-memory caches and large databases.
   Further reading: [Amazon EC2 instance type naming conventions](https://docs.aws.amazon.com/ec2/latest/instancetypes/instance-type-names.html)

2. **False.**
   AMIs are Region-specific. If you create an AMI in `us-east-1` and need to launch instances in `eu-west-1`, you must first copy the AMI to the target Region. You cannot use an AMI across Regions without copying it.
   Further reading: [Amazon Machine Images in Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)

3. **Sample answer:** In `m6i.large`, **m** is the instance family (general purpose, balanced compute, memory, and networking), **6** is the generation (sixth generation of the M family), **i** is an additional capability indicator showing the instance uses Intel processors, and **large** is the size (defines the amount of CPU and memory). The naming convention follows the format `[family][generation][additional capabilities].[size]`.
   Further reading: [Amazon EC2 instance type naming conventions](https://docs.aws.amazon.com/ec2/latest/instancetypes/instance-type-names.html)

4. **C) AWS Systems Manager Session Manager**
   Session Manager lets you connect to instances through a browser-based shell or the AWS CLI without opening inbound ports, managing SSH keys, or using bastion hosts. It requires the SSM Agent on the instance and an IAM role with the `AmazonSSMManagedInstanceCore` policy. SSH with a key pair (A) requires an open SSH port (22) and a public IP. EC2 Instance Connect (B) also requires port 22 open and a public IP. RDP (D) is for Windows instances and requires an open port (3389).
   Further reading: [Connect to your Amazon EC2 instance using Session Manager](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-with-systems-manager-session-manager.html)

5. **A and B**
   General Purpose SSD (`gp3`) volumes provide a baseline of 3,000 IOPS and 125 MiB/s throughput at no additional cost (A), and they can serve as boot volumes (B). You can independently scale IOPS and throughput beyond the baseline. Option C describes Throughput Optimized HDD (`st1`) volumes, which are designed for large sequential workloads. Option D describes a limit far below `gp3` capabilities (maximum is 16,000 IOPS). Option E is incorrect because `gp3` uses Solid State Drive (SSD) storage, not HDD.
   Further reading: [Amazon EBS volume types](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volume-types.html)

6. **B) The script does not run again because user data runs only on the first boot by default**
   By default, EC2 user data scripts execute only during the first boot of the instance. If you stop and start the instance, the script does not run again. The script remains accessible on the instance (it is not deleted), so option C is incorrect. Option A is incorrect because re-execution on every boot requires additional cloud-init configuration. Option D is incorrect because the behavior is the same for both reboot and stop/start: the script does not re-run by default.
   Further reading: [Run commands when you launch an EC2 instance with user data input](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)

7. **True.**
   EBS snapshots are incremental. The first snapshot captures all data on the volume. Each subsequent snapshot saves only the blocks that have changed since the previous snapshot. This makes snapshots storage-efficient and cost-effective. Snapshots are stored in Amazon S3 (managed by AWS).
   Further reading: [Amazon EBS snapshots](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-snapshots.html)

8. **B) 2**
   The minimum capacity setting defines the lowest number of instances the Auto Scaling group will maintain. The group never scales below this number. Even if demand drops significantly, the group keeps at least 2 instances running. Option A (0) is incorrect because the minimum is set to 2. Option C (4) is the desired capacity, not the floor. Option D (8) is the maximum capacity.
   Further reading: [Auto Scaling groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-groups.html)

9. **C) Spot Instances**
   Spot Instances let you use spare EC2 capacity at discounts of up to 90% compared to On-Demand prices. The trade-off is that AWS can interrupt your Spot Instance with a two-minute warning when it needs the capacity back. Reserved Instances (A) offer up to 72% discount with a 1- or 3-year commitment but are not interruptible. Savings Plans (B) also offer up to 72% discount with a commitment but are not interruptible. Dedicated Hosts (D) provide a physical server dedicated to your use and do not offer the same discount level.
   Further reading: [Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html)

10. **Sample answer:** An AMI contains: (1) a root volume template that defines the operating system and any pre-installed software, (2) launch permissions that control which AWS accounts can use the AMI to launch instances, and (3) a block device mapping that specifies the EBS volumes to attach to the instance at launch.
    Further reading: [Amazon Machine Images in Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)

</details>

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
