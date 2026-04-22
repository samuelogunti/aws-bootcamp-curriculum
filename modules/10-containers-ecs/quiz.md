# Module 10: Quiz

1. Which of the following correctly describes a key difference between containers and virtual machines?

   A) Containers include a full guest operating system, while virtual machines share the host kernel
   B) Containers share the host operating system kernel and isolate processes using namespaces and cgroups, while virtual machines run a separate guest OS per instance
   C) Virtual machines start in seconds, while containers take minutes to boot
   D) Containers provide hardware-level isolation, while virtual machines provide process-level isolation

2. True or False: In a Dockerfile, placing the `COPY package.json ./` and `RUN npm install` instructions before `COPY . .` allows Docker to cache the dependency installation layer and skip it when only the application source code changes.

3. A developer needs to push a locally built Docker image to Amazon Elastic Container Registry (Amazon ECR). Place the following steps in the correct order:

   A) Tag the local image with the ECR repository URI
   B) Authenticate Docker to the ECR registry using `aws ecr get-login-password`
   C) Push the image using `docker push`
   D) Create an ECR repository (if it does not exist)

4. Which of the following are valid rules you can define in an ECR lifecycle policy? (Select TWO.)

   A) Expire untagged images older than a specified number of days
   B) Automatically rebuild images when a new base image is available
   C) Keep only the N most recent images matching a tag prefix
   D) Trigger a Lambda function when an image is pushed
   E) Replicate images to a different AWS Region on push

5. In Amazon ECS, what is the relationship between a task definition and a task?

   A) A task definition is a running container, and a task is the JSON configuration file
   B) A task definition is a versioned blueprint that specifies container configuration, and a task is a running instance of that blueprint
   C) A task and a task definition are the same thing; the terms are interchangeable
   D) A task definition defines the ECS cluster, and a task defines the service

6. A team is configuring a Fargate task definition for a web application. Which of the following parameters must they specify at the task level for Fargate compatibility? (Select TWO.)

   A) The host port for dynamic port mapping
   B) The total CPU allocation (for example, 256 or 512)
   C) The EC2 instance type for the underlying host
   D) The total memory allocation (for example, 512 or 1024)
   E) The Auto Scaling group for the cluster

7. Your company runs a containerized microservices application on Amazon ECS. The development team wants to minimize infrastructure management, avoid patching operating systems, and scale individual services independently. The application does not require GPU access or custom kernel configuration. Which launch type should the team choose, and why?

   A) EC2 launch type, because it provides more control over the underlying instances
   B) Fargate launch type, because it removes the need to provision and manage EC2 instances, and each task scales independently with its own ENI
   C) Fargate launch type, because it supports GPU workloads and custom AMIs
   D) EC2 launch type, because Fargate does not support the awsvpc network mode

8. An ECS service is configured with a rolling update deployment, `minimumHealthyPercent` set to 50, and `maximumPercent` set to 200. The service has a desired count of 4 tasks. During a deployment, what is the maximum number of tasks that can be running at any point, and what is the minimum number of old tasks that must remain healthy before new tasks start?

9. A startup deploys a containerized API on Amazon ECS with Fargate behind an Application Load Balancer. The ALB target group is configured with target type `instance` and health checks on port 80. After deployment, the ALB reports all targets as unhealthy, and no traffic reaches the containers. The containers are confirmed to be running and listening on port 3000. Which TWO configuration errors are most likely causing this issue?

   A) The target group target type should be `ip` instead of `instance`, because Fargate tasks use the `awsvpc` network mode and register by IP address
   B) The health check should target port 3000 (or the application's health endpoint) instead of port 80, because the containers are not listening on port 80
   C) The ECS service desired count is set too low
   D) The ALB listener protocol should be changed from HTTP to HTTPS
   E) The ECS cluster does not have enough registered container instances

10. A company is evaluating AWS compute services for three different workloads: (1) a long-running web API that serves thousands of requests per minute and needs consistent performance, (2) a batch job that processes uploaded images and requires GPU acceleration, and (3) a webhook handler that receives 50 requests per day, each completing in under 2 seconds. Which combination of services best fits these requirements?

    A) ECS with Fargate for all three workloads
    B) ECS with Fargate for the web API, ECS with EC2 launch type (GPU instances) for the batch job, and AWS Lambda for the webhook handler
    C) Amazon EKS for all three workloads
    D) AWS Lambda for the web API, ECS with Fargate for the batch job, and ECS with EC2 launch type for the webhook handler

---

<details>
<summary>Answer Key</summary>

1. **B) Containers share the host operating system kernel and isolate processes using namespaces and cgroups, while virtual machines run a separate guest OS per instance**
   Containers virtualize the operating system rather than the hardware. They share the host kernel and use Linux kernel features (namespaces for isolation, cgroups for resource limits) to separate processes. Virtual machines virtualize the hardware and run a complete guest OS per instance, providing hardware-level isolation. This makes containers smaller (megabytes vs. gigabytes), faster to start (seconds vs. minutes), and more dense (hundreds per host vs. tens per host).
   Further reading: [Creating a container image for use on Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-container-image.html)

2. **True.**
   Docker builds images layer by layer. Each instruction in a Dockerfile creates a layer. If the files involved in a layer have not changed since the last build, Docker reuses the cached layer instead of rebuilding it. By copying the dependency manifest (`package.json`) and running `npm install` before copying the full application source, you ensure that the dependency layer is only rebuilt when `package.json` changes. If only the application code changes, Docker reuses the cached dependency layer, which significantly speeds up builds.
   Further reading: [Creating a container image for use on Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-container-image.html)

