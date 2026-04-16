# Design Document: AWS Bootcamp Curriculum

## Overview

This design specifies how to build out all 20 modules of the "AWS Bootcamp: From Novice to Architect" curriculum to full content-standards compliance. The project is a Markdown-based educational content repository with no application code. The deliverable is a set of structured Markdown files (README, labs, quizzes, resources, phase exams) that an instructor can use to deliver a 10 to 12 week bootcamp without additional content preparation.

The curriculum follows a progressive complexity model across five phases, starting with cloud fundamentals and ending with a capstone architecture project. Every piece of content is sourced exclusively from official AWS documentation. Learning objectives use Bloom's Taxonomy verbs matched to each phase's cognitive level. Labs progress from fully guided (Phase 1/2) to open-ended (Phase 5).

The build process is sequential: Module 01 is upgraded first as the reference template, then each subsequent module is built in numerical order. After all modules in a phase are complete, a phase exam is created. The progress tracker is updated after every module completion.

### Key Design Decisions

1. **Templates over automation.** Since this is a content repository (not code), quality is enforced through detailed templates and a manual checklist, not automated linting or CI pipelines.
2. **Module 01 as reference template.** The first module is built to full compliance and serves as the concrete example for all subsequent modules. This reduces ambiguity in the content standards.
3. **Phase exams are cumulative.** Each exam tests across all modules in its phase, requiring multi-service reasoning. This mirrors AWS certification exam style and reinforces cross-topic integration.
4. **No em dashes anywhere.** The writing style explicitly forbids em dashes. Use commas, periods, colons, semicolons, or parentheses instead. This is enforced via the content quality checklist.
5. **Official AWS documentation MCP tools for link verification.** Before including any AWS documentation URL, the content author uses the AWS documentation search and read tools to verify the URL is correct and the content matches the claim.

---

## Architecture

The project architecture is a flat file structure organized by module number. There is no build system, no dependencies, and no runtime. The "architecture" is the file and folder layout, the content templates, and the process for producing compliant content.

### High-Level Structure

```
.
├── README.md                              # Bootcamp overview, curriculum map, prerequisites
├── modules/
│   ├── 01-cloud-fundamentals/
│   │   ├── README.md                      # Lesson content
│   │   ├── lab/
│   │   │   └── lab-01-aws-account-setup.md
│   │   ├── quiz.md                        # Knowledge check
│   │   └── resources.md                   # Official AWS references
│   ├── 02-iam-and-security/
│   │   ├── README.md
│   │   ├── lab/
│   │   │   └── lab-02-*.md
│   │   ├── quiz.md
│   │   └── resources.md
│   ├── ...                                # Modules 03 through 19 follow the same pattern
│   ├── 20-capstone-project/
│   │   ├── README.md                      # Project requirements, rubric, timeline
│   │   └── resources.md                   # No lab/ or quiz.md (by design)
│   ├── phase-1-exam.md                    # Cumulative exam: Modules 01-03
│   ├── phase-2-exam.md                    # Cumulative exam: Modules 04-08
│   ├── phase-3-exam.md                    # Cumulative exam: Modules 09-12
│   ├── phase-4-exam.md                    # Cumulative exam: Modules 13-16
│   └── phase-5-exam.md                    # Cumulative exam: Modules 17-19
└── .kiro/
    └── steering/
        ├── content-standards.md
        ├── product.md
        ├── structure.md
        ├── progress.md
        └── tech.md
```

### Module-to-Phase Mapping

| Phase | Name | Modules | Bloom's Level | Lab Guidance |
|-------|------|---------|---------------|--------------|
| 1 | Cloud Foundations | 01, 02, 03 | Remember and Understand | Fully guided |
| 2 | Core Services | 04, 05, 06, 07, 08 | Understand and Apply | Fully guided |
| 3 | Building Applications | 09, 10, 11, 12 | Apply and Analyze | Semi-guided |
| 4 | Production Readiness | 13, 14, 15, 16 | Analyze and Evaluate | Semi-guided (13-14), Open-ended (15-16) |
| 5 | Architecting | 17, 18, 19, 20 | Evaluate and Create | Open-ended (17-19), Capstone (20) |

