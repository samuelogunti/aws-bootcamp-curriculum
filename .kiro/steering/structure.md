# Project Structure

AWS Bootcamp curriculum — 20 modules from cloud fundamentals to capstone project.

```
.
├── README.md                          # Bootcamp overview, curriculum map, prerequisites
├── modules/
│   ├── 01-cloud-fundamentals/
│   │   ├── README.md                  # Lesson content with doc links
│   │   ├── lab/
│   │   │   └── lab-01-*.md            # Hands-on exercise(s)
│   │   ├── quiz.md                    # Knowledge check with answer key
│   │   └── resources.md               # Official AWS doc references
│   ├── 02-iam-and-security/
│   ├── 03-networking-basics/
│   ├── 04-compute-ec2/
│   ├── 05-storage-s3/
│   ├── 06-databases-rds-dynamodb/
│   ├── 07-load-balancing-and-dns/
│   ├── 08-messaging-and-integration/
│   ├── 09-serverless-lambda/
│   ├── 10-containers-ecs/
│   ├── 11-infrastructure-as-code/
│   ├── 12-cicd-pipelines/
│   ├── 13-security-in-depth/
│   ├── 14-monitoring-and-observability/
│   ├── 15-cost-optimization/
│   ├── 16-reliability-and-disaster-recovery/
│   ├── 17-well-architected-framework/
│   ├── 18-architecture-patterns/
│   ├── 19-advanced-topics/
│   └── 20-capstone-project/
└── .kiro/
    └── steering/
        ├── content-standards.md       # Content quality and sourcing rules
        ├── product.md                 # Project overview and audience
        ├── structure.md               # This file
        └── tech.md                    # Tech stack and tools
```

## Per-Module File Requirements

Every module folder MUST contain:

| File | Purpose |
|------|---------|
| `README.md` | Lesson content: objectives, concepts with doc links, instructor notes, takeaways |
| `lab/*.md` | Step-by-step hands-on exercises with validation and cleanup |
| `quiz.md` | 8–12 questions with answer key (scenario-based from Module 09+) |
| `resources.md` | All referenced AWS documentation, whitepapers, FAQs, architecture refs |

See `content-standards.md` for detailed formatting and quality requirements.

## Phase Exam Requirements

At the end of each curriculum phase, a comprehensive exam file is required:

| File | Location | Purpose |
|------|----------|---------|
| `phase-1-exam.md` | `modules/` | Cumulative exam for Modules 01–03 |
| `phase-2-exam.md` | `modules/` | Cumulative exam for Modules 04–08 |
| `phase-3-exam.md` | `modules/` | Cumulative exam for Modules 09–12 |
| `phase-4-exam.md` | `modules/` | Cumulative exam for Modules 13–16 |
| `phase-5-exam.md` | `modules/` | Cumulative exam for Modules 17–19 (Module 20 is capstone, no exam) |

Each exam: 20–30 questions, certification-level complexity, 60–90 min duration, 70% passing score.
