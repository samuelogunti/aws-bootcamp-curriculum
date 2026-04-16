---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 19: Advanced Topics'
---

# Module 19: Advanced Topics

**Phase 5: Architecting**
Estimated lecture time: 90 to 105 minutes

<!-- Speaker notes: Welcome to Module 19. This module introduces advanced services at a conceptual level. Students are not expected to master every service here. The goal is awareness and the ability to evaluate applicability. Start by asking: "If you were starting a company today, how many AWS accounts would you create?" Breakdown: 15 min multi-account, 15 min CloudFront, 15 min ElastiCache, 15 min Step Functions, 15 min Athena, 10 min emerging services, 10 min ADRs, 5 min wrap-up. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Design a multi-account strategy using AWS Organizations and Control Tower
- Architect a CloudFront content delivery solution with OAC and caching behaviors
- Propose ElastiCache caching strategies for different access patterns
- Design multi-step workflows using Step Functions
- Architect serverless analytics with Athena on S3 data
- Critique emerging services (Bedrock, App Runner) for specific workloads
- Create an Architecture Decision Record (ADR) documenting a technology choice

---

## Prerequisites and agenda

**Prerequisites:** All modules 01-18, especially 05 (S3), 09 (Lambda), 13 (Security), 17 (Well-Architected), 18 (Patterns)

**Agenda:**
1. Multi-account strategy (Organizations, Control Tower)
2. Amazon CloudFront: global content delivery
3. Amazon ElastiCache: in-memory caching
4. AWS Step Functions: workflow orchestration
5. Amazon Athena: serverless SQL analytics
6. Emerging services and capabilities
7. Architecture Decision Records (ADRs)

---

# Multi-account strategy

<!-- Speaker notes: This section takes about 15 minutes. Emphasize that a single account creates security, billing, and quota risks in production. -->

---

## Organizational unit structure

| OU | Purpose | Example Accounts |
|----|---------|-----------------|
| Security | Centralized security services | Security tooling, log archive |
| Infrastructure | Shared networking, CI/CD | Network account, shared services |
| Workloads | Production and non-production apps | Prod-app-A, staging-app-A |
| Sandbox | Experimentation with limited budgets | Developer sandbox accounts |
| Suspended | Accounts pending closure | Decommissioned projects |

---

## Service Control Policies (SCPs)

| SCP | What It Prevents |
|-----|-----------------|
| Deny Region restriction | Creating resources outside approved Regions |
| Deny root user actions | Root user performing any actions in member accounts |
| Deny leaving organization | Member accounts removing themselves |
| Require encryption | Creating unencrypted S3 buckets or EBS volumes |

- SCPs set maximum permissions boundaries (they do not grant permissions)
- AWS Control Tower automates landing zone setup with guardrails

---

## Think about it: designing an account structure

Your company has three product teams, each with dev, staging, and production environments. Security requires centralized logging and separate blast radius per team.

**How many accounts and OUs would you create?**

<!-- Speaker notes: Expected answer: At minimum 12 accounts: 1 management, 1 security/log archive, 1 shared infrastructure, 3 teams x 3 environments (dev/staging/prod) = 9 workload accounts. OUs: Security, Infrastructure, Workloads (with sub-OUs per team or per environment type). SCPs on the Workloads OU to restrict Regions and require encryption. This demonstrates that multi-account is not optional for production organizations. -->

---

# Amazon CloudFront

<!-- Speaker notes: This section takes about 15 minutes. Use the warehouse analogy: edge locations are local warehouses for faster delivery. -->

---

## How CloudFront works

```
User (Tokyo) --> CloudFront Edge (Tokyo)
  --> Cache hit?  --> Return cached content
  --> Cache miss? --> Fetch from origin (us-east-1)
                  --> Cache at edge
                  --> Return to user
```

- Caches content at 400+ edge locations worldwide
- Reduces latency by serving from the nearest edge location

---

## Origins and cache behaviors

| Origin Type | Use Case |
|------------|----------|
| S3 bucket | Static assets (HTML, CSS, JS, images) |
| ALB | Dynamic API responses from EC2 or ECS |
| API Gateway | Serverless API responses |
| Custom HTTP server | Any HTTP/HTTPS endpoint |

