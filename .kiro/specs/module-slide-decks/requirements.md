# Requirements Document: Module Slide Decks

## Introduction

This specification defines the requirements for generating instructor-ready presentation slide decks for each of the 20 modules in the AWS Bootcamp curriculum. Each module already has a complete README with lesson content, learning objectives, concepts, instructor notes, and key takeaways. The slide decks transform this content into a visual presentation format that an instructor can deliver during the lecture portion of each module.

The slide decks are authored in Marp-compatible Markdown and can be exported to PowerPoint (.pptx), PDF, or HTML using the Marp CLI. The goal is to produce consistent, visually clean, pedagogically sound presentations that follow instructional design best practices: chunked content, visual hierarchy, progressive disclosure, and alignment with Bloom's Taxonomy learning objectives.

## Glossary

- **Slide_Deck**: A Marp-compatible Markdown file (`slides.md`) in a module folder that defines the presentation for that module's lecture
- **Marp**: An open-source Markdown-to-presentation tool that converts Markdown files with `---` slide separators into PowerPoint, PDF, or HTML presentations
- **Slide**: A single page in the presentation, separated by `---` in the Markdown source
- **Content_Slide**: A slide that teaches a concept, shows a comparison, or presents a code example
- **Engagement_Slide**: A slide that prompts student interaction (discussion question, poll, think-pair-share, or hands-on check)
- **Transition_Slide**: A slide that signals a shift between major topics (section divider)
- **Speaker_Notes**: Instructor-facing notes embedded in the Marp Markdown using HTML comments (`<!-- notes -->`) that are visible in presenter view but not on the projected slide
- **Chunking**: The instructional design practice of breaking content into small, digestible pieces (one idea per slide)
- **Progressive_Disclosure**: Revealing information incrementally rather than showing everything at once
- **Cognitive_Load**: The amount of mental effort required to process information on a slide; lower is better for learning retention
- **Source_README**: The module's `README.md` file that contains the lesson content from which the slide deck is generated
- **Source_Lab**: The module's lab file that contains the hands-on exercise the slides preview

---

## Requirements

### Requirement 1: Slide Deck File Structure and Marp Compatibility

**User Story:** As an instructor, I want each module's slide deck to be a single Marp-compatible Markdown file that I can export to PowerPoint or PDF, so that I can present using my preferred tool without additional conversion steps.

#### Acceptance Criteria

1. THE Slide_Deck SHALL be a single Markdown file named `slides.md` located in the module folder (for example, `modules/01-cloud-fundamentals/slides.md`).
2. THE Slide_Deck SHALL include Marp front matter at the top of the file with the following properties: `marp: true`, `theme: default`, `paginate: true`, `size: 16:9`.
3. THE Slide_Deck front matter SHALL include a `header` property with the text `AWS Bootcamp` and a `footer` property with the text `Module XX: Title` (matching the module number and name).
4. THE Slide_Deck SHALL use `---` as the slide separator between every slide.
5. THE Slide_Deck SHALL be exportable to PowerPoint (.pptx) using the command `npx @marp-team/marp-cli slides.md --pptx` without errors.

---

### Requirement 2: Slide Deck Structure and Flow

**User Story:** As an instructor, I want every slide deck to follow a consistent structure that mirrors the lesson flow, so that I can deliver a coherent lecture without rearranging slides.

#### Acceptance Criteria

1. THE Slide_Deck SHALL contain the following slide sections in this order: Title Slide, Objectives Slide, Prerequisites/Agenda Slide, Concept Slides (one or more per major topic), Engagement Slides (interspersed), Instructor Notes Slide, Key Takeaways Slide, Lab Preview Slide, Questions/Closing Slide.
2. THE Title Slide SHALL display the module number, module title, the curriculum phase name, and the estimated lecture duration.
3. THE Objectives Slide SHALL list all learning objectives from the Source_README, using the exact Bloom's Taxonomy verbs from the README.
4. THE Prerequisites/Agenda Slide SHALL list the prerequisite modules and provide a numbered agenda of the major topics to be covered.
5. THE Key Takeaways Slide SHALL reproduce the 3 to 5 bullet points from the Source_README Key Takeaways section verbatim.
6. THE Lab Preview Slide SHALL summarize the lab objective, the key services used, and the estimated duration from the Source_Lab.
7. THE Questions/Closing Slide SHALL display "Questions?" as the heading and include a reminder to review the module's `resources.md` for further reading.

