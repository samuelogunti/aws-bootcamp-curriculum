# Module 18: Architecture Patterns on AWS

## Learning Objectives

By the end of this module, you will be able to:

- Critique the trade-offs between monolithic and microservices architectures and defend the choice of one over the other for a given set of requirements
- Design a three-tier web architecture on AWS using ALB, compute (EC2, ECS, or Lambda), and a database tier, and justify the service selection for each tier
- Architect a serverless API pattern using API Gateway, Lambda, and DynamoDB, and propose optimizations for latency, cost, and scalability
- Design an event-driven architecture using EventBridge, SQS, SNS, and Lambda that decouples producers from consumers and handles failures gracefully
- Propose a data processing pipeline architecture using S3, Lambda, and analytics services, and defend the design against alternative approaches
- Critique the CQRS (Command Query Responsibility Segregation) pattern and evaluate when separating read and write models is justified versus when it adds unnecessary complexity
- Architect a migration strategy from a monolithic application to microservices using the strangler fig pattern, sequencing the migration to minimize risk
- Design a static website with dynamic API backend using S3, CloudFront, and API Gateway, and defend the architecture's cost, performance, and security characteristics

## Prerequisites

- Completion of all modules from Phase 1 through Phase 4 (Modules 01 through 16), as this module synthesizes services and concepts from every prior module
- Completion of [Module 17: The AWS Well-Architected Framework](../17-well-architected-framework/README.md) (the six pillars used to evaluate architecture patterns)
- Particular emphasis on:
  - [Module 07: Load Balancing and DNS](../07-load-balancing-and-dns/README.md) (ALB for the presentation tier)
  - [Module 08: Messaging and Integration](../08-messaging-and-integration/README.md) (SQS, SNS, EventBridge for event-driven patterns)
  - [Module 09: Serverless Computing with AWS Lambda](../09-serverless-lambda/README.md) (Lambda for serverless patterns)
  - [Module 10: Containers and Amazon ECS](../10-containers-ecs/README.md) (ECS for microservices patterns)

## Concepts

### Monolith vs. Microservices: When to Split

A monolithic architecture packages all functionality into a single deployable unit. A microservices architecture breaks the application into small, independently deployable services that own their own data and communicate through APIs or events. The table below highlights the key differences:

| Characteristic | Monolith | Microservices |
|---------------|----------|---------------|
| Deployment | Single unit; all components deploy together | Independent; each service deploys separately |
| Scaling | Scale the entire application, even if only one component needs more capacity | Scale individual services based on their specific demand |
| Data ownership | Shared database; all components access the same tables | Each service owns its data; no shared databases |
| Team structure | One team owns the entire application | Small teams own individual services (two-pizza teams) |
| Complexity | Simple to develop and deploy initially; complexity grows over time | Complex from the start (networking, service discovery, distributed tracing); complexity is distributed |
| Failure isolation | A bug in one component can crash the entire application | A failure in one service does not necessarily affect others (if resilience patterns are applied) |

The decision to adopt microservices should come from organizational and operational needs, not from hype. Start with a monolith (or a modular monolith) when your team is small, your domain boundaries are unclear, and speed of iteration matters most. Decompose into microservices when multiple teams need independent deployment cadences, when specific components have wildly different scaling profiles, or when you need different technology stacks for different components.

