# Instructor Guide: AWS Bootcamp (8-Week Delivery)

## Overview

This guide provides a week-by-week delivery plan for the "AWS Bootcamp: From Novice to Architect" curriculum delivered over 8 weeks with one instructor-led session per week. Students complete self-study (reading, labs, quizzes) between sessions. The instructor session focuses on lecture, live demos, discussion, and guided lab walkthroughs.

**Format:** 1 session per week, 3 hours per session (24 hours instructor-led, ~80 hours self-study)
**Session structure:** 60 min lecture/demo + 60 min guided lab + 30 min discussion/quiz review + 30 min Q&A
**Self-study per week:** 8 to 12 hours (reading READMEs, completing labs, taking quizzes)
**Audience:** Beginner to intermediate developers with basic programming knowledge, no prior AWS experience. Students who lack basic IT or programming knowledge should complete the [IT Fundamentals: Pre-Bootcamp Primer](it-fundamentals/README.md) before starting Week 1.

## How the Blended Model Works

Each week follows this pattern:

| Activity | When | Duration | Who Leads |
|----------|------|----------|-----------|
| Pre-reading | Before session (self-study) | 2-3 hours | Student |
| Instructor session | Scheduled day | 3 hours | Instructor |
| Labs | After session (self-study) | 3-5 hours | Student (with support) |
| Quizzes | After labs (self-study) | 30-60 min | Student |
| Phase exam | End of phase (in-session or take-home) | 60 min | Instructor |

**Before each session:** Students read the module READMEs assigned for that week. This frees the instructor session for discussion, demos, and deeper exploration rather than first-pass content delivery.

**During the session:** The instructor covers key concepts with live demos, walks through the most complex lab steps, facilitates discussion using the engagement slides, and answers questions from the pre-reading.

**After each session:** Students complete the labs and quizzes independently. The instructor provides support via a shared channel (Slack, Teams, or email).

## Materials Per Module

| Material | Location | Format |
|----------|----------|--------|
| Lesson content | `modules/XX-*/README.md` | Markdown (self-study reading) |
| Slide deck | `modules/XX-*/slides.md` or `slides.pptx` | Marp Markdown / PowerPoint |
| Lab exercise | `modules/XX-*/lab/*.md` | Markdown (self-study + guided) |
| Quiz | `modules/XX-*/quiz.md` | Markdown with answer key |
| Resources | `modules/XX-*/resources.md` | Markdown (reference) |
| Architecture diagram | `generated-diagrams/module-XX-*.png` | PNG |
| Phase exam | `modules/phase-N-exam.md` | Markdown with answer key |
| IT Fundamentals primer | `it-fundamentals/` (README, lab, quiz, resources, slides) | Markdown (optional pre-bootcamp) |

## Week-by-Week Schedule

---

### Week 1: Cloud Foundations

**Modules:** 01 (Cloud Fundamentals), 02 (IAM and Security), 03 (Networking Basics)
**Phase:** 1 (Cloud Foundations)

#### Self-Study Before Session (3 hours)
- Read Module 01 README: cloud computing, NIST characteristics, service models, AWS global infrastructure, shared responsibility model
- Read Module 02 README: IAM users, groups, roles, policies, policy evaluation, best practices
- Skim Module 03 README: VPC, subnets, gateways, route tables, security groups (detailed coverage in session)

#### Instructor Session (3 hours)

| Time | Activity | Details |
|------|----------|---------|
| 0:00-0:20 | Module 01 recap and demo | Live demo: navigate the console, show Region selector, open CloudShell, run `aws sts get-caller-identity`. Discuss shared responsibility model. |
| 0:20-0:50 | Module 02 deep dive | Live demo: create an IAM user, attach a policy, show policy evaluation (explicit deny wins). Walk through a JSON policy document. |
| 0:50-1:00 | Break | |
| 1:00-1:45 | Module 03 lecture and guided lab | Lecture: VPC, subnets, IGW, NAT GW, security groups vs. NACLs. Guided walkthrough: create a VPC with public/private subnets (first 3 steps of Lab 03). |
| 1:45-2:15 | Discussion | Engagement questions from slides: "Which subnet for the database?", "Security group vs. NACL?", "Design SG rules for a three-tier app." |
| 2:15-2:30 | Quiz 01 review | Review Quiz 01 answers together. Address misconceptions. |
| 2:30-3:00 | Q&A and lab preview | Preview Labs 01-03. Answer questions from pre-reading. Assign self-study. |

