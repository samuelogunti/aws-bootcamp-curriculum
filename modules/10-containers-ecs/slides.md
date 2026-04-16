---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 10: Containers and Amazon ECS'
---

# Module 10: Containers and Amazon ECS

**Phase 3: Building Applications**
Estimated lecture time: 90 minutes

<!-- Speaker notes: Welcome to Module 10. This module covers containers, Docker, ECR, and ECS. Breakdown: 10 min containers 101, 10 min Docker basics, 10 min Amazon ECR, 15 min ECS concepts, 10 min task definitions, 10 min Fargate vs EC2, 10 min services and deployments, 10 min ECS vs EKS vs Lambda, 5 min security. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Compare containers and virtual machines
- Build a container image from a Dockerfile
- Construct a container workflow: build, push to ECR, deploy on ECS
- Differentiate ECS clusters, task definitions, services, and tasks
- Compare Fargate and EC2 launch types
- Integrate an ALB with an ECS service for load balancing
- Differentiate when to use ECS, EKS, and Lambda
- Build container images following security best practices

---

## Prerequisites and agenda

**Prerequisites:** Module 03 (VPC, subnets, security groups), Module 04 (EC2, Auto Scaling), Module 07 (ALB, target groups, health checks)

**Agenda:**
1. Containers 101
2. Docker basics: images, containers, Dockerfiles
3. Amazon ECR: storing container images
4. ECS concepts: clusters, task definitions, services, tasks
5. Task definitions in detail
6. Fargate vs. EC2 launch type
7. Services, deployments, and auto scaling
8. ECS vs. EKS vs. Lambda
9. Container security best practices

---

# Containers 101

<!-- Speaker notes: This section takes approximately 10 minutes. Connect to Module 04's EC2 concepts. Draw an EC2 instance with multiple containers sharing the OS kernel. -->

---

## Containers vs. virtual machines

| Characteristic | Containers | Virtual Machines |
|----------------|-----------|------------------|
| Isolation | Process-level (shared kernel) | Hardware-level (separate kernel) |
| Startup time | Seconds | Minutes |
| Image size | Megabytes (50-500 MB) | Gigabytes (1-20 GB) |
| Resource overhead | Low (no guest OS) | High (full guest OS) |
| Density | Hundreds per host | Tens per host |
| Portability | Runs on any container runtime | Tied to hypervisor |

> Containers and VMs are not mutually exclusive. In production, containers often run on top of VMs (EC2 instances).

---

# Docker basics

<!-- Speaker notes: This section takes approximately 10 minutes. Walk through the Dockerfile structure and build workflow. -->

---

## Dockerfile example

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --production
COPY . .
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
EXPOSE 3000
CMD ["node", "server.js"]
```

> Order instructions from least to most frequently changing to maximize layer caching.

---

## Build and run workflow

```bash
# Build the image
docker build -t my-web-app:1.0 .

# Run locally
docker run -d -p 8080:3000 my-web-app:1.0

# Verify
docker ps
```

1. Write a Dockerfile
2. Build the image
3. Test locally
4. Push to a registry (ECR)
5. Deploy on an orchestrator (ECS)

---

# Amazon ECR

<!-- Speaker notes: This section takes approximately 10 minutes. Cover pushing images and lifecycle policies. -->

---

## Storing images in ECR

- Fully managed container image registry
- Integrates with ECS, EKS, and Lambda
- Organize images into repositories with tags

```bash
# Authenticate Docker to ECR
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS --password-stdin \
  123456789012.dkr.ecr.us-east-1.amazonaws.com

# Push image
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-web-app:1.0
```

> Configure lifecycle policies to clean up old images. Untagged images from failed builds accumulate quickly.

---

# ECS concepts

<!-- Speaker notes: This section takes approximately 15 minutes. Use the factory analogy: cluster is the factory, task definition is the blueprint, service is the production line, tasks are the products. -->

---

## ECS component hierarchy

```
Cluster
├── Service A (desired count: 3)
│   ├── Task 1 (task definition rev 5)
│   ├── Task 2 (task definition rev 5)
│   └── Task 3 (task definition rev 5)
└── Service B (desired count: 2)
    ├── Task 1 (task definition rev 12)
    └── Task 2 (task definition rev 12)
