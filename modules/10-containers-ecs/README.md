# Module 10: Containers and Amazon ECS

## Learning Objectives

By the end of this module, you will be able to:

- Compare containers and virtual machines, and differentiate the isolation, resource usage, and startup characteristics of each approach
- Build a container image from a Dockerfile and troubleshoot common image build errors
- Construct an end-to-end container workflow by pushing images to Amazon Elastic Container Registry (Amazon ECR) and deploying them on Amazon Elastic Container Service (Amazon ECS)
- Differentiate ECS core concepts including clusters, task definitions, services, and tasks, and integrate them into a working deployment
- Compare the Fargate and EC2 launch types and troubleshoot deployment issues specific to each
- Integrate an Application Load Balancer (ALB) with an ECS service for dynamic port mapping, health checks, and traffic distribution
- Differentiate when to use Amazon ECS, Amazon Elastic Kubernetes Service (Amazon EKS), and AWS Lambda based on workload requirements
- Build container images that follow security best practices including non-root users, image scanning, and secrets management

## Prerequisites

- Completion of [Module 03: Networking Basics (VPC)](../03-networking-basics/README.md) (VPCs, subnets, security groups, and Availability Zones for placing ECS tasks and load balancers)
- Completion of [Module 04: Compute with Amazon EC2](../04-compute-ec2/README.md) (EC2 instance concepts, IAM instance profiles, and Auto Scaling groups that underpin the EC2 launch type)
- Completion of [Module 07: Load Balancing and DNS](../07-load-balancing-and-dns/README.md) (ALB listeners, target groups, and health checks used for ECS service load balancing)

## Concepts

### Containers 101: What Containers Are and Why They Matter

A container is a lightweight, standalone package that includes everything needed to run a piece of software: the application code, runtime, system libraries, and settings. Containers share the host operating system kernel rather than bundling a full OS, which makes them smaller and faster to start than traditional virtual machines (VMs).

In [Module 04](../04-compute-ec2/README.md), you launched EC2 instances, each running a complete operating system. Containers take a different approach. Instead of virtualizing the hardware, containers virtualize the operating system. Multiple containers run on the same OS kernel, isolated from each other through Linux kernel features such as namespaces and cgroups.

#### Containers vs. Virtual Machines

| Characteristic | Containers | Virtual Machines |
|----------------|-----------|------------------|
| Isolation level | Process-level (shared kernel) | Hardware-level (separate kernel per VM) |
| Startup time | Seconds | Minutes |
| Image size | Megabytes (typically 50 to 500 MB) | Gigabytes (typically 1 to 20 GB) |
| Resource overhead | Low (no guest OS) | High (full guest OS per VM) |
| Density | Hundreds per host | Tens per host |
| Portability | Runs identically on any system with a container runtime | Tied to hypervisor and OS configuration |
| Use case | Microservices, CI/CD pipelines, stateless workloads | Legacy applications, workloads requiring full OS isolation |

Containers solve the "it works on my machine" problem. Because a container packages the application with its dependencies, it runs the same way in development, testing, and production. This consistency simplifies deployments and reduces environment-related bugs.

> **Tip:** Containers and VMs are not mutually exclusive. In production, containers often run on top of VMs (such as EC2 instances) to combine the hardware isolation of VMs with the lightweight packaging of containers.

### Docker Basics: Images, Containers, and the Build Workflow

[Docker](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-container-image.html) is the most widely used container runtime. Docker uses three core concepts:

- **Image.** A read-only template that contains the application code, runtime, libraries, and configuration. Images are built in layers, where each layer represents a set of file system changes.
- **Container.** A running instance of an image. You can run multiple containers from the same image, each with its own writable layer on top of the read-only image layers.
- **Dockerfile.** A text file with instructions for building an image. Each instruction creates a layer in the image.

#### Dockerfile Structure

A Dockerfile defines the steps to build your container image. Here is an example for a simple Node.js web application:

