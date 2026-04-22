# AWS Bootcamp: From Novice to Architect

A comprehensive, hands-on AWS bootcamp curriculum designed to take students from zero cloud experience to confidently designing and building production-grade architectures.

## Prerequisites

- Basic programming knowledge (any language). Students without this background should complete the [IT Fundamentals: Pre-Bootcamp Primer](it-fundamentals/README.md) first.
- A computer with a web browser
- An AWS account (Free Tier eligible)
- Git installed ([git-scm.com](https://git-scm.com/))
- Docker Desktop installed (needed for Module 10)
- Python 3 installed ([python.org](https://www.python.org/downloads/))

## IT Fundamentals: Pre-Bootcamp Primer (Optional)

For students with no prior IT or programming experience, the [IT Fundamentals primer](it-fundamentals/README.md) covers foundational topics in 6 self-paced modules:

| Module | Topics | Estimated Time |
|--------|--------|---------------|
| [01: Computers and OS](it-fundamentals/modules/01-computers-and-operating-systems/README.md) | Hardware, bits/bytes, operating systems, Linux, file systems | 18-28 min |
| [02: Command Line](it-fundamentals/modules/02-command-line/README.md) | Navigation, file operations, searching | 21-29 min |
| [03: Networking and Internet](it-fundamentals/modules/03-networking-and-internet/README.md) | TCP/IP, IP addresses, DNS, ports, HTTP, status codes | 28-37 min |
| [04: APIs and Programming](it-fundamentals/modules/04-apis-and-programming/README.md) | REST APIs, JSON, Python basics (variables, functions, loops) | 30-41 min |
| [05: Version Control with Git](it-fundamentals/modules/05-version-control-git/README.md) | Git concepts, init, add, commit, push, pull, branch | 21-29 min |
| [06: Security Fundamentals](it-fundamentals/modules/06-security-fundamentals/README.md) | Authentication, authorization, encryption, least privilege | 13-21 min |

Total self-study time: 2 to 3 hours. Skip this section if you already have basic programming and command-line experience.

## Curriculum Structure

| Phase | Modules | Focus |
|-------|---------|-------|
| 1: Cloud Foundations | 01-03 | Cloud concepts, IAM, VPC |
| 2: Core Services | 04-08 | Compute, storage, databases, load balancing, messaging |
| 3: Building Applications | 09-12 | Serverless, containers, IaC, CI/CD |
| 4: Production Readiness | 13-16 | Security, monitoring, cost optimization, reliability |
| 5: Architecting | 17-20 | Well-Architected, architecture patterns, advanced topics, capstone |

Each phase ends with a cumulative exam (25 questions, 70% passing score).

## What Each Module Contains

| File | Purpose |
|------|---------|
| `README.md` | Lesson content with learning objectives, concepts, instructor notes, and key takeaways |
| `lab/*.md` | Step-by-step hands-on exercises with validation and cleanup |
| `quiz.md` | 8-12 knowledge check questions with answer key |
| `resources.md` | Official AWS documentation, whitepapers, FAQs, and architecture references |
| `slides.md` | Marp-compatible slide deck for instructor-led delivery |
| `slides.pptx` | Editable PowerPoint export of the slide deck |

## Additional Materials

| Material | Location | Description |
|----------|----------|-------------|
| Instructor Guide | [INSTRUCTOR-GUIDE.md](INSTRUCTOR-GUIDE.md) | 8-week delivery plan with session timing, self-study expectations, and assessment schedule |
| Phase Exams | `modules/phase-N-exam.md` | 5 cumulative exams (one per phase), 25 questions each |
| Architecture Diagrams | `generated-diagrams/` | AWS architecture diagrams for each module's lab (PNG) |
| Slide Export Script | `scripts/md_to_pptx.py` | Python script to generate editable PowerPoint files from Marp slides |

## Delivery Format

The curriculum supports an 8-week blended delivery model:

- 1 instructor-led session per week (3 hours)
- 8-12 hours of self-study per week (reading, labs, quizzes)
- 24 hours instructor-led + ~80 hours self-study = ~104 hours total

See the [Instructor Guide](INSTRUCTOR-GUIDE.md) for the complete week-by-week schedule.

## License

[CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/). Others can use, share, and adapt the curriculum with attribution. Commercial use requires explicit permission.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
