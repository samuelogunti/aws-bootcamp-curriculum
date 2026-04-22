# Module 16: Quiz

1. What do RTO and RPO stand for, and what does each measure?

2. A company runs an e-commerce application that generates $10,000 in revenue per hour. The business requires that the application be restored within 15 minutes of a failure and can tolerate losing at most 5 minutes of transaction data. Which disaster recovery strategy meets these requirements?

   A) Backup and restore, because it is the most cost-effective strategy.
   B) Pilot light, because it keeps a minimal environment running in the recovery Region.
   C) Warm standby, because it maintains a scaled-down but fully functional environment that can be scaled up within minutes, with continuous replication providing an RPO of seconds to minutes.
   D) Multi-site active-active, because it is the only strategy that provides any level of disaster recovery.

3. True or False: An RDS Multi-AZ deployment uses synchronous replication to the standby instance and provides automatic failover if the primary instance fails.

4. A solutions architect is reviewing a web application architecture. The application runs on a single EC2 instance in one Availability Zone, connects to a Single-AZ RDS database, and uses a single NAT gateway. The architect needs to identify single points of failure. Which THREE components are single points of failure in this architecture? (Select THREE.)

   A) The single EC2 instance (if it fails, the application is unavailable)
   B) The Single-AZ RDS database (if the AZ fails, the database is unavailable)
   C) The single NAT gateway (if it fails, private subnet instances lose outbound internet access)
   D) The VPC (if the VPC fails, all resources are unavailable)
   E) The IAM role attached to the EC2 instance (if IAM fails, the instance cannot make API calls)

5. Which resilience pattern prevents a failing downstream service from consuming all resources in the calling service, potentially causing the caller to fail as well?

   A) Retry with exponential backoff
   B) Circuit breaker
   C) Timeout
   D) Fallback

6. A company stores critical financial records in an S3 bucket and an RDS PostgreSQL database. The compliance team requires that backups be taken daily, retained for 90 days, and stored in a different Region from the primary data. Which AWS service provides centralized management of these backup requirements across both S3 and RDS?

   A) Amazon S3 Cross-Region Replication
   B) RDS automated backups with cross-Region snapshot copy
   C) AWS Backup with a backup plan that includes daily schedules, 90-day retention, and cross-Region copy rules
   D) AWS CloudFormation with a template that creates daily snapshots

7. A development team implements retry logic for calls to a downstream API. During a downstream outage, all instances of the calling service retry simultaneously every second, overwhelming the downstream service as soon as it recovers. Which improvement should the team make to their retry logic?

   A) Remove retries entirely and fail immediately on the first error.
   B) Add exponential backoff with random jitter so that retries are spread over time and do not all hit the downstream service at the same moment.
   C) Increase the retry rate to 10 retries per second to recover faster.
   D) Switch from retries to a circuit breaker that never retries.

8. What is the difference between the pilot light and warm standby disaster recovery strategies?

9. A company runs a global SaaS platform that serves customers in North America, Europe, and Asia. The platform must remain available even if an entire AWS Region becomes unavailable. Users expect sub-100ms latency regardless of their location. Which architecture and DR strategy should the company implement?

   A) Multi-AZ deployment in a single Region (us-east-1) with Route 53 latency-based routing.
   B) Multi-site active-active deployment across three Regions (us-east-1, eu-west-1, ap-southeast-1) with DynamoDB Global Tables for data replication and Route 53 latency-based routing with health checks.
   C) Backup and restore with daily backups to a secondary Region and manual failover.
   D) Warm standby in a secondary Region with Route 53 failover routing.

10. A team wants to validate that their Auto Scaling group correctly replaces failed EC2 instances and that the ALB routes traffic to healthy instances during an instance failure. They want to test this in a controlled manner without waiting for a real failure. Which AWS service should the team use to simulate the instance failure?

    A) AWS CloudTrail, to audit the Auto Scaling group's API calls.
    B) AWS Config, to verify the Auto Scaling group's configuration compliance.
    C) AWS Fault Injection Service (FIS), to create an experiment that stops EC2 instances and observes the Auto Scaling group's recovery behavior.
    D) Amazon CloudWatch, to create an alarm that triggers when an instance fails.

---

<details>
<summary>Answer Key</summary>

1. **RTO stands for Recovery Time Objective: the maximum acceptable time between a disruption and the restoration of service. RPO stands for Recovery Point Objective: the maximum acceptable amount of data loss measured in time.** For example, an RTO of 1 hour means the service must be restored within 1 hour of a failure. An RPO of 15 minutes means you can lose at most 15 minutes of data, so backups or replication must occur at least every 15 minutes.
   Further reading: [Disaster Recovery Options in the Cloud](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html)

2. **C) Warm standby**
   Warm standby maintains a scaled-down but fully functional environment in the recovery Region with continuous replication. It can be scaled to full production capacity within minutes (meeting the 15-minute RTO) and continuous replication provides an RPO of seconds to minutes (meeting the 5-minute RPO). Backup and restore (A) has an RTO of hours, which exceeds the 15-minute requirement. Pilot light (B) has an RTO of minutes to hours, which may not reliably meet 15 minutes because compute resources must be provisioned from scratch. Multi-site active-active (D) would also meet the requirements but at significantly higher cost than necessary.
   Further reading: [Disaster Recovery Options](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html)