- Cache behaviors route URL paths to different origins
- `/static/*` to S3, `/api/*` to ALB
- Origin Access Control (OAC) keeps S3 private while CloudFront serves content

> Always use OAC when serving S3 content through CloudFront. Keep the bucket private with Block Public Access enabled.

---

# Amazon ElastiCache

<!-- Speaker notes: This section takes about 15 minutes. Cover the four caching strategies and when to use each. -->

---

## Caching strategies

| Strategy | How It Works | Best For |
|----------|-------------|----------|
| Cache-aside | App checks cache; on miss, reads DB, writes cache | Read-heavy, stale data acceptable briefly |
| Write-through | App writes to cache and DB simultaneously | Cache must always have latest data |
| Write-behind | App writes to cache only; cache writes to DB async | Write-heavy, slight data loss risk acceptable |
| Read-through | Cache fetches from DB on miss automatically | Simplified app code |

---

## Redis vs. Memcached

| Feature | Redis | Memcached |
|---------|-------|-----------|
| Data structures | Strings, lists, sets, hashes, streams | Strings only |
| Persistence | Optional (snapshots, AOF) | None |
| Replication | Multi-AZ with automatic failover | None |
| Pub/sub | Supported | Not supported |

> Choose Redis for most use cases. Choose Memcached only for simple key-value caching with multi-threaded performance.

---

## Quick check: caching strategy selection

Your application reads user profiles from DynamoDB. Profiles are updated once per day but read thousands of times per hour. Stale data for up to 5 minutes is acceptable.

**Which caching strategy would you use, and why?**

<!-- Speaker notes: Answer: Cache-aside (lazy loading). The application checks ElastiCache first. On a cache miss, it reads from DynamoDB and writes the result to the cache with a 5-minute TTL. This is ideal because: reads are much more frequent than writes, stale data is acceptable for a short period, and the TTL ensures the cache refreshes regularly. Write-through would work but adds latency to the infrequent writes unnecessarily. -->

---

# AWS Step Functions

<!-- Speaker notes: This section takes about 15 minutes. If time permits, demo the Workflow Studio visual editor. -->

---

## When to use Step Functions

| Scenario | Step Functions | SQS + Lambda |
|----------|---------------|--------------|
| Multi-step with branching logic | Yes | Complex in code |
| Long-running (minutes to months) | Yes (up to 1 year) | Lambda timeout: 15 min |
| Human approval step | Yes (Wait for Callback) | Difficult |
| Configurable retry per step | Yes (built-in) | Must implement per Lambda |
| Simple event processing | No (overhead not justified) | Yes (simpler) |
| High-throughput, low-latency | No (per-transition charges) | Yes (more cost-effective) |

---

## Workflow types

| Type | Duration | Pricing | Use Case |
|------|----------|---------|----------|
| Standard | Up to 1 year | Per state transition | Order processing, ETL pipelines |
| Express | Up to 5 minutes | Per execution + duration | Data transformation, IoT events |

> Use Step Functions when orchestration logic is too complex for direct Lambda invocation or SQS.

---

## Design challenge: Step Functions vs. SQS

You need to process image uploads: (1) validate format, (2) generate three thumbnail sizes in parallel, (3) extract metadata, (4) store results in DynamoDB. If validation fails, skip all other steps.

**Would you use Step Functions or SQS + Lambda? Defend your choice.**

<!-- Speaker notes: Expected answer: Step Functions. Reasons: branching logic (skip steps on validation failure), parallel execution (three thumbnails simultaneously), error handling per step, and the workflow is a clear multi-step process. SQS + Lambda would require custom orchestration code to handle branching and parallelism. The visual execution history in Step Functions also aids debugging. -->

---

# Amazon Athena

<!-- Speaker notes: This section takes about 10 minutes. If possible, run a live query on sample S3 data. Show the cost difference between CSV and Parquet. -->

---

## Serverless SQL analytics on S3