---

### Requirement 3: Content Slide Design (Cognitive Load Management)

**User Story:** As a curriculum designer, I want each content slide to present a single idea with minimal text, so that students can absorb the information without cognitive overload.

#### Acceptance Criteria

1. WHEN a content slide presents bullet points, THE Slide SHALL contain no more than 5 bullet points.
2. WHEN a content slide presents bullet points, EACH bullet point SHALL be a concise phrase or single sentence (no more than 15 words per bullet).
3. THE Slide_Deck SHALL NOT contain slides with more than 80 words of body text (excluding code blocks, tables, and speaker notes).
4. WHEN a concept from the Source_README requires more than 5 bullet points or 80 words, THE Slide_Deck SHALL split the content across multiple slides with a clear progression.
5. THE Slide_Deck SHALL use tables for service comparisons (carried over from the Source_README) where the table fits within the slide dimensions (no more than 5 rows and 4 columns per slide).
6. WHEN a table from the Source_README exceeds 5 rows, THE Slide_Deck SHALL split the table across multiple slides or simplify it to the most important rows.
7. THE Slide_Deck SHALL use one heading level per slide: `#` for section titles and `##` for content slide titles. No `###` or deeper headings on slides.

---

### Requirement 4: Code Example Slides

**User Story:** As an instructor, I want code examples on slides to be short, syntax-highlighted, and focused on the key concept, so that students can read and understand them during the lecture.

#### Acceptance Criteria

1. WHEN a slide contains a code example, THE code block SHALL use fenced Markdown syntax with a language tag (for example, ```bash, ```yaml, ```python, ```json).
2. WHEN a slide contains a code example, THE code block SHALL contain no more than 12 lines of code. Longer examples from the Source_README SHALL be truncated with a comment indicating the omitted portion (for example, `# ... (see README for full example)`).
3. WHEN a slide contains a code example, THE slide SHALL include a brief title or annotation (1 sentence) explaining what the code does. The code should not appear without context.
4. THE Slide_Deck SHALL NOT place more than one code block on a single slide.
5. WHEN a code example includes expected output, THE output SHALL appear on the same slide as the command (if both fit within the 12-line limit) or on the immediately following slide labeled "Expected Output."

---

### Requirement 5: Visual Elements and Diagrams

**User Story:** As a curriculum designer, I want slides to use visual elements (diagrams, icons, tables, callout boxes) to reinforce concepts, so that visual learners can engage with the material alongside text-based learners.

#### Acceptance Criteria

1. WHEN the Source_README contains an architecture diagram (text-based), THE Slide_Deck SHALL include the diagram on a dedicated slide using a fenced code block (preserving the ASCII art) or a descriptive bullet list of the components and their relationships.
2. THE Slide_Deck SHALL include at least one comparison table per module (carried over from the Source_README) to reinforce service selection decisions.
3. WHEN the Source_README contains a warning or tip callout, THE Slide_Deck SHALL reproduce it using bold text or a blockquote on the relevant slide.
4. THE Slide_Deck SHALL use consistent visual markers for slide types: bold text for key terms on first use, blockquotes for warnings/tips, and tables for comparisons.

---

### Requirement 6: Engagement Slides (Active Learning)

**User Story:** As an instructor, I want engagement slides interspersed throughout the deck that prompt student interaction, so that the lecture is not a passive experience and students actively process the material.

#### Acceptance Criteria

1. THE Slide_Deck SHALL include at least 3 Engagement_Slides per module, distributed across the lecture (not clustered at the end).
2. EACH Engagement_Slide SHALL be clearly labeled with a visual marker (for example, a heading prefix like "Discussion:" or "Think About It:" or "Quick Check:").
3. THE Engagement_Slides SHALL use one of the following formats: a discussion question related to the preceding concept, a "which service would you choose?" scenario, a true/false quick check, or a "what happens if...?" failure scenario.
4. WHEN an Engagement_Slide poses a question, THE immediately following slide (or speaker notes on the same slide) SHALL provide the answer or discussion points for the instructor.
5. THE Engagement_Slides SHALL align with the module's Bloom's Taxonomy level: recall questions for Phase 1, application questions for Phase 2-3, evaluation/design questions for Phase 4-5.

