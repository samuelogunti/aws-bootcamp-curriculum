# Module 18: Quiz

1. What is the primary advantage of event-driven architecture over request-driven (synchronous) architecture for a system where multiple services need to react to the same business event?

2. A startup is building its first product: a simple task management application with CRUD operations, a small team of 3 developers, and uncertain traffic patterns. The team is debating between a microservices architecture and a monolithic architecture. Which approach should the team choose, and why?

   A) Microservices, because they are the industry standard for modern applications and provide better scalability.
   B) A monolith (or serverless monolith using Lambda), because the team is small, the domain is simple, and a monolith allows faster iteration. The team can decompose into microservices later if needed.
   C) Microservices, because they provide better fault isolation from day one.
   D) Neither; the team should use a no-code platform instead of building custom software.

3. True or False: In the CQRS pattern, the read model and write model must use the same database and the same schema.

4. A media company needs to serve 1 million page views per day for a content website. The content is mostly static (articles, images) with a dynamic comments section. The company wants the lowest possible latency for users worldwide and the lowest hosting cost for static content. Which architecture pattern should the company use?

   A) Three-tier architecture with EC2 instances serving all content.
   B) Static website with dynamic API backend: S3 + CloudFront for static content, API Gateway + Lambda + DynamoDB for the comments API.
   C) Serverless API pattern with Lambda rendering HTML for every page request.
   D) ECS Fargate with a global NLB for all content delivery.

5. A company is migrating a monolithic Java application to microservices on AWS. The application has 8 major features, and the team wants to minimize migration risk. Which migration strategy should the team use?

   A) Rewrite the entire application from scratch as microservices and switch over in a single deployment.
   B) Use the strangler fig pattern: extract one feature at a time into a microservice, route traffic to the new service using ALB path-based routing, and keep the monolith running for remaining features until all are migrated.
   C) Deploy the monolith as-is on ECS and call it a microservices architecture.
   D) Split the monolith into 8 microservices simultaneously and deploy them all at once.

6. An e-commerce platform processes orders through the following steps: validate the order, charge the payment, update inventory, and send a confirmation email. Each step is handled by a different service. The payment service occasionally fails due to third-party API timeouts. The team wants to ensure that a payment failure does not block inventory updates or email notifications. Which architecture pattern best addresses this requirement?

   A) Synchronous API calls from the order service to each downstream service in sequence.
   B) Event-driven architecture: the order service publishes an "OrderPlaced" event to EventBridge, and each downstream service (payment, inventory, notification) subscribes independently with its own SQS queue for buffering and retry.
   C) A single Lambda function that handles all four steps sequentially.
   D) A shared database where each service polls for new orders.

7. When should you choose Amazon EventBridge over Amazon SQS for event routing?

   A) When you need a simple point-to-point queue between one producer and one consumer.
   B) When you need content-based routing that directs events to different targets based on the event content (source, type, attributes), and when you need native integration with AWS service events.
   C) When you need guaranteed message ordering (FIFO).
   D) When you need the lowest possible per-message cost.

8. A solutions architect is designing a data processing pipeline. Raw CSV files (50 MB each, 100 files per day) are uploaded to S3. Each file must be validated, transformed into Parquet format, and stored in a separate S3 bucket for analytics queries using Athena. Which pipeline architecture is most appropriate for this scale?

   A) S3 event notification triggers a Lambda function that validates and transforms each file, then writes the Parquet output to the analytics bucket.
   B) Amazon Kinesis Data Streams for real-time ingestion, followed by Kinesis Data Analytics for transformation.
   C) Amazon EMR cluster running 24/7 to process files as they arrive.
   D) EC2 instances polling the S3 bucket every minute for new files.

