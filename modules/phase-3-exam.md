# Phase 3 Exam: Building Applications

## Exam Information

| Field | Details |
|-------|---------|
| Phase | Phase 3: Building Applications |
| Modules Covered | Module 09 (Serverless Computing with AWS Lambda), Module 10 (Containers and Amazon ECS), Module 11 (Infrastructure as Code with CloudFormation, SAM, and CDK), Module 12 (CI/CD Pipelines) |
| Estimated Duration | 60 to 90 minutes |
| Passing Score | 70% |
| Total Questions | 25 |
| Question Types | Multiple choice (single correct), multiple choice (multiple correct), scenario-based, ordering/sequencing |

> **Tip:** Read each question carefully. For questions that say "select TWO" or "select THREE," you must choose the exact number of answers specified. Partial credit is not awarded.

---

## Questions

**Question 1**

A development team is building a serverless API that receives JSON payloads from a mobile application, validates the input, and stores the data in a DynamoDB table. The team wants to minimize operational overhead and avoid managing any servers. Which combination of AWS services should the team use to build this API?

A. Amazon EC2 instances behind an Application Load Balancer, with application code that writes to DynamoDB.

B. Amazon API Gateway integrated with AWS Lambda, where the Lambda function validates input and writes to DynamoDB.

C. Amazon CloudFront with an S3 origin, using S3 event notifications to trigger a Lambda function that writes to DynamoDB.

D. AWS App Runner with a containerized application that connects to DynamoDB.

---

**Question 2**

A Lambda function processes messages from an SQS queue. The function takes an average of 45 seconds to process each message. The operations team notices that some messages are being processed more than once. Which configuration change is most likely to resolve the duplicate processing?

A. Increase the Lambda function timeout to 60 seconds.

B. Increase the SQS queue visibility timeout to a value greater than the Lambda function timeout (for example, six times the function timeout).

C. Enable long polling on the SQS queue with a 20-second wait time.

D. Switch the SQS queue from Standard to FIFO to guarantee exactly-once processing.

---

**Question 3**

A solutions architect is designing a containerized application that must run on AWS. The application consists of 15 microservices, each with different CPU and memory requirements. The team does not want to manage EC2 instances, patch operating systems, or handle cluster capacity planning. Which ECS launch type should the architect recommend?

A. EC2 launch type, because it provides full control over the underlying instances and supports all ECS features.

B. Fargate launch type, because it eliminates the need to provision, configure, or scale EC2 instances and manages the compute infrastructure automatically.

C. External launch type, because it allows running ECS tasks on on-premises servers.

D. EC2 launch type with Auto Scaling, because Auto Scaling handles all capacity management automatically.

---

**Question 4**

A team is deploying a CloudFormation stack that creates a VPC, subnets, an RDS instance, and a Lambda function. The stack creation fails because the RDS instance cannot be created (the specified instance class is not available in the selected Availability Zone). What happens to the resources that were successfully created before the failure?

A. All successfully created resources remain in place, and the stack enters the CREATE_FAILED state. You must manually delete the resources.

B. CloudFormation automatically rolls back the entire stack, deleting all resources that were created, and the stack enters the ROLLBACK_COMPLETE state.

C. CloudFormation pauses the stack creation and waits for you to fix the error before continuing.

D. CloudFormation creates a new stack with only the failed resource and retries the creation.

---

**Question 5**

A company runs a web application on Amazon ECS with the Fargate launch type. The application experiences variable traffic throughout the day. During peak hours, the current number of tasks cannot handle the load, resulting in slow response times. Which TWO actions should the team take to handle traffic spikes automatically? (Select TWO.)

A. Configure ECS Service Auto Scaling with a target tracking policy based on average CPU utilization.

B. Manually increase the desired count of tasks in the ECS service before each peak period.

C. Switch from Fargate to the EC2 launch type, because EC2 instances scale faster than Fargate tasks.

D. Place an Application Load Balancer in front of the ECS service to distribute traffic across tasks.

E. Configure ECS Service Auto Scaling with a step scaling policy based on a custom CloudWatch metric for request count per target.

---

**Question 6**

A developer is writing a buildspec.yml file for AWS CodeBuild. The build must install Node.js 20, run linting and unit tests before the main build, compile the application, and then push a Docker image to Amazon ECR after the build completes. In which buildspec phase should the developer place the `docker push` command?

A. `install`, because Docker commands should run during the installation phase.

B. `pre_build`, because pushing the image must happen before the main build.

C. `build`, because all compilation and image operations belong in the build phase.

D. `post_build`, because pushing the Docker image is a task that runs after the main build completes.

---

**Question 7**

A solutions architect needs to deploy the same infrastructure (VPC, subnets, security groups, EC2 instances) across three AWS accounts: development, staging, and production. The infrastructure must be identical in structure but use different instance types and CIDR ranges per environment. Which CloudFormation feature allows the architect to use a single template for all three environments?

A. Nested stacks, which allow breaking a large template into smaller, reusable components.

B. Parameters, which allow passing environment-specific values (such as instance type and CIDR range) at stack creation time.

C. Mappings, which require hardcoding all environment values in the template and selecting them with `!FindInMap`.

D. Stack policies, which control which resources can be updated in each environment.

---

**Question 8**

A team is deploying a mission-critical e-commerce application to production. The team wants a deployment strategy that allows them to test the new version with a small percentage of real production traffic before routing all users to it. If the new version shows elevated error rates, the deployment must roll back automatically. Which deployment strategy meets these requirements?

A. All-at-once deployment, because it is the fastest way to get the new version into production.

B. Rolling deployment, because it updates instances in batches and maintains partial availability.

C. Blue/green deployment, because it creates a complete copy of the production environment for testing.

D. Canary deployment, because it routes a small percentage of traffic to the new version first and supports automatic rollback based on CloudWatch alarms.

---

**Question 9**

A developer is troubleshooting a Lambda function that processes S3 event notifications. The function is supposed to generate thumbnails when images are uploaded to an S3 bucket. The function works correctly when tested manually with a test event in the Lambda console, but it does not trigger when images are uploaded to the bucket. Which TWO areas should the developer investigate? (Select TWO.)

A. Verify that the S3 bucket has an event notification configuration that specifies the Lambda function as the destination for `s3:ObjectCreated:*` events.

B. Verify that the Lambda function's resource-based policy grants the S3 service (`s3.amazonaws.com`) permission to invoke the function.