#### Self-Study After Session (8 hours)
- Complete Lab 01: AWS Account Setup (45 min)
- Complete Lab 02: IAM Users, Groups, Roles (60 min)
- Complete Lab 03: VPC Setup (75 min)
- Take Quiz 01, Quiz 02, Quiz 03 (45 min total)
- Take Phase 1 Exam (60 min, take-home, submit before Week 2)

#### Instructor Prep
- Ensure all students have AWS accounts activated before the session
- Prepare a demo VPC in your own account for the live walkthrough
- Share the Phase 1 Exam as a take-home assignment due before Week 2

---

### Week 2: Core Services Part 1

**Modules:** 04 (Compute: EC2), 05 (Storage: S3), 06 (Databases: RDS and DynamoDB)
**Phase:** 2 (Core Services)

#### Self-Study Before Session (3 hours)
- Read Module 04 README: EC2 instance types, AMIs, EBS, user data, Auto Scaling, pricing models
- Read Module 05 README: S3 buckets, storage classes, versioning, lifecycle, encryption, access control
- Read Module 06 README: RDS vs. DynamoDB, Multi-AZ, read replicas, primary keys, capacity modes

#### Instructor Session (3 hours)

| Time | Activity | Details |
|------|----------|---------|
| 0:00-0:10 | Phase 1 Exam review | Review common mistakes from the Phase 1 Exam. Clarify any lingering questions. |
| 0:10-0:40 | Module 04 lecture and demo | Live demo: launch an EC2 instance with user data, connect via Instance Connect, show Auto Scaling group. Discuss instance type naming convention. |
| 0:40-1:10 | Module 05 lecture and demo | Live demo: create an S3 bucket, enable versioning, upload a file, show storage class transitions. Discuss lifecycle policies. |
| 1:10-1:20 | Break | |
| 1:20-1:50 | Module 06 lecture and demo | Live demo: create an RDS instance in a private subnet, create a DynamoDB table with composite key. Compare SQL vs. NoSQL with the decision framework table. |
| 1:50-2:20 | Guided lab walkthrough | Walk through the trickiest parts of Labs 04-06: security group for RDS, DynamoDB Query vs. Scan, EBS snapshot creation. |
| 2:20-2:45 | Discussion | "Which database for an e-commerce catalog?" "Which EBS volume type for a batch job?" "Which S3 storage class for quarterly compliance data?" |
| 2:45-3:00 | Q&A and self-study assignment | Preview Labs 04-06. Remind students about Free Tier limits for EC2 and RDS. |

#### Self-Study After Session (10 hours)
- Complete Lab 04: EC2 Instances (60 min)
- Complete Lab 05: S3 Storage (45 min)
- Complete Lab 06: Databases (60 min)
- Take Quiz 04, Quiz 05, Quiz 06 (45 min total)
- Review Module 07 and 08 READMEs for next week (2 hours)

#### Instructor Prep
- Have a pre-created RDS instance for the demo (creating one live takes 10+ minutes)
- Prepare sample DynamoDB data for the Query vs. Scan comparison

---

### Week 3: Core Services Part 2

**Modules:** 07 (Load Balancing and DNS), 08 (Messaging and Integration)
**Phase:** 2 (Core Services, completes this week)

#### Self-Study Before Session (2 hours)
- Read Module 07 README: ALB, NLB, target groups, health checks, Route 53, routing policies
- Read Module 08 README: SQS, SNS, EventBridge, fan-out pattern, dead-letter queues

#### Instructor Session (3 hours)

| Time | Activity | Details |
|------|----------|---------|
| 0:00-0:45 | Module 07 lecture and demo | Live demo: create an ALB with two EC2 targets, show health checks, demonstrate path-based routing. Discuss ALB vs. NLB selection. |
| 0:45-1:30 | Module 08 lecture and demo | Live demo: create an SQS queue, send/receive messages, create an SNS topic with email subscription, show the fan-out pattern (SNS to SQS). Draw the architecture on the whiteboard. |
| 1:30-1:40 | Break | |
| 1:40-2:10 | Guided lab walkthrough | Walk through ALB creation with health checks (Lab 07) and the DLQ configuration (Lab 08). These are the most common stumbling points. |
| 2:10-2:40 | Discussion | "When EventBridge vs. SNS vs. SQS?" "Design an event-driven order processing system." "What happens if the visibility timeout is too short?" |
| 2:40-3:00 | Phase 2 Exam prep and Q&A | Review key concepts across Modules 04-08. Assign Phase 2 Exam as take-home. |

