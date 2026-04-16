# Content Standards for AWS Bootcamp

This document defines the quality standards, structure, and sourcing requirements for all bootcamp content. Every module MUST follow these standards.

---

## Source Requirements

### Official Sources Only
- All technical claims, service descriptions, and feature explanations MUST be sourced from official AWS documentation at `docs.aws.amazon.com`
- Every module MUST include direct links to the relevant AWS documentation pages
- Do NOT use blog posts, Medium articles, or third-party tutorials as primary sources
- Acceptable official sources:
  - AWS Documentation: `https://docs.aws.amazon.com/`
  - AWS Whitepapers: `https://aws.amazon.com/whitepapers/`
  - AWS Well-Architected Framework: `https://docs.aws.amazon.com/wellarchitected/`
  - AWS Architecture Center: `https://aws.amazon.com/architecture/`
  - AWS Pricing pages: `https://aws.amazon.com/pricing/`
  - AWS FAQs: `https://aws.amazon.com/faqs/`
  - AWS Blogs (official): `https://aws.amazon.com/blogs/` — acceptable as supplementary, not primary

### Citation Format
- Inline links for every key concept: `[concept](https://docs.aws.amazon.com/...)`
- Each module's `resources.md` must list all referenced documentation pages
- When quoting or paraphrasing AWS docs, link directly to the specific page and section
- Use the AWS documentation MCP tools (`mcp_aws_documentation_search_documentation`, `mcp_aws_documentation_read_documentation`, `mcp_aws_documentation_read_sections`) to find and verify correct URLs before including them

---

## Module Structure

Every module folder MUST contain these four files:

### 1. `README.md` — Lesson Content

Required sections in this order:

```markdown
# Module XX: Title

## Learning Objectives
(Bloom's Taxonomy verbs — see Educational Standards below)

## Prerequisites
(What the student must have completed before this module)

## Concepts
(Detailed explanations of each topic, with inline doc links)

### Topic 1: Title
(Explanation, diagrams, examples, links to official docs)

### Topic 2: Title
...

## Instructor Notes
(Teaching tips, common student questions, timing suggestions)

## Key Takeaways
(3–5 bullet summary of the most important points)
```

### 2. `lab/` folder — Hands-On Exercises

Each lab file follows this format:

```markdown
# Lab XX: Title

## Objective
(One sentence: what the student will build or accomplish)

## Architecture Diagram
(Text description or image reference of what they're building)

## Prerequisites
(AWS services, prior labs, or tools required)

## Duration
(Estimated time in minutes)

## Instructions

### Step 1: Title
(Numbered sub-steps with exact console paths or CLI commands)
(Include expected output or screenshots where helpful)

### Step 2: Title
...

## Validation
(How to verify the lab was completed successfully — specific checks)

## Cleanup
(Exact steps to delete all resources created to avoid charges)

## Challenge (Optional)
(Stretch goal for advanced students)
```

### 3. `quiz.md` — Knowledge Check

```markdown
# Module XX: Quiz

(8–12 questions per module)
(Mix of question types: multiple choice, true/false, short answer, scenario-based)
(Scenario-based questions required for Modules 09+)

---

<details>
<summary>Answer Key</summary>
(Answers with brief explanations and doc links for further reading)
</details>
```

### 4. `resources.md` — Official References

```markdown
# Module XX: Resources

## Official Documentation
(Direct links to every AWS doc page referenced in the lesson)

## AWS Whitepapers
(Relevant whitepapers, if any)

## AWS FAQs
(Service-specific FAQ pages)

## AWS Architecture References
(Relevant reference architectures from the Architecture Center)
```

---

## Educational Standards

### Bloom's Taxonomy for Learning Objectives
Every module's learning objectives MUST use measurable verbs from Bloom's Taxonomy, appropriate to the module's phase:

**Phase 1 — Foundations (Modules 01–03): Remember & Understand**
- Verbs: define, describe, explain, identify, list, summarize, distinguish
- Example: "Explain the shared responsibility model"