```

- **Cluster:** logical grouping of tasks and services
- **Task definition:** blueprint (images, CPU, memory, ports, IAM roles)
- **Service:** maintains desired count, handles deployments
- **Task:** running instance of a task definition

---

## Quick check: ECS components

A container in your ECS service crashes. The service has a desired count of 3.

**What happens next?**

<!-- Speaker notes: Answer: The ECS service scheduler detects the stopped task and automatically launches a replacement to maintain the desired count of 3. If an ALB health check is configured, the load balancer stops sending traffic to the failed task. This self-healing behavior is one of the key benefits of using ECS services over running standalone tasks. -->

---

# Task definitions in detail

<!-- Speaker notes: This section takes approximately 10 minutes. Walk through the JSON fields and connect to IAM roles from Module 02. -->

---

## Key task definition parameters

- **Container image:** ECR URI with tag
- **CPU and memory:** task-level and container-level allocation
- **Port mappings:** container port to host port
- **Environment variables:** configuration values
- **Secrets:** references to Secrets Manager or Parameter Store
- **Log configuration:** CloudWatch Logs driver

---

## IAM roles for ECS tasks

| Role Type | Used By | Example Permissions |
|-----------|---------|---------------------|
| Task execution role | ECS agent (infrastructure) | Pull images, write logs, get secrets |
| Task role | Your application code | S3, DynamoDB, SQS access |

> Never embed AWS credentials in images or environment variables. Use IAM task roles for temporary credentials.

---

# Fargate vs. EC2 launch type

<!-- Speaker notes: This section takes approximately 10 minutes. Present scenarios and ask students to choose. -->

---

## Launch type comparison

| Feature | Fargate | EC2 Launch Type |
|---------|---------|-----------------|
| Infrastructure | AWS manages | You manage instances |
| Scaling | Per-task (automatic) | Instance + task scaling |
| Networking | Each task gets own ENI | Shared or awsvpc mode |
| Pricing | Per task (vCPU + memory/s) | Per EC2 instance |
| GPU support | Not supported | Supported |
| Best for | Most workloads | GPU, cost optimization at scale |

> Start with Fargate for new workloads. Move to EC2 only for GPU, custom OS, or large-scale cost optimization.

---

## Discussion: Fargate vs. EC2

Scenario A: A startup with 3 developers deploying 5 microservices.
Scenario B: A company running ML inference that requires GPU access.

**Which launch type for each?**

<!-- Speaker notes: Answer: Scenario A is Fargate (minimize ops burden, small team should focus on application code, not managing EC2 instances). Scenario B is EC2 launch type (Fargate does not support GPU instances; they need P or G family instances). This reinforces that the choice depends on specific requirements, not a blanket preference. -->

---

# Services, deployments, and auto scaling

<!-- Speaker notes: This section takes approximately 10 minutes. Cover rolling updates, blue/green, and service auto scaling. -->

---

## Deployment strategies

| Feature | Rolling Update | Blue/Green |
|---------|---------------|------------|
| Managed by | ECS scheduler | AWS CodeDeploy |
| Rollback | Manual (redeploy previous) | Automatic on failure |
| Traffic shift | Task by task | All at once, linear, or canary |
| Cost during deploy | Slightly above normal | Double capacity |
| Best for | Most deployments | Mission-critical services |

- **Deployment circuit breaker:** auto-detects failing deployments and rolls back
- **Service auto scaling:** target tracking on CPU/memory, step, or scheduled

---

# ECS vs. EKS vs. Lambda

<!-- Speaker notes: This section takes approximately 10 minutes. Present the comparison and ask students to choose for specific scenarios. -->

---

## Choosing the right compute service

| Feature | ECS | EKS | Lambda |
|---------|-----|-----|--------|
| Orchestration | AWS-native | Kubernetes | Event-driven |
| Max execution | No limit | No limit | 15 minutes |
| Scaling | Task level | Pod level | Per invocation |
| Pricing | Per task or instance | Per Pod or instance | Per invocation |
| Portability | AWS-specific | Multi-cloud | AWS-specific |
| Complexity | Low to medium | High | Very low |

---

## When to use each

- **ECS:** managed container orchestration, AWS-native, long-running services
- **EKS:** existing Kubernetes expertise, multi-cloud portability, advanced scheduling
- **Lambda:** event-driven, under 15 min, variable traffic, minimal ops

> These are not mutually exclusive. Many architectures combine ECS for web services, Lambda for event processing, and EKS for Kubernetes workloads.

---

## Think about it: compute selection

A webhook handler processes 100 requests per day, each taking 2 seconds.

**Which compute service would you choose, and why?**

<!-- Speaker notes: Answer: Lambda. The traffic is very low (100 requests/day) and sporadic, each execution is short (2 seconds), and the workload is event-driven. Running an ECS service 24/7 for 100 requests per day would waste resources. Lambda scales to zero when idle and charges only for the 200 seconds of actual compute per day. This is a textbook Lambda use case. -->

---

# Container security best practices

<!-- Speaker notes: This section takes approximately 5 minutes. Quick summary of the key practices. -->

---

## Security checklist

| Practice | Implementation | Benefit |
|----------|---------------|---------|
| Non-root user | `USER appuser` in Dockerfile | Limits privilege escalation |
| Image scanning | ECR scan-on-push | Detects vulnerabilities |
| Secrets management | Secrets Manager references | Prevents credential exposure |
| Read-only filesystem | `readonlyRootFilesystem: true` | Prevents file tampering |
| Minimal base images | Alpine or distroless | Reduces attack surface |
| Immutable tags | Use digest or unique tags | Ensures deploy consistency |

---

## Key takeaways

- Containers package applications with dependencies for consistent deployment. They are lighter and faster than VMs, sharing the host OS kernel.
- ECS orchestrates containers using clusters, task definitions, services, and tasks. Services maintain desired count and handle deployments automatically.
- Fargate removes EC2 management for container workloads. Start with Fargate; move to EC2 only for GPU, custom OS, or cost optimization at scale.
- ECS integrates with ALB for load balancing, service auto scaling for demand-based capacity, and ECR for secure image storage with lifecycle policies.
- Follow container security best practices: non-root user, image scanning, secrets via Secrets Manager, read-only filesystems, and minimal base images.

---

## Lab preview: deploying containers on ECS

**Objective:** Build a Docker image, push to ECR, create an ECS cluster and service on Fargate, and configure a rolling deployment

**Key services:** Amazon ECS, Amazon ECR, Fargate, ALB, VPC, IAM

**Duration:** 60 minutes

<!-- Speaker notes: This is a Phase 3 lab with semi-guided steps. Students will write a Dockerfile, build and push to ECR, create an ECS cluster, register a task definition, create a service with an ALB, and perform a rolling deployment with a new image version. Steps 1-4 are guided; steps 5-7 let students figure out the implementation. Remind students to delete the ECS service, ALB, and ECR repository after the lab. -->

---

# Questions?

Review `modules/10-containers-ecs/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions involve task execution role vs task role, Fargate vs EC2 cost comparison, and when to use EKS. Transition to the lab when ready. -->