> **Tip:** The [Implementing Microservices on AWS](https://docs.aws.amazon.com/whitepapers/latest/microservices-on-aws/simple-microservices-architecture-on-aws.html) whitepaper provides detailed guidance on building microservices architectures using AWS services. Read it before deciding to decompose a monolith.

### Pattern 1: Three-Tier Web Architecture

The three-tier architecture is the most common pattern for web applications. It separates the application into three layers: presentation (handles user requests), application (processes business logic), and data (stores and retrieves data).

```
Users
  |
  v
Presentation Tier: Application Load Balancer
  |  (distributes traffic, terminates SSL, path-based routing)
  v
Application Tier: EC2 Auto Scaling group / ECS Fargate / Lambda
  |  (processes business logic, calls external APIs)
  v
Data Tier: RDS Multi-AZ / DynamoDB / ElastiCache
  (stores application data, handles queries)
```

AWS service selection for each tier:

| Tier | Options | When to Use |
|------|---------|-------------|
| Presentation | ALB | Always (for HTTP/HTTPS workloads). Use NLB for TCP/UDP or extreme performance requirements. |
| Application | EC2 + Auto Scaling | Long-running processes, stateful applications, specific OS/runtime requirements |
| Application | ECS Fargate | Containerized applications, microservices, consistent performance needs |
| Application | Lambda | Event-driven, short-duration requests, variable traffic with idle periods |
| Data | RDS | Relational data with complex queries, joins, transactions |
| Data | DynamoDB | Key-value or document data with predictable access patterns, extreme scale |
| Data | ElastiCache | Caching layer to reduce database load for frequently accessed data |

You built each of these components in previous modules. This pattern combines them into a cohesive architecture. The key design decisions are: which compute service for the application tier (based on workload characteristics) and which database for the data tier (based on data model and access patterns).

### Pattern 2: Serverless API

The serverless API pattern removes server management from the equation entirely. API Gateway routes HTTP requests, Lambda runs your business logic, and DynamoDB stores your data. All three scale on demand and bill per request, so you pay nothing when traffic drops to zero.

```
Client (browser, mobile app)
  |
  v
Amazon API Gateway (HTTP API or REST API)
  |  (routing, authentication, throttling, request validation)
  v
AWS Lambda
  |  (business logic, input validation, data transformation)
  v
Amazon DynamoDB
  (data storage, single-digit millisecond reads/writes)
```

This pattern is ideal for APIs with variable traffic (including periods of zero traffic), CRUD operations on simple data models, and teams that want to minimize operational overhead. You built this pattern in [Module 09](../09-serverless-lambda/README.md).

Optimizations for production:

| Optimization | Benefit |
|-------------|---------|
| Use API Gateway HTTP APIs instead of REST APIs | Lower latency, lower cost (up to 71% cheaper) |
| Enable DynamoDB DAX caching | Microsecond read latency for frequently accessed items |
| Use Lambda Provisioned Concurrency | Eliminate cold starts for latency-sensitive endpoints |
| Implement API Gateway caching | Reduce Lambda invocations for cacheable responses |
| Use Lambda Powertools | Structured logging, tracing, and metrics with minimal code |

> **Tip:** The serverless API pattern works best for workloads with variable or unpredictable traffic. For workloads with steady, high-throughput traffic (thousands of requests per second continuously), a container-based approach (ECS Fargate with ALB) may be more cost-effective because you avoid per-invocation Lambda charges.

### Pattern 3: Event-Driven Architecture

Event-driven architecture flips the communication model: instead of Service A calling Service B directly, Service A announces "something happened" and walks away. Other services pick up that announcement and react on their own schedule. This decoupling is what you built with SQS and SNS in [Module 08](../08-messaging-and-integration/README.md), and it scales to entire system architectures.

```
Producer Service                    Consumer Services
  |                                   |
  v                                   v
Order placed --> Amazon EventBridge --> Rule: order.placed
                    |                    |
                    ├── SQS Queue -----> Inventory Service (Lambda)
                    ├── SQS Queue -----> Payment Service (Lambda)
                    └── SNS Topic -----> Notification Service (email)
```

Key components and their roles:

| Component | Role in Event-Driven Architecture |
|-----------|----------------------------------|
| [Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html) | Event bus that routes events based on content-based rules. Best for complex routing, AWS service integration, and schema management. |
| [Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html) | Message queue that buffers events between producer and consumer. Best for decoupling, load leveling, and guaranteed delivery. |
| [Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/welcome.html) | Pub/sub messaging that fans out events to multiple subscribers. Best for broadcasting events to multiple consumers simultaneously. |
| [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html) | Workflow orchestration that coordinates multi-step processes. Best for saga patterns, human approval workflows, and complex branching logic. |

You learned about SQS, SNS, and EventBridge in [Module 08](../08-messaging-and-integration/README.md) and integrated them with Lambda in [Module 09](../09-serverless-lambda/README.md). This pattern combines them into a complete event-driven architecture.

#### Choosing Between EventBridge, SQS, and SNS

The [decision guide](https://docs.aws.amazon.com/decision-guides/latest/sns-or-sqs-or-eventbridge/sns-or-sqs-or-eventbridge.html) for choosing between these services:

| Use Case | Recommended Service |
|----------|-------------------|
| Route events based on content to different targets | EventBridge (content-based filtering rules) |
| Buffer messages between a producer and a single consumer | SQS (point-to-point queue) |
| Fan out a message to multiple consumers simultaneously | SNS (pub/sub topic) |
| Fan out with buffering (each consumer processes at its own pace) | SNS + SQS (SNS fans out to multiple SQS queues) |
| Orchestrate a multi-step workflow with branching and error handling | Step Functions |

> **Tip:** Design events as immutable facts that describe what happened ("OrderPlaced," "PaymentProcessed"), not as commands that tell a service what to do ("ProcessPayment"). This keeps producers and consumers loosely coupled: the producer does not need to know which consumers exist or what they do with the event.

### Pattern 4: Static Website with Dynamic API Backend

This pattern splits your frontend (static HTML, CSS, JavaScript) from your backend (API). The frontend lives in S3 and reaches users through CloudFront's global edge network. The backend is a serverless API that the frontend calls via fetch requests. You get global performance for pennies, independent scaling, and independent deployments.

```
Users (browser)
  |
  v
Amazon CloudFront (CDN)
  |
  ├── Static content (HTML, CSS, JS, images)
  |     └── Amazon S3 (origin)
  |
  └── API requests (/api/*)
        └── Amazon API Gateway --> Lambda --> DynamoDB
```

Benefits of this pattern:

| Benefit | Explanation |
|---------|-------------|
| Global performance | CloudFront caches static content at edge locations worldwide, reducing latency for users regardless of location |
| Cost efficiency | S3 + CloudFront is extremely inexpensive for serving static content compared to running web servers |
| Independent scaling | The frontend (static files) and backend (API) scale independently. The frontend has virtually unlimited capacity through CloudFront. |
| Independent deployment | Frontend and backend can be deployed separately, enabling different release cadences |
| Security | S3 bucket remains private (no public access). CloudFront uses Origin Access Control (OAC) to access S3. API Gateway handles authentication. |

You built the S3 static website hosting in [Module 05](../05-storage-s3/README.md) and the serverless API in [Module 09](../09-serverless-lambda/README.md). This pattern combines them with CloudFront for production-grade delivery.

### Pattern 5: Data Processing Pipeline

Data processing pipelines take raw data, transform it, and store the results for analytics or downstream use. On AWS, these pipelines are typically triggered by events: a file lands in S3, which kicks off a processing chain.

```
Data Source
  |
  v
Amazon S3 (raw data landing zone)
  |  (S3 event notification on ObjectCreated)
  v
AWS Lambda (transform, validate, enrich)
  |
  ├── Amazon DynamoDB (processed records for application queries)
  ├── Amazon S3 (processed data in Parquet format for analytics)
  └── Amazon Athena (ad-hoc SQL queries on S3 data)
```

For larger-scale pipelines, replace Lambda with AWS Glue (managed ETL), Amazon Kinesis (real-time streaming), or Amazon EMR (big data processing). The choice depends on data volume, latency requirements, and processing complexity.

| Pipeline Scale | Recommended Services |
|---------------|---------------------|
| Small (files up to 100 MB, minutes of processing) | S3 + Lambda + DynamoDB/Athena |
| Medium (GB-scale, batch processing) | S3 + AWS Glue + Athena/Redshift |
| Large (real-time streaming, TB-scale) | Kinesis Data Streams + Lambda/Kinesis Data Analytics + S3/Redshift |

### Pattern 6: CQRS (Command Query Responsibility Segregation)

CQRS separates your read model (optimized for queries) from your write model (optimized for updates). Instead of one database handling both workloads, you maintain different data representations for each purpose. This is powerful when read and write patterns diverge significantly, but it adds complexity you should not take on lightly.

```
Write Path (Commands)                Read Path (Queries)
  |                                    |
  v                                    v
API Gateway --> Lambda                API Gateway --> Lambda
  |                                    |
  v                                    v
DynamoDB (write-optimized)           DynamoDB (read-optimized, GSIs)
  |                                  or ElastiCache (cached views)
  v                                  or OpenSearch (full-text search)
DynamoDB Streams --> Lambda
  |
  v
Update read model (denormalized views, search indexes)
```

CQRS is appropriate when:

- Read and write patterns differ significantly (for example, writes are simple inserts but reads require complex aggregations across multiple entities)
- Read traffic is orders of magnitude higher than write traffic
- Different query patterns require different data models (relational queries, full-text search, time-series aggregations)

CQRS adds complexity (eventual consistency between read and write models, additional infrastructure, synchronization logic). Do not apply it to simple CRUD applications where a single database handles both reads and writes efficiently.

> **Tip:** CQRS is a powerful pattern, but it is overkill for most applications. Apply it only when you have measured a clear mismatch between read and write requirements. Start with a single database and add CQRS only when the single-model approach becomes a bottleneck.

### Pattern 7: Strangler Fig Migration

The [strangler fig pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/strangler-fig.html) lets you migrate from a monolith to microservices incrementally, one feature at a time. Rather than a risky "big bang" rewrite, you route specific URL paths to new services while the monolith continues handling everything else. Over time, the monolith shrinks until you can retire it.

```
Phase 1: All traffic goes to the monolith
  Users --> ALB --> Monolith (all features)

Phase 2: One feature extracted to a microservice
  Users --> ALB --> /orders/* --> Order Microservice (Lambda/ECS)
                --> /* (everything else) --> Monolith

Phase 3: More features extracted
  Users --> ALB --> /orders/* --> Order Microservice
                --> /payments/* --> Payment Microservice
                --> /inventory/* --> Inventory Microservice
                --> /* --> Monolith (shrinking)

Phase 4: Monolith fully replaced
  Users --> ALB --> /orders/* --> Order Microservice
                --> /payments/* --> Payment Microservice
                --> /inventory/* --> Inventory Microservice
                --> /users/* --> User Microservice
```

The ALB's path-based routing (from [Module 07](../07-load-balancing-and-dns/README.md)) is the key enabler. You route specific URL paths to new microservices while the monolith continues to handle everything else. Over time, the monolith shrinks as more features are extracted, until it can be decommissioned entirely.

Migration sequencing principles:

1. Start with the feature that has the clearest domain boundary and the least coupling to other features.
2. Extract features that change frequently first (they benefit most from independent deployment).
3. Extract features with different scaling requirements (they benefit most from independent scaling).
4. Keep the shared database as long as possible; split it last (database decomposition is the hardest part).

> **Tip:** The strangler fig pattern reduces migration risk because you can stop at any point. If extracting a particular feature proves too complex, the monolith continues to handle it. You are never in a state where the old system is decommissioned but the new system is not ready.

## Instructor Notes

**Estimated lecture time:** 90 to 105 minutes

**Common student questions:**

- Q: When should I use microservices instead of a monolith?
  A: Use microservices when you have multiple teams that need to deploy independently, when specific components have significantly different scaling requirements, or when you need to use different technologies for different components. If you have a small team, a new product, or an unclear domain model, start with a monolith. You can always decompose later using the strangler fig pattern.

- Q: How do I choose between the serverless API pattern and the three-tier pattern?
  A: The serverless API pattern (API Gateway + Lambda + DynamoDB) is best for variable traffic, simple CRUD operations, and teams that want zero server management. The three-tier pattern (ALB + EC2/ECS + RDS) is better for steady high-throughput traffic, complex business logic that runs for more than 15 minutes, applications that need persistent connections (WebSockets, database connection pools), or workloads that require specific runtimes or libraries.

- Q: What is the difference between event-driven architecture and request-driven architecture?
  A: In request-driven architecture, a client sends a request and waits for a response (synchronous). In event-driven architecture, a producer publishes an event and does not wait for consumers to process it (asynchronous). Event-driven architecture is better for decoupling services, handling variable load (SQS buffers messages), and enabling multiple consumers to react to the same event independently.

- Q: Is CQRS the same as having a read replica?
  A: No. A read replica is a copy of the same database with the same schema, used to offload read traffic. CQRS uses different data models for reads and writes. The read model might be a denormalized view in DynamoDB, a search index in OpenSearch, or a cached aggregation in ElastiCache. CQRS is about optimizing the data model for each access pattern, not just distributing load.

**Teaching tips:**

- Start the lecture by presenting a real-world scenario: "You are building an e-commerce platform. Walk me through the architecture." Let students propose services and patterns, then guide them toward the appropriate pattern based on requirements.
- When explaining each pattern, draw the architecture diagram on the whiteboard first, then discuss the AWS services that implement each component. This reinforces that architecture is a design activity, not a service selection exercise.
- Pause after the event-driven architecture section for a group exercise. Give each team a scenario (order processing, image processing, IoT data ingestion) and ask them to design an event-driven architecture using EventBridge, SQS, SNS, and Lambda.
- The strangler fig pattern is best explained with a concrete example. Walk through a hypothetical migration: "Your company has a monolithic Java application. The order management feature changes weekly, but the user management feature changes quarterly. Which do you extract first, and why?"
- Emphasize that patterns are tools, not rules. The best architecture is the simplest one that meets the requirements. Over-engineering with unnecessary patterns (CQRS for a simple CRUD app, microservices for a two-person team) creates more problems than it solves.

## Key Takeaways

- Start with the simplest architecture that meets your requirements (monolith or serverless API), and evolve toward more complex patterns (microservices, event-driven, CQRS) only when specific needs justify the added complexity.
- The three-tier web architecture (ALB + compute + database) is the foundation for most web applications; the choice of compute (EC2, ECS, Lambda) and database (RDS, DynamoDB) depends on workload characteristics.
- Event-driven architectures decouple producers from consumers using EventBridge, SQS, and SNS, enabling independent scaling, failure isolation, and flexible routing.
- The strangler fig pattern enables safe, incremental migration from monolith to microservices by routing traffic feature-by-feature through ALB path-based routing.
- Architecture is a design activity: draw the diagram first, evaluate it against the Well-Architected Framework pillars, then build it.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