9. A company runs a three-tier web application (ALB, EC2 Auto Scaling group, RDS PostgreSQL). The application experiences 10x traffic spikes during flash sales that last 2 hours. During normal hours, traffic is steady and predictable. The company wants to optimize costs without degrading performance during spikes. Which architectural change should the company evaluate?

   A) Replace the entire application with a serverless architecture (API Gateway + Lambda + DynamoDB) to eliminate idle capacity costs.
   B) Keep the three-tier architecture for the steady baseline traffic (covered by Savings Plans) and add a serverless overflow layer: during spikes, ALB routes excess traffic to a Lambda function that handles the same API endpoints, scaling instantly without pre-provisioning.
   C) Over-provision the EC2 Auto Scaling group to handle peak traffic at all times.
   D) Disable Auto Scaling and manually add instances before each flash sale.

10. A solutions architect is presenting two architecture options to a stakeholder for a new application:

    Option A: ECS Fargate with ALB, RDS Multi-AZ, ElastiCache. Estimated cost: $2,500/month. Provides consistent sub-50ms latency, 99.95% availability, and supports WebSocket connections.

    Option B: API Gateway + Lambda + DynamoDB. Estimated cost: $800/month at current traffic. Provides variable latency (50ms to 500ms due to cold starts), 99.95% availability, and does not natively support WebSocket connections on REST APIs.

    The application requires WebSocket support for real-time notifications and consistent sub-100ms latency. Which option should the architect recommend, and how should the architect justify the higher cost?

    A) Option B, because it is cheaper and the latency difference is acceptable.
    B) Option A, because the application requirements (WebSocket support, consistent sub-100ms latency) are met by Option A but not fully by Option B. The $1,700/month cost difference is justified by the functional requirements that Option B cannot meet without significant additional complexity (API Gateway WebSocket APIs, provisioned concurrency).
    C) Option B with provisioned concurrency and API Gateway WebSocket APIs, because serverless is always the better choice.
    D) Neither; the architect should propose a third option using EC2 instances to reduce costs further.

---

<details>
<summary>Answer Key</summary>

1. **Event-driven architecture decouples producers from consumers, allowing multiple services to react to the same event independently without the producer needing to know about or call each consumer.** In a request-driven architecture, the producer must call each consumer sequentially or in parallel, creating tight coupling. If a new consumer is added, the producer code must change. In event-driven architecture, adding a new consumer requires only subscribing to the event; the producer is unaware of and unaffected by the change. This also provides failure isolation: if one consumer fails, the others continue processing independently.
   Further reading: [Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html)

2. **B) A monolith (or serverless monolith)**
   For a small team with a simple domain and uncertain traffic, a monolith allows the fastest iteration. The team can deploy a single unit, debug in a single codebase, and avoid the operational complexity of microservices (service discovery, distributed tracing, inter-service communication). A serverless monolith (single Lambda function or a few Lambda functions behind API Gateway) adds the benefit of automatic scaling and pay-per-use pricing. The team can decompose into microservices later using the strangler fig pattern if the application grows in complexity and team size. Microservices (A, C) add unnecessary complexity for a 3-person team with a simple CRUD application.
   Further reading: [Implementing Microservices on AWS](https://docs.aws.amazon.com/whitepapers/latest/microservices-on-aws/simple-microservices-architecture-on-aws.html)

3. **False.**
   CQRS explicitly separates the read model from the write model. They can (and often do) use different databases, different schemas, and different data structures. The write model is optimized for processing commands (inserts, updates), while the read model is optimized for queries (denormalized views, search indexes, cached aggregations). The read model is typically updated asynchronously from the write model using events (for example, DynamoDB Streams triggering a Lambda function that updates an ElastiCache read cache).
   Further reading: [Cloud Design Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/introduction.html)

4. **B) Static website with dynamic API backend**
   S3 + CloudFront serves static content (articles, images) globally with low latency and at minimal cost. CloudFront caches content at edge locations, so most page views are served from cache without hitting the origin. The dynamic comments section is handled by a serverless API (API Gateway + Lambda + DynamoDB), which scales independently and costs only for actual API calls. Option A (EC2 for all content) is more expensive and slower for global delivery. Option C (Lambda rendering HTML) incurs per-invocation costs for every page view, which is wasteful for static content. Option D (ECS + NLB) is over-engineered for serving static content.
   Further reading: [Serverless Multi-Tier Architectures](https://docs.aws.amazon.com/whitepapers/latest/serverless-multi-tier-architectures-api-gateway-lambda/sample-architecture-patterns.html)

5. **B) Strangler fig pattern**
   The strangler fig pattern extracts features incrementally, one at a time, while the monolith continues to handle remaining features. ALB path-based routing directs traffic for extracted features to the new microservice and everything else to the monolith. This minimizes risk because you can stop at any point, roll back a single feature extraction without affecting others, and validate each microservice in production before extracting the next. Rewriting from scratch (A) is high-risk and time-consuming. Deploying the monolith on ECS (C) does not decompose it. Splitting all features simultaneously (D) is as risky as a full rewrite.
   Further reading: [Strangler Fig Pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/strangler-fig.html)