C. Increase the Lambda function's memory allocation, because insufficient memory prevents the function from starting.

D. Change the Lambda function runtime from Python to Node.js, because S3 event notifications only work with Node.js functions.

E. Verify that the Lambda function's execution role has permission to read objects from the S3 bucket.

---

**Question 10**

A team manages infrastructure using CloudFormation. They need to update a production stack to change the instance type of an EC2 instance from `t3.medium` to `t3.large`. Before applying the change, the team wants to preview exactly which resources will be modified and whether any resources will be replaced. Which CloudFormation feature should the team use?

A. Stack drift detection, which compares the current stack resources to the template.

B. Change sets, which preview the proposed changes to a stack before executing the update.

C. Stack policies, which prevent accidental updates to critical resources.

D. Rollback triggers, which automatically roll back updates that cause CloudWatch alarms.

---

**Question 11**

A company is building a microservices application. One service processes payment transactions and must handle exactly 50 requests per second with consistent sub-100ms latency. The service runs continuously and has predictable, steady traffic. Another service generates monthly reports and runs for 10 minutes once per month. Which compute strategy best fits both workloads?

A. Run both services on EC2 instances in an Auto Scaling group.

B. Run both services as Lambda functions to minimize operational overhead.

C. Run the payment service on ECS with Fargate (or EC2) for consistent performance, and run the reporting service as a Lambda function for cost efficiency.

D. Run both services on ECS with the EC2 launch type to maximize control over the compute environment.

---

**Question 12**

A developer is creating a CloudFormation template that provisions a Lambda function and a DynamoDB table. The Lambda function needs the DynamoDB table name as an environment variable. Which CloudFormation intrinsic function should the developer use to reference the table name dynamically in the template?

A. `!Sub` to substitute the table's logical name into a string.

B. `!Ref` to return the DynamoDB table's resource name (which is the table name for `AWS::DynamoDB::Table`).

C. `!GetAtt` to retrieve the table's `StreamArn` attribute.

D. `!Join` to concatenate the account ID and a hardcoded table name.

---

**Question 13**

A solutions architect is designing a CI/CD pipeline for a web application. The pipeline must pull code from GitHub, build and test the application, deploy to a staging environment, wait for manual approval, and then deploy to production. Which pipeline structure correctly implements this workflow in AWS CodePipeline?

A. Source (GitHub) -> Build (CodeBuild) -> Deploy (CodeDeploy to staging) -> Deploy (CodeDeploy to production). No approval stage is needed because CodeDeploy handles rollback automatically.

B. Source (GitHub) -> Build (CodeBuild) -> Test (CodeBuild) -> Deploy (CodeDeploy to staging) -> Approval (manual approval action) -> Deploy (CodeDeploy to production).

C. Source (GitHub) -> Approval (manual approval action) -> Build (CodeBuild) -> Deploy (CodeDeploy to staging and production simultaneously).

D. Source (GitHub) -> Build (CodeBuild) -> Deploy (CodeDeploy to production) -> Test (CodeBuild) -> Approval (manual approval action).

---

**Question 14**

A Lambda function is configured with 128 MB of memory and a 3-second timeout. The function processes API Gateway requests and queries a DynamoDB table. During peak traffic, users report intermittent timeout errors. CloudWatch Logs show that the function occasionally takes 4 to 5 seconds to complete, and the "Max Memory Used" metric shows 125 MB. Which TWO changes should the developer make to resolve the issue? (Select TWO.)

A. Increase the function memory to 256 MB or higher, because Lambda allocates CPU proportionally to memory, which can reduce execution time.

B. Increase the function timeout to 10 seconds to accommodate the longer execution times.

C. Decrease the function memory to 64 MB to reduce cold start time.

D. Move the DynamoDB client initialization outside the handler function to reuse the connection across invocations.

E. Switch from DynamoDB to Amazon RDS, because RDS provides faster query performance for all workloads.

---

**Question 15**

A team is using AWS SAM to define a serverless application. The application consists of a Lambda function triggered by an API Gateway endpoint and a DynamoDB table. Which SAM resource type should the team use to define the Lambda function, and what is the advantage of using SAM over plain CloudFormation for this use case?

A. `AWS::Lambda::Function`, because SAM uses the same resource types as CloudFormation with no additional abstractions.

B. `AWS::Serverless::Function`, because SAM provides a simplified syntax that automatically creates the Lambda function, API Gateway endpoint, and IAM execution role from a single resource definition.

C. `AWS::ECS::TaskDefinition`, because SAM deploys Lambda functions as containers on ECS.

D. `AWS::Serverless::Api`, because the API resource automatically creates both the API Gateway and the Lambda function.

---

**Question 16**

A company runs a containerized application on ECS with the Fargate launch type. The application consists of a web frontend and a backend API, each defined as a separate ECS service. The web frontend must be accessible from the internet, but the backend API must only accept traffic from the web frontend. Which networking configuration achieves this?

A. Place both services in public subnets with public IP addresses. Use security groups to restrict the backend API to accept traffic only from the frontend's security group.

B. Place the web frontend in a public subnet with an ALB and the backend API in a private subnet. Configure the backend API's security group to allow traffic only from the frontend's security group.

C. Place both services in private subnets and use a NAT gateway for all internet traffic.

D. Place both services in the same subnet and use IAM policies to control which service can communicate with the other.

---

**Question 17**

Place the following steps in the correct order for how AWS CodePipeline processes a code change from a developer's push to production deployment.

1. CodeDeploy deploys the build artifact to the production environment using the configured deployment strategy.
2. The source stage detects a new commit on the configured branch and downloads the source code as an artifact.
3. A manual approval action pauses the pipeline and sends an SNS notification to the reviewer.
4. CodeBuild compiles the code, runs tests, and produces a build artifact.

A. 2, 4, 3, 1

B. 4, 2, 1, 3

C. 2, 3, 4, 1

D. 1, 2, 4, 3

---

**Question 18**

A developer is building a Lambda function that processes uploaded CSV files from S3. Each file contains 500,000 rows and takes approximately 8 minutes to process. The developer configures the Lambda function with the maximum timeout of 15 minutes. After deployment, the function works for small files but fails with a "Task timed out" error for large files. CloudWatch metrics show the function uses 2,800 MB of memory out of the 3,008 MB allocated. Which approach should the developer take to resolve this?