3. **True.**
   RDS Multi-AZ deployments maintain a synchronous standby replica in a different Availability Zone. All writes to the primary are synchronously replicated to the standby before being acknowledged. If the primary fails, RDS automatically promotes the standby to primary, typically within 1 to 2 minutes. The application connects through a DNS endpoint that RDS updates automatically.
   Further reading: [RDS Multi-AZ Deployments](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html)

4. **A, B, C**
   The single EC2 instance (A) is a single point of failure because there is no redundancy; if it fails, the application is down. The Single-AZ RDS database (B) is a single point of failure because if the AZ experiences an outage, the database is unavailable (Multi-AZ would provide automatic failover). The single NAT gateway (C) is a single point of failure because if it fails, all instances in private subnets lose outbound internet access (deploying a NAT gateway per AZ provides redundancy). The VPC (D) is not a single point of failure in the traditional sense; VPCs span multiple AZs and do not fail independently. IAM (E) is a global service with built-in redundancy and is not a single point of failure for individual applications.
   Further reading: [Reliability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html)

5. **B) Circuit breaker**
   A circuit breaker monitors the failure rate of calls to a downstream service. When failures exceed a threshold, the circuit opens and subsequent calls fail immediately without attempting the downstream call. This prevents the calling service from wasting resources (threads, connections, time) on a dependency that is clearly down, which could cause the caller to fail as well (cascading failure). Retry (A) attempts the call again, which can worsen the problem if the downstream service is overwhelmed. Timeout (C) limits how long a single call waits but does not stop subsequent calls. Fallback (D) provides an alternative response but does not prevent resource exhaustion from continued failed calls.
   Further reading: [Reliability Pillar: Implement Loosely Coupled Dependencies](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/design-interactions-in-a-distributed-system-to-mitigate-or-withstand-failures.html)

6. **C) AWS Backup**
   AWS Backup provides centralized backup management across multiple AWS services (S3, RDS, DynamoDB, EBS, EFS, and more). A single backup plan can define daily schedules, 90-day retention, and cross-Region copy rules that apply to both S3 and RDS resources. S3 Cross-Region Replication (A) replicates objects in near-real-time but is not a backup solution (deletions are also replicated). RDS automated backups (B) handle RDS but not S3. CloudFormation (D) can automate snapshot creation but does not provide the lifecycle management, retention policies, or cross-service coordination that AWS Backup offers.
   Further reading: [What Is AWS Backup?](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)

7. **B) Add exponential backoff with random jitter**
   Exponential backoff increases the delay between retries (1s, 2s, 4s, 8s), reducing the load on the downstream service. Random jitter adds a random component to the delay so that multiple clients do not retry at exactly the same time (the "thundering herd" problem). Together, they spread retry attempts over time and give the downstream service a chance to recover. Removing retries entirely (A) means transient failures are not recovered. Increasing the retry rate (C) worsens the problem. A circuit breaker (D) is complementary to retries but should not replace them entirely; retries handle transient failures, while circuit breakers handle sustained failures.
   Further reading: [Reliability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html)

8. **Pilot light maintains only the core infrastructure (database replicas, AMIs, network configuration) in the recovery Region, with no running compute resources. When a disaster occurs, you must provision and start compute resources before the application can serve traffic. Warm standby maintains a scaled-down but fully functional copy of the entire application in the recovery Region, including running compute resources.** The key difference is that warm standby has running compute that can serve traffic immediately (after scaling up), while pilot light requires time to provision compute from scratch. Warm standby has a shorter RTO (minutes) but higher ongoing cost than pilot light (minutes to hours RTO).
   Further reading: [Disaster Recovery Options](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html)

9. **B) Multi-site active-active across three Regions**
   The requirements (survive a full Region failure, sub-100ms latency globally) demand a multi-site active-active architecture. Running the application in three Regions (one per major geography) ensures that users connect to the nearest Region for low latency. DynamoDB Global Tables provide multi-Region, multi-active data replication. Route 53 latency-based routing directs users to the nearest healthy Region, and health checks automatically remove a failed Region from DNS responses. Single-Region multi-AZ (A) does not survive a Region failure or provide global low latency. Backup and restore (C) has an RTO of hours. Warm standby (D) provides DR but does not address the global latency requirement.
   Further reading: [DynamoDB Global Tables](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GlobalTables.html)

10. **C) AWS Fault Injection Service (FIS)**
    FIS is a managed chaos engineering service that lets you create experiments to simulate failures in a controlled manner. You can create an experiment that stops specific EC2 instances and observe how the Auto Scaling group and ALB respond. This validates your reliability assumptions without waiting for a real failure. CloudTrail (A) records API calls but does not simulate failures. Config (B) evaluates configuration compliance but does not inject faults. CloudWatch (D) monitors metrics and triggers alarms but does not simulate failures.
    Further reading: [AWS Fault Injection Service](https://docs.aws.amazon.com/fis/latest/userguide/what-is.html)

</details>

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