### File Count Summary

| File Type | Count | Notes |
|-----------|-------|-------|
| Module READMEs | 20 | One per module |
| Lab folders | 19 | Module 20 has no lab |
| Quiz files | 19 | Module 20 has no quiz |
| Resources files | 20 | All modules including capstone |
| Phase exams | 5 | One per phase |
| **Total content files** | **83** | Excluding steering and config files |

---

## Components and Interfaces

Since this is a content repository, "components" are the file types and "interfaces" are the structural contracts (templates) each file must satisfy. Each component has a defined template, and the interface between components is cross-referencing (prerequisites reference prior modules, resources list all URLs used in README and lab, etc.).

### Component 1: Module README (Lesson Content)

The README is the primary instructional document for each module. It is what the instructor delivers during the lecture portion.

**Template:**

```markdown
# Module XX: Title

## Learning Objectives

By the end of this module, you will be able to:

- [Bloom's verb] [measurable outcome]
- [Bloom's verb] [measurable outcome]
- [Bloom's verb] [measurable outcome]
- [Bloom's verb] [measurable outcome]
(4 to 8 objectives total)

## Prerequisites

- Completion of [Module NN: Name](../NN-folder-name/README.md)
- Completion of [Module NN: Name](../NN-folder-name/README.md)
- (For Module 01 only: basic programming knowledge, AWS account, web browser)

## Concepts

### Topic 1: Full Name (ACRONYM)

[Detailed explanation of the concept. Use inline links to official AWS documentation
for every key term: [service name](https://docs.aws.amazon.com/...). Use tables for
service comparisons. Use callout blocks for warnings and tips.]

> **Warning:** This will incur charges beyond Free Tier.

> **Tip:** You can use CloudShell instead of installing the CLI locally.

```bash
aws example-command --option value
```

Expected output:

```
{
    "key": "value"
}
```

### Topic 2: Title

[Continue with the same pattern for each major topic.]

## Instructor Notes

**Estimated lecture time:** NN minutes

**Common student questions:**
- Q: [Frequent question]?
  A: [Concise answer with doc link if relevant.]
- Q: [Frequent question]?
  A: [Concise answer.]
- Q: [Frequent question]?
  A: [Concise answer.]

**Teaching tips:**
- [Tip for explaining a difficult concept]
- [Where to pause for a demo or question break]
- [Common misconception to address proactively]

## Key Takeaways

- [Concise, actionable statement]
- [Concise, actionable statement]
- [Concise, actionable statement]
(3 to 5 bullet points)
```

**Bloom's Verb Reference by Phase:**

| Phase | Modules | Allowed Verbs |
|-------|---------|---------------|
| 1 | 01-03 | define, describe, explain, identify, list, summarize, distinguish |
| 2 | 04-08 | demonstrate, configure, implement, use, deploy, set up |
| 3 | 09-12 | build, construct, integrate, troubleshoot, compare, differentiate |
| 4 | 13-16 | analyze, assess, evaluate, optimize, recommend, justify |
| 5 | 17-20 | design, architect, propose, critique, defend, create |

### Component 2: Lab Exercise

Labs are hands-on exercises in the `lab/` subfolder. Each module has at least one lab file named `lab-XX-descriptive-name.md`. The guidance level varies by phase.

**Template (Fully Guided, Phases 1-2):**

```markdown
# Lab XX: Descriptive Title

## Objective

[Single sentence: what the student will build or accomplish.]

## Architecture Diagram

[Text description of the architecture being built. Describe the AWS services involved,
how they connect, and the data flow. Reference a diagram image if one exists.]

Example:
```
Student's browser --> AWS Management Console --> IAM service
                                              --> S3 bucket (private)
                                              --> CloudWatch (billing alarm)
