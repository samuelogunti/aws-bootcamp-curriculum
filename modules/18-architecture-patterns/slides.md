---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 18: Architecture Patterns'
---

# Module 18: Architecture Patterns on AWS

**Phase 5: Architecting**
Estimated lecture time: 90 to 105 minutes

<!-- Speaker notes: Welcome to Module 18. Start by presenting a scenario: "You are building an e-commerce platform. Walk me through the architecture." Let students propose services, then guide them toward appropriate patterns. Breakdown: 10 min monolith vs. microservices, 10 min three-tier, 10 min serverless API, 15 min event-driven, 10 min static+API, 10 min data pipeline, 10 min CQRS, 10 min strangler fig, 5 min wrap-up. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Critique trade-offs between monolithic and microservices architectures
- Design a three-tier web architecture and justify service selection
- Architect a serverless API with optimizations for latency and cost
- Design an event-driven architecture with failure handling
- Propose a data processing pipeline and defend the design
- Critique CQRS and evaluate when it is justified
- Architect a strangler fig migration from monolith to microservices
- Design a static website with dynamic API backend

---

## Prerequisites and agenda

**Prerequisites:** All modules 01-17, especially 07 (ALB), 08 (Messaging), 09 (Lambda), 10 (ECS), 17 (Well-Architected)

**Agenda:**
1. Monolith vs. microservices
2. Pattern 1: Three-tier web architecture
3. Pattern 2: Serverless API
4. Pattern 3: Event-driven architecture
5. Pattern 4: Static website with dynamic API
6. Pattern 5: Data processing pipeline
7. Pattern 6: CQRS
8. Pattern 7: Strangler fig migration

---

# Monolith vs. microservices

<!-- Speaker notes: This section takes about 10 minutes. Emphasize that the decision should be driven by organizational needs, not technology trends. -->

---

## Comparing monolith and microservices

| Characteristic | Monolith | Microservices |
|---------------|----------|---------------|
| Deployment | Single unit, all together | Independent per service |
| Scaling | Scale entire application | Scale individual services |
| Data ownership | Shared database | Each service owns its data |
| Team structure | One team owns everything | Small teams own services |
| Complexity | Simple initially, grows over time | Complex from the start |

> Start with a monolith when your team is small and the domain is unclear. Decompose when teams need independent deployment.

---

# Pattern 1: Three-tier web architecture

<!-- Speaker notes: This section takes about 10 minutes. Draw the architecture on the whiteboard first, then discuss service choices. -->

---

## Three-tier architecture

```
Users
  |
  v
Presentation Tier: Application Load Balancer
  |
  v
Application Tier: EC2 ASG / ECS Fargate / Lambda
  |
  v
Data Tier: RDS Multi-AZ / DynamoDB / ElastiCache
```

| Tier | Options | When to Use |
|------|---------|-------------|
| Presentation | ALB | Always for HTTP/HTTPS workloads |
| Application | EC2 + ASG | Long-running, stateful, specific OS needs |
| Application | ECS Fargate | Containerized microservices |
| Application | Lambda | Event-driven, short-duration, variable traffic |
| Data | RDS | Relational data, complex queries, joins |
| Data | DynamoDB | Key-value, predictable access patterns |

---

# Pattern 2: Serverless API

<!-- Speaker notes: This section takes about 10 minutes. Students built this in Module 09. Now discuss production optimizations. -->

---

## Serverless API pattern

```
Client --> API Gateway --> Lambda --> DynamoDB
```

- API Gateway handles routing, auth, throttling
- Lambda executes business logic
- DynamoDB stores data with single-digit ms reads
- All three scale automatically and charge per request

---

## Production optimizations

| Optimization | Benefit |
|-------------|---------|
| HTTP APIs instead of REST APIs | Lower latency, up to 71% cheaper |
| DynamoDB DAX caching | Microsecond read latency |
| Lambda Provisioned Concurrency | Eliminates cold starts |
| API Gateway caching | Reduces Lambda invocations |
| Lambda Powertools | Structured logging, tracing, metrics |