---

### Requirement 7: Speaker Notes for Instructor Guidance

**User Story:** As an instructor, I want speaker notes on key slides that provide talking points, timing cues, and additional context, so that I can deliver the lecture effectively even if I am not the original content author.

#### Acceptance Criteria

1. THE Slide_Deck SHALL include speaker notes on at least the following slides: Title Slide (welcome script and timing overview), each Transition_Slide (timing for the upcoming section), each Engagement_Slide (expected answers and facilitation tips), and the Lab Preview Slide (setup instructions or prerequisites to remind students about).
2. THE speaker notes SHALL be embedded using Marp's HTML comment syntax: `<!-- Speaker notes go here -->`.
3. THE speaker notes on the Title Slide SHALL include the total estimated lecture time and a suggested breakdown by section.
4. THE speaker notes on Engagement_Slides SHALL include the expected answer, common wrong answers, and a brief explanation the instructor can use to guide the discussion.
5. THE speaker notes SHALL NOT repeat the slide content verbatim. They SHALL provide additional context, transitions, or facilitation guidance.

---

### Requirement 8: Transition Slides (Section Dividers)

**User Story:** As an instructor, I want clear visual dividers between major topic sections, so that students understand when the lecture is shifting to a new concept area.

#### Acceptance Criteria

1. THE Slide_Deck SHALL include a Transition_Slide before each major topic section (corresponding to each `###` heading in the Source_README Concepts section).
2. THE Transition_Slide SHALL display only the section title (for example, "AWS Lambda Execution Model") centered on the slide, with no bullet points or body text.
3. THE Transition_Slide speaker notes SHALL include a timing estimate for the upcoming section and a one-sentence summary of what the section covers.

---

### Requirement 9: Progressive Complexity Alignment

**User Story:** As a curriculum designer, I want the slide deck complexity and engagement style to match the module's curriculum phase, so that the presentation approach evolves alongside the content difficulty.

#### Acceptance Criteria

1. WHILE a module belongs to Phase 1 (Modules 01-03), THE Slide_Deck SHALL emphasize definitions, diagrams, and recall-based engagement questions. Slides should introduce one concept at a time with clear definitions before moving to the next.
2. WHILE a module belongs to Phase 2 (Modules 04-08), THE Slide_Deck SHALL include step-by-step walkthrough slides that mirror the guided lab instructions, and engagement questions should ask "how would you configure...?" or "which service would you use for...?"
3. WHILE a module belongs to Phase 3 (Modules 09-12), THE Slide_Deck SHALL include comparison slides (service A vs. service B) and engagement questions that present troubleshooting scenarios or architectural trade-offs.
4. WHILE a module belongs to Phase 4 (Modules 13-16), THE Slide_Deck SHALL include evaluation-focused slides that present an architecture and ask students to identify weaknesses, and engagement questions should ask "how would you improve...?" or "what is the risk of...?"
5. WHILE a module belongs to Phase 5 (Modules 17-20), THE Slide_Deck SHALL include design-focused slides that present requirements and ask students to propose architectures, and engagement questions should ask "design a solution for..." or "defend your choice of..."

---

### Requirement 10: Slide Count and Timing Guidelines

**User Story:** As an instructor, I want the slide count to match the estimated lecture duration, so that I can pace the lecture without rushing or running out of content.

#### Acceptance Criteria

1. THE Slide_Deck SHALL target approximately 1 slide per 1.5 to 2 minutes of estimated lecture time. For a 90-minute lecture, the deck should contain 45 to 60 slides.
2. THE Slide_Deck SHALL include the estimated lecture duration (from the Source_README Instructor Notes) in the Title Slide speaker notes.
3. WHEN the Source_README Instructor Notes suggest pause points for demos or questions, THE Slide_Deck SHALL include a corresponding Engagement_Slide or a slide with a speaker note indicating "Pause for demo" or "Pause for questions."

---

### Requirement 11: Writing Style and Formatting Consistency

