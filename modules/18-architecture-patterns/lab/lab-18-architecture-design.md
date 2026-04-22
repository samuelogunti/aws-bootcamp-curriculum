# Lab 18: Designing an Architecture for a Real-World Application

## Objective

Design a complete AWS architecture for a real-world application scenario, select appropriate architecture patterns, justify service choices against the Well-Architected Framework, and produce a detailed architecture document.

## Architecture Diagram

This lab does not prescribe a specific architecture. You select a scenario and design the architecture from scratch using the patterns learned in this module.

## Prerequisites

- Completed all modules from Phase 1 through Phase 4 (Modules 01 through 16)
- Completed [Module 17: The AWS Well-Architected Framework](../../17-well-architected-framework/README.md)
- Completed [Module 18: Architecture Patterns on AWS](../README.md) lesson content
- Familiarity with all AWS services covered in the bootcamp

## Duration

90 minutes

## Goal

Select one of the application scenarios below (or propose your own with instructor approval), design a complete AWS architecture using appropriate patterns, and produce a detailed architecture document that justifies every design decision against the Well-Architected Framework pillars.

## Scenarios (Choose One)

**Scenario A: E-Commerce Platform**
Design the backend for an e-commerce platform that handles product catalog browsing, user authentication, shopping cart management, order processing, payment integration, and order notification emails. The platform expects 10,000 concurrent users during peak hours and must remain available during promotional events that cause 5x traffic spikes.

**Scenario B: Real-Time Data Dashboard**
Design a system that ingests sensor data from 1,000 IoT devices (each sending a reading every 5 seconds), processes the data in near-real-time, stores it for historical analysis, and displays live dashboards to 50 concurrent users. The system must retain raw data for 1 year and aggregated data for 5 years.

**Scenario C: Content Management System**
Design a content management system for a media company that publishes 100 articles per day, serves 1 million page views per day, supports full-text search across 500,000 articles, and delivers images and videos globally with low latency. The editorial team needs a private API for content creation, and the public website needs a high-performance read API.

## Constraints

- Your architecture must use at least two of the patterns covered in this module (for example, three-tier + event-driven, serverless API + data pipeline, static website + CQRS).
- Your architecture must address all six Well-Architected Framework pillars. For each pillar, you must identify at least one design decision that supports that pillar.
- You must justify the choice of compute service (EC2, ECS, Lambda) for each component based on the workload characteristics.
- You must justify the choice of database (RDS, DynamoDB, ElastiCache) for each data store based on the access patterns.
- Your architecture must include a disaster recovery strategy with defined RTO and RPO targets.
- Your architecture must include a monitoring strategy that covers the four golden signals.
- You must estimate the monthly cost of your architecture using the [AWS Pricing Calculator](https://calculator.aws/).

## Reference Links

- [Implementing Microservices on AWS](https://docs.aws.amazon.com/whitepapers/latest/microservices-on-aws/simple-microservices-architecture-on-aws.html)
- [Serverless Multi-Tier Architectures](https://docs.aws.amazon.com/whitepapers/latest/serverless-multi-tier-architectures-api-gateway-lambda/sample-architecture-patterns.html)
- [Cloud Design Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/introduction.html)
- [Strangler Fig Pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/strangler-fig.html)
- [SQS, SNS, or EventBridge Decision Guide](https://docs.aws.amazon.com/decision-guides/latest/sns-or-sqs-or-eventbridge/sns-or-sqs-or-eventbridge.html)
- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)
- [AWS Pricing Calculator](https://calculator.aws/)

## Deliverables

1. **Architecture diagram** showing:
   - All AWS services used and how they connect
   - Data flow between components (arrows with labels)
   - Network boundaries (VPC, subnets, public/private)
   - Which components are in which Availability Zones or Regions

2. **Architecture document** (Markdown) containing:
   - Scenario selection and requirements summary
   - Architecture overview with the diagram
   - Pattern selection: which patterns you used and why
   - Service justification table: for each component, the service chosen, why it was chosen, and what alternatives were considered
   - Well-Architected assessment: for each pillar, at least one design decision that supports it
   - Data model: how data is structured and stored in each database
   - DR strategy: RTO/RPO targets and the DR approach
   - Monitoring strategy: which metrics, alarms, and dashboards you would configure
   - Cost estimate: monthly cost breakdown from the AWS Pricing Calculator
   - Trade-offs: at least two trade-offs you made and your reasoning

3. **Presentation-ready summary** (5 bullet points) that you could present to a stakeholder in 2 minutes, covering: what the system does, the key architectural decisions, the estimated cost, and the primary risk.

## Validation

Confirm the following:

- [ ] Your architecture uses at least two patterns from this module
- [ ] Your architecture diagram shows all AWS services, data flows, and network boundaries
- [ ] Your service justification table explains the choice for each component
- [ ] Your Well-Architected assessment covers all six pillars with specific design decisions
- [ ] Your DR strategy defines RTO and RPO targets
- [ ] Your monitoring strategy covers the four golden signals
- [ ] You have a cost estimate from the AWS Pricing Calculator
- [ ] Your document identifies at least two trade-offs with reasoning

## Cleanup

This lab is a design exercise. No AWS resources are created. No cleanup is required.

## Challenge (Optional)

Extend this lab with the following advanced exercises:

1. Implement the core of your designed architecture using CloudFormation or CDK. Deploy the networking layer (VPC, subnets), one compute component (Lambda function or ECS service), and one data store (DynamoDB table or RDS instance). Verify that the deployed resources match your architecture diagram.

2. Design a migration plan for converting the architecture from Scenario A (e-commerce) from a monolithic implementation to the microservices architecture you designed, using the strangler fig pattern. Define the migration phases, the order of feature extraction, and the rollback plan for each phase.

3. Create a second version of your architecture optimized for a different priority. For example, if your original design prioritized reliability, create a version optimized for cost (and document what you sacrificed). Compare the two versions across all six Well-Architected pillars.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