#### Self-Study After Session (8 hours)
- Complete Lab 07: ALB and Route 53 (60 min)
- Complete Lab 08: SQS, SNS, Fan-Out (45 min)
- Take Quiz 07, Quiz 08 (30 min total)
- Take Phase 2 Exam (60 min, take-home, submit before Week 4)
- Begin reading Module 09 README for next week (1 hour)

---

### Week 4: Building Applications Part 1

**Modules:** 09 (Serverless: Lambda), 10 (Containers: ECS)
**Phase:** 3 (Building Applications)

#### Self-Study Before Session (3 hours)
- Read Module 09 README: Lambda execution model, event sources, cold starts, API Gateway integration, Layers
- Read Module 10 README: containers vs. VMs, Docker, ECR, ECS concepts, Fargate vs. EC2, service auto scaling

#### Instructor Session (3 hours)

| Time | Activity | Details |
|------|----------|---------|
| 0:00-0:10 | Phase 2 Exam review | Review common mistakes. Highlight cross-service questions (ALB + EC2, SNS + SQS fan-out). |
| 0:10-0:50 | Module 09 lecture and demo | Live demo: create a Lambda function, test with a sample event, connect to API Gateway, show X-Ray trace. Discuss push vs. poll event sources. |
| 0:50-1:00 | Break | |
| 1:00-1:40 | Module 10 lecture and demo | Live demo: build a Docker image, push to ECR, create an ECS Fargate service behind an ALB. Discuss ECS vs. EKS vs. Lambda selection. |
| 1:40-2:10 | Guided lab walkthrough | Walk through the semi-guided steps in Labs 09-10. Show how to approach a step that gives you the goal but not the exact commands. |
| 2:10-2:40 | Discussion | "Serverless API vs. three-tier: which for a payment processor?" "Your Lambda times out processing large files: what do you do?" "Fargate vs. EC2 for a startup with 3 developers?" |
| 2:40-3:00 | Q&A and self-study assignment | Preview Labs 09-10. Remind students that Phase 3 labs are semi-guided. |

#### Self-Study After Session (10 hours)
- Complete Lab 09: Lambda and API Gateway (60 min)
- Complete Lab 10: ECS Fargate (60 min)
- Take Quiz 09, Quiz 10 (30 min total)
- Read Module 11 and 12 READMEs for next week (3 hours)

#### Instructor Prep
- Have Docker installed and a pre-built container image ready for the ECS demo
- Prepare a Lambda function that intentionally fails to demonstrate error handling and CloudWatch Logs

---

### Week 5: Building Applications Part 2

**Modules:** 11 (Infrastructure as Code), 12 (CI/CD Pipelines)
**Phase:** 3 (Building Applications, completes this week)

#### Self-Study Before Session (3 hours)
- Read Module 11 README: CloudFormation templates, intrinsic functions, parameters, change sets, SAM, CDK
- Read Module 12 README: CI/CD fundamentals, CodeBuild, CodeDeploy, CodePipeline, deployment strategies

#### Instructor Session (3 hours)

| Time | Activity | Details |
|------|----------|---------|
| 0:00-0:40 | Module 11 lecture and demo | Live demo: deploy a CloudFormation stack (VPC template), show change sets, demonstrate SAM local invoke. Compare CloudFormation vs. SAM vs. CDK side by side. |
| 0:40-1:20 | Module 12 lecture and demo | Live demo: create a CodePipeline with GitHub source, CodeBuild, and CloudFormation deploy. Show a build failure and how to debug from CodeBuild logs. Discuss deployment strategies with the comparison table. |
| 1:20-1:30 | Break | |
| 1:30-2:00 | Guided lab walkthrough | Walk through the SAM template creation (Lab 11) and the CodePipeline setup (Lab 12). These are the most complex labs in Phase 3. |
| 2:00-2:30 | Discussion | "Your CloudFormation stack is in ROLLBACK_COMPLETE: what do you do?" "Blue/green vs. canary for an e-commerce site during Black Friday?" "When to use GitHub Actions vs. CodePipeline?" |
| 2:30-3:00 | Phase 3 Exam prep and Q&A | Review key concepts across Modules 09-12. Assign Phase 3 Exam as take-home. |

