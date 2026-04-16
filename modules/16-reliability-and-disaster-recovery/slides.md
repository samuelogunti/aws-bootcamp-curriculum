---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 16: Reliability and Disaster Recovery'
---

# Module 16: Reliability and Disaster Recovery

**Phase 4: Production Readiness**
Estimated lecture time: 90 to 105 minutes

<!-- Speaker notes: Welcome to Module 16, the final module in Phase 4. Start by asking: "Your production database goes down at 3 AM. What happens next?" Walk through the scenario to motivate RTO/RPO, automated recovery, and tested DR procedures. Breakdown: 10 min availability/nines, 10 min RTO/RPO, 20 min DR strategies, 10 min multi-AZ/multi-Region, 15 min resilience patterns, 10 min AWS Backup, 10 min chaos engineering, 5 min wrap-up. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Analyze availability requirements by calculating target uptime (nines)
- Assess RTO and RPO requirements and recommend a DR strategy
- Evaluate the four DR strategies and justify which fits a given workload
- Recommend multi-AZ and multi-Region architectures for high availability
- Analyze resilience patterns (retry, circuit breaker, bulkhead, timeout, fallback)
- Assess AWS Backup configurations for data protection
- Evaluate Route 53 failover routing for DNS-based DR
- Justify chaos engineering practices to validate reliability assumptions

---

## Prerequisites and agenda

**Prerequisites:** Modules 03 (VPC), 04 (EC2/ASG), 06 (RDS), 07 (ALB/Route 53), 14 (Monitoring)

**Agenda:**
1. Availability: uptime, SLAs, and the cost of nines
2. RTO and RPO: defining recovery objectives
3. Four disaster recovery strategies
4. Multi-AZ and multi-Region architectures
5. Resilience patterns for distributed applications
6. AWS Backup: centralized backup management
7. Chaos engineering with AWS FIS

---

# Availability and the cost of nines

<!-- Speaker notes: This section takes about 10 minutes. Emphasize that each additional nine roughly increases cost and complexity by an order of magnitude. -->

---

## Availability levels

| Availability | Nines | Downtime per Year | Downtime per Month |
|-------------|-------|-------------------|-------------------|
| 99% | Two nines | 3.65 days | 7.3 hours |
| 99.9% | Three nines | 8.77 hours | 43.8 minutes |
| 99.95% | Three and a half | 4.38 hours | 21.9 minutes |
| 99.99% | Four nines | 52.6 minutes | 4.38 minutes |
| 99.999% | Five nines | 5.26 minutes | 26.3 seconds |

> Availability is a business decision. Higher availability costs more. Define the target based on business impact of downtime.

---

# RTO and RPO

<!-- Speaker notes: This section takes about 10 minutes. Draw the timeline on the board: last backup, disaster, service restored. -->

---

## Recovery Time Objective and Recovery Point Objective

- **RTO:** Maximum acceptable time to restore service after a disruption
- **RPO:** Maximum acceptable data loss, measured in time

| RTO/RPO | Appropriate DR Strategy | Cost |
|---------|------------------------|------|
| Hours / Hours | Backup and restore | Lowest |
| Minutes to hours / Minutes | Pilot light | Low to moderate |
| Minutes / Seconds to minutes | Warm standby | Moderate to high |
| Near-zero / Near-zero | Multi-site active-active | Highest |

---

# Disaster recovery strategies

<!-- Speaker notes: This section takes about 20 minutes. Draw the four strategies as a spectrum from low cost/high RTO to high cost/low RTO. Use an e-commerce example. -->

---

## Backup and restore

- Back up data to another Region (AWS Backup, S3 cross-Region replication)
- No infrastructure runs in the recovery Region during normal operation
- On disaster: provision infrastructure and restore from backups
- **RTO:** Hours. **RPO:** Hours. **Cost:** Lowest.
- Best for non-critical workloads where hours of downtime are acceptable

---

## Pilot light

- Minimal infrastructure in recovery Region (database replicas, AMIs, network config)
- Compute resources are not running during normal operation
- On disaster: scale up compute and switch traffic
- **RTO:** Minutes to hours. **RPO:** Minutes. **Cost:** Low to moderate.
- Best for workloads needing faster recovery than backup/restore

