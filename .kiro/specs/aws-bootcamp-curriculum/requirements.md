# Requirements Document

## Introduction

This specification defines the requirements for building out all 20 modules of the AWS Bootcamp curriculum ("From Novice to Architect") to full content-standards compliance. The project currently has 20 module folders with draft README outlines, but zero modules meet the defined content standards. Module 01 has draft lab, quiz, and resources files that need upgrading; Modules 02-20 are missing lab, quiz, and resources files entirely.

The goal is to systematically produce detailed, instructor-ready content for every module, including inline AWS documentation links, Bloom's Taxonomy learning objectives, hands-on labs with progressive complexity, knowledge-check quizzes, and comprehensive official resource lists, so that an instructor can deliver the full 10 to 12 week bootcamp without additional content preparation.

## Glossary

- **Module**: A single unit of the curriculum covering one topic area, consisting of a README.md, lab/ folder, quiz.md, and resources.md
- **Content_Standards**: The quality and structural rules defined in `.kiro/steering/content-standards.md` that every module must satisfy
- **Bloom_Verb**: A measurable action verb from Bloom's Taxonomy used in learning objectives, appropriate to the module's curriculum phase
- **Phase**: One of five progressive difficulty groupings in the curriculum (Foundations, Core Services, Building Applications, Production Readiness, Architecting)
- **README**: The primary lesson content file for a module, containing Learning Objectives, Prerequisites, Concepts, Instructor Notes, and Key Takeaways
- **Lab**: A hands-on exercise file in the `lab/` folder with Objective, Architecture Diagram, Prerequisites, Duration, step-by-step Instructions, Validation, Cleanup, and optional Challenge
- **Quiz**: A knowledge-check file with 8-12 questions and an answer key with explanations and doc links
- **Resources_Page**: A file listing all official AWS documentation, whitepapers, FAQs, and architecture references used in the module
- **Guided_Lab**: A lab with exact step-by-step instructions and expected outputs (Modules 01-08)
- **Semi_Guided_Lab**: A lab with some steps left for the student to figure out independently (Modules 09-14)
- **Open_Ended_Lab**: A lab that describes the goal and constraints, and the student designs the solution (Modules 15-19)
- **Capstone**: Module 20, an open-ended project with requirements only and no lab/quiz by design
- **Progress_Tracker**: The file `.kiro/steering/progress.md` that records completion status for every module
- **Official_Source**: AWS documentation from `docs.aws.amazon.com`, AWS Whitepapers, AWS FAQs, AWS Architecture Center, or AWS Pricing pages
- **Scenario_Question**: A quiz question presenting a real-world situation where the student must choose or justify an AWS approach
- **Phase_Exam**: A comprehensive exam file (`exam.md`) at the end of each curriculum phase, testing cumulative knowledge across all modules in that phase at certification-level complexity
- **Exam_Question**: A question in a Phase_Exam that tests deep understanding, multi-service reasoning, or architectural judgment, modeled after AWS certification exam difficulty

---

## Requirements

### Requirement 1: README Lesson Content Structure

**User Story:** As an instructor, I want every module README to follow a consistent, detailed structure with proper sections, so that I can deliver lessons without additional preparation.

#### Acceptance Criteria

1. THE README SHALL contain the following sections in this order: Learning Objectives, Prerequisites, Concepts (with subsections per topic), Instructor Notes, Key Takeaways.
2. WHEN a README is created or updated, THE README SHALL use heading hierarchy consistently: `#` for module title, `##` for sections, `###` for concept topics.
3. THE README SHALL define all acronyms on first use in the format "Full Name (ACRONYM)".
4. THE README SHALL use active voice and address the student directly using "you".
5. THE README SHALL use fenced code blocks with language tags for all CLI commands and code examples.

---

### Requirement 2: Bloom's Taxonomy Learning Objectives

**User Story:** As a curriculum designer, I want every module's learning objectives to use measurable Bloom's Taxonomy verbs appropriate to the module's phase, so that learning outcomes are assessable and progressively complex.

#### Acceptance Criteria