3. **Correct order: D, B, A, C**
   First, create the ECR repository if it does not already exist (D). Then authenticate Docker to the ECR registry so that Docker has permission to push images (B). Next, tag the local image with the full ECR repository URI so Docker knows where to push it (A). Finally, push the tagged image to ECR (C). If you skip the authentication step, the push will fail with an authorization error. If you skip the tagging step, Docker will not know which ECR repository to target.
   Further reading: [What is Amazon ECR?](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)

4. **A, C**
   ECR lifecycle policies support rules that expire images based on age (for example, expire untagged images older than 7 days) and rules that expire images based on count (for example, keep only the 10 most recent images matching a tag prefix such as `prod-`). Lifecycle policies do not trigger Lambda functions (D), that is an ECR event rule via EventBridge. Automatic image rebuilds (B) and cross-Region replication (E) are separate ECR features, not lifecycle policy rules.
   Further reading: [Automate the cleanup of images by using lifecycle policies in Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html)

5. **B) A task definition is a versioned blueprint that specifies container configuration, and a task is a running instance of that blueprint**
   A task definition is a JSON document that describes one or more containers: which images to use, CPU and memory allocations, port mappings, environment variables, IAM roles, and logging configuration. Task definitions are versioned; each update creates a new revision. A task is a running instance of a task definition. When ECS launches a task, it pulls the specified container images, creates the containers, and starts them. An ECS service maintains a desired number of tasks running from a specific task definition revision.
   Further reading: [Amazon ECS task definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)

6. **B, D**
   Fargate tasks require you to specify the total CPU and memory at the task level. These values determine the compute resources allocated to the task and must be chosen from specific valid combinations (for example, 256 CPU units with 512 MB memory). Fargate does not use EC2 instances that you manage (C and E are irrelevant). Dynamic host port mapping (A) applies to the EC2 launch type, not Fargate; with Fargate, each task gets its own ENI, so the host port equals the container port.
   Further reading: [Amazon ECS task definition differences for Fargate](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/fargate-tasks-services.html)

7. **B) Fargate launch type, because it removes the need to provision and manage EC2 instances, and each task scales independently with its own ENI**
   Fargate is the right choice when the team wants to minimize operational overhead. With Fargate, AWS manages the underlying infrastructure: no EC2 instances to provision, no operating systems to patch, and no Auto Scaling groups to configure for the compute layer. Each Fargate task gets its own elastic network interface (ENI) and can scale independently. The application does not need GPU access (which Fargate does not support) or custom kernel configuration (which requires EC2), so there is no reason to choose the EC2 launch type. Option C is incorrect because Fargate does not support GPUs or custom AMIs. Option D is incorrect because Fargate requires and fully supports the awsvpc network mode.
   Further reading: [Architect for AWS Fargate for Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html)

8. **Maximum running tasks: 8. Minimum healthy old tasks: 2.**
   With a desired count of 4, `maximumPercent` of 200% means ECS can run up to 200% of 4 = 8 tasks simultaneously during the deployment. This allows ECS to start new tasks before stopping old ones, enabling zero-downtime deployments. The `minimumHealthyPercent` of 50% means at least 50% of 4 = 2 tasks must remain in a healthy state at all times during the deployment. ECS will not stop more than 2 old tasks until replacement tasks are running and healthy. These two parameters together control the pace of the rolling update: higher `maximumPercent` allows more parallelism, and higher `minimumHealthyPercent` provides more safety.
   Further reading: [Deploy Amazon ECS services by replacing tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html)

9. **A, B**
   Two configuration errors are causing the issue. First, Fargate tasks use the `awsvpc` network mode, which assigns each task its own private IP address. The ALB target group must use target type `ip` to register these tasks by their IP addresses (A). Using target type `instance` expects EC2 instance IDs, which do not exist with Fargate. Second, the health check is configured on port 80, but the containers listen on port 3000 (B). The ALB health check must target the port where the application is actually listening (or a dedicated health endpoint on that port, such as `/health` on port 3000). The desired count (C) is not the issue since the containers are confirmed running. Changing to HTTPS (D) is unrelated to the health check failure. Fargate does not use registered container instances (E).
   Further reading: [Use an Application Load Balancer for Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/alb.html), [Health checks for Application Load Balancer target groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html)

10. **B) ECS with Fargate for the web API, ECS with EC2 launch type (GPU instances) for the batch job, and AWS Lambda for the webhook handler**
    Each workload has different characteristics that map to a specific compute service. The long-running web API needs consistent performance and handles high traffic, making ECS with Fargate a good fit (managed infrastructure, per-task scaling, no execution time limit). The image processing batch job requires GPU acceleration, which Fargate does not support, so ECS with the EC2 launch type using P or G instance families is necessary. The webhook handler receives only 50 requests per day with sub-2-second execution times, making it an ideal Lambda use case (scales to zero when idle, pay-per-invocation pricing, millisecond billing). Option A fails because Fargate does not support GPUs. Option C adds unnecessary Kubernetes complexity. Option D mismatches Lambda (15-minute limit, not ideal for sustained high-traffic APIs) with the web API workload.
    Further reading: [What is Amazon ECS?](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html), [What is Amazon EKS?](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html), [What is AWS Lambda?](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)

</details>

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
