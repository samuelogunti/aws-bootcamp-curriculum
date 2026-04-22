# IT Fundamentals Module 05: Version Control with Git (Module 05 of 06)

## Learning Objectives

By the end of this module, you will be able to:

- Use Git for basic version control operations: init, add, commit, push, pull, and branch

## Prerequisites

- A computer with internet access
- A terminal application (Terminal on macOS, Git Bash on Windows, terminal on Linux)
- Git installed ([git-scm.com](https://git-scm.com/))
- Module 02 recommended for terminal skills

**Estimated self-study time:**

| Activity | Estimated Time |
|----------|---------------|
| Reading | 8 to 12 minutes |
| Lab | 10 to 12 minutes |
| Quiz | 3 to 5 minutes |
| Total | 21 to 29 minutes |

## Concepts

### Version Control with Git

[Git](https://git-scm.com/doc) is a version control system that tracks changes to files over time. It lets you save snapshots of your work, collaborate with others, and revert to previous versions if something goes wrong.

#### Why Git Matters for Cloud

- Infrastructure-as-code templates (CloudFormation, SAM, CDK) are stored in Git repositories
- CI/CD pipelines pull code from Git repositories to build and deploy applications
- Git provides an audit trail of every change: who changed what, when, and why

#### Core Git Concepts

| Concept | Description |
|---------|-------------|
| Repository (repo) | A directory tracked by Git, containing your files and their history |
| Commit | A snapshot of your files at a point in time, with a message describing the change |
| Branch | A parallel line of development; `main` is the default branch |
| Remote | A copy of the repository hosted on a server (GitHub, GitLab, CodeCommit) |
| Clone | Download a remote repository to your local machine |
| Push | Upload your local commits to the remote repository |
| Pull | Download new commits from the remote repository to your local machine |

#### Essential Git Commands

```bash
# Set up Git (one-time)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Start a new repository
git init my-project
cd my-project

# Check the status of your files
git status

# Stage files for commit
git add index.html          # Stage a specific file
git add .                   # Stage all changed files

# Commit staged files
git commit -m "Add homepage"

# Connect to a remote repository
git remote add origin https://github.com/you/my-project.git

# Push commits to the remote
git push origin main

# Pull changes from the remote
git pull origin main

# Create and switch to a new branch
git checkout -b feature/add-login

# Switch back to main
git checkout main

# View commit history
git log --oneline
```

> **Tip:** In the AWS Bootcamp, you will use Git to store CloudFormation templates and application code.

> **Bootcamp connection:** Understanding Git prepares you for CI/CD pipelines in Module 12: CI/CD Pipelines, where pipelines automatically pull code from Git repositories to build and deploy applications.

## Instructor Notes

**Estimated lecture time:** 10 to 15 minutes

**Common student questions:**

- Q: Do I need a GitHub account?
  A: You do not need a GitHub account for this module or the lab. The lab uses Git locally on your machine. However, a GitHub account is useful for the bootcamp, where you will push code to remote repositories. You can create a free account at [github.com](https://github.com/).

- Q: What is the difference between git add and git commit?
  A: `git add` stages your changes, telling Git which files you want to include in the next snapshot. `git commit` saves that snapshot with a descriptive message. Think of `git add` as placing items on a conveyor belt and `git commit` as pressing the button to package them.

**Teaching tips:**

- Do a live demo: create a repo, make a commit, push to GitHub. Show the commit history on GitHub to demonstrate that Git tracks every change.
- Show the commit history on GitHub so students can see the visual representation of changes over time.

**Pause point:**

- Ask students what `git status` shows. Expected answer: it shows which files have been modified, which are staged for commit, and which are untracked.

## Key Takeaways

- Git tracks changes to files over time and is the foundation for infrastructure-as-code and CI/CD pipelines.

---

[Previous: Module 04, APIs and Programming Basics](../04-apis-and-programming/README.md) | [Next: Module 06, Security Fundamentals](../06-security-fundamentals/README.md) | [IT Fundamentals Overview](../../README.md)