```

## Prerequisites

- Completed [Module NN: Name](../NN-folder-name/README.md)
- AWS account with IAM admin user (from Lab 01)
- AWS CLI installed and configured (or use CloudShell)

## Duration

NN minutes

## Instructions

### Step 1: Title

1. Navigate to the AWS Management Console.
2. In the search bar, type "ServiceName" and select it.
3. Click "Create [resource]".
4. Configure the following settings:
   - Setting A: `value`
   - Setting B: `value`
5. Click "Create".

**Expected result:** You see a success message with the resource ARN.

### Step 2: Title

1. Open a terminal (or CloudShell).
2. Run the following command:

```bash
aws service-name action --parameter value --region us-east-1
```

Expected output:

```json
{
    "ResourceId": "abc-123",
    "Status": "ACTIVE"
}
```

3. Verify the output matches.

(Continue with additional steps as needed.)

## Validation

Confirm the following:

- [ ] [Specific check: resource exists, output matches, behavior observed]
- [ ] [Specific check]
- [ ] [Specific check]

## Cleanup

Delete all resources created in this lab to avoid charges:

1. Navigate to [Service] in the console.
2. Select [resource name] and click "Delete".
3. Confirm deletion.
4. (Repeat for each resource created.)

> **Warning:** Failing to clean up may result in unexpected charges.

## Challenge (Optional)

[Stretch goal that extends the lab. Must use only concepts and services covered
up to and including this module.]
```

**Variations by Phase:**

For **semi-guided labs (Phases 3, early Phase 4: Modules 09-14)**, replace some Step sections with goal descriptions:

```markdown
### Step 3: Configure the Event Source

Configure your Lambda function to trigger when a new object is uploaded to the S3 bucket
you created in Step 1.

> **Hint:** Look at the S3 event notification settings or the Lambda trigger configuration
> in the console. You need the `s3:ObjectCreated:*` event type.

(The student determines the exact console path or CLI command.)
```

For **open-ended labs (late Phase 4 and Phase 5: Modules 15-19)**, replace the Instructions section with a goal and constraints format:

```markdown
## Goal

Design and implement a cost optimization strategy for the multi-tier application
built in previous modules. Your solution should reduce monthly costs by at least 20%
without degrading availability below 99.9%.

## Constraints

- You must use at least two different cost optimization techniques.
- All changes must be defined in infrastructure as code.
- You must document the before/after cost estimate using the AWS Pricing Calculator.
- Availability must remain at 99.9% or higher.

## Reference Links

- [AWS Cost Optimization Pillar](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html)
- [AWS Pricing Calculator](https://calculator.aws/)

## Deliverables

- Updated IaC templates
- Cost comparison document (before vs after)
- Brief justification for each optimization decision
```

### Component 3: Quiz (Knowledge Check)

Quizzes assess comprehension after each module. Question types and complexity scale with the phase.

**Template:**

```markdown
# Module XX: Quiz

1. [Question text]

   A) [Option A]
   B) [Option B]
   C) [Option C]
   D) [Option D]

2. True or False: [Statement].

3. [Short answer question.]

4. [Scenario-based question for Modules 09+]

   Your company runs a web application on EC2 instances behind an Application Load
   Balancer. Traffic spikes unpredictably during marketing campaigns. Which approach
   best handles these traffic spikes while minimizing cost?

   A) [Plausible but incorrect option]
   B) [Correct option]
   C) [Plausible but incorrect option]
   D) [Plausible but incorrect option]

(8 to 12 questions total. At least 2 scenario-based for Modules 09+.)

---

<details>
<summary>Answer Key</summary>

1. **B) [Correct answer]**
   [Brief explanation of why B is correct and why other options are wrong.]
   Further reading: [Relevant AWS doc page](https://docs.aws.amazon.com/...)

2. **True/False.**
   [Explanation.]
   Further reading: [Relevant AWS doc page](https://docs.aws.amazon.com/...)

3. **[Answer]**
   [Explanation.]
   Further reading: [Relevant AWS doc page](https://docs.aws.amazon.com/...)

4. **B) [Correct answer]**
   [Explanation of why B is correct. Explain why A, C, and D are plausible but wrong.]
   Further reading: [Relevant AWS doc page](https://docs.aws.amazon.com/...)

</details>
```

**Quiz Complexity by Phase:**

