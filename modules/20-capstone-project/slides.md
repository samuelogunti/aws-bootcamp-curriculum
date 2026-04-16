---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 20: Capstone Project'
---

# Module 20: Capstone Project

**Phase 5: Architecting**
Estimated project time: 3 weeks

<!-- Speaker notes: Welcome to Module 20, the capstone. This session is a project kickoff, not a traditional lecture. Walk through requirements, deliverables, evaluation criteria, timeline, and project ideas. Encourage students to ask questions throughout. Total kickoff time: ~60 minutes. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Design a complete, production-ready AWS architecture for a real-world application
- Architect infrastructure as code using CloudFormation, SAM, or CDK
- Create a CI/CD pipeline automating build, test, and deployment
- Defend architectural decisions against the six Well-Architected pillars
- Propose a monitoring strategy covering the four golden signals
- Critique your own architecture by identifying trade-offs and limitations

---

## Prerequisites and agenda

**Prerequisites:** All modules 01 through 19

**Agenda:**
1. Project overview and goals
2. Functional requirements
3. Technical requirements
4. Deliverables
5. Suggested project ideas
6. Evaluation criteria
7. Three-week timeline
8. Well-Architected self-assessment guide

---

# Project overview

<!-- Speaker notes: This section sets the stage. Emphasize that the capstone synthesizes knowledge from every module. The goal is a working application with proper infrastructure, not a commercial product. -->

---

## What the capstone demonstrates

- You can design an architecture and justify service choices
- You can implement IaC, CI/CD, monitoring, and security
- You can evaluate your own work against the Well-Architected Framework
- You can communicate and defend technical decisions

> A well-designed URL shortener with proper infrastructure is better than a half-finished e-commerce platform.

---

# Functional requirements

<!-- Speaker notes: Walk through each requirement. Emphasize that these are minimum requirements, not maximum scope. -->

---

## Four functional requirements

1. **User-facing interface:** Web page, mobile app, or CLI that users interact with (S3 + CloudFront, or ECS/EC2)
2. **Backend API:** At least three endpoints for create, read, and update operations (API Gateway + Lambda, or ALB + ECS)
3. **Data persistence:** At least one database (RDS for relational, DynamoDB for key-value)
4. **Asynchronous processing:** At least one async workflow (SQS, SNS, or Step Functions)

---

# Technical requirements

<!-- Speaker notes: Walk through each technical requirement. These are the production-readiness standards from Modules 11-16. -->

---

## Infrastructure as Code

- All resources defined in CloudFormation, SAM, or CDK
- No resources created manually through the console
- Templates stored in a Git repository
- Instructor must be able to deploy from scratch using templates

---

## CI/CD pipeline

- Automated pipeline using CodePipeline, GitHub Actions, or equivalent
- Must include: source stage, build/test stage, deploy stage
- Application deploys when code is pushed to the main branch

---

## Multi-AZ, monitoring, and security

**Multi-AZ deployment:**
- At least two Availability Zones for high availability

**Monitoring and observability:**
- CloudWatch dashboards with the four golden signals
- Alarms on error rate and latency
- Structured JSON logging to CloudWatch Logs
- X-Ray tracing on at least one service

---

## Security and cost awareness

**Security best practices:**
- IAM roles with least-privilege policies
- Encryption at rest using KMS
- Secrets in Secrets Manager or Parameter Store
- Security groups with no `0.0.0.0/0` on non-public ports

**Cost-conscious design:**
- All resources tagged with `Project`, `Environment`, `Owner`
- AWS Budget configured with alert thresholds
- Cost estimate from the AWS Pricing Calculator

---

# Deliverables

<!-- Speaker notes: Walk through each deliverable. Share the evaluation rubric so students know exactly what is expected. -->

---

## Seven deliverables

| Deliverable | Description |
|-------------|-------------|
| Architecture diagram | Annotated diagram with services, data flows, VPC, AZs |
| Working application | Deployed and accessible; instructor can interact with it |
| IaC templates | All templates in Git; deployable from scratch |
| CI/CD pipeline | Working pipeline that deploys on push to main |

---

## Seven deliverables (continued)

| Deliverable | Description |
|-------------|-------------|
| Well-Architected self-assessment | Evaluate against all six pillars; strengths and gaps |
| Cost estimate | Monthly estimate from Pricing Calculator, by service |
| Presentation | 15 minutes: problem, architecture, live demo, trade-offs, lessons |

---

# Suggested project ideas

<!-- Speaker notes: Walk through each idea briefly. Students can also propose their own with instructor approval. -->

---

## Idea 1: Event ticketing system

Users browse events, purchase tickets, receive email confirmations. Tracks inventory in real time, prevents overselling.

**Key services:** API Gateway, Lambda, DynamoDB, SQS, SNS, S3 + CloudFront, Step Functions

---

## Idea 2: URL shortener with analytics

Users create shortened URLs, share them, view click analytics (total clicks, by country, over time). Short URLs expire after a configurable period.

**Key services:** API Gateway, Lambda, DynamoDB (with TTL), CloudFront, S3, Athena, CloudWatch

---

## Idea 3: Image processing pipeline

Users upload images. System generates thumbnails, extracts metadata, stores processed images. Users browse and search their library.

**Key services:** S3, Lambda, SQS, DynamoDB, API Gateway, CloudFront

---

## Idea 4: Real-time chat application

Users create chat rooms, send messages, see messages in real time. Persists history and supports user presence (online/offline).

**Key services:** API Gateway WebSocket, Lambda, DynamoDB, SNS, S3 + CloudFront, CloudWatch

---

## Idea 5: IoT sensor dashboard

Simulated devices send sensor readings. System processes in near-real-time, stores for historical analysis, displays live dashboards.

