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

- Use Git for basic version control: init, add, commit, push, pull, and branch
- Describe how Git supports collaboration through remotes and branching

<!-- Speaker notes: Two objectives for this module at the Remember and Understand levels of Bloom's Taxonomy. Git is the foundation for infrastructure-as-code and CI/CD pipelines in the bootcamp. Approximately 1 minute on this slide. -->

---

## Prerequisites and Agenda

**Prerequisites:** Terminal skills (Module 02), Git installed ([git-scm.com](https://git-scm.com/))

**Agenda:**
1. Why Git matters for cloud
2. Core Git concepts
3. Essential Git commands (local workflow)
4. Remote operations (push, pull, clone)
5. Branching basics

<!-- Speaker notes: Module 02 is recommended for terminal skills. Git must be installed before the lab. Approximately 1 minute. -->

---

# Version Control with Git

<!-- Speaker notes: Transition slide. Git is a version control system that tracks changes to files over time. It lets you save snapshots of your work, collaborate with others, and revert to previous versions. -->

---

## Why Git Matters for Cloud

- Infrastructure-as-code templates (CloudFormation, SAM, CDK) are stored in Git repositories
- CI/CD pipelines pull code from Git to build and deploy applications automatically
- Git provides an audit trail: who changed what, when, and why
- Every professional development team uses Git

> Without Git, there is no CI/CD, no infrastructure-as-code, and no collaborative development.

<!-- Speaker notes: Emphasize that Git is not just for application code. CloudFormation templates, SAM templates, and CDK projects are all stored in Git. In Module 12 (CI/CD), pipelines automatically deploy when code is pushed to a Git repository. Approximately 3 minutes. -->

---

## Core Git Concepts

| Concept | Description |
|---------|-------------|
| Repository (repo) | A directory tracked by Git, containing files and their history |
| Commit | A snapshot of your files at a point in time, with a descriptive message |
| Branch | A parallel line of development; `main` is the default branch |
| Remote | A copy of the repo on a server (GitHub, GitLab, CodeCommit) |
| Clone | Download a remote repository to your local machine |
| Push | Upload your local commits to the remote |
| Pull | Download new commits from the remote to your local machine |

<!-- Speaker notes: Walk through each concept. The conveyor belt analogy works well: git add places items on the belt, git commit packages them, git push ships them to the remote. Approximately 3 minutes. -->

---

## Git Setup (One-Time)

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

- These settings identify you in commit history
- Run these commands once on each computer you use
- Your name and email appear in every commit you make

<!-- Speaker notes: Students will do this in the lab. The email does not need to match a GitHub account for local use. Approximately 1 minute. -->

---

## Local Git Workflow

```bash
git init my-project        # Create a new repository
cd my-project
git status                 # Check which files have changed

git add index.html         # Stage a specific file
git add .                  # Stage all changed files

git commit -m "Add homepage"  # Save a snapshot with a message

git log --oneline          # View commit history
```

- `git add` stages changes (tells Git what to include in the next snapshot)
- `git commit` saves the snapshot with a descriptive message
- `git status` is your best friend for understanding what Git sees

<!-- Speaker notes: Walk through the workflow: edit files, git add, git commit. In the lab, students will practice this exact sequence. Remind them that git status shows modified, staged, and untracked files. Approximately 3 minutes. -->

---

## Remote Operations

```bash
# Connect to a remote repository
git remote add origin https://github.com/you/my-project.git

# Upload commits to the remote
git push origin main

# Download new commits from the remote
git pull origin main

# Download an existing repository
git clone https://github.com/you/my-project.git
```

- `git push` uploads your local commits to the server
- `git pull` downloads new commits from the server
- `git clone` downloads an entire repository for the first time

<!-- Speaker notes: In the bootcamp, students will push CloudFormation templates and application code to GitHub or CodeCommit. CI/CD pipelines in Module 12 automatically pull from these repositories. Approximately 2 minutes. -->

---

## Branching Basics

```bash
# Create and switch to a new branch
git checkout -b feature/add-login

# Make changes, add, commit on the branch
git add .
git commit -m "Add login page"

# Switch back to main
git checkout main
```

- Branches let you work on features without affecting the main code
- `main` is the default branch (production-ready code)
- Feature branches are merged back into `main` when complete

<!-- Speaker notes: Branching is essential for CI/CD. In Module 12, students will see that pipelines trigger when code is pushed to specific branches. Approximately 2 minutes. -->

---

## Bootcamp Connection

> **Bootcamp connection:** Understanding Git prepares you for CI/CD pipelines in Module 12: CI/CD Pipelines, where pipelines automatically pull code from Git repositories to build and deploy applications.

- Module 11 (IaC): CloudFormation and CDK templates stored in Git
- Module 12 (CI/CD): Pipelines triggered by Git push events
- Module 20 (Capstone): Your entire project lives in a Git repository

<!-- Speaker notes: Git is used in every module from Module 11 onward. The capstone project requires a Git repository with proper commit history. Approximately 1 minute. -->

---

## Discussion: Why Is It Important to Write Descriptive Commit Messages?

Consider a project with hundreds of commits over several months.

**Why does it matter what you write in your commit messages? What makes a good commit message?**

<!-- Speaker notes: Expected answers: descriptive messages help understand what changed when reviewing history, make code review easier, help when debugging (find the commit that introduced a bug), and serve as documentation. A good commit message is short (under 72 characters), uses imperative mood ("Add feature" not "Added feature"), and explains what and why. Give students 2 to 3 minutes to discuss. Approximately 4 minutes total. -->

---

## Instructor Notes: Common Questions

**Q: Do I need a GitHub account?**
Not for this module or the lab (Git works locally). A GitHub account is useful for the bootcamp. Create a free account at github.com.

**Q: What is the difference between git add and git commit?**
`git add` stages changes (places items on the conveyor belt). `git commit` saves the snapshot (presses the button to package them).

<!-- Speaker notes: The conveyor belt analogy is effective for explaining the staging area. Approximately 2 minutes. -->

---

## Key Takeaways

- Git tracks changes to files over time and is the foundation for IaC and CI/CD
- Core local workflow: `git init`, `git add`, `git commit`, `git log`
- Remote workflow: `git push` (upload), `git pull` (download), `git clone` (first download)
- Branches let you work on features without affecting main
- Always write descriptive commit messages
- Every professional development team and every bootcamp module from 11 onward uses Git

<!-- Speaker notes: Six key takeaways covering all major Git concepts. Approximately 1 minute. -->

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

<!-- Speaker notes: Students need Git installed before starting. If students see "On branch master" instead of "On branch main," that is fine and depends on their Git version. Take 2 to 3 minutes for questions before transitioning to the lab. -->