| Phase | Modules | Question Focus | Scenario Questions Required |
|-------|---------|----------------|----------------------------|
| 1 | 01-03 | Recall and comprehension | Not required |
| 2 | 04-08 | Comprehension and application | Not required |
| 3 | 09-12 | Application and analysis | At least 2 per quiz |
| 4 | 13-16 | Analysis and evaluation | At least 2 per quiz |
| 5 | 17-19 | Evaluation and design judgment | At least 2 per quiz |

### Component 4: Resources Page

The resources page aggregates all official AWS links used in the module. It serves as a reference list for students who want to explore further.

**Template:**

```markdown
# Module XX: Resources

## Official Documentation

- [Service/Concept Name](https://docs.aws.amazon.com/...)
- [Service/Concept Name](https://docs.aws.amazon.com/...)
(Every AWS doc page referenced in the README and lab.)

## AWS Whitepapers

- [Whitepaper Title](https://aws.amazon.com/whitepapers/...)
(Or: "No whitepapers specific to this module.")

## AWS FAQs

- [Service Name FAQ](https://aws.amazon.com/service/faqs/)
(Service-specific FAQ pages for each AWS service covered.)

## AWS Architecture References

- [Reference Architecture Title](https://aws.amazon.com/architecture/...)
(Or: "No specific architecture references for this module.")
```

**Allowed Source Domains:**

| Domain | Use |
|--------|-----|
| `docs.aws.amazon.com` | Primary source for all technical content |
| `aws.amazon.com/whitepapers/` | Whitepapers section |
| `docs.aws.amazon.com/wellarchitected/` | Well-Architected Framework content |
| `aws.amazon.com/architecture/` | Architecture references |
| `aws.amazon.com/pricing/` | Pricing information |
| `aws.amazon.com/faqs/` | Service FAQs |
| `aws.amazon.com/blogs/` | Supplementary only, never primary |

### Component 5: Phase Exam

Phase exams are cumulative assessments at the end of each phase. They test cross-module integration at AWS certification-level difficulty.

**Template:**

```markdown
# Phase N Exam: Phase Name

**Modules covered:** Module XX through Module YY
**Estimated duration:** 60 to 90 minutes
**Passing score:** 70% (NN out of MM questions)
**Question types:** Multiple choice (single correct), multiple choice (multiple correct),
scenario-based, ordering/sequencing

---

1. [Multiple choice, single correct]

   A company is migrating its on-premises application to AWS. The application requires
   [specific requirements]. Which combination of services best meets these requirements?

   A) [Plausible distractor]
   B) [Correct answer]
   C) [Plausible distractor]
   D) [Plausible distractor]

2. (Select TWO.) [Multiple correct]

   A solutions architect is designing a highly available web application. Which two
   configurations improve availability? (Select TWO.)

   A) [Option]
   B) [Option]
   C) [Option]
   D) [Option]
   E) [Option]

3. [Scenario-based with trade-off reasoning]

   A startup needs to minimize costs while maintaining 99.9% availability for their
   customer-facing API. They currently use [architecture description]. The CTO asks
   you to recommend changes. Which approach best balances cost and availability?

   A) [Option emphasizing cost at expense of availability]
   B) [Correct balanced option]
   C) [Option emphasizing availability at high cost]
   D) [Plausible but architecturally flawed option]

4. [Ordering/sequencing]

   Place the following steps in the correct order for [process]:

   A) [Step]
   B) [Step]
   C) [Step]
   D) [Step]

(20 to 30 questions total. At least 40% scenario-based.)

---

<details>
<summary>Answer Key</summary>

1. **B) [Correct answer]**
   [Why B is correct. Why A is wrong (common misconception). Why C is wrong.
   Why D is wrong.]
   Reference: [AWS doc link](https://docs.aws.amazon.com/...)

2. **B, D**
   [Why B and D are correct. Why A, C, E are wrong.]
   Reference: [AWS doc link](https://docs.aws.amazon.com/...)

3. **B) [Correct answer]**
   [Explain the trade-off reasoning. Why the balanced approach is best.]
   Reference: [AWS doc link](https://docs.aws.amazon.com/...)

4. **C, A, D, B**
   [Explain the correct sequence and why.]
   Reference: [AWS doc link](https://docs.aws.amazon.com/...)

</details>

---

## Study Guide

If you scored below 70%, review the following topics organized by module:

### Module XX: Title
- [Key topic to review]
- [Key topic to review]
- Reference: [AWS doc link](https://docs.aws.amazon.com/...)

### Module YY: Title
- [Key topic to review]
- Reference: [AWS doc link](https://docs.aws.amazon.com/...)
```