- Point Athena at S3 data, define a schema, run SQL queries
- No infrastructure to manage; charges per query (data scanned)
- Schema-on-read: data stays in S3 in its original format

```sql
SELECT eventName, userIdentity.arn, sourceIPAddress
FROM cloudtrail_logs
WHERE eventName = 'DeleteBucket'
  AND eventTime > '2026-01-01'
ORDER BY eventTime DESC
LIMIT 20;
```

---

## Athena performance and cost optimization

| Optimization | Impact |
|-------------|--------|
| Columnar formats (Parquet, ORC) | Reduces data scanned by 30-90% |
| Partition by date, Region, or filter keys | Skips irrelevant partitions |
| Compress data (Snappy, GZIP) | Reduces scan volume and storage cost |
| AWS Glue Data Catalog | Centralized schema management |

> Convert CSV/JSON to Parquet before querying. The cost savings from reduced scanning pay for the conversion quickly.

---

# Emerging services

<!-- Speaker notes: This section takes about 10 minutes. Brief conceptual overview. Students should know these exist and their primary use cases. -->

---

## Amazon Bedrock and AWS App Runner

**Amazon Bedrock (Generative AI):**
- Managed access to foundation models (Claude, Titan, Llama)
- Integrates with the serverless API pattern: API Gateway, Lambda, Bedrock API
- Add text generation, summarization, conversational AI to your apps

**AWS App Runner:**
- Fully managed container deployment (no VPC, ALB, ECS config)
- Sits between Lambda (fully serverless) and ECS Fargate (full orchestration)
- Good for teams wanting containers without ECS operational complexity

> Evaluate new services against Well-Architected pillars. If a service does not clearly improve at least one pillar, the existing approach may be sufficient.

---

# Architecture Decision Records

<!-- Speaker notes: This section takes about 5 minutes. Show the ADR template. Emphasize writing ADRs for decisions that are hard to reverse. -->

---

## ADR template

```markdown
# ADR-001: Use DynamoDB for Session Storage

## Status
Accepted

## Context
Sub-10ms read latency needed. Key-value access pattern.
Lambda cannot maintain in-memory state.

## Decision
DynamoDB with TTL for automatic session expiration.

## Alternatives Considered
- ElastiCache Redis: Sub-ms latency but requires VPC
  for Lambda, adding cold start and complexity.
- RDS: Over-engineered for key-value access pattern.

## Consequences
- Single-digit ms latency meets requirements.
- TTL handles expiration automatically.
- Trade-off: no complex queries across sessions.
```

---

## Key takeaways

- Multi-account strategy (Organizations + Control Tower) is standard for production; it provides security isolation, cost attribution, and quota separation
- CloudFront accelerates content delivery globally; use OAC for secure S3 origins and ALB origins for dynamic API content
- ElastiCache (Redis) reduces database load; choose caching strategy (cache-aside, write-through) based on consistency and latency needs
- Step Functions orchestrate multi-step workflows with built-in error handling and retries; use when logic is too complex for SQS + Lambda
- Athena provides serverless SQL on S3 data; optimize with columnar formats (Parquet) and partitioning

---

## Lab preview: advanced services exercises

**What you will do (choose 2 of 3):**
- Configure CloudFront with an S3 origin and OAC
- Build a Step Functions workflow with branching and parallel states
- Query CloudTrail logs in S3 using Athena
- Write an Architecture Decision Record for one of your choices

**Duration:** 60 minutes (open-ended, choose your exercises)
**Key services:** CloudFront, Step Functions, Athena, S3

<!-- Speaker notes: Students choose 2 of 3 exercises plus write an ADR. This gives them flexibility to explore topics most relevant to their interests. Remind students that the capstone project (Module 20) is next, and these services may be useful in their capstone architecture. -->

---

# Questions?

Review `modules/19-advanced-topics/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions: "CloudFront vs. ALB?" (CloudFront caches at edge locations globally; ALB distributes within a Region. Often used together.) "Step Functions vs. calling Lambda from Lambda?" (Step Functions when you have branching, parallel execution, or retries per step. Direct invocation for simple A-calls-B patterns.) -->