1. WHILE a module belongs to Phase 1 (Modules 01-03), THE README SHALL use only Remember and Understand verbs in learning objectives: define, describe, explain, identify, list, summarize, distinguish.
2. WHILE a module belongs to Phase 2 (Modules 04-08), THE README SHALL use only Understand and Apply verbs in learning objectives: demonstrate, configure, implement, use, deploy, set up.
3. WHILE a module belongs to Phase 3 (Modules 09-12), THE README SHALL use only Apply and Analyze verbs in learning objectives: build, construct, integrate, troubleshoot, compare, differentiate.
4. WHILE a module belongs to Phase 4 (Modules 13-16), THE README SHALL use only Analyze and Evaluate verbs in learning objectives: analyze, assess, evaluate, optimize, recommend, justify.
5. WHILE a module belongs to Phase 5 (Modules 17-20), THE README SHALL use only Evaluate and Create verbs in learning objectives: design, architect, propose, critique, defend, create.
6. THE README SHALL contain between 4 and 8 learning objectives per module.

---

### Requirement 3: Prerequisites and Progressive Dependency

**User Story:** As a student, I want each module to clearly state what I need to know before starting, so that I can follow the content without knowledge gaps.

#### Acceptance Criteria

1. THE README SHALL include a Prerequisites section listing specific prior modules the student must have completed.
2. WHEN a module introduces a concept that builds on a prior module, THE README SHALL reference the prior module by number and name in the Prerequisites section.
3. WHEN a module is Module 01, THE README SHALL list only external prerequisites (basic programming knowledge, AWS account, web browser) since there are no prior modules.
4. THE README SHALL connect new concepts to previously learned material in the Concepts section by referencing the relevant prior module.

---

### Requirement 4: Concepts Section with Official AWS Documentation Links

**User Story:** As a student, I want detailed concept explanations with direct links to official AWS documentation, so that I can deepen my understanding from authoritative sources.

#### Acceptance Criteria