**Phase Exam Complexity Scaling:**

| Phase | Exam Focus | Bloom's Level | Example Question Style |
|-------|-----------|---------------|----------------------|
| 1 | Recall, comprehension, distinguishing concepts | Remember and Understand | "Which of the following best describes the shared responsibility model?" |
| 2 | Applying knowledge to configure and deploy, choosing the right service | Understand and Apply | "You need to store 500 TB of infrequently accessed data. Which S3 storage class is most cost-effective?" |
| 3 | Building solutions, troubleshooting failures, comparing approaches | Apply and Analyze | "Your Lambda function times out when processing large S3 uploads. Which two changes would resolve this?" |
| 4 | Evaluating architectures, recommending improvements, justifying decisions | Analyze and Evaluate | "A security audit reveals [finding]. Recommend the most effective remediation." |
| 5 | Designing complete architectures, defending decisions, critiquing proposals | Evaluate and Create | "Design a multi-region active-active architecture for [requirements]. Justify your service choices." |

---

## Data Models

This project has no runtime data models, databases, or APIs. The "data" is the Markdown content itself. The structural contracts (templates above) serve as the schema for each file type.

The key structural invariants are:

1. **Every module folder** (except Module 20) contains exactly four items: `README.md`, `lab/` folder with at least one lab file, `quiz.md`, and `resources.md`.
2. **Module 20** contains only `README.md` and `resources.md` (no lab or quiz by design).
3. **Phase exam files** are located directly in `modules/` (not inside a module folder).
4. **All links** point to official AWS domains listed in the allowed source domains table.
5. **Learning objectives** use only the Bloom's verbs allowed for that module's phase.
6. **Quizzes** contain 8 to 12 questions. Phase exams contain 20 to 30 questions.
7. **No em dashes** appear anywhere in any content file.

### Cross-Reference Model

Modules reference each other through relative Markdown links:

```
Prerequisites: [Module 02: IAM & Security](../02-iam-and-security/README.md)
```

Resources pages aggregate all AWS doc links used in the README and lab for that module. This creates a traceability chain:

```
README (inline doc links) ──┐
                             ├──> resources.md (aggregated list)
Lab (inline doc links)   ───┘
```

Phase exams reference modules in their header and study guide sections, but do not use relative links to module files (they are standalone assessment documents).

---

## Error Handling

Since this is a content repository, "errors" are content quality violations. The error handling strategy is a manual checklist applied before marking any module complete.

### Content Quality Checklist (per module)

This checklist is applied after writing each module. A module cannot be marked ✅ in the progress tracker until all items pass.

- [ ] All learning objectives use Bloom's Taxonomy verbs appropriate to the phase
- [ ] Every technical concept links to official AWS documentation
- [ ] All AWS doc links have been verified using the AWS documentation MCP tools
- [ ] Lab has clear step-by-step instructions (or goal/constraints for open-ended) with expected outcomes
- [ ] Lab includes Architecture Diagram, Validation, Cleanup, and Challenge sections
- [ ] Lab specifies AWS Region (default `us-east-1`)
- [ ] Quiz has 8 to 12 questions with an answer key in a collapsible `<details>` block
- [ ] Quiz answer key includes explanations and doc links for each answer
- [ ] Scenario-based questions included (at least 2) for Modules 09+
- [ ] Resources page has all four sections: Official Documentation, AWS Whitepapers, AWS FAQs, AWS Architecture References
- [ ] Resources page lists every AWS doc URL referenced in the README and lab
- [ ] No third-party sources used as primary references
- [ ] All links are from allowed official AWS domains
- [ ] Code examples use fenced blocks with language tags
- [ ] Consistent terminology matching AWS official naming
- [ ] Acronyms defined on first use
- [ ] Prerequisites section references specific prior modules (or external prereqs for Module 01)
- [ ] Instructor Notes included with timing, at least 3 common questions, and teaching tips
- [ ] Key Takeaways has 3 to 5 concise bullet points
- [ ] No em dashes used anywhere in the content
- [ ] No exclamation points in lesson content
- [ ] Active voice used throughout
- [ ] Student addressed as "you"
- [ ] Heading hierarchy is consistent (`#` title, `##` sections, `###` topics)