6. **B) Event-driven architecture**
   Publishing an "OrderPlaced" event to EventBridge allows each downstream service to subscribe independently with its own SQS queue. If the payment service fails, the message remains in its SQS queue for retry (with a dead-letter queue for persistent failures), while the inventory and notification services process the event independently. Synchronous calls (A) mean a payment failure blocks the entire order flow. A single Lambda (C) has no failure isolation between steps. A shared database with polling (D) creates tight coupling and is inefficient.
   Further reading: [SQS, SNS, or EventBridge Decision Guide](https://docs.aws.amazon.com/decision-guides/latest/sns-or-sqs-or-eventbridge/sns-or-sqs-or-eventbridge.html)

7. **B) Content-based routing and AWS service integration**
   EventBridge excels at routing events to different targets based on event content (source, detail-type, specific field values in the event body). It also natively integrates with over 90 AWS services as event sources, so you can react to AWS service events (EC2 state changes, S3 uploads, CodePipeline status changes) without custom code. SQS (A) is better for simple point-to-point queuing. SQS FIFO (C) provides ordering, which EventBridge does not guarantee. SQS (D) has lower per-message costs for high-volume, simple queuing scenarios.
   Further reading: [Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html)

8. **A) S3 + Lambda + S3 (Parquet) + Athena**
   For 100 files per day at 50 MB each (5 GB total daily), Lambda is well within its limits (15-minute timeout, 10 GB memory). S3 event notifications trigger Lambda automatically on each upload, and Lambda can use libraries like `pandas` or `pyarrow` to convert CSV to Parquet. This is the simplest and most cost-effective architecture for this scale. Kinesis (B) is designed for real-time streaming, which is unnecessary for batch file processing. EMR (C) running 24/7 is over-provisioned for 5 GB of daily data. EC2 polling (D) adds unnecessary infrastructure management and latency.
   Further reading: [Using Lambda with S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html)

9. **B) Three-tier baseline with serverless overflow**
   This hybrid approach uses the cost-efficient three-tier architecture (covered by Savings Plans) for the predictable baseline traffic and adds a serverless overflow layer that scales instantly during flash sales. The ALB routes excess traffic to Lambda functions that handle the same API endpoints. After the spike, the serverless layer scales back to zero, incurring no cost. Over-provisioning (C) wastes money during normal hours. Full serverless (A) may not be cost-effective for the steady baseline. Manual scaling (D) requires advance planning and human intervention.
   Further reading: [AWS Architecture Center](https://aws.amazon.com/architecture/)

10. **B) Option A**
    The application requires WebSocket support and consistent sub-100ms latency. Option A (ECS + ALB + RDS + ElastiCache) meets both requirements natively: ALB supports WebSocket connections, and ECS Fargate provides consistent latency without cold starts. Option B does not natively support WebSockets on REST APIs (API Gateway WebSocket APIs are a separate product with different pricing and complexity), and Lambda cold starts cause variable latency that may exceed 100ms. The $1,700/month cost difference is justified by the functional requirements. The architect should present this as a requirements-driven decision, not a cost-driven one: "Option B is cheaper but does not meet two of our stated requirements without significant additional complexity."
    Further reading: [ALB WebSocket Support](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-listeners.html)

</details>