**Phase 2 — Core Services (Modules 04–08): Understand & Apply**
- Verbs: demonstrate, configure, implement, use, deploy, set up
- Example: "Configure an Application Load Balancer with health checks"

**Phase 3 — Building Applications (Modules 09–12): Apply & Analyze**
- Verbs: build, construct, integrate, troubleshoot, compare, differentiate
- Example: "Build a serverless API using API Gateway, Lambda, and DynamoDB"

**Phase 4 — Production Readiness (Modules 13–16): Analyze & Evaluate**
- Verbs: analyze, assess, evaluate, optimize, recommend, justify
- Example: "Evaluate a workload's security posture using AWS security services"

**Phase 5 — Architecting (Modules 17–20): Evaluate & Create**
- Verbs: design, architect, propose, critique, defend, create
- Example: "Design a multi-tier architecture that meets specific availability requirements"

### Progressive Complexity
- Each module MUST build on knowledge from previous modules
- Labs in later modules should reference and extend infrastructure from earlier labs
- Introduce new concepts by connecting them to what students already know
- Modules 01–08: guided step-by-step labs with exact instructions
- Modules 09–14: labs with some steps left for the student to figure out
- Modules 15–19: labs that describe the goal and constraints, student designs the solution
- Module 20: open-ended capstone with requirements only

### Assessment Design
- Quizzes must test comprehension, not just recall
- Include at least 2 scenario-based questions per quiz starting from Module 09
- Scenario questions should present a real-world situation and ask students to choose or justify an AWS approach
- Wrong answer choices in multiple-choice should be plausible (common misconceptions)

---

## Writing Style

### Language and Tone
- Professional and educational throughout. Write with authority and clarity, as a subject-matter expert teaching practitioners.
- NEVER use em dashes (—). Use commas, periods, colons, semicolons, or parentheses instead.
  - Bad: "S3 is object storage — virtually unlimited and highly durable"
  - Good: "S3 is object storage. It is virtually unlimited and highly durable."
  - Good: "S3 is object storage: virtually unlimited and highly durable."
- Use "you" to address the student directly.
- Use active voice: "Create a bucket" not "A bucket should be created."
- Be concise. Remove filler words and unnecessary qualifiers.
- Avoid casual slang, humor, or colloquialisms. Keep the register consistent with university-level technical instruction.
- Do not use exclamation points in lesson content.

### Technical Writing
- Define acronyms on first use: "Virtual Private Cloud (VPC)"
- Use consistent terminology. Match AWS official naming (e.g., "security group" not "firewall rule")
- Use fenced code blocks with language tags for all CLI commands and code:
  ```bash
  aws s3 ls
  ```
- Specify the AWS Region when it matters (default to `us-east-1` for labs)
- Include expected output for CLI commands where it aids understanding

### Formatting
- Use heading hierarchy consistently: `#` for module title, `##` for sections, `###` for topics
- Use tables for comparisons (e.g., SQS vs SNS, RDS vs DynamoDB)
- Use callout blocks for warnings and tips:
  ```markdown
  > **Warning:** This will incur charges beyond Free Tier.

  > **Tip:** You can use CloudShell instead of installing the CLI locally.
  ```
- Use checklists in labs for validation steps

---

## Content Quality Checklist

Before considering any module complete, verify:

- [ ] All learning objectives use Bloom's Taxonomy verbs appropriate to the phase
- [ ] Every technical concept links to official AWS documentation
- [ ] Lab has clear step-by-step instructions with expected outcomes
- [ ] Lab includes a cleanup section to avoid unexpected charges
- [ ] Quiz has 8–12 questions with an answer key
- [ ] Resources page lists all referenced AWS doc pages
- [ ] No third-party sources used as primary references
- [ ] Code examples use fenced blocks with language tags
- [ ] Consistent terminology matching AWS official naming
- [ ] Prerequisites section references specific prior modules
- [ ] Instructor notes included with timing and teaching tips
- [ ] No em dashes (—) used anywhere in the content
