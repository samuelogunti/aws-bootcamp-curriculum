---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 12: CI/CD Pipelines'
---

# Module 12: CI/CD Pipelines

**Phase 3: Building Applications**
Estimated lecture time: 75 to 90 minutes

<!-- Speaker notes: Welcome to Module 12, the final module in Phase 3. This module connects IaC (Module 11) with automated deployment. Breakdown: 10 min CI/CD fundamentals, 10 min AWS developer tools, 15 min CodeBuild, 15 min CodeDeploy, 10 min CodePipeline, 10 min deployment strategies, 5 min GitHub Actions, 5 min wrap-up. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Build a CI/CD pipeline using AWS CodePipeline with source, build, test, and deploy stages
- Construct a buildspec.yml for CodeBuild with install, pre-build, build, and post-build phases
- Integrate CodeDeploy with deployment groups and appspec.yml for EC2 and ECS
- Troubleshoot failed pipeline executions using CodeBuild logs and lifecycle events
- Compare deployment strategies (all-at-once, rolling, blue/green, canary)
- Differentiate CodePipeline and GitHub Actions for CI/CD orchestration
- Integrate artifact management using S3 and CodeArtifact

---

## Prerequisites and agenda

**Prerequisites:** Modules 09 (Lambda), 10 (ECS), 11 (IaC with CloudFormation/SAM)

**Agenda:**
1. CI/CD fundamentals
2. AWS developer tools overview
3. Source stage and repository connections
4. CodeBuild: compiling, testing, packaging
5. CodeDeploy: automating deployments
6. CodePipeline: orchestrating the release process
7. Deployment strategies
8. GitHub Actions as an alternative
9. Artifact management

---

# CI/CD fundamentals

<!-- Speaker notes: This section takes about 10 minutes. Start by asking students how they currently deploy code. Use the pipeline diagram to show the full flow from commit to production. -->

---

## What CI and CD mean

- **Continuous Integration (CI):** Automatically build and test code on every push
- **Continuous Delivery (CD):** Automatically deploy validated code to staging or production
- Together, they create an automated pipeline from commit to running application

---

## Benefits of CI/CD

| Benefit | Description |
|---------|-------------|
| Faster feedback | Build failures detected in minutes, not days |
| Reduced risk | Small, frequent deploys are easier to troubleshoot |
| Consistent process | Every deployment follows the same automated steps |
| Audit trail | Every execution records who, what, and when |
| Higher confidence | Automated tests run on every change |

---

## The CI/CD pipeline flow

```
Developer pushes code
    |
    v
Source Stage (pull from repository)
    |
    v
Build Stage (compile, test, package)
    |
    v
Staging Deployment
    |
    v
Manual Approval (optional gate)
    |
    v
Production Deployment
```

---

# AWS developer tools overview

<!-- Speaker notes: This section takes about 5 minutes. Briefly introduce each service and its role in the pipeline. -->

---

## AWS developer tools

| Service | Purpose | Pipeline Stage |
|---------|---------|----------------|
| CodeBuild | Managed build service (compile, test, package) | Build and Test |
| CodeDeploy | Automated deployment to EC2, ECS, Lambda | Deploy |
| CodePipeline | Orchestration connecting all stages | Orchestration |
| CodeArtifact | Managed package repository (npm, pip, Maven) | Dependencies |

> You do not need to use every tool together. Mix and match with GitHub, Jenkins, etc.

---

# CodeBuild: compiling, testing, and packaging

<!-- Speaker notes: This section takes about 15 minutes. Show a buildspec.yml example and walk through each phase. -->

---

## How CodeBuild works

- Spins up a temporary Docker container for each build
- Downloads source code, executes your commands, uploads artifacts
- Scales automatically for concurrent builds
- You pay only for build minutes consumed

---

## The buildspec.yml file

```yaml
version: 0.2
phases:
  install:
    runtime-versions:
      nodejs: 20
    commands:
      - npm ci
  pre_build:
    commands:
      - npm run lint
      - npm run test
  build:
    commands:
      - npm run build
  post_build:
    commands:
      - echo "Build completed on $(date)"
artifacts:
  files: ["**/*"]
  base-directory: dist
```

---

## Build phases

| Phase | Purpose | Common Commands |
|-------|---------|-----------------|
| `install` | Install dependencies and runtimes | `npm ci`, `pip install`, select runtimes |
| `pre_build` | Tasks before the main build | Linting, unit tests, Docker login |
| `build` | Compile code, run main build | `npm run build`, `docker build` |
| `post_build` | Tasks after build completes | Push images, generate reports |

> If any command fails (non-zero exit code), the build fails and skips remaining phases.

---

## Discussion: why use `npm ci` instead of `npm install`?

Your pipeline runs `npm install` in the build stage. Occasionally, builds produce different results even though the code has not changed.

**What causes this inconsistency, and how does `npm ci` fix it?**

<!-- Speaker notes: Expected answer: npm install may update the lock file and resolve different dependency versions. npm ci installs exactly from the lock file, making builds deterministic and faster. This applies to all package managers: always use the lock-file-based install command. -->

---

# CodeDeploy: automating deployments

<!-- Speaker notes: This section takes about 10 minutes. Cover the appspec.yml structure and deployment configurations. -->

---

## CodeDeploy core concepts

- **Application:** A name identifying the software to deploy
- **Deployment group:** Target instances or services (by tags or ASG)
- **Revision:** The application version plus an appspec.yml file

---