> For steady high-throughput traffic, ECS Fargate + ALB may be more cost-effective than per-invocation Lambda charges.

---

# Pattern 3: Event-driven architecture

<!-- Speaker notes: This section takes about 15 minutes. Pause for a group exercise: give each team a scenario and ask them to design an event-driven architecture. -->

---

## Event-driven architecture

```
Producer Service
  |
  v
Amazon EventBridge --> Rule: order.placed
  |
  +--> SQS Queue --> Inventory Service (Lambda)
  +--> SQS Queue --> Payment Service (Lambda)
  +--> SNS Topic --> Notification Service (email)
```

- Producers publish events; consumers react independently
- Services are decoupled: producer does not know which consumers exist

---

## Choosing EventBridge, SQS, or SNS

| Use Case | Recommended Service |
|----------|-------------------|
| Route events by content to different targets | EventBridge |
| Buffer messages for a single consumer | SQS |
| Fan out to multiple consumers | SNS |
| Fan out with buffering per consumer | SNS + SQS |
| Orchestrate multi-step workflow | Step Functions |

> Design events as immutable facts ("OrderPlaced"), not commands ("ProcessPayment").

---

## Think about it: designing an event-driven order system

An e-commerce platform needs to: (1) validate payment, (2) update inventory, (3) send confirmation email, (4) generate an invoice PDF. Steps 2, 3, and 4 can happen in parallel after payment succeeds.

**Design the event-driven architecture. Which services handle each step?**

<!-- Speaker notes: Expected answer: Order placed event published to EventBridge. Rule triggers Step Functions for payment validation (sequential). On payment success, Step Functions publishes PaymentProcessed event. EventBridge routes to: SQS queue for inventory Lambda, SNS for email notification, SQS queue for invoice Lambda. DLQs on each queue for failure handling. This exercises EventBridge routing, Step Functions orchestration, and SQS/SNS fan-out. -->

---

## Think about it: monolith or microservices?

A two-person startup is building an MVP for a food delivery app. They plan to use 8 microservices from day one: user service, restaurant service, order service, payment service, delivery service, notification service, search service, and analytics service.

**What would you recommend instead, and why?**

<!-- Speaker notes: Expected answer: Start with a monolith (or modular monolith). Two developers cannot effectively build, deploy, and operate 8 independent services. The operational overhead (networking, service discovery, distributed tracing, independent deployments) would consume most of their time. Build the MVP as a single deployable unit, validate the product-market fit, then decompose using the strangler fig pattern when the team grows and specific components need independent scaling. -->

---

# Pattern 4: Static website with dynamic API

<!-- Speaker notes: This section takes about 10 minutes. This combines S3 (Module 05) with the serverless API (Module 09) and CloudFront. -->

---

## Static + API pattern

```
Users (browser)
  |
  v
CloudFront (CDN)
  |
  +--> /static/* --> S3 (HTML, CSS, JS, images)
  |
  +--> /api/*   --> API Gateway --> Lambda --> DynamoDB
```

| Benefit | Explanation |
|---------|-------------|
| Global performance | CloudFront caches static content at edge locations |
| Cost efficiency | S3 + CloudFront is very inexpensive for static content |
| Independent scaling | Frontend and backend scale independently |
| Security | S3 stays private; CloudFront uses OAC for access |

---

# Pattern 5: Data processing pipeline

<!-- Speaker notes: This section takes about 10 minutes. Cover the spectrum from small (Lambda) to large (Kinesis/Glue). -->

---

## Data pipeline architecture

```
Data Source --> S3 (raw data) --> Lambda (transform)
  |
  +--> DynamoDB (processed records)
  +--> S3 (Parquet for analytics)
  +--> Athena (SQL queries)
```