**User Story:** As a curriculum designer, I want all slide decks to follow the same writing style and formatting rules as the rest of the curriculum, so that the bootcamp materials feel cohesive.

#### Acceptance Criteria

1. THE Slide_Deck SHALL NOT contain em dashes or en dashes anywhere in the content. Use commas, periods, colons, semicolons, or parentheses instead.
2. THE Slide_Deck SHALL NOT contain exclamation points in slide content (speaker notes are exempt).
3. THE Slide_Deck SHALL use active voice and address the student as "you" where applicable.
4. THE Slide_Deck SHALL define acronyms on first use in the format "Full Name (ACRONYM)" on the slide where the acronym first appears.
5. THE Slide_Deck SHALL use consistent terminology matching AWS official naming conventions (matching the Source_README).
6. THE Slide_Deck SHALL use sentence case for slide titles (capitalize only the first word and proper nouns), not title case.

---

### Requirement 12: Source Fidelity and Content Accuracy

**User Story:** As a curriculum designer, I want the slide content to be derived exclusively from the module's README and lab files, so that the slides do not introduce information that contradicts or diverges from the lesson content.

#### Acceptance Criteria

1. THE Slide_Deck SHALL derive all technical content, service descriptions, and feature explanations from the Source_README. No new technical claims shall be introduced that are not present in the README.
2. THE Slide_Deck learning objectives SHALL match the Source_README learning objectives exactly (same verbs, same outcomes).
3. THE Slide_Deck key takeaways SHALL match the Source_README Key Takeaways section exactly.
4. WHEN the Slide_Deck simplifies a concept for slide brevity, THE speaker notes SHALL reference the Source_README section where the full explanation can be found.
5. THE Slide_Deck SHALL NOT include links to external documentation (links are for the README and resources page, not for projected slides). AWS documentation references SHALL appear only in speaker notes if needed.

---

### Requirement 13: Accessibility and Readability

**User Story:** As a curriculum designer, I want the slides to be readable from the back of a classroom and accessible to students with visual impairments who may view the slides on their own devices, so that no student is excluded from the learning experience.

#### Acceptance Criteria

1. THE Slide_Deck SHALL use a minimum effective font size of 24pt for body text and 32pt for slide titles (enforced by using Marp's default theme, which meets these minimums at 16:9 aspect ratio).
2. THE Slide_Deck SHALL NOT use color as the sole means of conveying information. Any color-coded element (for example, a status indicator) SHALL also include a text label.
3. THE Slide_Deck SHALL use high-contrast text (dark text on light background) for all content slides.
4. THE Slide_Deck code blocks SHALL use a monospace font with syntax highlighting provided by Marp's default code block rendering.
5. THE Slide_Deck tables SHALL include header rows that clearly label each column.

---

### Requirement 14: Sequential Build Order

**User Story:** As a curriculum developer, I want slide decks built in module order starting from Module 01, so that each deck can reference the visual style and structure established by earlier decks.

#### Acceptance Criteria

1. WHEN slide decks are built, THE build process SHALL complete Module 01's slide deck first as the reference template for all subsequent decks.
2. WHEN slide decks are built, THE build process SHALL complete each module's slide deck in numerical order (01 through 20).
3. WHEN a slide deck is completed, THE Progress_Tracker SHALL be updated to reflect the slide deck's completion status.

---

### Requirement 15: Module 20 Capstone Slide Deck

**User Story:** As an instructor, I want the Module 20 slide deck to focus on project kickoff, requirements walkthrough, and evaluation criteria, so that students understand what is expected for the capstone project.

#### Acceptance Criteria

1. THE Module 20 Slide_Deck SHALL focus on project requirements, deliverables, evaluation criteria, timeline, and suggested project ideas (not lesson concepts, since Module 20 has no traditional Concepts section).
2. THE Module 20 Slide_Deck SHALL include a slide for each suggested project idea with a brief description and the key AWS services involved.
3. THE Module 20 Slide_Deck SHALL include a slide showing the evaluation rubric (criteria and weights) from the Source_README.
4. THE Module 20 Slide_Deck SHALL include a slide showing the 3-week timeline with milestones.
5. THE Module 20 Slide_Deck SHALL include an Engagement_Slide where students discuss which project idea they are considering and why.