A. Increase the Lambda function memory to 10,240 MB, because Lambda allocates more CPU with more memory, which will speed up processing enough to complete within 15 minutes.

B. Split the CSV processing into smaller chunks using AWS Step Functions, where each step processes a portion of the file and passes the state to the next step.

C. Switch from Lambda to an ECS Fargate task, because Fargate tasks have no execution time limit and can handle long-running processing workloads.

D. Enable provisioned concurrency on the Lambda function to eliminate cold starts and reduce total execution time.

---

**Question 19**

A team is managing a CloudFormation stack that includes an RDS database instance. The team needs to update the stack to change the database engine version, which requires replacing the RDS instance. The team wants to prevent accidental deletion of the database during stack updates. Which CloudFormation feature should the team use?

A. Stack policies, which define update rules that prevent CloudFormation from replacing or deleting specific resources during stack updates.

B. Termination protection, which prevents the entire stack from being deleted.

C. DeletionPolicy attribute set to `Retain`, which keeps the resource when the stack is deleted but does not prevent replacement during updates.

D. Change sets, which preview changes but do not prevent any specific update action.

---

**Question 20**

A company is migrating from manual deployments to CI/CD. The team currently deploys a Node.js application by SSHing into EC2 instances and running scripts manually. They want to automate this process using AWS CodeDeploy. Which file must the team add to their application source code to tell CodeDeploy where to copy files and which scripts to run during deployment?

A. buildspec.yml, which defines the build phases and artifact locations for CodeBuild.

B. appspec.yml, which defines the file mappings and lifecycle hook scripts that CodeDeploy executes during deployment.

C. template.yaml, which defines the infrastructure resources for CloudFormation.

D. Dockerfile, which defines the container image build instructions.

---

**Question 21**

A solutions architect is comparing AWS CloudFormation, AWS SAM, and AWS CDK for a new project. The project involves a complex application with 50+ resources, conditional logic for multi-environment deployments, and the development team is proficient in TypeScript. Which IaC tool is the best fit for this project, and why?

A. CloudFormation, because it supports the most resource types and has the longest track record.

B. AWS SAM, because it simplifies serverless resource definitions and reduces template size.

C. AWS CDK with TypeScript, because it allows the team to use familiar programming constructs (loops, conditionals, abstractions) to define infrastructure, which is more maintainable for complex applications than YAML templates.

D. Terraform, because it supports multi-cloud deployments and has a larger community than any AWS-native tool.

---

**Question 22**

A Lambda function is triggered by an API Gateway REST API. The function connects to an RDS PostgreSQL database in a private subnet. After deploying the function, all API requests return a timeout error. The function's CloudWatch Logs show no invocation records. Which TWO configuration issues should the developer investigate? (Select TWO.)

A. The Lambda function is not configured with a VPC, so it cannot reach the RDS instance in the private subnet.

B. The security group attached to the RDS instance does not allow inbound traffic from the Lambda function's security group on the PostgreSQL port (5432).

C. The Lambda function's execution role does not have the `rds:Connect` IAM permission.

D. The API Gateway endpoint is configured as a private endpoint and is not accessible from the internet.

E. The Lambda function's timeout is set to 3 seconds, which is too short for establishing a database connection.

---

**Question 23**

A team is building a Docker image for an ECS task. The Dockerfile installs build tools (gcc, make), compiles a C extension for a Python application, and then runs the application. The resulting image is 1.2 GB. The team wants to reduce the image size without removing the C extension. Which approach should the team use?

A. Use a smaller base image such as `python:3.12-alpine` instead of `python:3.12`.

B. Use a multi-stage Docker build where the first stage compiles the C extension and the second stage copies only the compiled artifacts into a minimal runtime image.

C. Compress the Docker image using `docker save` and `gzip` before pushing to ECR.

D. Remove the C extension and rewrite the code in pure Python to eliminate the need for build tools.

---

**Question 24**

A company has a CodePipeline that deploys a Lambda function using CloudFormation. The pipeline has been working correctly, but after a recent code change, the deploy stage fails with the error: "UPDATE_ROLLBACK_COMPLETE." The developer checks the CloudFormation events and sees that the Lambda function's code package exceeds the 250 MB unzipped deployment package limit. Which approach should the developer take to resolve this while keeping the pipeline functional?

A. Increase the Lambda deployment package size limit by contacting AWS Support.

B. Package the Lambda function as a container image (up to 10 GB) instead of a .zip archive, update the CloudFormation template to reference the ECR image URI, and update the pipeline to build and push the container image.

C. Split the Lambda function into two smaller functions that each stay under the 250 MB limit.

D. Move the Lambda function code to an S3 bucket and have the function download its dependencies at runtime.

---

**Question 25**

A company is designing a deployment architecture for a microservices application running on Amazon ECS. The application serves real-time financial data and cannot tolerate any downtime during deployments. The team also needs the ability to instantly roll back to the previous version if the new deployment causes errors. The team uses CodePipeline with CodeDeploy for deployments. Which deployment configuration and architecture should the team implement? (Select THREE.)

A. Configure CodeDeploy with a blue/green deployment type for the ECS service, which creates a new task set running the updated version alongside the existing task set.

B. Configure CodeDeploy with an all-at-once in-place deployment to minimize deployment time.

C. Use an Application Load Balancer with two target groups (blue and green) so CodeDeploy can shift traffic between the old and new task sets.

D. Configure a CodeDeploy deployment configuration that shifts 10% of traffic to the new version initially (canary), then shifts the remaining 90% after a validation period.

E. Remove the ALB and have clients connect directly to ECS task IP addresses for the lowest latency.

F. Use a single target group and replace tasks in-place using a rolling update strategy.

---

<details>
<summary>Answer Key</summary>

### Question 1

**Correct Answers: A, C**

Lambda allocates CPU proportionally to memory. At 128 MB, the function receives minimal CPU, which makes image processing extremely slow. Increasing memory to 1024 MB or higher (A) provides significantly more CPU, speeding up the resize operation. Additionally, the 3-second default timeout (C) is far too short for processing 10 MB images. Increasing the timeout to 60 seconds or more gives the function enough time to complete.