---

## Warm standby

- Scaled-down but fully functional copy runs in recovery Region
- On disaster: scale to full capacity and redirect all traffic
- **RTO:** Minutes. **RPO:** Seconds to minutes. **Cost:** Moderate to high.
- Best for business-critical workloads requiring recovery within minutes

---

## Multi-site active-active

- Full production runs simultaneously in two or more Regions
- Each Region handles a portion of traffic
- On disaster: failed Region removed from rotation; others absorb traffic
- **RTO:** Near-zero. **RPO:** Near-zero. **Cost:** Highest.
- Best for mission-critical workloads where any downtime is unacceptable

---

## Strategy comparison

| Strategy | RTO | RPO | Cost | Complexity |
|----------|-----|-----|------|------------|
| Backup and restore | Hours | Hours | $ | Low |
| Pilot light | Min to hours | Minutes | $$ | Moderate |
| Warm standby | Minutes | Seconds | $$$ | High |
| Active-active | Near-zero | Near-zero | $$$$ | Very high |

---

## Discussion: choosing a DR strategy

Your company runs three workloads: (1) a marketing blog, (2) an e-commerce storefront, (3) a payment processing system. Budget is limited.

**Which DR strategy would you recommend for each, and why?**

<!-- Speaker notes: Expected answer: Marketing blog: backup and restore (low business impact, hours of downtime acceptable). E-commerce storefront: warm standby (revenue loss during downtime, need recovery in minutes). Payment processing: multi-site active-active or warm standby (financial transactions, near-zero tolerance for downtime). The key insight is that different workloads within the same organization can use different DR strategies based on business criticality. -->

---

# Multi-AZ and multi-Region

<!-- Speaker notes: This section takes about 10 minutes. Multi-AZ is the minimum for production. Multi-Region adds complexity; justify it with RTO/RPO or geographic requirements. -->

---

## Multi-AZ: the minimum for production

| Service | Multi-AZ Mechanism |
|---------|-------------------|
| EC2 + Auto Scaling | ASG spans multiple AZs; unhealthy instances replaced |
| RDS Multi-AZ | Synchronous standby with automatic failover |
| ALB | Distributes traffic across targets in multiple AZs |
| ECS/Fargate | Tasks distributed across AZs by scheduler |
| NAT Gateway | Deploy one per AZ for AZ-independent access |

---

## Multi-Region capabilities

| Service | Multi-Region Capability |
|---------|------------------------|
| Route 53 | DNS failover routing with health checks |
| S3 Cross-Region Replication | Automatic object replication to another Region |
| RDS Cross-Region Read Replicas | Async replication; can be promoted to primary |
| DynamoDB Global Tables | Multi-Region, multi-active replication |
| Aurora Global Database | Sub-second replication, fast failover |

> Start with multi-AZ. Add multi-Region only when RTO/RPO or geographic needs justify the complexity.

---

# Resilience patterns

<!-- Speaker notes: This section takes about 15 minutes. Present a microservices diagram and ask teams to identify where to apply each pattern. -->

---

## Retry with exponential backoff and jitter

```
Attempt 1: immediate
Attempt 2: wait 1s + random(0-500ms)
Attempt 3: wait 2s + random(0-500ms)
Attempt 4: wait 4s + random(0-500ms)
(give up after max retries)
```

- Handles transient failures (network blips, throttling)
- Jitter prevents all clients from retrying simultaneously
- AWS SDKs implement this automatically for most API calls

---

## Circuit breaker pattern

```
Closed (normal) --> failure rate exceeds threshold --> Open (fail fast)
Open --> timeout expires --> Half-Open (test calls)
Half-Open --> succeed --> Closed
Half-Open --> fail --> Open
```

- Prevents a failing dependency from consuming caller resources
- Stops cascading failures across services

---

## Bulkhead, timeout, and fallback