**Key services:** API Gateway, Lambda, DynamoDB, S3 (Parquet), Athena, CloudWatch, SNS

---

## Think about it: which project idea?

Review the five project ideas (or propose your own). Consider: which idea exercises the most bootcamp skills? Which interests you most? Which is achievable in three weeks?

**Share your top choice and one reason why.**

<!-- Speaker notes: Go around the room and have each student share their choice. This helps identify common interests for potential team formation (if teams are allowed). Encourage students to pick a project that is complex enough to demonstrate all technical requirements but not so ambitious that they cannot finish in three weeks. -->

---

# Evaluation criteria

<!-- Speaker notes: Share this rubric at the start so students can prioritize their effort. -->

---

## How your project will be evaluated

| Criteria | Weight | What the Instructor Evaluates |
|----------|--------|-------------------------------|
| Architecture design | 25% | Well-designed? Service choices justified? Trade-offs documented? |
| Implementation quality | 25% | Does it work? Requirements met? Code clean? |
| Security and best practices | 20% | Least-privilege IAM? Encryption? Secrets managed? |
| Operational readiness | 15% | Dashboards, alarms, logging? CI/CD functional? Redeployable from IaC? |
| Presentation | 15% | Clear? Demo works? Can you explain and defend decisions? |

---

# Three-week timeline

<!-- Speaker notes: Emphasize the Week 1 architecture review. Catching design issues early prevents wasted implementation effort. -->

---

## Week-by-week plan

| Week | Activities |
|------|-----------|
| Week 1 | Select idea. Design architecture. Create diagram. Write IaC for networking and data layers. Submit for instructor review. |
| Week 2 | Implement app code and remaining IaC. Set up CI/CD pipeline. Deploy application. Begin Well-Architected self-assessment. |
| Week 3 | Add monitoring (dashboards, alarms, logging, tracing). Complete self-assessment. Create cost estimate. Polish and fix bugs. Prepare presentation. |

**Final day:** Deliver 15-minute capstone presentation with live demo.

---

## Week 1 checkpoint: architecture review

- This is the most important checkpoint
- Submit your architecture diagram and IaC approach
- Instructor reviews service choices and identifies design issues
- Catching problems early prevents wasted implementation effort

> Start with IaC templates and CI/CD before writing application code. This ensures infrastructure is repeatable from the start.

---

# Well-Architected self-assessment

<!-- Speaker notes: Walk through the guiding questions for each pillar. Students do not need perfection; they need to demonstrate critical self-evaluation. -->

---

## Self-assessment guide

| Pillar | Guiding Questions |
|--------|-------------------|
| Operational Excellence | Is infra defined as code? Do you have CI/CD? How do you monitor? |
| Security | Least-privilege IAM? Encryption at rest/transit? Secrets managed? |
| Reliability | Multi-AZ? How does it recover from failures? Backups? |
| Performance Efficiency | Right compute and database? Caching considered? How does it scale? |
| Cost Optimization | Resources tagged? Budget with alerts? Cost-effective service choices? |
| Sustainability | Resources right-sized? Using managed/serverless services? |

> Documenting a known limitation with a plan to address it is more valuable than claiming perfection.

---

## Design challenge: defending your architecture

After selecting your project idea, a colleague asks: "Why did you choose DynamoDB instead of RDS? Why Lambda instead of ECS? Why not just deploy everything on a single EC2 instance?"

**How would you defend your service choices using the Well-Architected pillars?**

<!-- Speaker notes: This is a preview of what students will face during their capstone presentation. Expected approach: justify each choice against specific pillars. DynamoDB for key-value access patterns (performance efficiency), Lambda for variable traffic (cost optimization), multi-service architecture for independent scaling (reliability). A single EC2 instance is a single point of failure (reliability), requires OS patching (operational excellence), and cannot scale independently (performance). -->

---

## Think about it: scoping your capstone

A common mistake is choosing a project that is too ambitious. You have three weeks, and the technical requirements (IaC, CI/CD, monitoring, security) take significant time regardless of application complexity.

**What is the minimum viable application that demonstrates all technical requirements?**

<!-- Speaker notes: Expected answer: A simple CRUD API with a web frontend. For example, a task list or bookmark manager. The application itself can be simple; the infrastructure around it (IaC, CI/CD, multi-AZ, monitoring, security, cost tagging) is what demonstrates mastery. Students who choose overly complex applications often run out of time on the infrastructure requirements. Encourage students to get the infrastructure right first, then add application features if time permits. -->

---

## Capstone kickoff

You are now ready to begin your capstone project.

**Next steps:**
1. Choose your project idea (or propose your own)
2. Start designing the architecture diagram
3. Set up your Git repository
4. Begin writing IaC templates for the networking layer
5. Submit your architecture for instructor review by end of Week 1

**Remember:** Clean up all resources after presentations to avoid charges.

<!-- Speaker notes: Remind students to set up an AWS Budget with a low threshold ($10-$20) and monitor daily. Most capstone projects can be built within $20-$50 if resources are managed carefully. Schedule office hours during Week 2 for implementation blockers. Common issues: IAM permission errors, VPC networking, CI/CD pipeline configuration. -->

---

# Questions?

Review `modules/20-capstone-project/resources.md` for further reading.

<!-- Speaker notes: Take 10-15 minutes for questions. This is the most important Q&A session because students need clarity on requirements before starting. Common questions: "Can I use a service not covered in the bootcamp?" (Yes, if you can explain why.) "Can I work in a team?" (Instructor's preference; if teams, each member owns a component.) "What if I exceed Free Tier?" (Set a budget alert, use smallest instances, clean up when not developing.) -->
