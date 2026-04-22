# Lab 16: Designing a Disaster Recovery Strategy for a Multi-Tier Application

## Objective

Design and document a disaster recovery strategy for a multi-tier web application, define RTO and RPO targets, select an appropriate DR strategy, configure AWS Backup for data protection, and validate recovery by performing a database restore.

## Architecture Diagram

This lab does not prescribe a specific architecture. You analyze a multi-tier application (built in previous modules) and design a DR strategy for it. The reference application includes:

```
Reference application (from previous modules):
    ├── Route 53 (DNS)
    ├── Application Load Balancer (Module 07)
    ├── EC2 instances in Auto Scaling group (Module 04)
    |   └── Across 2 Availability Zones
    ├── RDS PostgreSQL Multi-AZ (Module 06)
    ├── DynamoDB table (Module 06/09)
    ├── S3 bucket for static assets (Module 05)
    ├── Lambda functions (Module 09)
    └── SQS queues (Module 08)

Your deliverable: a DR strategy document and implemented backup configuration.
```

## Prerequisites

- Completed labs from Modules 03 through 14 (or have a clear understanding of the architectures built)
- Completed [Module 16: Reliability and Disaster Recovery](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

90 minutes

## Goal

Design a disaster recovery strategy for the reference multi-tier application. Define RTO and RPO targets for each component, select a DR strategy, configure AWS Backup to protect critical data, and validate your backup configuration by performing a restore operation.

## Constraints

- You must define RTO and RPO targets for at least three components of the application (for example, the database, the application tier, and the static assets).
- You must select one of the four DR strategies (backup/restore, pilot light, warm standby, multi-site active-active) and justify your choice based on the RTO/RPO targets you defined.
- You must configure at least one AWS Backup plan that protects a resource from a previous lab (for example, an RDS instance, a DynamoDB table, or an EBS volume).
- You must perform at least one restore operation to validate that your backup is recoverable (for example, restore an RDS snapshot to a new instance or restore a DynamoDB table from a backup).
- Your DR strategy document must identify single points of failure in the current architecture and recommend changes to eliminate them.
- All backup vaults must use KMS encryption (you can use the key from [Module 13](../../13-security-in-depth/README.md) or create a new one).

## Reference Links

- [Disaster Recovery Options in the Cloud](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html)
- [Reliability Pillar: AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html)
- [AWS Backup Developer Guide](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)
- [Route 53 DNS Failover](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-configuring.html)
- [Route 53 Health Checks](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover.html)
- [RDS Automated Backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)
- [DynamoDB Backups](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/BackupRestore.html)
- [AWS Elastic Disaster Recovery](https://docs.aws.amazon.com/drs/latest/userguide/getting-started.html)
- [AWS Resilience Hub](https://docs.aws.amazon.com/resilience-hub/latest/userguide/arh-mgmt.html)
- [AWS Fault Injection Service](https://docs.aws.amazon.com/fis/latest/userguide/what-is.html)

## Deliverables

1. **DR strategy document** (Markdown document or text file) containing:
   - A table listing each application component, its current availability configuration, and its RTO/RPO targets
   - The selected DR strategy with a justification explaining why it meets the defined RTO/RPO targets
   - A list of single points of failure in the current architecture with recommended changes
   - A recovery procedure outline: the steps to execute if the primary Region or AZ becomes unavailable
   - An estimated cost comparison between the current architecture and the proposed DR-enhanced architecture

2. **AWS Backup configuration** with:
   - At least one backup plan with a defined schedule and retention period
   - At least one resource assigned to the backup plan
   - A backup vault encrypted with a KMS key

3. **Restore validation** with:
   - Evidence of a successful restore operation (screenshot, CLI output, or description of the restored resource)
   - Verification that the restored data matches the original (for example, query the restored database and confirm the data is present)

## Validation

Confirm the following:

- [ ] Your DR strategy document defines RTO and RPO targets for at least three application components
- [ ] Your DR strategy document selects and justifies a specific DR strategy
- [ ] Your DR strategy document identifies at least three single points of failure with recommended changes
- [ ] An AWS Backup plan exists with at least one scheduled backup rule
- [ ] At least one resource is assigned to the backup plan
- [ ] The backup vault uses KMS encryption
- [ ] You have performed a restore operation and verified the restored data

## Cleanup

Delete all resources created in this lab:

1. **Delete restored resources:**
   - If you restored an RDS instance, delete the restored instance (not the original).
   - If you restored a DynamoDB table, delete the restored table (not the original).

2. **Delete the AWS Backup plan:**
   - Navigate to the [AWS Backup console](https://console.aws.amazon.com/backup/).
   - Choose **Backup plans**, select your plan, and choose **Delete**.
   - Delete any recovery points in the backup vault (select recovery points and choose **Delete**).
   - Delete the backup vault after it is empty.

3. **Keep original resources** from previous modules intact for use in remaining modules.

> **Warning:** Do not delete the original RDS instances, DynamoDB tables, or other resources from previous modules. Only delete the restored copies and the backup configuration created in this lab.

## Challenge (Optional)

Extend this lab with the following advanced exercises:

1. Configure Route 53 failover routing with health checks for the ALB from [Module 07](../../07-load-balancing-and-dns/README.md). Create a primary record pointing to the ALB and a secondary record pointing to an S3 static website that displays a "maintenance mode" page. Test the failover by intentionally failing the health check.

2. Use AWS Fault Injection Service (FIS) to simulate an AZ failure by stopping all EC2 instances in one AZ. Observe how the Auto Scaling group and ALB respond. Document the recovery time and compare it to your RTO target.

3. Configure S3 Cross-Region Replication for the S3 bucket from [Module 05](../../05-storage-s3/README.md). Upload an object to the source bucket and verify it appears in the destination bucket in another Region. Measure the replication lag.

These challenges combine DR concepts with Route 53, Auto Scaling, S3, and FIS from previous modules.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