## The appspec.yml file (EC2)

```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/myapp
hooks:
  BeforeInstall:
    - location: scripts/stop_server.sh
      timeout: 120
  AfterInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 120
```

---

# CodePipeline: orchestrating the release

<!-- Speaker notes: This section takes about 10 minutes. Draw the pipeline structure on the board and explain artifact passing between stages. -->

---

## Pipeline structure

```
Pipeline: my-app-pipeline
  Stage 1: Source (GitHub, triggers on push)
  Stage 2: Build (CodeBuild, compiles and tests)
  Stage 3: Staging (CodeDeploy to staging)
  Stage 4: Approval (manual gate)
  Stage 5: Production (CodeDeploy to prod)
```

- Stages run sequentially; actions within a stage can run in parallel
- Artifacts pass between stages via S3

---

## Manual approval actions

- Pauses the pipeline for human review before production
- Sends notification via SNS to designated reviewers
- Reviewer inspects staging, then approves or rejects
- Configurable timeout (default 7 days)

> Start with manual approvals for production. Remove the gate as confidence in automated testing grows.

---

# Deployment strategies

<!-- Speaker notes: This section takes about 10 minutes. Use the comparison table to highlight trade-offs. Ask students which strategy they would choose for an e-commerce site during peak traffic. -->

---

## Strategy comparison

| Strategy | Downtime | Rollback Speed | Cost | Risk |
|----------|----------|----------------|------|------|
| All-at-once | Brief | Slow (redeploy) | Normal | High |
| Rolling | None | Medium | Normal | Medium |
| Blue/green | None | Instant | Double during deploy | Low |
| Canary | None | Instant | Slightly above normal | Very low |

---

## Blue/green deployment

- Creates a full copy ("green") alongside existing ("blue")
- Traffic shifts from blue to green after health checks pass
- Instant rollback by redirecting traffic back to blue
- Best for production where availability is critical

---

## Canary deployment

- Shifts a small percentage of traffic (5-10%) to the new version first
- Monitors error rate and latency on the canary group
- If healthy, shifts remaining traffic; if not, rolls back automatically
- Best for high-traffic services needing real-traffic validation

---

## Quick check: which deployment strategy?

Your team deploys a payment processing service that handles 10,000 transactions per minute. A failed deployment could cause financial losses.

**Which deployment strategy would you choose, and why?**

A) All-at-once
B) Rolling
C) Blue/green
D) Canary

<!-- Speaker notes: Answer: D) Canary or C) Blue/green. Canary limits blast radius to a small percentage of traffic. Blue/green provides instant rollback. All-at-once is too risky for a payment service. Rolling runs mixed versions, which can cause issues with financial transactions. -->

---

## Think about it: pipeline failure at 5 PM on Friday

Your production pipeline fails at the CodeDeploy stage. The staging deployment succeeded, but production health checks are failing. The team wants to go home.

**What is your immediate response, and what pipeline feature saves you?**

<!-- Speaker notes: Expected answer: Immediate response is to let CodeDeploy's automatic rollback handle it (configured to roll back on health check failure). The team does not need to manually intervene if rollback is configured. The pipeline feature that saves you is automatic rollback on alarm trigger. Lesson: always configure automatic rollback for production deployments. Never rely on manual intervention for Friday evening failures. -->

---

## GitHub Actions vs. CodePipeline

| Feature | GitHub Actions | CodePipeline |
|---------|---------------|--------------|
| Source integration | Native GitHub | GitHub, S3, Bitbucket, GitLab |
| Deployment to AWS | Requires credential config (OIDC) | Native CodeDeploy, CloudFormation, ECS |
| Marketplace | Thousands of community actions | AWS service integrations |
| Best for | GitHub-centric teams, multi-cloud | Deep AWS deployment integration |

> Many teams use GitHub Actions for CI and CodePipeline for CD (hybrid approach).

---

## Pipeline best practices

- Separate stages for separate concerns (build, test, deploy)
- Automated tests at every level (unit, integration, smoke)
- Manual approval for production deployments
- Define pipeline as code (CloudFormation or CDK)
- Apply least privilege to pipeline IAM roles

---

## Key takeaways

- CI/CD automates the build, test, and deploy cycle so every code change follows a consistent path from commit to production
- CodePipeline orchestrates the release process; CodeBuild handles compilation and testing; CodeDeploy handles deployment
- Deployment strategies (all-at-once, rolling, blue/green, canary) offer different trade-offs between speed, risk, and cost
- Always include automated tests and gate production deployments with manual approvals until confidence is high
- Define your pipeline as code and store it in version control alongside your application

---

## Lab preview: building a CI/CD pipeline

**What you will do:**
- Connect a GitHub repository to CodePipeline
- Configure CodeBuild with a buildspec.yml
- Set up CodeDeploy for staging and production
- Add a manual approval gate before production
- Test the pipeline with a code push

**Duration:** 60 minutes
**Key services:** CodePipeline, CodeBuild, CodeDeploy, S3

<!-- Speaker notes: The lab has 4 guided steps and 3 semi-guided steps. Students will need a GitHub repository with a simple Node.js or Python app. Remind them to configure OIDC if using GitHub Actions for the optional challenge. -->

---

# Questions?

Review `modules/12-cicd-pipelines/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions: "Continuous Delivery vs. Continuous Deployment?" (Delivery has a manual gate; Deployment is fully automated.) "How to handle database migrations?" (Run migrations before deploying new app code; ensure backward compatibility.) -->