```dockerfile
# Start from an official Node.js base image
FROM node:20-alpine

# Set the working directory inside the container
WORKDIR /app

# Copy dependency manifests first (for layer caching)
COPY package.json package-lock.json ./

# Install dependencies
RUN npm ci --production

# Copy application source code
COPY . .

# Create a non-root user and switch to it
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Expose the application port
EXPOSE 3000

# Define the command to run the application
CMD ["node", "server.js"]
```

#### Build and Run Workflow

The typical Docker workflow follows these steps:

1. Write a Dockerfile that defines your application environment.
2. Build the image using `docker build`.
3. Test the image locally using `docker run`.
4. Push the image to a container registry (such as Amazon ECR).
5. Deploy the image on a container orchestrator (such as Amazon ECS).

```bash
# Build the image and tag it
docker build -t my-web-app:1.0 .

# Run the container locally
docker run -d -p 8080:3000 my-web-app:1.0

# Verify the container is running
docker ps
```

Expected output:

```
CONTAINER ID   IMAGE             COMMAND           STATUS          PORTS
a1b2c3d4e5f6   my-web-app:1.0   "node server.js"  Up 10 seconds   0.0.0.0:8080->3000/tcp
```

> **Tip:** Order your Dockerfile instructions from least to most frequently changing. Place dependency installation before source code copying so that Docker can cache the dependency layer and skip reinstalling packages when only your application code changes.

### Amazon ECR: Storing and Managing Container Images

[Amazon Elastic Container Registry (Amazon ECR)](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) is where you store your container images on AWS. It integrates directly with ECS, EKS, and Lambda, so your orchestration services can pull images without extra authentication steps. Think of ECR as a private Docker Hub that lives inside your AWS account.

Each AWS account gets a default private registry in each Region. Within a registry, you organize images into repositories. A repository holds multiple versions of a related image, identified by tags (such as `latest`, `v1.0`, or a Git commit hash).

#### Pushing an Image to ECR

To push a locally built image to ECR:

```bash
# Authenticate Docker to your ECR registry
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com

# Create a repository (if it does not exist)
aws ecr create-repository --repository-name my-web-app --region us-east-1

# Tag the local image with the ECR repository URI
docker tag my-web-app:1.0 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-web-app:1.0

# Push the image to ECR
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-web-app:1.0
```

#### Image Lifecycle Policies