### Phase Exam Quality Checklist

- [ ] 20 to 30 questions covering all modules in the phase
- [ ] At least 40% of questions are scenario-based
- [ ] Includes multiple choice (single and multiple correct), scenario-based, and ordering questions
- [ ] Wrong answers are plausible distractors based on common misconceptions
- [ ] At least 2 questions require multi-service reasoning
- [ ] Scenario questions require trade-off consideration
- [ ] Answer key has detailed explanations with doc links for every answer
- [ ] Study guide section lists key review topics organized by module
- [ ] Header includes: title, modules covered, duration, passing score
- [ ] Question complexity matches the phase's Bloom's level
- [ ] All technical claims sourced from official AWS documentation
- [ ] No em dashes used anywhere

---

## Testing Strategy

### Why Property-Based Testing Does Not Apply

This project is a Markdown-based educational content repository. There is no application code, no functions, no parsers, no serializers, and no algorithms. The deliverables are static Markdown files that must conform to structural and content rules. Property-based testing requires code with inputs and outputs where universal properties can be verified across generated inputs. None of those conditions exist here.

The appropriate quality assurance strategy is:

1. **Template conformance review.** Each file is checked against its component template (defined in the Components and Interfaces section above) to verify all required sections are present and in the correct order.
2. **Content quality checklist.** The checklist in the Error Handling section is applied to every module before marking it complete.
3. **Link verification.** AWS documentation URLs are verified using the AWS documentation MCP tools (`mcp_aws_documentation_search_documentation`, `mcp_aws_documentation_read_documentation`) before inclusion.
4. **Cross-reference validation.** Prerequisites, resource aggregation, and inter-module links are manually verified for correctness.

### Review Process

Each module goes through this review sequence:

1. **Write.** Author the README, lab, quiz, and resources files using the templates.
2. **Verify links.** Use AWS documentation MCP tools to confirm every URL resolves to the correct page.
3. **Apply checklist.** Run through the content quality checklist. Fix any violations.
4. **Update tracker.** Mark the module ✅ in the progress tracker.
5. **Phase exam.** After all modules in a phase are complete, write the phase exam and apply the phase exam checklist.

### Specific Checks by File Type

| File Type | Key Checks |
|-----------|-----------|
| README | Bloom's verbs match phase, all concepts have doc links, prerequisites reference prior modules, instructor notes have 3+ questions |
| Lab | Architecture diagram present, validation checklist present, cleanup steps present, challenge section present, guidance level matches phase |
| Quiz | 8-12 questions, answer key in `<details>` block, doc links in answers, scenario questions for Modules 09+ |
| Resources | All four sections present, every README/lab doc link aggregated, all links from allowed domains |
| Phase Exam | 20-30 questions, 40%+ scenario-based, multi-service reasoning questions, study guide section, complexity matches phase Bloom's level |

---

## Appendix: Progressive Complexity Implementation

This section details exactly how progressive complexity is implemented across the five curriculum phases, covering learning objectives, lab guidance, quiz design, and cross-module dependencies.

### Phase 1: Cloud Foundations (Modules 01-03)

**Cognitive level:** Remember and Understand
**Lab style:** Fully guided with exact commands and expected outputs
**Quiz style:** Recall and comprehension (no scenario questions required)
**Dependencies:** Module 01 has no module prerequisites. Modules 02-03 reference prior modules.