#### Self-Study After Session (10 hours)
- Complete Lab 11: CloudFormation and SAM (60 min)
- Complete Lab 12: CI/CD Pipeline (60 min)
- Take Quiz 11, Quiz 12 (30 min total)
- Take Phase 3 Exam (60 min, take-home, submit before Week 6)
- Begin reading Modules 13-14 READMEs for next week (2 hours)

---

### Week 6: Production Readiness

**Modules:** 13 (Security in Depth), 14 (Monitoring), 15 (Cost Optimization), 16 (Reliability and DR)
**Phase:** 4 (Production Readiness)

> **Note:** This is the densest week with 4 modules. Modules 15 and 16 have open-ended labs that students complete as self-study. The session focuses on Modules 13-14 (which have the most complex new concepts) and provides an overview of 15-16.

#### Self-Study Before Session (4 hours)
- Read Module 13 README: KMS, Secrets Manager, WAF, Shield, CloudTrail, GuardDuty, Config, Security Hub
- Read Module 14 README: three pillars of observability, CloudWatch metrics/alarms/logs, X-Ray, four golden signals
- Skim Module 15 README: Cost Explorer, Budgets, Compute Optimizer, Savings Plans
- Skim Module 16 README: RTO/RPO, DR strategies, resilience patterns, AWS Backup

#### Instructor Session (3 hours)

| Time | Activity | Details |
|------|----------|---------|
| 0:00-0:10 | Phase 3 Exam review | Review common mistakes. Focus on IaC and CI/CD questions. |
| 0:10-0:50 | Module 13 lecture and demo | Live demo: create a KMS key, encrypt an S3 bucket, store a secret in Secrets Manager, enable GuardDuty and show sample findings. Discuss defense in depth layers. |
| 0:50-1:30 | Module 14 lecture and demo | Live demo: create a CloudWatch dashboard with the four golden signals, create an alarm, run a Logs Insights query, show an X-Ray trace. Discuss alerting best practices. |
| 1:30-1:40 | Break | |
| 1:40-2:10 | Modules 15-16 overview | Condensed lecture covering: Cost Explorer and right-sizing (15 min), RTO/RPO and the four DR strategies (15 min). Use the comparison tables from the slides. Students will read the full READMEs as self-study. |
| 2:10-2:40 | Discussion | "GuardDuty vs. Security Hub: what is the difference?" "Design a monitoring dashboard for a serverless API." "Your startup has $5,000/month: Multi-AZ RDS or CloudFront?" "Which DR strategy for a payment system vs. a marketing blog?" |
| 2:40-3:00 | Q&A and self-study assignment | Preview all four labs. Emphasize that Labs 15-16 are open-ended: students design their own solutions. |

#### Self-Study After Session (12 hours)
- Complete Lab 13: Security Services (75 min)
- Complete Lab 14: Monitoring (60 min)
- Complete Lab 15: Cost Optimization Audit (90 min, open-ended)
- Complete Lab 16: DR Strategy (90 min, open-ended)
- Take Quiz 13, Quiz 14, Quiz 15, Quiz 16 (60 min total)
- Take Phase 4 Exam (60 min, take-home, submit before Week 7)

#### Instructor Prep
- Enable GuardDuty in your demo account and generate sample findings before the session
- Create a CloudWatch dashboard with sample data for the live demo
- Prepare a one-page handout summarizing the four DR strategies with RTO/RPO/cost for the Module 16 overview

---

### Week 7: Architecting

**Modules:** 17 (Well-Architected Framework), 18 (Architecture Patterns), 19 (Advanced Topics)
**Phase:** 5 (Architecting)

#### Self-Study Before Session (4 hours)
- Read Module 17 README: six pillars, trade-offs, Well-Architected Tool, lenses
- Read Module 18 README: monolith vs. microservices, three-tier, serverless API, event-driven, CQRS, strangler fig
- Read Module 19 README: Organizations, CloudFront, ElastiCache, Step Functions, Athena, ADRs

#### Instructor Session (3 hours)

