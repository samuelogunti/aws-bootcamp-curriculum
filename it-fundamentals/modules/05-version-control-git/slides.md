---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'IT Fundamentals: Module 05, Version Control with Git'
---

# IT Fundamentals Module 05: Version Control with Git

**Module 05 of 06**
Estimated lecture time: 10 to 15 minutes

<!-- Speaker notes: Welcome students to Module 05 of the IT Fundamentals primer. This module covers version control with Git. Git is the foundation for infrastructure-as-code and CI/CD in the bootcamp. Total lecture time is approximately 10 to 15 minutes. -->

---

## Learning Objectives

By the end of this module, you will be able to:

- Use Git for basic version control: init, add, commit, log
- Describe how Git supports collaboration through push, pull, and branches

<!-- Speaker notes: One primary objective for this module at the Remember and Understand levels of Bloom's Taxonomy. Git is the foundation for infrastructure-as-code and CI/CD pipelines in the bootcamp. Approximately 1 minute on this slide. -->

---

# Version Control with Git

<!-- Speaker notes: Transition slide. This section takes approximately 8 minutes across two slides. Git is the foundation for infrastructure-as-code and CI/CD in the bootcamp. -->

---

## Why Git Matters

- Infrastructure-as-code templates are stored in Git repositories
- CI/CD pipelines pull code from Git to build and deploy applications
- Git provides an audit trail: who changed what, when, and why

| Concept | Description |
|---------|-------------|
| Repository | A directory tracked by Git |
| Commit | A snapshot of your files at a point in time |
| Branch | A parallel line of development |
| Remote | A copy of the repo on a server (GitHub, CodeCommit) |

<!-- Speaker notes: Emphasize that Git is not just for application code. CloudFormation templates, SAM templates, and CDK projects are all stored in Git. In Module 12 (CI/CD), pipelines automatically deploy when code is pushed to a Git repository. Approximately 4 minutes. -->

---

## Core Git Commands

```bash
git init my-project        # Create a new repository
git status                 # Check which files have changed
git add .                  # Stage all changed files
git commit -m "Add homepage"  # Save a snapshot
git log --oneline          # View commit history
git push origin main       # Upload commits to remote
git pull origin main       # Download commits from remote
```

- `git add` stages changes; `git commit` saves them
- Always write descriptive commit messages

<!-- Speaker notes: Walk through the workflow: edit files, git add, git commit, git push. In the lab, students will practice this exact sequence. Remind them that git status is their best friend for understanding what Git sees. Approximately 4 minutes. -->

---

## Discussion: Why Is It Important to Write Descriptive Commit Messages?

Consider a project with hundreds of commits over several months.

**Why does it matter what you write in your commit messages? What makes a good commit message?**

<!-- Speaker notes: Expected answers include: descriptive messages help you understand what changed when reviewing history, they make it easier for teammates to review code, they help when debugging (you can find the commit that introduced a bug), and they serve as documentation. A good commit message is short (under 72 characters), uses imperative mood ("Add feature" not "Added feature"), and explains what and why. Give students 2 to 3 minutes to discuss. Approximately 4 minutes total. -->

---

## Key Takeaways

- Git tracks every change and is the foundation for CI/CD pipelines.
- The core workflow is: edit, `git add`, `git commit`, `git push`.
- Descriptive commit messages make collaboration and debugging easier.

<!-- Speaker notes: Three key takeaways for this module. Reinforce that Git is used for infrastructure-as-code, not just application code. Approximately 1 minute. -->

---

## Lab Preview and Questions

**Lab 00: Version Control with Git**

What you will do:
- Configure Git with your name and email
- Initialize a repository and make your first commits
- Edit a file, stage the change, and commit it
- View the commit history with `git log`

**Duration:** 10 to 12 minutes
**No cloud resources created. Everything runs on your local machine.**

Questions?

<!-- Speaker notes: Walk through the lab structure briefly. Remind students that the lab is fully guided with exact commands and expected output for every step. Students need Git installed before starting. If students see "On branch master" instead of "On branch main," that is fine and depends on their Git version. Take 2 to 3 minutes for questions before transitioning to the lab. -->