| Module | Topic | Builds On |
|--------|-------|-----------|
| 01 | Cloud Fundamentals | None (entry point) |
| 02 | IAM and Security | Module 01 (AWS account, console navigation) |
| 03 | Networking Basics (VPC) | Module 02 (IAM roles, security groups concept) |

### Phase 2: Core Services (Modules 04-08)

**Cognitive level:** Understand and Apply
**Lab style:** Fully guided with exact commands and expected outputs
**Quiz style:** Comprehension and application (no scenario questions required)
**Dependencies:** All modules reference Phase 1 concepts (IAM, VPC, security groups).

| Module | Topic | Builds On |
|--------|-------|-----------|
| 04 | Compute (EC2) | Modules 02 (IAM roles), 03 (VPC, subnets, security groups) |
| 05 | Storage (S3) | Modules 02 (IAM policies, bucket policies), 04 (EC2 for access demos) |
| 06 | Databases (RDS, DynamoDB) | Modules 03 (VPC, subnets), 02 (IAM), 04 (EC2 for connectivity) |
| 07 | Load Balancing and DNS | Modules 03 (VPC), 04 (EC2 instances to balance) |
| 08 | Messaging (SQS, SNS, EventBridge) | Modules 02 (IAM), 04 (EC2), 05 (S3 events) |

### Phase 3: Building Applications (Modules 09-12)

**Cognitive level:** Apply and Analyze
**Lab style:** Semi-guided (some steps describe the goal, student determines approach)
**Quiz style:** Application and analysis (at least 2 scenario questions per quiz)
**Dependencies:** Reference and extend infrastructure from Phases 1 and 2.

| Module | Topic | Builds On |
|--------|-------|-----------|
| 09 | Serverless (Lambda) | Modules 02 (IAM roles), 05 (S3 triggers), 06 (DynamoDB), 08 (SQS/SNS triggers) |
| 10 | Containers (ECS) | Modules 03 (VPC), 04 (EC2/compute concepts), 07 (ALB) |
| 11 | Infrastructure as Code | All prior modules (codifies what was built manually) |
| 12 | CI/CD Pipelines | Module 11 (IaC templates to deploy), 09 (Lambda to deploy), 10 (ECS to deploy) |

### Phase 4: Production Readiness (Modules 13-16)

**Cognitive level:** Analyze and Evaluate
**Lab style:** Semi-guided for Modules 13-14, open-ended for Modules 15-16
**Quiz style:** Analysis and evaluation (at least 2 scenario questions per quiz)
**Dependencies:** Apply production concerns to architectures built in prior phases.

| Module | Topic | Builds On |
|--------|-------|-----------|
| 13 | Security in Depth | Module 02 (IAM foundations), all prior modules (securing existing infra) |
| 14 | Monitoring and Observability | All prior modules (monitoring existing applications) |
| 15 | Cost Optimization | All prior modules (optimizing existing architectures) |
| 16 | Reliability and DR | Modules 03 (multi-AZ), 06 (database backups), 07 (load balancing) |

### Phase 5: Architecting (Modules 17-20)

**Cognitive level:** Evaluate and Create
**Lab style:** Open-ended for Modules 17-19, capstone for Module 20
**Quiz style:** Evaluation and design judgment (at least 2 scenario questions per quiz, Modules 17-19 only)
**Dependencies:** Synthesize knowledge from all prior phases.

| Module | Topic | Builds On |
|--------|-------|-----------|
| 17 | Well-Architected Framework | All prior modules (review existing architectures) |
| 18 | Architecture Patterns | All prior modules (apply patterns to real designs) |
| 19 | Advanced Topics | All prior modules (multi-account, edge cases, emerging services) |
| 20 | Capstone Project | All modules (design and build from scratch) |

---

## Appendix: Sequential Build Process

### Step-by-Step Build Order

1. **Upgrade Module 01** to full content-standards compliance. This becomes the reference template.
   - Rewrite README with all required sections and Phase 1 Bloom's verbs
   - Rewrite lab with Architecture Diagram, Validation, Cleanup, Challenge
   - Expand quiz to 8-12 questions with answer key (explanations + doc links)
   - Expand resources to all four sections with verified AWS doc links
   - Apply content quality checklist
   - Update progress tracker: Module 01 = ✅