| Time | Activity | Details |
|------|----------|---------|
| 0:00-0:10 | Phase 4 Exam review | Review common mistakes. Focus on security, monitoring, and DR trade-off questions. |
| 0:10-0:40 | Module 17 lecture | Walk through the six pillars using the bootcamp architecture as the example. For each pillar, ask: "How does our architecture score?" Live demo: create a workload in the Well-Architected Tool and answer a few questions. |
| 0:40-1:10 | Module 18 lecture | Present the architecture patterns using the comparison tables. Focus on: when monolith vs. microservices, serverless API vs. three-tier, and the strangler fig migration pattern. |
| 1:10-1:20 | Break | |
| 1:20-1:50 | Module 19 overview | Condensed coverage: multi-account strategy (10 min), CloudFront + ElastiCache (10 min), Step Functions vs. SQS+Lambda (10 min). Students read the full details as self-study. |
| 1:50-2:30 | Architecture design exercise | Group exercise: give each team a scenario (e-commerce, IoT dashboard, content platform). Teams design an architecture in 20 minutes, then present in 5 minutes each. Instructor critiques using Well-Architected pillars. |
| 2:30-3:00 | Capstone preview and Q&A | Walk through Module 20 requirements, deliverables, and evaluation rubric. Students choose their capstone project idea before leaving. |

#### Self-Study After Session (12 hours)
- Complete Lab 17: Well-Architected Review (90 min, open-ended)
- Complete Lab 18: Architecture Design (90 min, open-ended)
- Complete Lab 19: Advanced Services (90 min, choose 2 of 3 exercises)
- Take Quiz 17, Quiz 18, Quiz 19 (45 min total)
- Begin capstone project: finalize architecture diagram, start IaC templates (3 hours)

---

### Week 8: Capstone Project and Final Exam

**Modules:** 20 (Capstone Project)
**Phase:** 5 (Architecting, completes this week)

#### Self-Study Before Session (15 hours)
- Complete capstone implementation: IaC templates, application code, CI/CD pipeline, monitoring
- Prepare the Well-Architected self-assessment
- Create the cost estimate using the AWS Pricing Calculator
- Prepare the 15-minute presentation with live demo
- Study for the Phase 5 Exam

#### Instructor Session (3 hours)

| Time | Activity | Details |
|------|----------|---------|
| 0:00-0:45 | Phase 5 Exam | Administer the Phase 5 Exam (25 questions, 45 minutes). |
| 0:45-0:55 | Break and setup | Students prepare their demos. |
| 0:55-2:25 | Capstone presentations | Each student presents for 15 minutes: problem, architecture, live demo, trade-offs, lessons learned. Use the evaluation rubric from Module 20. (Adjust timing based on class size.) |
| 2:25-2:45 | Bootcamp wrap-up | Celebrate completion. Share key takeaways from the 8 weeks. Recommend next steps (AWS certification, continued learning). |
| 2:45-3:00 | Cleanup reminder and feedback | Remind students to delete all AWS resources. Collect bootcamp feedback. |

> **Class size adjustment:** With 15-minute presentations, a 90-minute window fits 6 students. For larger classes, reduce to 10-minute presentations (9 students) or extend the session. Alternatively, split presentations across two days.

#### Instructor Prep
- Print the evaluation rubric for each student
- Test your own internet connection and projector setup for live demos
- Have a backup plan if a student's demo fails (screenshot walkthrough)
- Prepare certificates of completion (optional)

---

## Assessment Schedule Summary

| Assessment | When | Format | Duration | Passing Score |
|------------|------|--------|----------|---------------|
| Quizzes 01-03 | Week 1 self-study | Self-graded | 15 min each | Formative (not graded) |
| Phase 1 Exam | Due before Week 2 | Take-home | 60 min | 70% |
| Quizzes 04-08 | Weeks 2-3 self-study | Self-graded | 15 min each | Formative |
| Phase 2 Exam | Due before Week 4 | Take-home | 60 min | 70% |
| Quizzes 09-12 | Weeks 4-5 self-study | Self-graded | 15 min each | Formative |
| Phase 3 Exam | Due before Week 6 | Take-home | 60 min | 70% |
| Quizzes 13-16 | Week 6 self-study | Self-graded | 15 min each | Formative |
| Phase 4 Exam | Due before Week 7 | Take-home | 60 min | 70% |
| Quizzes 17-19 | Week 7 self-study | Self-graded | 15 min each | Formative |
| Phase 5 Exam | Week 8 session | In-session | 45 min | 70% |
| Capstone | Week 8 session | Presentation + demo | 15 min | Rubric-based |