Over time, repositories accumulate old images that consume storage and increase costs. [ECR lifecycle policies](https://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html) automate image cleanup by defining rules that expire images based on age or count. For example, you can create a policy that keeps only the 10 most recent images and expires everything older than 30 days.

Lifecycle policy rules evaluate images based on:

- **Tag status.** Whether the image is tagged or untagged.
- **Tag prefix.** A pattern that matches specific tag prefixes (for example, `prod-` or `dev-`).
- **Count type.** Expire images by count (keep the N most recent) or by age (expire images older than N days).

> **Tip:** Always configure a lifecycle policy on your ECR repositories. Untagged images from failed builds and old tagged images accumulate quickly. A simple rule to expire untagged images older than 1 day and keep only the last 20 tagged images covers most use cases.

### ECS Concepts: Clusters, Task Definitions, Services, and Tasks

[Amazon Elastic Container Service (Amazon ECS)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html) orchestrates your containers so you do not have to. It launches containers, monitors their health, replaces failures, and scales capacity based on demand. You tell ECS what to run (task definitions), where to run it (clusters), and how many copies to keep alive (services), and it handles the rest.

#### Clusters

An [ECS cluster](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/clusters.html) is a logical grouping of tasks and services. A cluster serves as the boundary for your container workloads. You can create separate clusters for different environments (development, staging, production) or for different applications.

A cluster contains the infrastructure that runs your tasks. Depending on the launch type you choose, this infrastructure is either EC2 instances that you manage or AWS Fargate capacity that AWS manages on your behalf.

#### Task Definitions

A [task definition](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html) is a blueprint for your application. It describes one or more containers that form your application, similar to a `docker-compose.yml` file. A task definition specifies:

- Which container images to use
- How much CPU and memory each container needs
- Which ports to expose
- Environment variables to pass to the containers
- IAM roles for the task
- Logging configuration
- Volume mounts

Task definitions are versioned. Each time you update a task definition, ECS creates a new revision. You can roll back to a previous revision if a new deployment causes issues.

#### Tasks

A task is a running instance of a task definition. When ECS launches a task, it pulls the container images specified in the task definition, creates the containers, and starts them. A task can contain one or more containers that run together on the same host and share networking and storage resources.

#### Services

An [ECS service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html) maintains a specified number of running tasks. If a task fails or stops, the service scheduler launches a replacement to maintain the desired count. Services also integrate with load balancers to distribute traffic across tasks and with auto scaling to adjust the number of tasks based on demand.

The relationship between these concepts:

```
Cluster
├── Service A (desired count: 3)
│   ├── Task 1 (running task definition revision 5)
│   ├── Task 2 (running task definition revision 5)
│   └── Task 3 (running task definition revision 5)
└── Service B (desired count: 2)
    ├── Task 1 (running task definition revision 12)
    └── Task 2 (running task definition revision 12)
```

### Task Definitions: Container Configuration in Detail

A task definition is a JSON document that tells ECS exactly how to run your containers. Understanding its key parameters is essential for building reliable container deployments.

#### Container Definitions

Each task definition contains one or more [container definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html). Each container definition specifies the image, resource limits, port mappings, and environment configuration for a single container.

#### CPU and Memory

You allocate CPU and memory at two levels:

- **Task level.** The total CPU and memory available to all containers in the task. Required for Fargate tasks.
- **Container level.** The CPU and memory reserved for (or limited to) each individual container within the task.

For Fargate tasks, you must choose from specific CPU and memory combinations:

| CPU (vCPU) | Memory Options |
|------------|----------------|
| 0.25 vCPU | 0.5 GB, 1 GB, 2 GB |
| 0.5 vCPU | 1 GB, 2 GB, 3 GB, 4 GB |
| 1 vCPU | 2 GB, 3 GB, 4 GB, 5 GB, 6 GB, 7 GB, 8 GB |
| 2 vCPU | 4 GB through 16 GB (in 1 GB increments) |
| 4 vCPU | 8 GB through 30 GB (in 1 GB increments) |

#### Port Mappings

Port mappings connect a port on the container to a port on the host. For Fargate tasks, the host port and container port must be the same (because each task gets its own elastic network interface). For EC2 launch type tasks, you can use dynamic port mapping by setting the host port to 0, which lets the ALB assign a random available port.

#### Environment Variables

You pass configuration to containers through environment variables. You can define them directly in the task definition or reference values stored in [AWS Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) or [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) for sensitive values such as database passwords and API keys.

#### IAM Task Roles

ECS supports two types of IAM roles for tasks:

| Role Type | Purpose | Example Permissions |
|-----------|---------|---------------------|
| [Task execution role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html) | Permissions that the ECS agent needs to manage the task (pull images, write logs, retrieve secrets) | `ecr:GetAuthorizationToken`, `logs:CreateLogStream`, `secretsmanager:GetSecretValue` |
| [Task role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) | Permissions that your application code inside the container needs to access AWS services | `s3:GetObject`, `dynamodb:PutItem`, `sqs:SendMessage` |

The task execution role is used by the ECS infrastructure. The task role is used by your application. Keep them separate and apply the principle of least privilege to each, as you learned in [Module 02](../02-iam-and-security/README.md).

> **Warning:** Do not embed AWS credentials in your container images or pass them as environment variables. Use IAM task roles instead. The ECS agent automatically provides temporary credentials to your containers through the task metadata endpoint.

#### Example Task Definition

```json
{
  "family": "my-web-app",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512",
  "executionRoleArn": "arn:aws:iam::123456789012:role/ecsTaskExecutionRole",
  "taskRoleArn": "arn:aws:iam::123456789012:role/myAppTaskRole",
  "containerDefinitions": [
    {
      "name": "web",
      "image": "123456789012.dkr.ecr.us-east-1.amazonaws.com/my-web-app:1.0",
      "portMappings": [
        {
          "containerPort": 3000,
          "protocol": "tcp"
        }
      ],
      "environment": [
        {
          "name": "NODE_ENV",
          "value": "production"
        }
      ],
      "secrets": [
        {
          "name": "DB_PASSWORD",
          "valueFrom": "arn:aws:secretsmanager:us-east-1:123456789012:secret:my-db-password"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/my-web-app",
          "awslogs-region": "us-east-1",
          "awslogs-stream-prefix": "web"
        }
      },
      "essential": true
    }
  ]
}
```


### Fargate vs. EC2 Launch Type

When you create an ECS cluster, you choose how to provide the underlying compute capacity for your tasks. ECS supports two primary launch types: [AWS Fargate](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html) (serverless) and [EC2](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/clusters.html) (self-managed instances).

#### Fargate Launch Type

With Fargate, AWS manages the infrastructure. You do not provision, configure, or scale EC2 instances. You specify the CPU and memory your task needs, and Fargate allocates the right amount of compute. Each Fargate task runs in its own isolated environment with a dedicated elastic network interface (ENI).

#### EC2 Launch Type

With the EC2 launch type, you manage a fleet of EC2 instances that form the cluster's capacity. You are responsible for choosing instance types, patching the operating system, scaling the instance fleet, and monitoring instance health. In return, you get more control over the underlying infrastructure and access to features like GPU instances and custom AMIs.

#### Comparison

| Feature | Fargate | EC2 Launch Type |
|---------|---------|-----------------|
| Infrastructure management | AWS manages everything | You manage EC2 instances |
| Scaling | Per-task scaling (automatic) | Instance-level scaling (Auto Scaling groups) plus task-level scaling |
| Networking | Each task gets its own ENI | Tasks share the host network or use awsvpc mode |
| Pricing | Pay per task (vCPU and memory per second) | Pay for EC2 instances regardless of task utilization |
| Startup time | Slightly longer (infrastructure provisioning) | Faster if instances are already running |
| GPU support | Not supported | Supported (P and G instance families) |
| Custom AMIs | Not applicable | Supported (custom ECS-optimized AMIs) |
| Persistent storage | Ephemeral storage (20 GB default, up to 200 GB) | EBS volumes, instance store, EFS |
| Best for | Most workloads, teams that want to focus on applications | GPU workloads, large-scale cost optimization, workloads needing custom OS configuration |

#### When to Use Each

Choose Fargate when:

- You want to minimize operational overhead and avoid managing servers.
- Your workloads have variable or unpredictable traffic patterns.
- You are running many small, independent services.
- Your team is small and you want to focus on application development rather than infrastructure.

Choose EC2 launch type when:

- You need GPU instances for machine learning or graphics workloads.
- You have large, steady-state workloads where Reserved Instances or Savings Plans reduce costs significantly.
- You need custom kernel parameters, specific OS configurations, or specialized storage.
- You need to run Windows containers with specific OS version requirements.

> **Tip:** Start with Fargate for new workloads. It removes the undifferentiated heavy lifting of managing EC2 instances. Move to the EC2 launch type only when you have a specific requirement that Fargate cannot meet, such as GPU access or cost optimization at scale.

### ECS Services: Desired Count, Deployments, and Auto Scaling

An [ECS service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html) ensures that a specified number of task instances are running at all times. If a task fails, the service scheduler automatically launches a replacement. Services also manage deployments when you update your task definition.

#### Desired Count

The desired count is the number of task instances the service tries to maintain. If you set the desired count to 3, the service scheduler ensures that 3 tasks are always running. If a task stops (due to a crash, health check failure, or host issue), the scheduler launches a new task to replace it.

#### Deployment Strategies

When you update a service (for example, by deploying a new container image), ECS replaces the running tasks with new ones. ECS supports two deployment strategies:

**Rolling Update**

The [rolling update](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html) strategy replaces tasks incrementally. ECS stops a batch of old tasks and starts new tasks, repeating until all tasks run the new version. You control the pace with two parameters:

- **minimumHealthyPercent.** The minimum percentage of tasks that must remain running during the deployment. For example, 50% means ECS can stop up to half the tasks before starting replacements.
- **maximumPercent.** The maximum percentage of tasks (relative to the desired count) that can run during the deployment. For example, 200% means ECS can temporarily run twice the desired count to ensure zero downtime.

**Blue/Green Deployment**

[Blue/green deployments](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-blue-green.html) use AWS CodeDeploy to create a complete replacement set of tasks (the "green" environment) alongside the existing tasks (the "blue" environment). Traffic shifts from blue to green gradually or all at once. If the green environment fails health checks, CodeDeploy automatically rolls back to the blue environment.

| Feature | Rolling Update | Blue/Green |
|---------|---------------|------------|
| Managed by | ECS service scheduler | AWS CodeDeploy |
| Rollback | Manual (redeploy previous task definition) | Automatic (CodeDeploy rolls back on failure) |
| Traffic shift | Gradual (task by task) | Configurable (all at once, linear, or canary) |
| Cost during deployment | Slightly above normal (temporary extra tasks) | Double capacity during transition |
| Complexity | Simple (built into ECS) | More complex (requires CodeDeploy configuration) |
| Best for | Most deployments | Mission-critical services requiring instant rollback |

#### Deployment Circuit Breaker

The ECS [deployment circuit breaker](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html) automatically detects when a rolling update deployment is failing. If new tasks repeatedly fail to reach a healthy state, the circuit breaker stops the deployment and optionally rolls back to the last successful deployment. Enable the circuit breaker to prevent a bad deployment from replacing all healthy tasks with failing ones.

#### Service Auto Scaling

[ECS service auto scaling](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html) adjusts the desired count of tasks in a service based on demand. It uses Application Auto Scaling and supports three scaling policy types:

- **Target tracking.** Maintain a target value for a specific metric. For example, keep average CPU utilization at 50%. ECS adds tasks when utilization rises above the target and removes tasks when it drops below.
- **Step scaling.** Define scaling adjustments based on CloudWatch alarm thresholds. For example, add 2 tasks when CPU exceeds 70% and add 4 tasks when CPU exceeds 90%.
- **Scheduled scaling.** Scale based on a schedule. For example, increase the desired count to 10 tasks every weekday at 8:00 AM and decrease to 2 tasks at 8:00 PM.

In [Module 04](../04-compute-ec2/README.md), you learned about EC2 Auto Scaling groups that scale instances. ECS service auto scaling is similar but operates at the task level. For Fargate, task-level scaling is all you need. For the EC2 launch type, you may need both instance-level scaling (to add EC2 capacity) and task-level scaling (to add tasks onto that capacity).

> **Tip:** Start with target tracking on CPU or memory utilization. It is the simplest policy to configure and handles most scaling scenarios. Add step scaling or scheduled scaling only when target tracking does not meet your requirements.

### Service Discovery and Load Balancing with ALB

In [Module 07](../07-load-balancing-and-dns/README.md), you learned how an Application Load Balancer distributes traffic across EC2 instances. ECS extends this pattern to containers, with some important differences.

#### ECS and ALB Integration

When you [associate an ALB with an ECS service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/alb.html), the service automatically registers new tasks with the ALB target group and deregisters tasks that stop. This means the ALB always knows which tasks are healthy and available to receive traffic.

For Fargate tasks (which use `awsvpc` network mode), each task gets its own private IP address and ENI within your VPC subnets, as you configured in [Module 03](../03-networking-basics/README.md). The ALB routes traffic to each task's IP address on the container port.

#### Dynamic Port Mapping

With the EC2 launch type, multiple tasks can run on the same EC2 instance. If each task listens on port 3000, they would conflict. Dynamic port mapping solves this by assigning a random host port to each task. The ALB uses the target group to track which host port maps to which task.

```
ALB (port 443)
├── EC2 Instance A
│   ├── Task 1: host port 32768 -> container port 3000
│   └── Task 2: host port 32769 -> container port 3000
└── EC2 Instance B
    └── Task 3: host port 32768 -> container port 3000
```

To enable dynamic port mapping, set the host port to 0 in your task definition's port mappings. The ALB target group must use the `instance` target type.

> **Tip:** With Fargate, you do not need dynamic port mapping because each task has its own IP address. The ALB target group uses the `ip` target type and routes directly to each task's IP and container port.

#### Health Checks

The ALB performs health checks against your ECS tasks, just as it does for EC2 instances. Configure the health check path to an endpoint in your application that verifies the application is ready to serve traffic (for example, `/health`). If a task fails its health check, the ALB stops sending traffic to it, and the ECS service scheduler replaces it.

Health check parameters to configure:

| Parameter | Recommended Value | Reason |
|-----------|-------------------|--------|
| Path | `/health` | Dedicated endpoint that checks application readiness |
| Interval | 30 seconds | Balances detection speed with request overhead |
| Healthy threshold | 2 | Confirms recovery before sending traffic |
| Unhealthy threshold | 3 | Avoids marking tasks unhealthy due to transient issues |
| Timeout | 5 seconds | Allows time for the health endpoint to respond |

#### Service Discovery with AWS Cloud Map

For service-to-service communication that does not go through a load balancer, ECS integrates with [AWS Cloud Map](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-discovery.html) for DNS-based service discovery. When you enable service discovery, ECS automatically registers each task's IP address with a Cloud Map namespace. Other services can find your tasks by querying a DNS name (for example, `api.my-app.local`).

Service discovery is useful for internal microservices that communicate directly with each other. For services that receive external traffic, use an ALB instead.

### ECS vs. EKS vs. Lambda: Choosing the Right Compute Option

AWS offers multiple compute services for running application code. Choosing the right one depends on your workload characteristics, team expertise, and operational requirements. In [Module 09](../09-serverless-lambda/README.md), you built serverless applications with Lambda. Now you can compare Lambda with the container orchestration options.

| Feature | [Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html) | [Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html) | [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) |
|---------|------------|------------|------------|
| Orchestration | AWS-native (ECS scheduler) | Kubernetes (open-source) | Event-driven (no orchestration needed) |
| Unit of deployment | Container (task definition) | Container (Pod spec) | Function (code package) |
| Max execution time | No limit (long-running services) | No limit (long-running services) | 15 minutes |
| Scaling granularity | Task level | Pod level | Function invocation level |
| Cold start | Seconds (container pull and start) | Seconds (container pull and start) | Milliseconds to seconds |
| Pricing model | Per task (Fargate) or per instance (EC2) | Per Pod (Fargate) or per instance (EC2) | Per invocation and duration |
| Portability | AWS-specific | Kubernetes-portable across clouds | AWS-specific |
| Operational complexity | Low to medium | High (Kubernetes expertise required) | Very low |
| Best for | Containerized web services, APIs, background workers | Teams with Kubernetes expertise, multi-cloud strategy, complex scheduling needs | Event-driven processing, APIs with variable traffic, short-duration tasks |

#### When to Use Each

**Choose ECS when:**

- You want a managed container orchestration service without the complexity of Kubernetes.
- Your team is already using AWS services and wants tight integration with the AWS ecosystem.
- You are running long-running web services, APIs, or background workers in containers.

**Choose EKS when:**

- Your team has existing Kubernetes expertise and tooling.
- You need portability across cloud providers or on-premises environments.
- You require advanced scheduling features, custom controllers, or the Kubernetes ecosystem of tools (Helm, Istio, Argo).

**Choose Lambda when:**

- Your workload is event-driven (responding to S3 uploads, API requests, queue messages).
- Individual executions complete within 15 minutes.
- Traffic is highly variable or unpredictable, with periods of zero traffic.
- You want to minimize operational overhead completely.

> **Tip:** These services are not mutually exclusive. Many production architectures combine them. For example, you might use ECS for your core web application, Lambda for event-driven processing (such as image resizing on S3 upload), and EKS for workloads that your team already manages with Kubernetes.

### Container Security Best Practices

Running containers in production requires attention to security at every layer: the image, the runtime, and the orchestration platform. The [ECS security best practices guide](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security-tasks-containers.html) provides detailed recommendations.

#### Run as a Non-Root User

By default, containers run as the root user. If an attacker exploits a vulnerability in your application, they gain root access inside the container. To limit the blast radius, create a non-root user in your Dockerfile and switch to it:

```dockerfile
# Create a non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Switch to the non-root user
USER appuser
```

#### Scan Images for Vulnerabilities

[Amazon ECR image scanning](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-scanning.html) identifies software vulnerabilities in your container images. ECR supports two scanning types:

- **Basic scanning.** Uses the Common Vulnerabilities and Exposures (CVE) database to scan for OS package vulnerabilities. You can configure scan-on-push to automatically scan every image when it is pushed to a repository.
- **Enhanced scanning.** Uses Amazon Inspector to continuously monitor images for both OS and programming language package vulnerabilities. Enhanced scanning provides more comprehensive results and continuous monitoring.

> **Warning:** Image scanning identifies known vulnerabilities but does not guarantee that your image is secure. Combine scanning with other practices such as using minimal base images, keeping dependencies updated, and performing static code analysis.

#### Secrets Management

Never store sensitive values (database passwords, API keys, tokens) in your container images or as plain-text environment variables in task definitions. Instead, use one of these approaches:

- **AWS Secrets Manager.** Store secrets in [Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) and reference them in your task definition using the `secrets` field. ECS injects the secret value as an environment variable at runtime.
- **AWS Systems Manager Parameter Store.** Store configuration values in [Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) as SecureString parameters. Reference them in your task definition the same way as Secrets Manager values.

Both approaches require the task execution role to have permission to read the secrets.

#### Use Read-Only File Systems

Configure your containers with a [read-only root file system](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security-tasks-containers.html) to prevent attackers from writing malicious files. In your task definition, set `readonlyRootFilesystem` to `true`. If your application needs to write temporary files, mount a writable volume at a specific path (such as `/tmp`).

#### Keep Images Minimal

Start from minimal base images such as Alpine Linux or distroless images. Smaller images have fewer packages, which means fewer potential vulnerabilities. Remove build tools, package caches, and temporary files in the same Dockerfile layer where they are created.

#### Summary of Container Security Practices

| Practice | Implementation | Benefit |
|----------|---------------|---------|
| Non-root user | `USER appuser` in Dockerfile | Limits privilege escalation |
| Image scanning | ECR scan-on-push or enhanced scanning | Detects known vulnerabilities |
| Secrets management | Secrets Manager or Parameter Store references in task definition | Prevents credential exposure |
| Read-only file system | `readonlyRootFilesystem: true` in task definition | Prevents file system tampering |
| Minimal base images | Use Alpine or distroless base images | Reduces attack surface |
| Immutable tags | Use image digest or unique tags (not `latest`) | Ensures deployment consistency |

## Instructor Notes

**Estimated lecture time:** 90 minutes

**Common student questions:**

- Q: What is the difference between the task execution role and the task role?
  A: The task execution role is used by the ECS agent (the infrastructure layer) to perform actions such as pulling container images from ECR, writing logs to CloudWatch, and retrieving secrets from Secrets Manager. The task role is used by your application code running inside the container to access AWS services such as S3, DynamoDB, or SQS. Think of the execution role as "what ECS needs to set up the task" and the task role as "what your application needs to do its job." See the [task execution role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html) and [task role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) documentation for details.

- Q: When should I use Fargate versus the EC2 launch type?
  A: Start with Fargate for most workloads. It eliminates the need to manage EC2 instances, patch operating systems, and configure Auto Scaling groups for the underlying infrastructure. Use the EC2 launch type when you need GPU instances, when you have large steady-state workloads where Reserved Instances significantly reduce costs, or when you need custom OS-level configuration. See the [Fargate documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html) for supported configurations.

- Q: How does ECS know when to replace a failed task?
  A: ECS monitors task health through multiple mechanisms. First, if a container exits (crashes), ECS detects the stopped task and launches a replacement. Second, if you configure an ALB health check, the load balancer marks unhealthy tasks, and the ECS service scheduler replaces them. Third, the deployment circuit breaker detects when new tasks repeatedly fail to start and can automatically roll back the deployment. See the [ECS service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html) documentation for details on the service scheduler.

- Q: Can I run both Fargate and EC2 tasks in the same cluster?
  A: Yes. An ECS cluster can use capacity providers to mix Fargate and EC2 launch types. You can configure a capacity provider strategy that spreads tasks across both. This is useful when most of your workloads run on Fargate but a few require EC2 (for example, GPU tasks). See the [capacity providers](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/clusters.html) section of the cluster documentation.

**Teaching tips:**

- Start by connecting containers to the EC2 concepts from Module 04. Draw an EC2 instance on the whiteboard, then show how multiple containers run inside it, sharing the OS kernel. Compare this to running multiple separate EC2 instances, each with its own OS. This visual helps students understand the efficiency gain of containers.
- When explaining the ECS component hierarchy (cluster, service, task definition, task), use an analogy: the cluster is a factory, the task definition is a blueprint for a product, the service is the production line that ensures a certain number of products are always being made, and tasks are the individual products rolling off the line.
- Walk through the example task definition JSON field by field. Ask students to identify which fields correspond to concepts they already know (IAM roles from Module 02, port mappings from Module 03, log groups from CloudWatch). This reinforces cross-module connections.
- For the Fargate vs. EC2 comparison, present scenarios and ask students to choose: "Your startup has 3 developers and needs to deploy 5 microservices. Which launch type?" (Fargate, to minimize ops burden.) "Your company runs a machine learning pipeline that needs GPU access. Which launch type?" (EC2, because Fargate does not support GPUs.)

**Pause points:**

- After Containers 101: ask students to name three advantages of containers over VMs (faster startup, smaller size, higher density, portability). Then ask for a scenario where a VM is still the better choice (legacy application requiring a specific OS kernel version, or workloads needing full hardware isolation).
- After the task definition walkthrough: ask students what would happen if you set `essential: true` on a sidecar container and it crashes (answer: the entire task stops, because an essential container failure stops the task).
- After the Fargate vs. EC2 comparison: present a cost scenario. "You run 10 tasks 24/7 on Fargate at 0.25 vCPU and 0.5 GB each. Would it be cheaper on EC2 with a Reserved Instance?" (Likely yes for steady-state workloads, because a single `t3.medium` Reserved Instance could host all 10 tasks at a lower hourly rate.)
- After the ECS vs. EKS vs. Lambda comparison: ask students which service they would choose for a webhook handler that processes 100 requests per day and takes 2 seconds per request (answer: Lambda, because the traffic is low and sporadic, and each execution is short).

## Key Takeaways

- Containers package applications with their dependencies for consistent deployment across environments. They are lighter and faster than VMs, sharing the host OS kernel instead of running a separate guest OS.
- Amazon ECS orchestrates containers using four core concepts: clusters (where tasks run), task definitions (how tasks are configured), services (how many tasks to maintain), and tasks (running instances of a task definition).
- AWS Fargate removes the need to manage EC2 instances for container workloads. Start with Fargate for most use cases and move to the EC2 launch type only when you need GPU support, custom OS configuration, or cost optimization at scale with Reserved Instances.
- ECS integrates with ALB for load balancing, with service auto scaling for demand-based capacity, and with ECR for secure image storage. Use lifecycle policies to clean up old images and scan-on-push to detect vulnerabilities.
- Follow container security best practices: run as a non-root user, scan images for vulnerabilities, manage secrets through Secrets Manager or Parameter Store (never in images or plain-text environment variables), and use read-only file systems where possible.