- B is incorrect because changing the event notification to use SNS does not increase the Lambda function's execution time or CPU. SNS simply invokes the Lambda function asynchronously, which does not change the function's resource allocation.
- D is incorrect because Lambda Layers provide shared code and dependencies. They do not run in a separate execution environment or provide additional resources. The function's memory and timeout settings determine its available resources.
- E is incorrect because runtime performance depends on the specific workload and libraries used, not the language alone. Python with optimized image processing libraries (such as Pillow) can process images efficiently when given adequate memory and CPU.

Reference: [Configure Lambda Function Memory](https://docs.aws.amazon.com/lambda/latest/dg/configuration-memory.html), [Configure Lambda Function Timeout](https://docs.aws.amazon.com/lambda/latest/dg/configuration-timeout.html)

---

### Question 2

**Correct Answer: B**

Reserved concurrency serves two purposes: it guarantees a minimum number of concurrent execution environments for the function, and it caps the maximum concurrency. If 50 concurrent executions are insufficient for peak traffic, increasing the reserved concurrency allows more concurrent invocations. However, setting it too high could overwhelm DynamoDB if the table uses provisioned capacity. The correct approach is to increase reserved concurrency to match expected peak traffic while keeping it within the limits that DynamoDB can handle.

- A is incorrect because removing reserved concurrency entirely allows the function to scale to the account-level limit (typically 1,000 concurrent executions by default). This could send thousands of concurrent requests to DynamoDB, potentially exceeding its provisioned capacity and causing throttling at the database layer.
- C is incorrect because replacing DynamoDB with RDS does not solve the throttling issue. RDS has its own connection limits, and the problem is Lambda concurrency management, not the database choice.
- D is incorrect because adding an SQS queue between API Gateway and Lambda changes the API from synchronous to asynchronous. The client would no longer receive an immediate response with the processed data. SQS is appropriate for asynchronous workloads but not for synchronous API responses.

Reference: [Configuring Reserved Concurrency](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html)

---

### Question 3

**Correct Answer: B**

Fargate pricing is based on the vCPU and memory allocated to the task. The smallest configuration that meets the 512 MB memory requirement is 0.25 vCPU with 512 MB memory. This is the most cost-effective option because you pay only for the resources you allocate, and 0.25 vCPU is sufficient for a moderate-traffic Node.js application.

- A is incorrect because 1 vCPU and 2 GB memory provides four times the CPU and four times the memory needed. This over-provisioning increases cost without providing a proportional benefit for moderate traffic.
- C is incorrect because 0.25 vCPU does support 512 MB memory. The valid Fargate configurations for 0.25 vCPU are 0.5 GB, 1 GB, and 2 GB of memory.
- D is incorrect because 2 vCPU and 4 GB memory is significantly over-provisioned for a 512 MB application. Fargate does not require extra resources for container runtime overhead beyond what is specified in the task definition.

Reference: [Amazon ECS Task Definitions for Fargate](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/fargate-tasks-services.html)

---

### Question 4

**Correct Answer: B**

When a CloudFormation stack creation fails and rolls back completely, the stack enters the ROLLBACK_COMPLETE state. In this state, the stack cannot be updated. The only option is to delete the failed stack and create a new one with the corrected template or parameters. After creating the missing DB subnet group, the team must delete the ROLLBACK_COMPLETE stack and run `create-stack` again.

- A is incorrect because `update-stack` cannot be used on a stack in the ROLLBACK_COMPLETE state. This state indicates that the stack creation failed and all resources were rolled back. The stack has no successfully created resources to update.
- C is incorrect because `continue-update-rollback` is used when a stack update rollback fails (UPDATE_ROLLBACK_FAILED state), not when a stack creation fails. It does not apply to the ROLLBACK_COMPLETE state.
- D is incorrect because you cannot modify parameters of a stack in the ROLLBACK_COMPLETE state. The stack must be deleted and recreated.

Reference: [Choose How to Handle Failures When Provisioning Resources](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stack-failure-options.html)

---

### Question 5

**Correct Answer: D**

ECS service auto scaling with a target tracking policy on a latency-related metric (such as ALB target response time) automatically adds API service tasks when latency increases. This ensures the API service maintains performance even when the background job service is consuming resources. Fargate provides resource isolation at the task level (each task gets its own CPU and memory allocation), so the real issue is likely that the API service needs more tasks to handle the load, not that the background jobs are stealing resources.

- A is incorrect because moving to the EC2 launch type increases operational overhead (managing instances, patching, scaling) without directly solving the latency issue. Fargate already provides task-level resource isolation.
- B is incorrect because placing services in separate ECS clusters does not guarantee separate Fargate capacity. Fargate manages capacity transparently, and tasks in different clusters may still run on shared infrastructure. The isolation benefit of separate clusters is logical (organizational), not physical.
- C is incorrect because increasing the API service's task-level resources (CPU and memory) helps if individual tasks are resource-constrained, but it does not address the need for more task instances during high-load periods. Auto scaling is the appropriate solution for handling variable demand.

Reference: [Automatically Scale Your Amazon ECS Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html)

---

### Question 6

**Correct Answer: A**

The AWSLambdaBasicExecutionRole managed policy grants only CloudWatch Logs permissions (`logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`). It does not grant S3 access. The function needs an additional inline policy (or managed policy) that grants `s3:GetObject` permission on the specific bucket and object to read from S3.

- B is incorrect because AWSLambdaFullAccess is an overly permissive policy that violates the principle of least privilege. It grants broad access to many AWS services. The correct approach is to add only the specific S3 permissions the function needs.
- C is incorrect because adding the bucket ARN as an environment variable does not grant permissions. Environment variables pass configuration data to the function; they do not affect IAM authorization. The execution role's policies determine what AWS API calls the function can make.
- D is incorrect because the AssumeRolePolicyDocument defines which service can assume the role, not what the role can do. The Lambda service principal (`lambda.amazonaws.com`) must be the one assuming the role. Changing it to the S3 service principal would prevent Lambda from assuming the role entirely.

Reference: [Lambda Execution Role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)

---

### Question 7

**Correct Answer: B**

The SAM `Globals` section defines properties that apply to all resources of a given type. Setting `Runtime`, `Timeout`, and `Environment` under `Globals: Function:` applies these values to every `AWS::Serverless::Function` in the template. Individual functions can override any global property if needed. This eliminates repetition and ensures consistency across functions.

- A is incorrect because while parameters can hold values referenced by `!Ref`, they are designed for values that change between deployments (such as environment names or table names). Using parameters for static configuration like runtime and timeout adds unnecessary complexity compared to the Globals section.
- C is incorrect because combining three functions into one with multiple handlers creates a monolithic function that violates the single-responsibility principle. It also means all three functions share the same memory, timeout, and concurrency settings, which may not be appropriate.
- D is incorrect because `Mappings` are static lookup tables used for conditional value selection (such as choosing an AMI based on Region). They are not designed for defining shared function configuration. The Globals section is the SAM-specific feature built for this purpose.

Reference: [AWS SAM Template Anatomy](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html)

---

### Question 8

**Correct Answer: C**

The workload requires GPU access, persistent connections, long-running execution (days), and 4 GB of memory. Amazon ECS with the EC2 launch type using GPU-enabled instances (P or G family) is the only option that meets all four requirements. ECS on EC2 supports long-running services with no execution time limit, persistent WebSocket connections, and GPU instances for ML inference.

- A is incorrect because Lambda has a maximum execution time of 15 minutes, does not support persistent WebSocket connections (Lambda functions are stateless and short-lived), and does not provide GPU access.
- B is incorrect because Fargate does not support GPU instances. Fargate manages the underlying infrastructure, and GPU instance families (P and G) are not available as Fargate capacity.
- D is incorrect because Step Functions is a workflow orchestration service, not a compute service. Step Functions coordinates calls to other services (such as Lambda or ECS) but does not run application code directly. It also does not provide GPU access or persistent connections.

Reference: [Amazon ECS on EC2](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)

---

### Question 9

**Correct Answer: B**

The error message "Error while executing command: npm test. Reason: exit status 1" indicates that the `npm test` command ran but returned a non-zero exit code, which means one or more tests failed. CodeBuild treats any non-zero exit code as a failure and stops the build. The team should examine the test output in the CodeBuild logs to identify which tests failed and fix them.

- A is incorrect because the source stage (not the build stage) handles repository access. If CodeBuild could not access the source code, the error would occur during artifact download, not during command execution. The error message shows that `npm test` executed, which means the source code was available.
- C is incorrect because the `artifacts` section is optional in buildspec.yml. Its absence does not cause a build failure. The build fails because a command in the `phases` section returned a non-zero exit code, not because of a missing artifacts configuration.
- D is incorrect because the error message shows that `npm test` executed (it returned exit status 1, not "command not found"). If Node.js were not installed, the error would be "npm: command not found" or similar.

Reference: [Build Specification Reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)

---

### Question 10

**Correct Answer: B**

A CloudFormation change set lets you preview how proposed changes will affect running resources before executing them. The change set shows whether each resource will be added, modified (in place or with interruption), or replaced. The team can review the change set to confirm that adding a subnet does not trigger replacement of existing resources, then execute the change set if the changes are acceptable.

- A is incorrect because `validate-template` checks only the template's syntax and structure (such as valid JSON/YAML, correct resource type names, and valid intrinsic function usage). It does not compare the template against the current stack state or predict which resources will be modified or replaced.
- C is incorrect because drift detection compares the current state of deployed resources against the last deployed template. It identifies manual changes made outside CloudFormation but does not preview the impact of a proposed template update.
- D is incorrect because deploying to a separate stack in a different Region creates new resources rather than updating existing ones. This does not reveal whether the update would replace resources in the production stack, because the behavior depends on the current state of the production stack's resources.

Reference: [Example Change Sets for CloudFormation Stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets-samples.html)

---

### Question 11

**Correct Answer: B**

Blue/green deployment with CodeDeploy creates a complete replacement task set (green) alongside the existing task set (blue). Traffic shifts from blue to green only after the green tasks pass health checks. If the green tasks fail health checks or if CloudWatch alarms detect elevated error rates, CodeDeploy automatically rolls back by redirecting traffic to the blue task set. This provides zero downtime and instant rollback.

- A is incorrect because a rolling update with minimumHealthyPercent at 100% and maximumPercent at 200% does provide zero downtime, but rollback is not instant. To roll back a rolling update, you must redeploy the previous task definition, which takes time as ECS replaces tasks incrementally. Blue/green provides instant rollback by simply redirecting traffic.
- C is incorrect because the ECSAllAtOnce configuration shifts all traffic to the new task set at once without gradual validation. If the new version has issues, all traffic is affected immediately. While CodeDeploy can still roll back, the blast radius is larger than with a canary or linear shift.
- D is incorrect because stopping and restarting the service causes downtime. During the period between stopping the old tasks and starting the new ones, no tasks are available to serve traffic.

Reference: [Amazon ECS Blue/Green Deployments](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-blue-green.html)

---

### Question 12

**Correct Answer: A**

The SQS visibility timeout determines how long a message remains hidden after a consumer receives it. If the visibility timeout (30 seconds) is shorter than the processing time (120 seconds), the message becomes visible again while the first consumer is still processing it. Another consumer (or the same consumer on the next poll) picks up the message and processes it again. Increasing the visibility timeout to at least 120 seconds (with buffer for variability) keeps the message hidden until the Lambda function finishes processing and deletes it.

- B is incorrect because while FIFO queues provide exactly-once processing, the duplicate processing in this scenario is caused by the visibility timeout being too short. Switching to a FIFO queue would also reduce throughput (FIFO queues support up to 300 messages per second without batching, or 3,000 with batching) and may not be necessary if the visibility timeout is configured correctly.
- C is incorrect because reducing the batch size to 1 does not affect the visibility timeout. The function still takes up to 120 seconds to process each message, and the 30-second visibility timeout still expires before processing completes.
- D is incorrect because increasing the DLQ's maximum receive count allows more retries before a message is moved to the DLQ, but it does not prevent duplicate processing. The message is still reprocessed each time the visibility timeout expires.

Reference: [Process SQS Messages with Lambda](https://docs.aws.amazon.com/lambda/latest/dg/services-sqs-parameters.html)

---

### Question 13

**Correct Answer: B**

Decoupling the email sending from the API request improves reliability by separating the synchronous API response (order confirmation) from the asynchronous side effect (email delivery). After the DynamoDB write succeeds, the function publishes a message to an SNS topic or SQS queue. A separate Lambda function processes the message and sends the email. If the email fails, the message remains in the queue for retry without affecting the order processing.

- A is incorrect because increasing the timeout does not address the root cause (SES throttling). The function would still fail if SES continues to throttle, and a 15-minute timeout for an API request would cause the client to wait unacceptably long.
- C is incorrect because adding retry logic inside the synchronous Lambda function increases the API response time. If the SES call requires multiple retries, the client waits for each retry attempt. This degrades the user experience and does not guarantee success if throttling persists.
- D is incorrect because Amazon SNS is a notification service, not an email service. While SNS can send basic email notifications, it does not provide the rich email formatting, templates, and deliverability features of SES. The issue is architectural (tight coupling), not a limitation of SES.

Reference: [Lambda Event Source Mapping for SQS](https://docs.aws.amazon.com/lambda/latest/dg/services-sqs-parameters.html)

---

### Question 14

**Correct Answer: A**

The correct sequence is: (1) Write the SAM template defining the resources (step 4), (2) Build the application to install dependencies and prepare artifacts (step 2), (3) Test the function locally with Docker (step 3), (4) Deploy to AWS (step 1). The sequence is 4, 2, 3, 1.

- B is incorrect because you cannot build the application (step 2) before writing the template (step 4). The `sam build` command reads the template to determine which functions to build and where their code is located.
- C is incorrect because you should build the application (step 2) before testing locally (step 3). The `sam local invoke` command uses the built artifacts in the `.aws-sam/build/` directory. Testing before building would use stale or missing artifacts.
- D is incorrect because deploying (step 1) before writing the template (step 4) is not possible. The deployment requires a template to define the resources.

Reference: [AWS SAM CLI Core Commands](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-corecommands.html)

---

### Question 15

**Correct Answer: B**

A manual approval action pauses the pipeline and sends an SNS notification to designated reviewers. The reviewer examines the staging deployment, verifies it works correctly, and either approves (allowing the pipeline to proceed to production) or rejects (stopping the pipeline). This is the standard CodePipeline feature for gating production deployments.

- A is incorrect because while a Lambda invoke action can send notifications, it does not natively provide the approve/reject workflow that a manual approval action provides. Building a custom approval system with Lambda adds unnecessary complexity.
- C is incorrect because permanently disabling the transition requires manual intervention for every deployment and does not provide a structured approval workflow with notifications, comments, or audit trails.
- D is incorrect because automated tests in a CodeBuild action do not replace human review. The question specifically requires manual review of the staging deployment. Automated tests can complement manual approval but should not replace it for production gates.

Reference: [CodePipeline Concepts](https://docs.aws.amazon.com/codepipeline/latest/userguide/concepts.html)

---

### Question 16

**Correct Answer: B**

A parameterized template with the VPC CIDR block as a parameter is the simplest and most maintainable approach. The team deploys the same template to each account, passing the appropriate CIDR block as a parameter value. This follows the CloudFormation best practice of using parameters to make templates reusable across environments.

- A is incorrect because maintaining three separate templates creates duplication. Any change to the network configuration (such as adding a subnet or modifying a security group rule) must be applied to all three templates, increasing the risk of inconsistency.
- C is incorrect because using Conditions with account ID checks embeds account-specific logic into the template. This approach is fragile (account IDs change if accounts are recreated) and harder to maintain than a simple parameter.
- D is incorrect because while Mappings with `AWS::AccountId` would work, it requires updating the template every time a new account is added. Parameters are more flexible because the CIDR block is provided at deployment time without modifying the template.

Reference: [CloudFormation Best Practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html)

---

### Question 17

**Correct Answers: A, B**

Health check failures occur when the ALB cannot get a successful response from the container. The two most common causes are: the health check path does not exist in the application (A), causing the application to return a 404 instead of a 200; and the container port in the task definition does not match the port the application actually listens on (B), causing the ALB to send health check requests to a port where nothing is listening.

- C is incorrect because the desired count controls how many tasks the service maintains. A low desired count means fewer tasks, but it does not cause individual tasks to fail health checks. Each task either passes or fails its health check independently of the desired count.
- D is incorrect because the ALB listener protocol (HTTP or HTTPS) is separate from the health check configuration. Health checks can be configured to use HTTP even when the listener uses HTTPS. Additionally, the health check protocol is configurable in the target group settings.
- E is incorrect because the service runs on Fargate, not the EC2 launch type. Fargate manages the underlying infrastructure, so there are no "registered container instances" to run out of. Fargate provisions capacity automatically for each task.

Reference: [Health Checks for ALB Target Groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html)

---

### Question 18

**Correct Answer: B**

When you use `!Ref` on an `AWS::S3::Bucket` resource, CloudFormation returns the bucket name (the physical resource ID). This is the simplest way to pass the bucket name to the Lambda function's environment variables without hardcoding it.

- A is incorrect because `!GetAtt MyBucket.Arn` returns the bucket's ARN (for example, `arn:aws:s3:::my-bucket-name`), not the bucket name. If the function needs the bucket name for API calls like `s3.get_object(Bucket=name)`, the ARN format would not work.
- C is incorrect because `${MyBucket.BucketName}` is not valid `!Sub` syntax for CloudFormation. The correct syntax to get an attribute in a `!Sub` string is `${MyBucket}` (which calls Ref) or you would need to use `!GetAtt` separately. Additionally, the bucket name is not derived from the stack name unless explicitly configured.
- D is incorrect because `!ImportValue` imports values exported from other stacks. The bucket is defined in the same template, so there is no need to import it from another stack. `!Ref` is the correct function for referencing resources within the same template.

Reference: [Ref Intrinsic Function](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference-ref.html)

---

### Question 19

**Correct Answer: B**

Lambda is the better fit for this workload. The traffic pattern (zero at night, 500 requests per second at peak) with short execution times (2 to 5 seconds) is a textbook Lambda use case. Lambda scales automatically from zero to handle peak traffic, charges nothing during zero-traffic periods, and requires no infrastructure management. With Fargate, the team would need to maintain at least one running task (or configure scaling to zero, which adds cold start latency), paying for idle capacity during off-peak hours.

- A is incorrect because while Fargate provides consistent performance, Lambda also provides consistent performance for short-duration API requests. The key differentiator is cost: Lambda charges per invocation and duration, while Fargate charges per task per second regardless of whether the task is processing requests.
- C is incorrect because each API request takes 2 to 5 seconds, well within Lambda's 15-minute timeout. The timeout is not a limiting factor for this workload.
- D is incorrect because Lambda functions do not maintain persistent database connections across invocations (connections are reused within a single execution environment but not guaranteed across invocations). ECS tasks, being long-running, can maintain persistent connection pools more effectively. However, the question asks about the overall best fit, and Lambda's scaling and cost advantages outweigh this consideration for the described workload.

Reference: [AWS Lambda Overview](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)

---

### Question 20

**Correct Answer: B**

The `artifacts` section in buildspec.yml specifies which files CodeBuild should package as output artifacts. Without this section, CodeBuild does not produce any output artifact, and the deploy stage has no `imagedefinitions.json` file to consume. Adding `artifacts: files: - imagedefinitions.json` tells CodeBuild to include this file in the output artifact that CodePipeline passes to the deploy stage.

- A is incorrect because CodeBuild managed build images include Docker pre-installed. The `install` phase is optional and is used for installing additional tools or runtime versions, not Docker itself. The build commands (`docker build`, `docker tag`, `docker push`) executed successfully, confirming Docker is available.
- C is incorrect because the `cache` section is optional and improves build speed by caching dependencies between builds. Its absence does not cause the deploy stage to fail. The issue is that the build output (imagedefinitions.json) is not being passed to the next stage.
- D is incorrect because the ECR repository URI can be hardcoded in the buildspec commands (as shown in the example). While using environment variables is a best practice for maintainability, their absence does not cause the deploy stage to fail. The build commands executed successfully.

Reference: [Build Specification Reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)

---

### Question 21

**Correct Answer: C**

The ECSCanary10Percent5Minutes configuration shifts 10% of traffic to the new task set initially. If the new tasks pass health checks and CloudWatch alarms do not trigger during the 5-minute bake period, the remaining 90% of traffic shifts to the new version. If the canary group shows elevated error rates, CodeDeploy automatically rolls back by redirecting all traffic to the original task set. This limits the blast radius to 10% of traffic during validation.

- A is incorrect because a rolling update replaces tasks incrementally but does not provide a controlled traffic shift with a bake period. During a rolling update, new tasks receive traffic as soon as they pass health checks, without a canary validation phase. Rollback requires redeploying the previous task definition.
- B is incorrect because ECSAllAtOnce shifts all traffic to the new task set at once, without a canary phase. If the new version has issues, 100% of traffic is affected before the team can detect and respond to the problem.
- D is incorrect because an all-at-once deployment updates all tasks simultaneously. While the deployment circuit breaker can trigger a rollback if new tasks fail to stabilize, all traffic is affected during the deployment. There is no gradual traffic shift or canary validation.

Reference: [Amazon ECS Canary Deployments](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/canary-deployment.html)

---

### Question 22

**Correct Answer: B**

SAM automatically generates an IAM execution role for each `AWS::Serverless::Function` resource. The generated role includes the policies specified in the function's `Policies` property (such as `DynamoDBCrudPolicy`, which grants CRUD operations on the specified table) and basic CloudWatch Logs permissions for logging. This is one of the key simplifications SAM provides over raw CloudFormation, where you must define the IAM role explicitly.

- A is incorrect because SAM fully supports IAM roles. SAM generates them automatically, and you can also specify a custom role using the `Role` property if needed. SAM does not use a "default Lambda execution role" from the account.
- C is incorrect because `DynamoDBCrudPolicy` is a SAM policy template that generates an inline policy scoped to a specific DynamoDB table. It does not create a standalone IAM role. Each function gets its own generated role with its own set of policies.
- D is incorrect because API Gateway does not provide IAM permissions for Lambda functions. API Gateway needs permission to invoke the Lambda function (which SAM configures automatically), but the Lambda function's execution role is what grants the function permission to access DynamoDB and other AWS services.

Reference: [AWS SAM Template Anatomy](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html)

---

### Question 23

**Correct Answer: A**

Using a CloudFormation change set action in the pipeline allows the team to review proposed changes before they are applied. The change set would show that changing the RDS engine from MySQL to PostgreSQL requires resource replacement (the database is deleted and recreated). The team can reject the change set and plan a proper migration strategy (such as using AWS Database Migration Service) instead of allowing CloudFormation to replace the database and lose data.

- B is incorrect because a stack policy that denies all updates to the RDS instance would prevent any changes to the database, including legitimate updates like increasing storage or changing the instance class. Stack policies are a blunt instrument that blocks all modifications, not just replacements.
- C is incorrect because `DeletionPolicy: Retain` prevents CloudFormation from deleting the old database during replacement, but it does not prevent the replacement itself. CloudFormation would create a new PostgreSQL database and leave the old MySQL database orphaned (not managed by the stack). The application would point to the new, empty database, effectively losing access to the data.
- D is incorrect because `UPDATE_ONLY` prevents CloudFormation from creating the stack if it does not exist, but it does not prevent resource replacement during updates. CloudFormation would still replace the RDS instance when the engine property changes.

Reference: [CloudFormation Best Practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html)

---

### Question 24

**Correct Answer: B**

AWS Cloud Map provides DNS-based service discovery for ECS services. When you enable service discovery for Service B, ECS automatically registers each task's IP address with a Cloud Map namespace. Service A resolves the DNS name (such as `service-b.internal`) to get the current IP addresses of Service B's tasks. This works entirely within the VPC, so Service B does not need to be exposed to the internet.

- A is incorrect because exposing Service B through a public ALB makes it accessible from the internet, even with security group restrictions. Security groups can be misconfigured, and the requirement is that Service B should not be accessible from the internet at all. An internal ALB or service discovery is the correct approach.
- C is incorrect because Fargate tasks receive dynamic IP addresses that change when tasks are replaced (due to scaling, deployments, or failures). Hardcoding IP addresses requires manual updates every time a task changes, which is operationally unsustainable.
- D is incorrect because API Gateway is designed for external-facing APIs, not internal service-to-service communication within a VPC. Using API Gateway adds unnecessary latency, cost, and complexity for internal traffic that stays within the VPC.

Reference: [ECS Service Discovery](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-discovery.html)

---

### Question 25

**Correct Answers: A, B, D**

CodeBuild with `sam build` and `npm test` (A) handles the build and test stages. CloudFormation deploy actions (B) deploy the SAM template to staging and production environments by creating or updating CloudFormation stacks. A manual approval action (D) between staging and production provides the human gate the team requires. Together, these three components create a complete CI/CD pipeline for a SAM application.

- C is incorrect because CodeDeploy deploys to EC2 instances, ECS services, or Lambda functions, not SAM applications. SAM applications are deployed through CloudFormation (which SAM extends). The CloudFormation deploy action in CodePipeline is the correct mechanism for deploying SAM templates.
- E is incorrect because combining build and deploy in a single CodeBuild project eliminates the ability to add stages between them (such as manual approval). Separate stages provide better visibility, control, and the ability to insert approval gates.
- F is incorrect because using S3 as the source provider requires manually uploading the template for each deployment. The team uses GitHub for source control, so the pipeline should pull code from GitHub automatically when changes are pushed.

Reference: [CodePipeline Concepts](https://docs.aws.amazon.com/codepipeline/latest/userguide/concepts.html)

</details>

---

## Study Guide

If you scored below 70%, review the following topics organized by module before retaking the exam.

### Module 09: Serverless Computing with AWS Lambda

- Lambda execution model: handler function, event object, context object, and the execution environment lifecycle (Init, Invoke, Shutdown phases)
- Memory and CPU relationship: Lambda allocates CPU proportionally to memory, so increasing memory also increases CPU and can reduce execution time
- Timeout configuration: default 3 seconds, maximum 900 seconds (15 minutes), and how to choose an appropriate value based on workload
- Cold starts: causes (first invocation, scaling up, code changes), duration factors (runtime, package size, VPC), and mitigation strategies (provisioned concurrency, minimize package size, initialize outside the handler)
- Event source types: push-based triggers (API Gateway, S3, SNS, EventBridge) vs. poll-based event source mappings (SQS, DynamoDB Streams, Kinesis)
- IAM execution roles: trust policy for Lambda service principal, permissions policies for AWS service access, and the difference between the execution role and resource-based policies
- Reserved concurrency vs. provisioned concurrency: reserved concurrency caps and guarantees concurrent executions; provisioned concurrency pre-initializes execution environments to eliminate cold starts
- Lambda Layers: sharing code and dependencies across functions, layer versioning, and the `/opt` directory structure
- API Gateway integration: Lambda proxy integration, request/response format, and HTTP API vs. REST API
- Reference: [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)

### Module 10: Containers and Amazon ECS

- Containers vs. virtual machines: isolation level, startup time, resource overhead, and portability
- Docker fundamentals: images, containers, Dockerfiles, layer caching, and the build/run workflow
- Amazon ECR: private registries, repositories, image tagging, lifecycle policies for automated cleanup, and image scanning
- ECS core concepts: clusters (logical grouping), task definitions (blueprints), tasks (running instances), and services (desired count maintenance)
- Task definitions: container definitions, CPU/memory allocation (task-level vs. container-level), port mappings, environment variables, secrets, IAM task roles vs. task execution roles
- Fargate vs. EC2 launch type: infrastructure management, scaling model, networking, pricing, GPU support, and when to use each
- ECS services: desired count, rolling update vs. blue/green deployment, deployment circuit breaker, and service auto scaling (target tracking, step, scheduled)
- ALB integration with ECS: dynamic port mapping (EC2 launch type), IP-based routing (Fargate), health checks, and target group configuration
- Service discovery with AWS Cloud Map: DNS-based discovery for internal service-to-service communication
- Compute selection: ECS vs. EKS vs. Lambda, and when to use each based on workload characteristics
- Reference: [Amazon ECS Developer Guide](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)

### Module 11: Infrastructure as Code with CloudFormation, SAM, and CDK

- Why IaC: repeatability, version control, automation, drift detection, and consistency across environments
- CloudFormation template anatomy: Parameters, Mappings, Conditions, Resources (required), Outputs, and the AWSTemplateFormatVersion declaration
- Intrinsic functions: `!Ref` (parameter values and resource IDs), `!Sub` (string substitution), `!GetAtt` (resource attributes), `!Join`, `!Select`, `!If`, and `Fn::ImportValue` (cross-stack references)
- Parameters and Outputs: parameterizing templates for reuse across environments, AWS-specific parameter types, and cross-stack references with Export/ImportValue
- Stack lifecycle: creating, updating (no interruption, some interruption, replacement), and deleting stacks; rollback behavior on failure; ROLLBACK_COMPLETE state
- Change sets: previewing proposed changes before applying them, reviewing add/modify/replace actions, and executing or discarding change sets
- Drift detection: identifying manual changes to stack resources, IN_SYNC vs. MODIFIED vs. DELETED status
- AWS SAM: `Transform: AWS::Serverless-2016-10-31`, SAM resource types (`AWS::Serverless::Function`, `AWS::Serverless::Api`, `AWS::Serverless::SimpleTable`), Globals section, and policy templates
- SAM CLI: `sam init`, `sam build`, `sam local invoke`, `sam local start-api`, `sam deploy --guided`
- AWS CDK: imperative vs. declarative IaC, constructs (L1, L2, L3), `cdk synth`, `cdk deploy`, and when to choose CDK over CloudFormation or SAM
- Reference: [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)

### Module 12: CI/CD Pipelines

- CI/CD fundamentals: Continuous Integration (automated build and test on every commit) and Continuous Delivery (automated deployment to staging/production)
- AWS developer tools: CodeBuild (build and test), CodeDeploy (deployment automation), CodePipeline (orchestration), and CodeArtifact (package management)
- CodeBuild: buildspec.yml structure (phases: install, pre_build, build, post_build), artifacts section, caching, build environments, and compute types
- CodeDeploy: applications, deployment groups, revisions, appspec.yml (EC2 and ECS formats), and deployment configurations (AllAtOnce, HalfAtATime, OneAtATime, canary, linear)
- CodePipeline: pipeline structure (stages and actions), artifact passing between stages, source providers (GitHub, S3, ECR), manual approval actions, and transitions
- Deployment strategies: all-at-once (fast but risky), rolling (partial availability), blue/green (zero downtime, instant rollback), and canary (gradual traffic shift with validation)
- Deployment strategy trade-offs: downtime, rollback speed, infrastructure cost, complexity, and risk level for each strategy
- GitHub Actions vs. CodePipeline: when to use each, hybrid approaches (GitHub Actions for CI, CodePipeline for CD), and OIDC federation for secure AWS access from GitHub Actions
- Artifact management: S3 for build artifacts, ECR for container images, and CodeArtifact for package dependencies
- Reference: [AWS CodePipeline User Guide](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html)


---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