## Grading Breakdown (Suggested)

| Component | Weight |
|-----------|--------|
| Phase Exams (5 exams, equal weight) | 35% |
| Lab Completion (19 labs + capstone deliverables) | 25% |
| Capstone Project and Presentation | 30% |
| Participation and Quizzes | 10% |

## Self-Study Expectations

> **Note:** Students who need the IT Fundamentals primer should add 2 to 3 hours of self-study before Week 1 (reading, lab, and quiz in `it-fundamentals/`).

| Week | Pre-Reading | Post-Session Labs/Quizzes | Exam | Total Self-Study |
|------|-------------|---------------------------|------|-----------------|
| 1 | 3 hours | 5 hours | Phase 1 (1 hour) | 9 hours |
| 2 | 3 hours | 5 hours | | 8 hours |
| 3 | 2 hours | 4 hours | Phase 2 (1 hour) | 7 hours |
| 4 | 3 hours | 5 hours | | 8 hours |
| 5 | 3 hours | 5 hours | Phase 3 (1 hour) | 9 hours |
| 6 | 4 hours | 7 hours | Phase 4 (1 hour) | 12 hours |
| 7 | 4 hours | 8 hours | | 12 hours |
| 8 | 15 hours (capstone) | | Phase 5 (in-session) | 15 hours |
| **Total** | **37 hours** | **39 hours** | **4 hours** | **~80 hours** |

## Tips for the 1-Session-Per-Week Format

1. **Pre-reading is non-negotiable.** The session assumes students have read the READMEs. Without pre-reading, the session becomes a first-pass lecture instead of a discussion and demo. Set this expectation in Week 1.

2. **Focus sessions on what students cannot do alone.** Live demos, guided walkthroughs of tricky lab steps, group discussions, and Q&A are high-value session activities. Reading and straightforward labs are better as self-study.

3. **Week 6 is the hardest week.** Four modules with 12 hours of self-study. Warn students in Week 5 and suggest they start the pre-reading early. Consider offering extra office hours during Week 6.

4. **Capstone needs early start.** Students should choose their project idea in Week 7 and begin architecture design immediately. The 15 hours of self-study in Week 8 is aggressive. Encourage students to start implementation during Week 7.

5. **Use a shared channel for support.** Students will get stuck on labs between sessions. A Slack channel, Teams group, or discussion forum where students can ask questions (and help each other) is essential for the self-study model.

6. **Phase exams as take-home.** With one session per week, administering exams in-session consumes too much of the limited face time. Make Phases 1-4 exams take-home with a deadline before the next session. Only Phase 5 is in-session (Week 8).

7. **Review exam results at the start of each session.** Spend 10 minutes reviewing common mistakes from the previous phase exam. This reinforces learning and identifies students who need extra support.

8. **Clean up resources weekly.** Remind students at the end of every session to delete lab resources. With a week between sessions, forgotten resources accumulate charges.

## Pre-Bootcamp Checklist

Send this to students one week before the bootcamp starts:

- [ ] Create an AWS account at [aws.amazon.com](https://aws.amazon.com/) (activation can take up to 24 hours)
- [ ] Install a virtual MFA app (Google Authenticator, Microsoft Authenticator, or Authy)
- [ ] Install a modern web browser (Chrome, Firefox, Safari, or Edge)
- [ ] Install Git and create a GitHub account
- [ ] Install Python 3 (needed for Lambda functions starting in Module 09)
- [ ] Install Docker Desktop (needed for Module 10)
- [ ] Install the AWS CLI (optional; CloudShell is available in the browser)
- [ ] Read Module 01 README before the first session
- [ ] Verify basic programming knowledge (any language). If you are new to IT and programming, complete the [IT Fundamentals: Pre-Bootcamp Primer](it-fundamentals/README.md) first (2 to 3 hours of self-study).

## Post-Bootcamp

After the final session:

1. Remind students to delete all AWS resources across all Regions to avoid charges
2. Share the `resources.md` files as a reference library for continued learning
3. Recommend the AWS Solutions Architect Associate certification as the natural next step
4. Share the bootcamp GitHub repository for future reference
5. Collect feedback on the bootcamp for future improvements

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
