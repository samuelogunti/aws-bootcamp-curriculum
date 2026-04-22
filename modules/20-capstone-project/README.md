# Module 20: Capstone Project

## Learning Objectives

By the end of this module, you will be able to:

- Design a complete, production-ready AWS architecture for a real-world application, selecting appropriate services for compute, storage, database, networking, and security based on the requirements
- Architect infrastructure as code using CloudFormation, SAM, or CDK that provisions all application resources in a repeatable, version-controlled manner
- Create a CI/CD pipeline that automates the build, test, and deployment process for the application
- Defend architectural decisions by evaluating the application against the six pillars of the [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- Propose a monitoring and observability strategy that covers the four golden signals (latency, traffic, errors, saturation) using CloudWatch metrics, alarms, logs, and dashboards
- Critique your own architecture by identifying trade-offs, limitations, and areas for future improvement

## Prerequisites

- Completion of all modules from Phase 1 through Phase 5 (Modules 01 through 19)
- Proficiency with the AWS Management Console and AWS CLI
- Familiarity with at least one programming language (Python, Node.js, or Java) for application code
- Git installed and configured for version control
- Docker installed (if using containerized workloads)

This capstone project pulls together everything you have learned across all 19 modules. You will apply concepts from cloud fundamentals, IAM, networking, compute, storage, databases, load balancing, messaging, serverless, containers, infrastructure as code, CI/CD, security, monitoring, cost optimization, reliability, the Well-Architected Framework, architecture patterns, and advanced topics into a single working application.

## Project Requirements

### Functional Requirements

Your application must satisfy the following functional requirements:

1. **User-facing interface.** The application must have a frontend (web page, mobile app, or CLI) that users interact with. This can be a static website served through [Amazon CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html) and [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html), or a server-rendered application on [Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html) or [Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html).

2. **Backend API.** The application must expose at least three API endpoints that perform create, read, and update operations. Use [Amazon API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html) with [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html), or an [Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) with ECS or EC2.

3. **Data persistence.** The application must store data in at least one database. Choose [Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html) for relational data or [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) for key-value or document data based on your access patterns.

4. **Asynchronous processing.** At least one workflow must use asynchronous processing. For example, use [Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html) to decouple a request from its processing, [Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/welcome.html) to fan out notifications, or [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html) to orchestrate a multi-step workflow.

### Technical Requirements

Your application must satisfy the following technical requirements:

1. **Infrastructure as Code.** All AWS resources must be defined in [CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html), [SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html), or [CDK](https://docs.aws.amazon.com/cdk/v2/guide/home.html) templates. No resources should be created manually through the console. The templates must be stored in a Git repository.

2. **CI/CD pipeline.** The application must have an automated deployment pipeline using [AWS CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html), GitHub Actions, or an equivalent CI/CD tool. The pipeline must include at least: a source stage, a build/test stage, and a deploy stage.

3. **Multi-AZ deployment.** The application must be deployed across at least two Availability Zones for high availability. Use [Auto Scaling groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-groups.html) for EC2, [ECS service scheduling](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html) across AZs for containers, or Lambda (which is multi-AZ by default) for serverless.

4. **Monitoring and observability.** The application must include:
   - [CloudWatch dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) displaying the four golden signals
   - [CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Alarms.html) on error rate and latency
   - Structured JSON logging to [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)
   - [AWS X-Ray](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html) tracing enabled on at least one service

5. **Security best practices.** The application must implement:
   - [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) with least-privilege policies (no `AdministratorAccess` or `*` permissions on production resources)
   - Encryption at rest using [AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) for databases and S3 buckets
   - Secrets stored in [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) or [Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) (no hardcoded credentials)
   - [Security groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html) following the principle of least privilege (no `0.0.0.0/0` on non-public ports)

6. **Cost-conscious design.** The application must demonstrate cost awareness:
   - All resources tagged with `Project`, `Environment`, and `Owner`
   - An [AWS Budget](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) configured with alert thresholds
   - A brief cost estimate using the [AWS Pricing Calculator](https://calculator.aws/)

### Deliverables

| Deliverable | Description |
|-------------|-------------|
| Architecture diagram | An annotated diagram showing all AWS services, data flows, network boundaries (VPC, subnets), and Availability Zones. Use text-based diagrams or the [AWS Architecture Icons](https://aws.amazon.com/architecture/icons/). |
| Working application | A deployed, functional application accessible through a URL or CLI. The instructor must be able to interact with the application during the presentation. |
| IaC templates | All CloudFormation, SAM, or CDK templates stored in a Git repository. The instructor must be able to deploy the application from scratch using the templates. |
| CI/CD pipeline | A working pipeline that deploys the application when code is pushed to the main branch. |
| Well-Architected self-assessment | A document evaluating the application against each of the [six Well-Architected pillars](https://docs.aws.amazon.com/wellarchitected/latest/framework/the-pillars-of-the-framework.html). For each pillar, identify at least one strength and one area for improvement. |
| Cost estimate | A monthly cost estimate from the AWS Pricing Calculator, with a breakdown by service. |
| Presentation | A 15-minute presentation covering: the problem being solved, the architecture and design decisions, a live demo, trade-offs made, and lessons learned. |

## Suggested Project Ideas

Choose one of the following ideas or propose your own (with instructor approval). Each idea is designed to exercise the full range of bootcamp skills.

### Idea 1: Event Ticketing System

Users browse upcoming events, purchase tickets, and receive email confirmations. The system tracks ticket inventory in real time and prevents overselling.

Key services: API Gateway, Lambda, DynamoDB (events and tickets), SQS (order processing queue), SNS (email confirmations), S3 + CloudFront (frontend), Step Functions (order workflow).

### Idea 2: URL Shortener with Analytics

Users create shortened URLs, share them, and view click analytics (total clicks, clicks by country, clicks over time). Short URLs expire after a configurable period.

Key services: API Gateway, Lambda, DynamoDB (URL mappings with TTL), CloudFront (redirect endpoint), S3 (analytics data), Athena (analytics queries), CloudWatch (click metrics).

### Idea 3: Image Processing Pipeline

Users upload images through a web interface. The system generates thumbnails in multiple sizes, extracts metadata (dimensions, format, EXIF data), and stores the processed images in S3. Users can browse and search their image library.

Key services: S3 (uploads and processed images), Lambda (image processing), SQS (processing queue), DynamoDB (image metadata), API Gateway (browse/search API), CloudFront (image delivery).

### Idea 4: Real-Time Chat Application

Users create chat rooms, send messages, and see messages from other users in real time. The system persists message history and supports user presence (online/offline status).

Key services: API Gateway WebSocket API, Lambda (message handling), DynamoDB (messages and user state), SNS (presence notifications), S3 + CloudFront (frontend), CloudWatch (connection metrics).

### Idea 5: IoT Sensor Dashboard

Simulated IoT devices send sensor readings (temperature, humidity, pressure) to AWS. The system processes readings in near-real-time, stores them for historical analysis, and displays live dashboards.

Key services: API Gateway or IoT Core (data ingestion), Lambda (processing), DynamoDB (recent readings), S3 (historical data in Parquet), Athena (historical queries), CloudWatch (dashboards), SNS (threshold alerts).

## Evaluation Criteria

| Criteria | Weight | What the Instructor Evaluates |
|----------|--------|-------------------------------|
| Architecture design and justification | 25% | Is the architecture well-designed? Are service choices justified? Are trade-offs documented? Does the architecture diagram clearly communicate the design? |
| Implementation quality and completeness | 25% | Does the application work? Are all functional and technical requirements met? Is the code clean and well-organized? |
| Security and best practices | 20% | Are IAM policies least-privilege? Is data encrypted? Are secrets managed properly? Are security groups restrictive? |
| Operational readiness | 15% | Are dashboards, alarms, and logging configured? Is the CI/CD pipeline functional? Can the application be redeployed from IaC templates? |
| Presentation and communication | 15% | Is the presentation clear and well-structured? Does the demo work? Can the student explain and defend their design decisions? |

## Timeline

| Week | Activities |
|------|-----------|
| Week 1 | Select a project idea. Design the architecture. Create the architecture diagram. Write the IaC templates for the networking and data layers. Submit the architecture for instructor review. |
| Week 2 | Implement the application code and remaining IaC templates. Set up the CI/CD pipeline. Deploy the application. Begin the Well-Architected self-assessment. |
| Week 3 | Add monitoring (dashboards, alarms, logging, tracing). Complete the Well-Architected self-assessment. Create the cost estimate. Polish the application and fix bugs. Prepare the presentation. |
| Final day | Deliver the 15-minute capstone presentation with a live demo. |

## Well-Architected Self-Assessment Guide

Use the [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html) as a self-assessment tool for your capstone architecture. For each pillar, answer the guiding questions and document your findings.

| Pillar | Guiding Questions for Self-Assessment |
|--------|---------------------------------------|
| [Operational Excellence](https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/welcome.html) | Is your infrastructure defined as code? Do you have a CI/CD pipeline? How do you monitor the application? How would you respond to an incident? |
| [Security](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html) | Are IAM policies least-privilege? Is data encrypted at rest and in transit? Are secrets managed securely? How do you detect security threats? |
| [Reliability](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html) | Is the application deployed across multiple AZs? How does it recover from component failures? What are your RTO and RPO? Do you have backups? |
| [Performance Efficiency](https://docs.aws.amazon.com/wellarchitected/2025-02-25/framework/a-performance-efficiency.html) | Are you using the right compute and database services for your workload? Have you considered caching? How does the application scale under load? |
| [Cost Optimization](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html) | Are all resources tagged? Do you have a budget with alerts? Are you using the most cost-effective service options? Could you reduce costs without degrading the user experience? |
| [Sustainability](https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/sustainability-pillar.html) | Are resources right-sized? Are you using managed/serverless services that maximize utilization? Could you reduce data transfer or storage waste? |

> **Tip:** You do not need a perfect score on every pillar. The self-assessment is about demonstrating that you can evaluate your own architecture critically, identify gaps, and propose improvements. Documenting a known limitation with a plan to address it is more valuable than claiming perfection.

## Instructor Notes

**Estimated time:** 3 weeks (in addition to regular lecture time)

**Common student questions:**

- Q: Can I use a service that was not covered in the bootcamp?
  A: Yes, as long as you can explain why you chose it and how it fits into your architecture. The capstone is an opportunity to explore beyond the bootcamp curriculum. However, the core requirements (IaC, CI/CD, monitoring, security) must use the services and practices taught in the bootcamp.

- Q: How complex does the application need to be?
  A: The application should be complex enough to demonstrate the technical requirements (IaC, CI/CD, multi-AZ, monitoring, security, async processing) but does not need to be a production-grade commercial product. A well-designed URL shortener with proper infrastructure is better than a half-finished e-commerce platform.

- Q: Can I work in a team?
  A: This depends on the instructor's preference. If teams are allowed, each team member should own a specific component and be able to explain their contribution during the presentation. The architecture should be more complex for teams (for example, a microservices architecture with each member owning a service).

- Q: What if I run out of Free Tier during the capstone?
  A: Set up an AWS Budget with a low threshold (for example, $10) and monitor it daily. Use the smallest instance types and on-demand capacity modes. Clean up resources when not actively developing. Most capstone projects can be built within $20 to $50 if resources are managed carefully.

**Teaching tips:**

- Schedule a mandatory architecture review at the end of Week 1. This is the most important checkpoint. Catching design issues early prevents wasted implementation effort. Review the architecture diagram, IaC approach, and service choices.
- Encourage students to start with the IaC templates and CI/CD pipeline before writing application code. This ensures that the infrastructure is repeatable and deployable from the start, rather than being retrofitted at the end.
- During Week 2, hold office hours for students who are stuck on implementation issues. Common blockers include IAM permission errors, VPC networking issues, and CI/CD pipeline configuration.
- For the final presentations, create a rubric based on the evaluation criteria table and share it with students at the start of the capstone. This sets clear expectations and helps students prioritize their effort.
- Remind students to clean up all resources after the capstone presentations to avoid ongoing charges.

## Key Takeaways

- The capstone project is your opportunity to demonstrate mastery of the entire bootcamp curriculum by designing, building, and operating a complete AWS application.
- Start with the architecture: design the diagram, justify your service choices, and get instructor feedback before writing code.
- Infrastructure as code, CI/CD, monitoring, and security are not optional extras; they are core requirements that distinguish a production-ready application from a prototype.
- Use the Well-Architected Framework as a self-assessment tool to evaluate your architecture critically and identify areas for improvement.
- Document your trade-offs: every architecture involves compromises, and explaining why you made a specific choice is as important as the choice itself.