2. **Build Modules 02-03** sequentially, referencing Module 01 as the template.
   - Each module references prior modules in Prerequisites
   - Apply content quality checklist to each
   - Update progress tracker after each module

3. **Write Phase 1 Exam** after Modules 01-03 are all ✅.
   - 20-30 questions covering Modules 01-03
   - Focus on recall and comprehension (Phase 1 Bloom's level)
   - Apply phase exam quality checklist
   - Update progress tracker: Phase 1 Exam = ✅

4. **Build Modules 04-08** sequentially (Phase 2).
   - Reference Phase 1 concepts in prerequisites
   - Fully guided labs
   - Apply checklist, update tracker after each

5. **Write Phase 2 Exam** after Modules 04-08 are all ✅.

6. **Build Modules 09-12** sequentially (Phase 3).
   - Semi-guided labs
   - At least 2 scenario questions per quiz
   - Apply checklist, update tracker after each

7. **Write Phase 3 Exam** after Modules 09-12 are all ✅.

8. **Build Modules 13-16** sequentially (Phase 4).
   - Semi-guided labs for 13-14, open-ended for 15-16
   - At least 2 scenario questions per quiz
   - Apply checklist, update tracker after each

9. **Write Phase 4 Exam** after Modules 13-16 are all ✅.

10. **Build Modules 17-19** sequentially (Phase 5, excluding capstone).
    - Open-ended labs
    - At least 2 scenario questions per quiz
    - Apply checklist, update tracker after each

11. **Build Module 20** (Capstone).
    - README with project requirements, rubric, timeline
    - Resources file only (no lab or quiz)
    - Apply checklist, update tracker

12. **Write Phase 5 Exam** after Modules 17-19 are all ✅.
    - Covers Modules 17-19 only (Module 20 capstone is not examined)

### Progress Tracker Update Protocol

After completing each module or exam:

1. Change the file status symbols in the phase table (🔶/❌ to ✅).
2. Update the "Standards Met" column to "Yes".
3. Update the Summary section totals.
4. Add a Decision Log entry with the date and what was completed.

---

## Appendix: Writing Style Rules

These rules apply to all content files (READMEs, labs, quizzes, resources, phase exams).

### Mandatory Rules

1. **No em dashes.** Never use the em dash character. Use commas, periods, colons, semicolons, or parentheses instead.
2. **No exclamation points** in lesson content.
3. **Active voice.** "Create a bucket" not "A bucket should be created."
4. **Address the student as "you."** "You will configure..." not "The student will configure..."
5. **Define acronyms on first use.** "Virtual Private Cloud (VPC)" on first mention, then "VPC" thereafter.
6. **AWS official terminology.** Use "security group" not "firewall rule." Use "bucket" not "container" for S3.
7. **Fenced code blocks with language tags** for all CLI commands and code.
8. **Default region `us-east-1`** for all labs unless the topic requires a different region.
9. **Professional, educational tone.** University-level technical instruction. No casual slang, humor, or colloquialisms.
10. **Concise.** Remove filler words and unnecessary qualifiers.

### Formatting Rules

1. Heading hierarchy: `#` for module/exam title, `##` for sections, `###` for topics/steps.
2. Tables for service comparisons.
3. Callout blocks for warnings and tips: `> **Warning:**` and `> **Tip:**`.
4. Checklists in labs for validation steps.
5. Collapsible `<details>` blocks for answer keys.

### AWS Documentation Link Verification Process

Before including any AWS documentation URL in content:

1. Use `mcp_aws_documentation_search_documentation` to find the correct page for the topic.
2. Use `mcp_aws_documentation_read_documentation` or `mcp_aws_documentation_read_sections` to verify the page content matches the claim being made.
3. Use the exact URL returned by the tool (do not guess or construct URLs manually).
4. Add the verified URL to both the inline reference in the README/lab and the resources.md aggregated list.