| Pipeline Scale | Recommended Services |
|---------------|---------------------|
| Small (files up to 100 MB) | S3 + Lambda + DynamoDB/Athena |
| Medium (GB-scale, batch) | S3 + AWS Glue + Athena/Redshift |
| Large (real-time, TB-scale) | Kinesis + Lambda + S3/Redshift |

---

# Pattern 6: CQRS

<!-- Speaker notes: This section takes about 10 minutes. Emphasize that CQRS is overkill for most applications. -->

---

## CQRS: separating read and write models

- Write path: API Gateway, Lambda, DynamoDB (write-optimized)
- DynamoDB Streams triggers Lambda to update read model
- Read path: API Gateway, Lambda, read-optimized store (GSIs, ElastiCache, OpenSearch)

**When CQRS is justified:**
- Read and write patterns differ significantly
- Read traffic is orders of magnitude higher than writes
- Different query patterns need different data models

> CQRS adds complexity (eventual consistency, extra infrastructure). Do not apply it to simple CRUD applications.

---

# Pattern 7: Strangler fig migration

<!-- Speaker notes: This section takes about 10 minutes. Walk through a concrete example: extracting the order management feature first because it changes weekly. -->

---

## Incremental migration from monolith to microservices

```
Phase 1: Users --> ALB --> Monolith (all features)

Phase 2: Users --> ALB --> /orders/* --> Order Service
                       --> /*        --> Monolith

Phase 3: Users --> ALB --> /orders/*    --> Order Service
                       --> /payments/* --> Payment Service
                       --> /*          --> Monolith (shrinking)
```

- ALB path-based routing (Module 07) is the key enabler
- Extract features with clearest domain boundaries first
- Extract frequently changing features first
- Keep the shared database as long as possible; split it last

---

## Design challenge: choosing an architecture pattern

A startup is building a task management app. Requirements: web frontend, REST API with CRUD operations, user authentication, email notifications on task assignment. Team size: 3 developers. Traffic: low initially, expected to grow.

**Which architecture pattern(s) would you recommend, and why?**

<!-- Speaker notes: Expected answer: Static website + serverless API pattern. S3 + CloudFront for the frontend. API Gateway + Lambda + DynamoDB for the backend. SNS for email notifications. Reasoning: small team (no microservices overhead), variable traffic (serverless scales to zero), low operational burden. The three-tier pattern with EC2 would work but adds unnecessary server management for a small team. CQRS and event-driven are overkill for simple CRUD. -->

---

## Key takeaways

- Start with the simplest architecture that meets requirements; evolve toward complexity only when specific needs justify it
- The three-tier architecture (ALB + compute + database) is the foundation for most web apps; service choice depends on workload characteristics
- Event-driven architectures decouple producers from consumers using EventBridge, SQS, and SNS for independent scaling and failure isolation
- The strangler fig pattern enables safe, incremental migration from monolith to microservices via ALB path-based routing
- Architecture is a design activity: draw the diagram first, evaluate against Well-Architected pillars, then build

---

## Lab preview: architecture design exercise

**What you will do:**
- Choose a real-world scenario (e-commerce, IoT, content platform)
- Design a complete architecture using patterns from this module
- Create an architecture diagram with all services and data flows
- Write an architecture document justifying service choices
- Produce a cost estimate using the AWS Pricing Calculator

**Duration:** 60 minutes (open-ended format)
**Key services:** Varies by chosen scenario

<!-- Speaker notes: This is a fully open-ended lab. Students design an architecture from requirements. Deliverables include a diagram, document, and cost estimate. Encourage students to evaluate their design against the Well-Architected pillars from Module 17. Schedule peer reviews where teams present their architectures to each other. -->

---

# Questions?

Review `modules/18-architecture-patterns/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions: "Serverless API vs. three-tier?" (Serverless for variable traffic and zero server management; three-tier for steady throughput, long-running processes, or persistent connections.) "When to use CQRS?" (Only when read and write patterns differ significantly. Most apps do not need it.) -->