- **Bulkhead:** Isolate components so one failure does not affect others (separate SQS queues, separate Lambda concurrency limits)
- **Timeout:** Set timeouts on all external calls; fail fast rather than hang
- **Fallback:** Provide degraded but functional response when a dependency fails (default recommendations instead of personalized ones)

---

## Quick check: retry vs. circuit breaker

A Lambda function calls a downstream API. The API has been returning 500 errors for the past 5 minutes.

**Should the Lambda function keep retrying, or should it use a circuit breaker? Why?**

<!-- Speaker notes: Answer: Circuit breaker. The API is consistently failing, not experiencing a transient blip. Retrying adds load to an already struggling service and wastes Lambda execution time. The circuit breaker should open after detecting sustained failures, fail fast for subsequent calls, and periodically test if the API has recovered. Retries are for transient failures; circuit breakers are for sustained failures. -->

---

# AWS Backup and chaos engineering

<!-- Speaker notes: This section takes about 10 minutes each. Cover AWS Backup plans and FIS experiment workflow. -->

---

## Think about it: testing your DR plan

Your team has a warm standby DR setup in a secondary Region. The DR plan has never been tested. Management asks: "Are we confident this will work?"

**How would you validate the DR plan without affecting production?**

<!-- Speaker notes: Expected answer: Run a planned DR drill. Use Route 53 to failover a small percentage of traffic (weighted routing) to the secondary Region. Verify that the standby environment handles requests correctly. Test database promotion from read replica to primary. Use AWS FIS to simulate a Region-level failure in a staging environment. Document the results and fix any issues found. A DR plan that has never been tested is a plan that may not work. Schedule DR drills quarterly. -->

---

## AWS Backup: centralized management

| Concept | Description |
|---------|-------------|
| Backup plan | Policy defining frequency, retention, lifecycle |
| Backup vault | Encrypted container for stored backups |
| Resource assignment | Which resources to back up (by tag, type, or ARN) |
| Cross-Region copy | Automatically copy backups to another Region |

- Supports EC2, EBS, RDS, DynamoDB, EFS, S3, Aurora
- Use tag-based assignment: tag resources `Backup=daily`

---

## Chaos engineering with AWS FIS

| FIS Action | What It Simulates |
|-----------|-------------------|
| Stop EC2 instances | Instance failure in an ASG |
| Throttle EBS I/O | Degraded storage performance |
| Inject Lambda errors | Function failures |
| Disrupt network | Network partition between services |
| Failover RDS | Database failover to standby |

1. Define a hypothesis ("ASG replaces failed instance in 5 min")
2. Run the experiment (stop an EC2 instance via FIS)
3. Observe results (CloudWatch, ALB health checks)
4. Improve if recovery did not meet expectations

---

## Key takeaways

- Define RTO and RPO for every critical workload before designing the architecture; these objectives determine the DR strategy and investment
- Multi-AZ is the minimum for production; it protects against single-AZ failures with automatic failover for most AWS services
- The four DR strategies (backup/restore, pilot light, warm standby, active-active) are a spectrum of cost vs. recovery speed
- Apply resilience patterns (retry, circuit breaker, bulkhead, timeout) to prevent cascading failures in distributed applications
- Test reliability through chaos engineering; a DR plan that has never been tested may not work when you need it

---

## Lab preview: disaster recovery planning

**What you will do:**
- Design a DR strategy document for a sample application
- Configure AWS Backup with a backup plan and vault
- Perform a backup and validate a restore
- Configure Route 53 health checks for failover routing

**Duration:** 60 minutes (open-ended format)
**Key services:** AWS Backup, Route 53, RDS, S3

<!-- Speaker notes: This is an open-ended lab. Students receive goals and constraints, then design the solution. Deliverables include a DR strategy document, AWS Backup configuration, and restore validation. Remind students to clean up backup vaults and Route 53 records after the lab. -->

---

# Questions?

Review `modules/16-reliability-and-disaster-recovery/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions: "HA vs. DR?" (HA minimizes downtime during normal operations; DR recovers from major disruptions.) "Do I always need multi-Region?" (No. Multi-AZ is sufficient for most workloads. Multi-Region adds significant complexity and cost.) -->
