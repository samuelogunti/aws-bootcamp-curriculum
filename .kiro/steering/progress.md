# Bootcamp Progress Tracker

This file tracks the current state of every module in the AWS Bootcamp curriculum. Update this file after every content change.

## Content Standards Compliance

All modules must meet the standards defined in `content-standards.md`:
- README with: Learning Objectives (Bloom's verbs), Prerequisites, Concepts (with AWS doc links), Instructor Notes, Key Takeaways
- Lab folder with step-by-step exercises including Architecture Diagram, Validation, Cleanup, and optional Challenge
- Quiz with 8–12 questions (scenario-based from Module 09+), answer key with doc links
- Resources page with official AWS documentation, whitepapers, FAQs, architecture refs

## Status Legend

| Symbol | Meaning |
|--------|---------|
| ✅ | Complete — meets content standards |
| 🔶 | Draft — outline exists but does NOT meet content standards (missing doc links, Bloom's verbs, proper structure, or required files) |
| ❌ | Not started |

---

## Phase 1: Cloud Foundations

| Module | README | Lab | Quiz | Resources | Standards Met |
|--------|--------|-----|------|-----------|---------------|
| 01 — Cloud Fundamentals | ✅ | ✅ | ✅ | ✅ | Yes |
| 02 — IAM & Security | ✅ | ✅ | ✅ | ✅ | Yes |
| 03 — Networking Basics | ✅ | ✅ | ✅ | ✅ | Yes |

## Phase 2: Core Services

| Module | README | Lab | Quiz | Resources | Standards Met |
|--------|--------|-----|------|-----------|---------------|
| 04 — Compute (EC2) | ✅ | ✅ | ✅ | ✅ | Yes |
| 05 — Storage (S3) | ✅ | ✅ | ✅ | ✅ | Yes |
| 06 — Databases (RDS & DynamoDB) | ✅ | ✅ | ✅ | ✅ | Yes |
| 07 — Load Balancing & DNS | ✅ | ✅ | ✅ | ✅ | Yes |
| 08 — Messaging & Integration | ✅ | ✅ | ✅ | ✅ | Yes |

## Phase 3: Building Applications

| Module | README | Lab | Quiz | Resources | Standards Met |
|--------|--------|-----|------|-----------|---------------|
| 09 — Serverless (Lambda) | ✅ | ✅ | ✅ | ✅ | Yes |
| 10 — Containers (ECS) | ✅ | ✅ | ✅ | ✅ | Yes |
| 11 — Infrastructure as Code | ✅ | ✅ | ✅ | ✅ | Yes |
| 12 — CI/CD Pipelines | ✅ | ✅ | ✅ | ✅ | Yes |

## Phase 4: Production Readiness

| Module | README | Lab | Quiz | Resources | Standards Met |
|--------|--------|-----|------|-----------|---------------|
| 13 — Security in Depth | ✅ | ✅ | ✅ | ✅ | Yes |
| 14 — Monitoring & Observability | ✅ | ✅ | ✅ | ✅ | Yes |
| 15 — Cost Optimization | ✅ | ✅ | ✅ | ✅ | Yes |
| 16 — Reliability & DR | ✅ | ✅ | ✅ | ✅ | Yes |

## Phase 5: Architecting

| Module | README | Lab | Quiz | Resources | Standards Met |
|--------|--------|-----|------|-----------|---------------|
| 17 — Well-Architected Framework | ✅ | ✅ | ✅ | ✅ | Yes |
| 18 — Architecture Patterns | ✅ | ✅ | ✅ | ✅ | Yes |
| 19 — Advanced Topics | ✅ | ✅ | ✅ | ✅ | Yes |
| 20 — Capstone Project | ✅ | N/A | N/A | ✅ | Yes |

---

## Phase Exams

| Exam | File | Status | Notes |
|------|------|--------|-------|
| Phase 1 Exam (Modules 01–03) | `modules/phase-1-exam.md` | ✅ | Complete. 25 questions covering Modules 01-03. |
| Phase 2 Exam (Modules 04–08) | `modules/phase-2-exam.md` | ✅ | Complete. 25 questions covering Modules 04-08. |
| Phase 3 Exam (Modules 09–12) | `modules/phase-3-exam.md` | ✅ | Complete. 25 questions covering Modules 09-12. |
| Phase 4 Exam (Modules 13–16) | `modules/phase-4-exam.md` | ✅ | Complete. 25 questions covering Modules 13-16. |
| Phase 5 Exam (Modules 17–19) | `modules/phase-5-exam.md` | ✅ | Complete. 25 questions covering Modules 17-19. |

---

## Summary

- **Total modules:** 20
- **Phase exams:** 5 (one per phase)
- **Fully complete (✅):** All 20 modules, all 5 phase exams. Curriculum is 100% complete.
- **Draft outlines (🔶):** 0
- **Not started (❌):** 0
- **Next priority:** Curriculum complete. Final review and commit.

## Common Gaps Across All Draft Modules

No draft modules remain. All 20 modules meet content standards.
3. Missing `Prerequisites` section referencing prior modules
4. Missing `Instructor Notes` section with timing and teaching tips
5. `Topics` section is a bullet outline, not the detailed `Concepts` format with explanations
6. Lab descriptions are inline in README instead of separate files in `lab/` folder
7. No quiz files (except Module 01 which has a draft)
8. No resources files (except Module 01 which has a draft without all required sections)

## Decision Log

| Date | Decision |
|------|----------|
| 2026-04-13 | Project created with 20-module curriculum structure |
| 2026-04-13 | Content standards established: official AWS docs only, Bloom's Taxonomy, structured module format |
| 2026-04-13 | Progress tracker created for agent context awareness |
| 2026-04-13 | Hook added: "Code Quality Review on Save" — analyzes source files on edit for code smells, patterns, best practices |
| 2026-04-13 | Hook added: "Update Progress Tracker" — auto-updates progress.md after every agent session |
| 2026-04-13 | Spec created: `.kiro/specs/aws-bootcamp-curriculum/requirements.md` — 23 EARS-pattern requirements. Requirements-first workflow selected. |
| 2026-04-13 | Added Requirements 24–27: Phase exams — certification-level Q&A exam at end of each phase (20–30 questions, 70% pass, scenario-based, multi-service reasoning). 5 exam files added to project scope. |
| 2026-04-13 | Writing style rules added to content-standards.md: professional/educational tone, no em dashes, no exclamation points, no casual slang. Quality checklist updated. |
| 2026-04-13 | Design document completed: `.kiro/specs/aws-bootcamp-curriculum/design.md` with templates for all 5 component types, progressive complexity tables, build process, quality checklists, and writing style rules. Next step: generate tasks. |
| 2026-04-13 | Tasks document completed: `.kiro/specs/aws-bootcamp-curriculum/tasks.md` with 30 top-level tasks (20 modules, 5 phase exams, 5 checkpoints). Full spec now complete (requirements, design, tasks). Ready for task execution. |
| 2026-04-13 | Spec cleanup: removed all em/en dashes from requirements.md, fixed Req 11 to exclude Module 20 from quiz scope (09-19 not 09-20), normalized requirement headings. All three spec files verified clean. |
| 2026-04-13 | Module 01 upgraded to full content-standards compliance. Serves as the reference template for all subsequent modules. README (7 objectives, 5 concept sections, 17+ doc links), Lab (5 guided steps, architecture diagram, validation, cleanup, challenge), Quiz (10 questions, answer key with doc links), Resources (24 doc links, 2 whitepapers, FAQ, architecture refs). |
| 2026-04-13 | Module 02 (IAM and Security) built to full content-standards compliance. README (7 objectives, 6 concept sections, 20+ doc links), Lab (7 guided steps covering users/groups/roles/policies/policy simulator/explicit deny), Quiz (10 questions), Resources (25 doc links). |
| 2026-04-14 | Module 03 (Networking Basics) built to full content-standards compliance. README (8 objectives, 6 concept sections, 25+ doc links), Lab (10 guided steps: VPC, subnets, IGW, NAT GW, route tables, security groups, EC2 instances), Quiz (10 questions), Resources (22 doc links). All Phase 1 modules now complete. |
| 2026-04-14 | Phase 1 Exam written: 25 questions covering Modules 01-03 at certification-level complexity. 64% scenario-based (exceeds 40% minimum), 4 cross-module reasoning questions, ordering/sequencing question included. Answer key with detailed explanations and verified AWS doc links. Study guide organized by module. All doc links verified via MCP tools. |
| 2026-04-14 | Module 04 (Compute: EC2) built to full content-standards compliance. README (8 objectives, 8 concept sections, 26+ doc links), Lab (9 guided steps: default VPC, security group, EC2 launch with user data, Instance Connect, EBS volume, snapshot, launch template, Auto Scaling group), Quiz (10 questions), Resources (28 doc links, 3 FAQs). First Phase 2 module complete. |
| 2026-04-14 | Module 05 (Storage: S3) built to full content-standards compliance. README (7 objectives, 8 concept sections, 39 doc links), Lab (7 guided steps: bucket creation, upload, versioning, lifecycle, encryption, Block Public Access, static website hosting), Quiz (10 questions), Resources (34 doc links). |
| 2026-04-14 | Module 06 (Databases: RDS and DynamoDB) built to full content-standards compliance. README (8 objectives, 11 concept sections, 30+ doc links), Lab (9 guided steps: DB subnet group, security group, RDS PostgreSQL with psql, DynamoDB table with composite key, Query vs Scan comparison), Quiz (10 questions), Resources (30+ doc links, 2 FAQs). |
| 2026-04-14 | Module 07 (Load Balancing and DNS) README completed. 8 objectives, 11 concept sections (ELB overview, ALB/NLB/GLB comparison, ALB deep dive, target groups, health checks, SSL/TLS, connection draining, Route 53, record types, routing policies, Route 53 health checks), 23 doc links. Lab, quiz, resources in progress. |
| 2026-04-14 | Module 07 (Load Balancing and DNS) built to full content-standards compliance. Lab (8 guided steps: VPC setup, security groups, 2 EC2 instances, target group, ALB, load balancing test, health check observation, optional Route 53), Quiz (10 questions), Resources (28 doc links, 2 FAQs). |
| 2026-04-14 | Hook added: "Validate Module Content" — auto-validates module .md files on save against content standards (em dashes, sections, Bloom's verbs, doc links, heading hierarchy). |
| 2026-04-14 | Module 08 (Messaging and Integration) built to full content-standards compliance. README (8 objectives, 8 concept sections, 38 doc links), Lab (6 guided steps: SQS queue, long polling, SNS topic with email, fan-out pattern, DLQ with redrive policy, CloudWatch monitoring), Quiz (10 questions), Resources (35+ doc links, 2 FAQs, 1 whitepaper, 1 architecture ref). All Phase 2 modules now complete. |
| 2026-04-14 | Phase 2 Exam written: 25 questions covering Modules 04-08. 88% scenario-based (exceeds 40% minimum), cross-module questions (EC2+ALB, Route 53+ALB, SNS+SQS fan-out, DynamoDB+GSI), 5 multi-select, 1 ordering. Study guide organized by module. Phase 2 complete. |
| 2026-04-15 | Module 09 (Serverless: Lambda) built to full content-standards compliance. First Phase 3 module. README (7 objectives with Phase 3 Bloom's verbs, 9 concept sections, 30+ doc links), Lab (7 steps: 4 guided + 3 semi-guided including DynamoDB+Lambda, API Gateway route, S3 trigger), Quiz (10 questions with 3 scenario-based), Resources (45+ doc links, 2 whitepapers, 2 FAQs, 2 architecture refs). |
| 2026-04-15 | Module 10 (Containers: ECS) built to full content-standards compliance. README (8 objectives, 10 concept sections, 29 doc links), Lab (7 steps: 4 guided + 3 semi-guided including ECS cluster/service setup and rolling deployment), Quiz (10 questions with 3 scenario-based), Resources (40+ doc links, 2 whitepapers, 2 FAQs, 3 architecture refs). |
| 2026-04-15 | Module 11 (Infrastructure as Code) built to full content-standards compliance. README (7 objectives, 11 concept sections covering CloudFormation/SAM/CDK, 28 doc links), Lab (7 steps: 4 guided + 3 semi-guided including CloudFormation VPC template and SAM serverless API), Quiz (10 questions with 2 scenario-based), Resources (40+ doc links, 2 whitepapers, 1 FAQ, 2 architecture refs). |
| 2026-04-15 | Module 12 (CI/CD Pipelines) built to full content-standards compliance. README (7 objectives, 10 concept sections, 25+ doc links), Lab (7 steps: 4 guided + 3 semi-guided including CodePipeline setup and test stage), Quiz (10 questions with 3 scenario-based), Resources (57 doc links, 3 whitepapers, 3 FAQs, 3 architecture refs). All Phase 3 modules now complete. |
| 2026-04-15 | Phase 3 Exam written: 25 questions covering Modules 09-12 at certification-level complexity. 60% scenario-based (exceeds 40% minimum), 8 multi-service reasoning questions, 4 multi-select questions, 1 ordering question, 1 select-THREE question. Topics: Lambda event sources and VPC config, ECS Fargate vs EC2, CloudFormation rollback/change sets/stack policies/parameters/intrinsic functions, SAM resource types, CDK for complex apps, CodePipeline stages, CodeBuild buildspec phases, CodeDeploy appspec and deployment strategies (canary/blue-green/rolling), Docker multi-stage builds, Lambda container images. Study guide organized by module. Phase 3 complete. |
| 2026-04-15 | Module 13 (Security in Depth) built to full content-standards compliance. First Phase 4 module. README (8 objectives with Phase 4 Bloom's verbs, 8 concept sections covering KMS/Secrets Manager/Parameter Store/WAF/Shield/CloudTrail/GuardDuty/Config/Security Hub, 25+ doc links), Lab (6 steps: 3 guided + 3 semi-guided covering KMS key creation, S3 encryption, Secrets Manager, CloudTrail trail, GuardDuty, Config rules), Quiz (10 questions with 2 scenario-based), Resources (23 doc links, 2 whitepapers, 5 FAQs, 2 architecture refs). |
| 2026-04-15 | Module 14 (Monitoring and Observability) built to full content-standards compliance. README (8 objectives with Phase 4 Bloom's verbs, 8 concept sections covering three pillars of observability, CloudWatch metrics/alarms/logs/dashboards, X-Ray tracing, four golden signals, alerting best practices, 25+ doc links), Lab (6 steps: 3 guided + 3 semi-guided covering Lambda with structured logging, CloudWatch alarms, Logs Insights queries, X-Ray traces, dashboard creation, custom metrics), Quiz (10 questions with 2 scenario-based), Resources (20 doc links, 2 whitepapers, 2 FAQs, 2 architecture refs). |
| 2026-04-16 | Module 15 (Cost Optimization) built to full content-standards compliance. First open-ended lab in the curriculum. README (8 objectives with Phase 4 Bloom's verbs, 8 concept sections covering AWS pricing, Cost Explorer, Budgets, tagging, Compute Optimizer, Savings Plans/RIs, cost traps, storage optimization, 20+ doc links), Lab (open-ended: goal/constraints format with deliverables including cost optimization report, budget setup, and tagging audit), Quiz (10 questions with 2 scenario-based), Resources (17 doc links, 2 whitepapers, 3 FAQs, 2 architecture refs). |
| 2026-04-16 | Module 16 (Reliability and Disaster Recovery) built to full content-standards compliance. Last Phase 4 module. README (8 objectives with Phase 4 Bloom's verbs, 8 concept sections covering availability/nines, RTO/RPO, four DR strategies, multi-AZ/multi-Region, resilience patterns, AWS Backup, chaos engineering, 25+ doc links), Lab (open-ended: goal/constraints format with deliverables including DR strategy document, AWS Backup configuration, and restore validation), Quiz (10 questions with 2 scenario-based), Resources (18 doc links, 3 whitepapers, 3 FAQs, 2 architecture refs). All Phase 4 modules now complete. |
| 2026-04-16 | Phase 4 Exam written: 25 questions covering Modules 13-16 at certification-level complexity. 72% scenario-based (exceeds 40% minimum), 8+ multi-service reasoning questions (WAF+Shield, KMS+S3+RDS, GuardDuty+CloudTrail, Config+SNS, X-Ray+Lambda+DynamoDB, Budgets+IAM, Route 53+ALB+RDS), 6 multi-select questions, 1 ordering question, 1 select-THREE question. Trade-off reasoning in DR strategy selection, cost optimization, and alerting configuration. Study guide organized by module. Phase 4 complete. |
| 2026-04-16 | Module 17 (Well-Architected Framework) built to full content-standards compliance. First Phase 5 module. README (7 objectives with Phase 5 Bloom's verbs, 8 concept sections covering all six pillars, trade-offs, Well-Architected Tool, lenses, 20+ doc links), Lab (open-ended: Well-Architected Tool review with deliverables including review report, improvement plan, and milestone), Quiz (10 questions with 2 scenario-based), Resources (11 doc links, 6 whitepapers, 1 FAQ, 3 architecture refs). |
| 2026-04-16 | Module 18 (Architecture Patterns) built to full content-standards compliance. README (8 objectives with Phase 5 Bloom's verbs, 7 concept sections covering monolith vs microservices, three-tier, serverless API, event-driven, static+API, data pipeline, CQRS, strangler fig, 20+ doc links), Lab (open-ended: design a complete architecture for a real-world scenario with deliverables including architecture diagram, document, and cost estimate), Quiz (10 questions with 2 scenario-based), Resources (11 doc links, 3 whitepapers, 3 FAQs, 3 architecture refs). |
| 2026-04-16 | Module 19 (Advanced Topics) built to full content-standards compliance. README (7 objectives with Phase 5 Bloom's verbs, 7 concept sections covering multi-account strategy/Organizations/Control Tower, CloudFront/OAC, ElastiCache/caching strategies, Step Functions, Athena, emerging services, ADRs, 25+ doc links), Lab (open-ended: choose 2 of 3 exercises: CloudFront+S3, Step Functions workflow, Athena queries, plus write an ADR), Quiz (10 questions with 2 scenario-based), Resources (13 doc links, 3 whitepapers, 4 FAQs, 3 architecture refs). All Phase 5 modules except capstone now complete. |
| 2026-04-16 | Module 20 (Capstone Project) built to full content-standards compliance. README (6 objectives with Phase 5 Bloom's verbs, project requirements with functional/technical specs, 5 suggested project ideas, evaluation rubric, 3-week timeline, Well-Architected self-assessment guide, 30+ inline AWS doc links, Instructor Notes, Key Takeaways), Resources (26 doc links, 4 whitepapers, 5 FAQs, 4 architecture refs). No lab or quiz (by design). All 20 modules now complete. |
| 2026-04-16 | Phase 5 Exam written: 25 questions covering Modules 17-19 at certification-level complexity. 80% scenario-based (exceeds 40% minimum), 10+ multi-service reasoning questions, 5 multi-select questions, 1 ordering question, 1 select-THREE question. Topics: Well-Architected pillars and trade-offs, architecture pattern selection, monolith vs microservices, event-driven design, CloudFront/OAC, ElastiCache caching strategies, Step Functions vs SQS+Lambda, Athena optimization, multi-account/SCPs, strangler fig migration, CQRS evaluation, ADRs. Study guide organized by module. Phase 5 and entire curriculum now complete. |
| 2026-04-16 | Hook added: "Generate Module Slides" (userTriggered) generates Marp-compatible Markdown slide decks from module READMEs. Slides can be converted to PowerPoint with `npx @marp-team/marp-cli slides.md --pptx`. |
| 2026-04-16 | Spec created: `.kiro/specs/module-slide-decks/requirements.md` with 15 EARS-pattern requirements for generating instructor-ready slide decks from module READMEs. Covers Marp compatibility, cognitive load management (max 5 bullets, 80 words, 12 lines of code per slide), active learning (3+ engagement slides per module), speaker notes, progressive complexity alignment, accessibility, and source fidelity. |
| 2026-04-16 | All 20 module slide decks generated (`slides.md` in each module folder). Marp-compatible Markdown with 16:9 aspect ratio. Each deck includes: title slide, learning objectives, prerequisites/agenda, transition slides, content slides (max 5 bullets), 3+ engagement slides aligned to Bloom's level, tables and code examples, speaker notes with timing and facilitation tips, key takeaways, lab preview, and closing slide. No em dashes. Convertible to PowerPoint via `npx @marp-team/marp-cli slides.md --pptx`. |
| 2026-04-16 | Created `scripts/md_to_pptx.py` using python-pptx to export all 20 slide decks to editable PowerPoint files (`slides.pptx` in each module folder). Unlike Marp's image-based export, these files contain native editable text, styled tables, code blocks with dark backgrounds, and speaker notes. Run `python3 scripts/md_to_pptx.py --all` to regenerate. |