1. THE README Concepts section SHALL contain a subsection (###) for each major topic in the module.
2. WHEN a key AWS concept, service, or feature is introduced, THE README SHALL include an inline link to the relevant official AWS documentation page.
3. THE README SHALL source all technical claims, service descriptions, and feature explanations from official AWS documentation at `docs.aws.amazon.com`.
4. THE README SHALL use consistent terminology matching AWS official naming conventions.
5. THE README SHALL use tables for service comparisons where two or more services are contrasted.
6. THE README SHALL use callout blocks (blockquotes with bold prefix) for warnings about charges and tips for shortcuts.

---

### Requirement 5: Instructor Notes

**User Story:** As an instructor, I want teaching tips, timing suggestions, and common student questions documented in each module, so that I can deliver effective lessons.

#### Acceptance Criteria

1. THE README SHALL include an Instructor Notes section with estimated timing for the lecture portion of the module.
2. THE README Instructor Notes SHALL list at least 3 common student questions or misconceptions for the module topic.
3. THE README Instructor Notes SHALL include teaching tips for explaining difficult concepts.
4. THE README Instructor Notes SHALL suggest where to pause for questions or demonstrations during the lecture.

---

### Requirement 6: Key Takeaways

**User Story:** As a student, I want a concise summary of the most important points at the end of each module, so that I can quickly review what I learned.

#### Acceptance Criteria

1. THE README SHALL include a Key Takeaways section with 3 to 5 bullet points summarizing the most important concepts from the module.
2. THE Key Takeaways SHALL be concise, actionable statements that a student can use as a quick reference.

---

### Requirement 7: Lab Exercise Structure

**User Story:** As a student, I want structured, hands-on lab exercises with clear instructions and validation steps, so that I can practice what I learned and confirm my work is correct.

#### Acceptance Criteria

1. WHEN a lab file is created, THE Lab SHALL contain the following sections in order: Objective, Architecture Diagram, Prerequisites, Duration, Instructions (with numbered steps), Validation, Cleanup, and optional Challenge.
2. THE Lab Objective SHALL be a single sentence describing what the student will build or accomplish.
3. THE Lab Architecture Diagram SHALL describe the architecture being built using a text description or image reference.
4. THE Lab Duration SHALL specify the estimated time in minutes.
5. THE Lab Instructions SHALL use numbered sub-steps with exact console navigation paths or CLI commands.
6. THE Lab Instructions SHALL include expected output for CLI commands where the output aids understanding.
7. THE Lab Validation SHALL provide specific checks the student can perform to verify successful completion.
8. THE Lab Cleanup SHALL provide exact steps to delete all resources created during the lab to avoid unexpected charges.
9. THE Lab SHALL specify the AWS Region to use, defaulting to `us-east-1`.

---

### Requirement 8: Progressive Lab Complexity

**User Story:** As a curriculum designer, I want labs to progress from fully guided to open-ended across the curriculum phases, so that students build increasing independence.

#### Acceptance Criteria

1. WHILE a module belongs to Phase 1 or Phase 2 (Modules 01-08), THE Lab SHALL provide guided step-by-step instructions with exact commands, console paths, and expected outputs for every step.
2. WHILE a module belongs to Phase 3 (Modules 09-12), THE Lab SHALL provide semi-guided instructions where some steps describe the goal and let the student determine the exact approach.
3. WHILE a module belongs to Phase 4 (Modules 13-14), THE Lab SHALL provide semi-guided instructions with increasing portions left for the student to figure out independently.
4. WHILE a module belongs to Phase 4 or Phase 5 (Modules 15-19), THE Lab SHALL describe the goal and constraints and let the student design the solution, providing only hints or reference links.
5. WHEN a module is Module 20 (Capstone), THE Capstone SHALL provide project requirements and evaluation criteria only, with no step-by-step lab instructions.

---

### Requirement 9: Lab Challenge Section

**User Story:** As an advanced student, I want optional stretch goals in each lab, so that I can push beyond the basics when I finish early.

#### Acceptance Criteria

1. THE Lab SHALL include an optional Challenge section after the Cleanup section.
2. THE Lab Challenge SHALL describe a stretch goal that extends the lab exercise with additional complexity or an alternative approach.
3. THE Lab Challenge SHALL be achievable using only concepts and services covered up to and including the current module.

---

### Requirement 10: Quiz Structure and Question Count

**User Story:** As an instructor, I want standardized quizzes with enough questions to assess comprehension, so that I can evaluate student understanding consistently.

#### Acceptance Criteria

1. THE Quiz SHALL contain between 8 and 12 questions per module.
2. THE Quiz SHALL include a mix of question types: multiple choice, true/false, and short answer.
3. THE Quiz SHALL include an answer key inside a collapsible `<details>` block.
4. THE Quiz answer key SHALL provide brief explanations for each answer.
5. THE Quiz answer key SHALL include links to relevant official AWS documentation for further reading.
6. WHEN a wrong answer choice is provided in a multiple-choice question, THE Quiz SHALL use plausible distractors based on common misconceptions.

---

### Requirement 11: Scenario-Based Quiz Questions

**User Story:** As a curriculum designer, I want scenario-based questions in advanced modules, so that students practice applying knowledge to real-world situations.

#### Acceptance Criteria

1. WHILE a module belongs to Phase 3, Phase 4, or Phase 5 (Modules 09-19), THE Quiz SHALL include at least 2 scenario-based questions.
2. THE scenario-based questions SHALL present a real-world situation and ask the student to choose or justify an AWS approach.
3. WHILE a module belongs to Phase 1 or Phase 2 (Modules 01-08), THE Quiz SHALL focus on comprehension and recall questions without requiring scenario-based questions.

---

### Requirement 12: Resources Page Structure

**User Story:** As a student, I want a comprehensive list of official AWS references for each module, so that I can explore topics in depth after the lesson.

#### Acceptance Criteria

1. THE Resources_Page SHALL contain the following sections: Official Documentation, AWS Whitepapers, AWS FAQs, and AWS Architecture References.
2. THE Resources_Page Official Documentation section SHALL include direct links to every AWS documentation page referenced in the module README and lab.
3. THE Resources_Page AWS Whitepapers section SHALL include links to relevant AWS whitepapers for the module topic, or state "No whitepapers specific to this module" if none apply.
4. THE Resources_Page AWS FAQs section SHALL include links to the service-specific FAQ pages for AWS services covered in the module.
5. THE Resources_Page AWS Architecture References section SHALL include links to relevant reference architectures from the AWS Architecture Center, or state "No specific architecture references for this module" if none apply.
6. THE Resources_Page SHALL source all links from official AWS domains only.

---

### Requirement 13: Official AWS Documentation Sourcing

**User Story:** As a curriculum designer, I want all content sourced exclusively from official AWS documentation, so that students learn from authoritative and up-to-date material.

#### Acceptance Criteria

1. THE Module content SHALL source all technical claims, service descriptions, and feature explanations from official AWS documentation.
2. THE Module content SHALL use inline links in the format `[concept](https://docs.aws.amazon.com/...)` for every key concept.
3. THE Module content SHALL accept links only from these official domains: `docs.aws.amazon.com`, `aws.amazon.com/whitepapers/`, `docs.aws.amazon.com/wellarchitected/`, `aws.amazon.com/architecture/`, `aws.amazon.com/pricing/`, `aws.amazon.com/faqs/`.
4. THE Module content SHALL treat official AWS blog posts (`aws.amazon.com/blogs/`) as supplementary sources only, not primary references.

---

### Requirement 14: Module 01 Upgrade to Reference Template

**User Story:** As a curriculum developer, I want Module 01 upgraded to full content-standards compliance first, so that it serves as the reference template for all subsequent modules.

#### Acceptance Criteria

1. WHEN Module 01 is built, THE Module_01 SHALL be the first module upgraded to full Content_Standards compliance.
2. THE Module_01 README SHALL be rewritten to include all required sections: Learning Objectives (with Phase 1 Bloom_Verbs), Prerequisites, Concepts (with inline AWS doc links), Instructor Notes, and Key Takeaways.
3. THE Module_01 Lab SHALL be rewritten to include all required sections: Objective, Architecture Diagram, Prerequisites, Duration, Instructions, Validation, Cleanup, and Challenge.
4. THE Module_01 Quiz SHALL be expanded to 8-12 questions with an answer key containing explanations and doc links.
5. THE Module_01 Resources_Page SHALL be expanded to include all four required sections: Official Documentation, AWS Whitepapers, AWS FAQs, and AWS Architecture References.

---

### Requirement 15: Phase 1 Modules (01-03): Cloud Foundations

**User Story:** As a student with no cloud experience, I want the first three modules to build my foundational understanding of cloud computing, IAM, and networking, so that I have the base knowledge for all subsequent modules.

#### Acceptance Criteria

1. THE Phase_1 modules SHALL cover: cloud computing fundamentals (Module 01), IAM and security foundations (Module 02), and networking basics with VPC (Module 03).
2. THE Phase_1 READMEs SHALL use Remember and Understand Bloom_Verbs exclusively.
3. THE Phase_1 Labs SHALL be fully guided with exact step-by-step instructions.
4. THE Phase_1 Quizzes SHALL focus on recall and comprehension questions.
5. THE Phase_1 modules SHALL assume no prior AWS or cloud experience from the student.

---

### Requirement 16: Phase 2 Modules (04-08): Core Services

**User Story:** As a student building on cloud foundations, I want to learn the core AWS services hands-on, so that I can provision and configure compute, storage, database, networking, and messaging resources.

#### Acceptance Criteria

1. THE Phase_2 modules SHALL cover: EC2 compute (Module 04), S3 storage (Module 05), RDS and DynamoDB databases (Module 06), load balancing and Route 53 DNS (Module 07), and SQS/SNS/EventBridge messaging (Module 08).
2. THE Phase_2 READMEs SHALL use Understand and Apply Bloom_Verbs exclusively.
3. THE Phase_2 Labs SHALL be fully guided with exact step-by-step instructions.
4. THE Phase_2 modules SHALL reference Phase 1 concepts (IAM roles, VPC, security groups) as prerequisites.

---

### Requirement 17: Phase 3 Modules (09-12): Building Applications

**User Story:** As a student ready to build real applications, I want to learn serverless, containers, infrastructure as code, and CI/CD, so that I can construct and deploy complete applications on AWS.

#### Acceptance Criteria

1. THE Phase_3 modules SHALL cover: Lambda serverless (Module 09), ECS containers (Module 10), CloudFormation/SAM/CDK infrastructure as code (Module 11), and CI/CD pipelines with CodePipeline (Module 12).
2. THE Phase_3 READMEs SHALL use Apply and Analyze Bloom_Verbs exclusively.
3. THE Phase_3 Labs SHALL be semi-guided, with some steps describing the goal and letting the student determine the approach.
4. THE Phase_3 Quizzes SHALL include at least 2 scenario-based questions each.
5. THE Phase_3 modules SHALL reference and extend infrastructure built in Phase 1 and Phase 2 labs.

---

### Requirement 18: Phase 4 Modules (13-16): Production Readiness

**User Story:** As a student preparing for production workloads, I want to learn security in depth, monitoring, cost optimization, and reliability, so that I can evaluate and harden AWS architectures.

#### Acceptance Criteria

1. THE Phase_4 modules SHALL cover: advanced security services (Module 13), monitoring and observability with CloudWatch and X-Ray (Module 14), cost optimization strategies (Module 15), and reliability and disaster recovery (Module 16).
2. THE Phase_4 READMEs SHALL use Analyze and Evaluate Bloom_Verbs exclusively.
3. WHILE a module is Module 13 or Module 14, THE Lab SHALL be semi-guided with increasing portions left for the student.
4. WHILE a module is Module 15 or Module 16, THE Lab SHALL describe the goal and constraints and let the student design the solution.
5. THE Phase_4 Quizzes SHALL include at least 2 scenario-based questions each.
6. THE Phase_4 modules SHALL reference architectures and services from all prior phases.

---

### Requirement 19: Phase 5 Modules (17-20): Architecting

**User Story:** As a student ready to design production-grade architectures, I want to learn the Well-Architected Framework, architecture patterns, and advanced topics, and complete a capstone project, so that I can independently design and defend AWS solutions.

#### Acceptance Criteria

1. THE Phase_5 modules SHALL cover: Well-Architected Framework (Module 17), architecture patterns (Module 18), advanced topics (Module 19), and capstone project (Module 20).
2. THE Phase_5 READMEs SHALL use Evaluate and Create Bloom_Verbs exclusively.
3. WHILE a module is Module 17, 18, or 19, THE Lab SHALL describe the goal and constraints and let the student design the solution.
4. WHEN a module is Module 20, THE Capstone SHALL provide project requirements, evaluation criteria, and a timeline only. No step-by-step lab, no quiz.
5. THE Phase_5 Quizzes (Modules 17-19) SHALL include at least 2 scenario-based questions each.
6. THE Phase_5 modules SHALL synthesize knowledge from all prior phases.

---

### Requirement 20: Module 20 Capstone Project

**User Story:** As a student completing the bootcamp, I want an open-ended capstone project with clear requirements and evaluation criteria, so that I can demonstrate mastery by designing and building a complete AWS application.

#### Acceptance Criteria

1. THE Capstone SHALL include: project requirements (functional and technical), deliverables list, suggested project ideas, evaluation criteria with weights, and a timeline.
2. THE Capstone technical requirements SHALL require infrastructure as code, CI/CD pipeline, multi-AZ deployment, monitoring, security best practices, and cost-conscious design.
3. THE Capstone SHALL include a resources.md file with links to official AWS documentation relevant to capstone-level architecture.
4. THE Capstone SHALL reference the Well-Architected Framework as a self-assessment tool for the student's architecture.

---

### Requirement 21: Sequential Module Build Order

**User Story:** As a curriculum developer, I want modules built sequentially starting from Module 01, so that each module can properly reference and build upon completed prior modules.

#### Acceptance Criteria

1. WHEN modules are built, THE build process SHALL complete Module 01 to full Content_Standards compliance before starting Module 02.
2. WHEN modules are built, THE build process SHALL complete each module in numerical order (01 through 20).
3. WHEN a module is completed, THE Progress_Tracker SHALL be updated to reflect the module's new status.

---

### Requirement 22: Progress Tracker Updates

**User Story:** As a curriculum developer, I want the progress tracker updated after each module is completed, so that I can monitor overall curriculum completion status.

#### Acceptance Criteria

1. WHEN a module reaches full Content_Standards compliance, THE Progress_Tracker SHALL update that module's status from 🔶 or ❌ to ✅ for each completed file (README, Lab, Quiz, Resources).
2. WHEN a module reaches full Content_Standards compliance, THE Progress_Tracker SHALL update the Standards Met column to "Yes".
3. THE Progress_Tracker SHALL update the Summary section totals after each module completion.

---

### Requirement 23: Content Quality Compliance

**User Story:** As a curriculum designer, I want every completed module to pass the content quality checklist, so that the bootcamp maintains consistent educational standards.

#### Acceptance Criteria

1. WHEN a module is marked complete, THE Module SHALL satisfy all items on the Content Quality Checklist defined in Content_Standards.
2. THE Module SHALL have all learning objectives using phase-appropriate Bloom_Verbs.
3. THE Module SHALL have every technical concept linked to official AWS documentation.
4. THE Module Lab SHALL have clear step-by-step instructions with expected outcomes.
5. THE Module Lab SHALL include a cleanup section to avoid unexpected charges.
6. THE Module Quiz SHALL have 8-12 questions with an answer key.
7. THE Module Resources_Page SHALL list all referenced AWS documentation pages.
8. THE Module SHALL use no third-party sources as primary references.
9. THE Module SHALL use consistent terminology matching AWS official naming.
10. THE Module SHALL have a Prerequisites section referencing specific prior modules.
11. THE Module SHALL have Instructor Notes with timing and teaching tips.

---

### Requirement 24: Phase Exam Structure

**User Story:** As an instructor, I want a comprehensive exam at the end of each curriculum phase that tests cumulative knowledge at AWS certification-level complexity, so that I can assess whether students are ready to advance to the next phase.

#### Acceptance Criteria

1. WHEN a phase is complete, THE Phase SHALL include an `exam.md` file located at `modules/phase-{N}-exam.md` (e.g., `modules/phase-1-exam.md`).
2. THE Phase_Exam SHALL contain 20-30 questions covering all modules in that phase.
3. THE Phase_Exam SHALL test cumulative knowledge. Questions may require combining concepts from multiple modules within the phase.
4. THE Phase_Exam SHALL include an answer key inside a collapsible `<details>` block with detailed explanations and official AWS documentation links for each answer.
5. THE Phase_Exam SHALL include a header section with: exam title, phase covered, modules covered, estimated duration (60-90 minutes), and passing score (70%).

---

### Requirement 25: Phase Exam Question Complexity

**User Story:** As a curriculum designer, I want phase exam questions to match AWS certification exam difficulty, so that students are prepared for real-world assessments and professional certifications.

#### Acceptance Criteria

1. THE Phase_Exam SHALL include the following question types: multiple choice (single correct), multiple choice (multiple correct, such as "select TWO" or "select THREE"), scenario-based, and ordering/sequencing questions.
2. THE Phase_Exam SHALL have at least 40% of questions be scenario-based, presenting a real-world situation requiring multi-step reasoning.
3. THE Phase_Exam wrong answer choices SHALL be plausible distractors based on common misconceptions, partial solutions, or services that are similar but inappropriate for the scenario.
4. THE Phase_Exam scenario questions SHALL require the student to consider trade-offs (cost vs performance, security vs convenience, availability vs complexity).
5. THE Phase_Exam SHALL include at least 2 questions that require reasoning across multiple AWS services (e.g., "which combination of services" or "what is the correct order of steps").

---

### Requirement 26: Phase Exam: Phase-Specific Complexity Scaling

**User Story:** As a curriculum designer, I want each phase exam to match the cognitive level of its phase, so that exam difficulty progresses alongside the curriculum.

#### Acceptance Criteria

1. WHEN the Phase_Exam is for Phase 1 (Cloud Foundations), THE Exam_Questions SHALL focus on recall, comprehension, and distinguishing between concepts, matching Remember and Understand Bloom levels.
2. WHEN the Phase_Exam is for Phase 2 (Core Services), THE Exam_Questions SHALL focus on applying knowledge to configure and deploy services, and choosing the right service for a given requirement, matching Understand and Apply Bloom levels.
3. WHEN the Phase_Exam is for Phase 3 (Building Applications), THE Exam_Questions SHALL focus on building solutions, troubleshooting failures, and comparing architectural approaches, matching Apply and Analyze Bloom levels.
4. WHEN the Phase_Exam is for Phase 4 (Production Readiness), THE Exam_Questions SHALL focus on evaluating architectures for security, cost, reliability, and recommending improvements, matching Analyze and Evaluate Bloom levels.
5. WHEN the Phase_Exam is for Phase 5 (Architecting), THE Exam_Questions SHALL focus on designing complete architectures, defending design decisions, and critiquing proposed solutions, matching Evaluate and Create Bloom levels.

---

### Requirement 27: Phase Exam Content and Sourcing

**User Story:** As a student, I want phase exam answers to reference official AWS documentation, so that I can verify answers and deepen my understanding of topics I got wrong.

#### Acceptance Criteria

1. THE Phase_Exam answer key SHALL provide a detailed explanation for each answer, including why the correct answer is right and why each distractor is wrong.
2. THE Phase_Exam answer key SHALL include at least one official AWS documentation link per answer explanation.
3. THE Phase_Exam SHALL source all technical claims and correct answers from official AWS documentation.
4. THE Phase_Exam SHALL include a "Study Guide" section at the end listing the key topics to review, organized by module, for students who did not pass.
