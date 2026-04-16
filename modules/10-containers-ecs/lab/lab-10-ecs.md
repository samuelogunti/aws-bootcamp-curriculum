# Lab 10: Deploying Containers on Amazon ECS with Fargate

## Objective

Build a containerized web application, push the image to Amazon Elastic Container Registry (Amazon ECR), deploy it on Amazon Elastic Container Service (Amazon ECS) using AWS Fargate, and perform a rolling deployment with an updated container image behind an Application Load Balancer (ALB).

## Architecture Diagram

This lab builds a containerized deployment using Docker, ECR, ECS Fargate, and an ALB. The components and their relationships are as follows:

```
Internet
    |
    v
Application Load Balancer (lab10-alb)
    |
    ├── Listener (port 80)
    |       |
    |       v
    |   Target Group (lab10-tg, type: ip, port 3000)
    |       |
    |       ├── ECS Task 1 (Fargate, 0.25 vCPU, 0.5 GB)
    |       |       └── Container: lab10-web-app:1.0
    |       |
    |       └── ECS Task 2 (Fargate, 0.25 vCPU, 0.5 GB)
    |               └── Container: lab10-web-app:1.0
    |
    v
ECS Cluster (lab10-cluster)
    └── Service (lab10-service, desired count: 2)
            └── Task Definition (lab10-web-app, revision N)
                    └── Container image from ECR

ECR Repository (lab10-web-app)
    ├── lab10-web-app:1.0
    └── lab10-web-app:2.0 (after update)

CloudWatch Logs
    └── /ecs/lab10-web-app
```

You start by writing a simple Dockerfile for a Node.js web application and building the image in CloudShell. You push the image to an ECR repository. You then create an ECS cluster, write a task definition, and launch a Fargate service with two tasks. You create an ALB to distribute traffic across the tasks. Finally, you update the application, push a new image, and trigger a rolling deployment.

## Prerequisites

- Completed [Lab 03: VPC Setup](../../03-networking-basics/lab/lab-03-vpc-setup.md) (understanding of VPCs, subnets, and security groups)
- Completed [Lab 04: EC2 Instances](../../04-compute-ec2/lab/lab-04-ec2-instances.md) (understanding of compute resources and security groups)
- Completed [Lab 07: Application Load Balancer and Route 53](../../07-load-balancing-and-dns/lab/lab-07-alb-route53.md) (understanding of ALBs, target groups, and health checks)
- Completed [Module 10: Containers and Amazon ECS](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (Docker is pre-installed in CloudShell)

## Duration

90 minutes

## Instructions

### Step 1: Write a Dockerfile for a Simple Web Application (Guided)

In this step, you create a minimal Node.js web application and write a [Dockerfile](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-container-image.html) to package it as a container image.

1. Open CloudShell by choosing the terminal icon in the navigation bar.

2. Verify that the Region selector in the top-right corner displays **US East (N. Virginia) us-east-1**.

3. Create a project directory for the application:

```bash
mkdir -p ~/lab10-web-app && cd ~/lab10-web-app
```

4. Create a simple Node.js web server that responds with a message and the container's hostname (which corresponds to the ECS task ID):

```bash
cat > server.js << 'EOF'
const http = require("http");
const os = require("os");

const PORT = 3000;
const MESSAGE = process.env.APP_MESSAGE || "Hello from ECS";

const server = http.createServer((req, res) => {
  if (req.url === "/health") {
    res.writeHead(200, { "Content-Type": "application/json" });
    res.end(JSON.stringify({ status: "healthy" }));
    return;
  }
  res.writeHead(200, { "Content-Type": "text/html" });
  res.end(`
    <h1>${MESSAGE}</h1>
    <p>Hostname: ${os.hostname()}</p>
    <p>Version: 1.0</p>
  `);
});

server.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
EOF
```

5. Create a `package.json` file:

```bash
cat > package.json << 'EOF'
{
  "name": "lab10-web-app",
  "version": "1.0.0",
  "description": "ECS Lab 10 web application",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  }
}
EOF
```

6. Create the Dockerfile. This follows container best practices from [Module 10](../README.md): use a minimal base image, copy dependency manifests first for layer caching, and run as a non-root user.

```bash
cat > Dockerfile << 'EOF'
FROM node:20-alpine

WORKDIR /app

COPY package.json ./

RUN npm install --production

COPY server.js ./

RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

EXPOSE 3000

CMD ["node", "server.js"]
EOF
```

7. Verify the files are in place:

```bash
ls -la ~/lab10-web-app/
```

Expected output:

```
total 12
drwxr-xr-x 2 cloudshell-user cloudshell-user 4096 ... .
drwxr-xr-x 5 cloudshell-user cloudshell-user 4096 ... ..
-rw-r--r-- 1 cloudshell-user cloudshell-user  ... Dockerfile
-rw-r--r-- 1 cloudshell-user cloudshell-user  ... package.json
-rw-r--r-- 1 cloudshell-user cloudshell-user  ... server.js
```

> **Tip:** The application includes a `/health` endpoint that returns a JSON response. The ALB will use this endpoint for health checks to determine whether each ECS task is ready to receive traffic.

### Step 2: Build the Docker Image in CloudShell (Guided)

[CloudShell](https://docs.aws.amazon.com/cloudshell/latest/userguide/welcome.html) includes Docker, so you can build and test container images directly without installing anything locally.

1. Build the Docker image and tag it as `lab10-web-app:1.0`:

```bash
cd ~/lab10-web-app
docker build -t lab10-web-app:1.0 .
```

Expected output (abbreviated):

```
Sending build context to Docker daemon  ...
Step 1/8 : FROM node:20-alpine
 ---> ...
...
Successfully built ...
Successfully tagged lab10-web-app:1.0
```

2. Verify the image was created:

```bash
docker images lab10-web-app
```

Expected output:

```
REPOSITORY       TAG       IMAGE ID       CREATED          SIZE
lab10-web-app    1.0       a1b2c3d4e5f6   10 seconds ago   ...
```

3. Test the image locally by running a container:

```bash
docker run -d -p 8080:3000 --name lab10-test lab10-web-app:1.0
```

4. Verify the container is running and the application responds:

```bash
curl http://localhost:8080
```

Expected output:

```html

    <h1>Hello from ECS</h1>
    <p>Hostname: a1b2c3d4e5f6</p>
    <p>Version: 1.0</p>

```

5. Test the health endpoint:

```bash
curl http://localhost:8080/health
```

Expected output:

```json
{"status":"healthy"}
```

6. Stop and remove the test container:

```bash
docker stop lab10-test && docker rm lab10-test
```

> **Tip:** If Docker is not available in your CloudShell session, you can skip the local test and proceed directly to pushing the image to ECR. The image will be tested when you deploy it on ECS.

### Step 3: Create an ECR Repository and Push the Image (Guided)

[Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) stores your container images so that ECS can pull them during task launches. In this step, you create a repository and push your image.

1. Store your AWS account ID in a variable for use throughout the lab:

```bash
ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)
echo "Account ID: $ACCOUNT_ID"
```

2. Create an ECR repository:

```bash
aws ecr create-repository \
  --repository-name lab10-web-app \
  --region us-east-1 \
  --query "repository.repositoryUri" \
  --output text
```

Expected output:

```
123456789012.dkr.ecr.us-east-1.amazonaws.com/lab10-web-app
```

3. Save the repository URI:

```bash
ECR_URI="${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/lab10-web-app"
echo "ECR URI: $ECR_URI"
```

4. Authenticate Docker to your ECR registry:

```bash
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS --password-stdin \
  ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com
```

Expected output:

```
Login Succeeded
```

5. Tag the local image with the ECR repository URI:

```bash
docker tag lab10-web-app:1.0 ${ECR_URI}:1.0
```

6. Push the image to ECR:

```bash
docker push ${ECR_URI}:1.0
```

7. Verify the image is in ECR:

```bash
aws ecr describe-images \
  --repository-name lab10-web-app \
  --region us-east-1 \
  --query "imageDetails[*].{Tags:imageTags,Pushed:imagePushedAt,Size:imageSizeInBytes}" \
  --output table
```

You should see one image with the tag `1.0`.

> **Tip:** In production, configure an [ECR lifecycle policy](https://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html) to automatically clean up old images and reduce storage costs.

### Step 4: Create an ECS Cluster, Task Definition, and Service (Semi-Guided)

**Goal:** Create an ECS cluster, register a Fargate task definition for your container image, and launch an ECS service that maintains two running tasks.

**Cluster requirements:**

- Cluster name: `lab10-cluster`
- Use the default settings (Fargate capacity provider is included by default)

**Task definition requirements:**

- Family name: `lab10-web-app`
- Launch type compatibility: Fargate
- Network mode: `awsvpc`
- CPU: 256 (0.25 vCPU)
- Memory: 512 (0.5 GB)
- Execution role: use the ARN of the `ecsTaskExecutionRole` (this role is created automatically in most accounts; if it does not exist, create it with the `AmazonECSTaskExecutionRolePolicy` managed policy)
- Container name: `web`
- Container image: your ECR image URI with the `1.0` tag
- Port mapping: container port 3000, protocol TCP
- Log configuration: `awslogs` driver with log group `/ecs/lab10-web-app`, region `us-east-1`, stream prefix `web`
- Mark the container as essential

**Service requirements:**

- Service name: `lab10-service`
- Launch type: Fargate
- Desired count: 2
- Network configuration: use the default VPC, select at least two public subnets, assign a public IP, and attach a security group that allows inbound traffic on port 3000

> **Hint:** Create the cluster with `aws ecs create-cluster --cluster-name lab10-cluster`. See the [ECS cluster documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/clusters.html).

> **Hint:** Write your task definition as a JSON file and register it with `aws ecs register-task-definition --cli-input-json file://task-definition.json`. Refer to the example task definition in the [Module 10 README](../README.md) and the [task definition documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html) for the required fields.

> **Hint:** Before creating the service, create the CloudWatch Logs log group: `aws logs create-log-group --log-group-name /ecs/lab10-web-app --region us-east-1`. Without this, tasks will fail to start because the log driver cannot write to a non-existent log group.

> **Hint:** Look up the `ecsTaskExecutionRole` ARN with `aws iam get-role --role-name ecsTaskExecutionRole --query "Role.Arn" --output text`. If the role does not exist, create it with a trust policy for `ecs-tasks.amazonaws.com` and attach the `AmazonECSTaskExecutionRolePolicy` managed policy. See the [task execution role documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html).

> **Hint:** To create the service, you need your default VPC ID, subnet IDs, and a security group. Retrieve them with:
> ```bash
> VPC_ID=$(aws ec2 describe-vpcs --filters "Name=isDefault,Values=true" --query "Vpcs[0].VpcId" --output text)
> SUBNET_IDS=$(aws ec2 describe-subnets --filters "Name=vpc-id,Values=$VPC_ID" --query "Subnets[?MapPublicIpOnLaunch==\`true\`].SubnetId" --output text | tr '\t' ',')
> ```
> Create a security group that allows inbound TCP on port 3000 from anywhere (0.0.0.0/0). You will restrict this to ALB-only traffic in Step 5.

> **Hint:** Create the service with `aws ecs create-service`. Use `--network-configuration "awsvpcConfiguration={subnets=[subnet-xxx,subnet-yyy],securityGroups=[sg-xxx],assignPublicIp=ENABLED}"`. See the [create-service CLI reference](https://docs.aws.amazon.com/cli/latest/reference/ecs/create-service.html).

**Verify your work** by checking that the service reaches a steady state with two running tasks:

```bash
aws ecs describe-services \
  --cluster lab10-cluster \
  --services lab10-service \
  --query "services[0].{Status:status,Running:runningCount,Desired:desiredCount}" \
  --output table
```

You should see `Running: 2` and `Desired: 2`. If tasks are failing, check the stopped tasks for error messages:

```bash
aws ecs list-tasks --cluster lab10-cluster --service-name lab10-service --desired-status STOPPED --output text
```

Then describe a stopped task to see the stop reason:

```bash
aws ecs describe-tasks --cluster lab10-cluster --tasks <task-arn> --query "tasks[0].stoppedReason"
```

**Reference links:**
- [Creating an ECS cluster](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/clusters.html)
- [Task definition parameters](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)
- [Task execution IAM role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html)
- [Creating an ECS service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html)
- [Fargate task networking](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html)


### Step 5: Create an ALB and Connect It to the ECS Service (Guided)

In this step, you create an [Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) with a target group and update your ECS service to register tasks with the ALB. This distributes incoming traffic across your two Fargate tasks.

1. First, retrieve your VPC and subnet information (if you have not already):

```bash
VPC_ID=$(aws ec2 describe-vpcs \
  --filters "Name=isDefault,Values=true" \
  --query "Vpcs[0].VpcId" \
  --output text)
echo "VPC ID: $VPC_ID"

SUBNET_IDS=$(aws ec2 describe-subnets \
  --filters "Name=vpc-id,Values=$VPC_ID" "Name=map-public-ip-on-launch,Values=true" \
  --query "Subnets[*].SubnetId" \
  --output text)
echo "Subnet IDs: $SUBNET_IDS"
```

2. Create a security group for the ALB that allows inbound HTTP traffic on port 80:

```bash
ALB_SG=$(aws ec2 create-security-group \
  --group-name lab10-alb-sg \
  --description "Security group for Lab 10 ALB" \
  --vpc-id $VPC_ID \
  --query "GroupId" \
  --output text)
echo "ALB Security Group: $ALB_SG"

aws ec2 authorize-security-group-ingress \
  --group-id $ALB_SG \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0
```

3. Create the Application Load Balancer. The ALB needs at least two subnets in different Availability Zones:

```bash
SUBNET_ARRAY=($SUBNET_IDS)

ALB_ARN=$(aws elbv2 create-load-balancer \
  --name lab10-alb \
  --subnets ${SUBNET_ARRAY[0]} ${SUBNET_ARRAY[1]} \
  --security-groups $ALB_SG \
  --scheme internet-facing \
  --type application \
  --query "LoadBalancers[0].LoadBalancerArn" \
  --output text)
echo "ALB ARN: $ALB_ARN"
```

4. Create a target group. Because Fargate tasks use `awsvpc` network mode, the target type must be `ip` (not `instance`). The target group health check points to the `/health` endpoint on port 3000:

```bash
TG_ARN=$(aws elbv2 create-target-group \
  --name lab10-tg \
  --protocol HTTP \
  --port 3000 \
  --vpc-id $VPC_ID \
  --target-type ip \
  --health-check-protocol HTTP \
  --health-check-path /health \
  --health-check-interval-seconds 30 \
  --healthy-threshold-count 2 \
  --unhealthy-threshold-count 3 \
  --query "TargetGroups[0].TargetGroupArn" \
  --output text)
echo "Target Group ARN: $TG_ARN"
```

5. Create a listener on the ALB that forwards traffic on port 80 to the target group:

```bash
aws elbv2 create-listener \
  --load-balancer-arn $ALB_ARN \
  --protocol HTTP \
  --port 80 \
  --default-actions Type=forward,TargetGroupArn=$TG_ARN \
  --query "Listeners[0].ListenerArn" \
  --output text
```

6. Now update the ECS task security group to allow inbound traffic only from the ALB security group. First, retrieve the security group you created for the ECS tasks in Step 4:

```bash
ECS_SG=$(aws ec2 describe-security-groups \
  --filters "Name=group-name,Values=lab10-ecs-sg" \
  --query "SecurityGroups[0].GroupId" \
  --output text)
echo "ECS Security Group: $ECS_SG"
```

> **Tip:** If you named your ECS security group differently in Step 4, replace `lab10-ecs-sg` with the name you used. You can list all security groups with `aws ec2 describe-security-groups --query "SecurityGroups[*].{Name:GroupName,ID:GroupId}" --output table`.

7. Remove the open inbound rule on port 3000 and replace it with a rule that allows traffic only from the ALB security group:

```bash
aws ec2 revoke-security-group-ingress \
  --group-id $ECS_SG \
  --protocol tcp \
  --port 3000 \
  --cidr 0.0.0.0/0

aws ec2 authorize-security-group-ingress \
  --group-id $ECS_SG \
  --protocol tcp \
  --port 3000 \
  --source-group $ALB_SG
```

8. Delete the existing ECS service (you need to recreate it with the load balancer configuration, because you cannot add a load balancer to an existing service):

```bash
aws ecs update-service \
  --cluster lab10-cluster \
  --service lab10-service \
  --desired-count 0

echo "Waiting for tasks to drain..."
aws ecs wait services-stable --cluster lab10-cluster --services lab10-service

aws ecs delete-service \
  --cluster lab10-cluster \
  --service lab10-service
```

9. Retrieve the subnet IDs as a comma-separated list for the network configuration:

```bash
SUBNET_CSV=$(echo $SUBNET_IDS | tr ' ' ',' | sed 's/,$//')
echo "Subnets: $SUBNET_CSV"
```

10. Recreate the service with the load balancer configuration:

```bash
aws ecs create-service \
  --cluster lab10-cluster \
  --service-name lab10-service \
  --task-definition lab10-web-app \
  --desired-count 2 \
  --launch-type FARGATE \
  --network-configuration "awsvpcConfiguration={subnets=[${SUBNET_CSV}],securityGroups=[${ECS_SG}],assignPublicIp=ENABLED}" \
  --load-balancers "targetGroupArn=${TG_ARN},containerName=web,containerPort=3000" \
  --query "service.{Name:serviceName,Status:status}" \
  --output table
```

11. Wait for the service to reach a steady state:

```bash
echo "Waiting for service to stabilize (this may take 2-3 minutes)..."
aws ecs wait services-stable --cluster lab10-cluster --services lab10-service
echo "Service is stable."
```

12. Get the ALB DNS name:

```bash
ALB_DNS=$(aws elbv2 describe-load-balancers \
  --load-balancer-arns $ALB_ARN \
  --query "LoadBalancers[0].DNSName" \
  --output text)
echo "ALB DNS: http://${ALB_DNS}"
```

13. Test the deployment by accessing the ALB DNS name:

```bash
curl http://${ALB_DNS}
```

Expected output:

```html

    <h1>Hello from ECS</h1>
    <p>Hostname: a1b2c3d4e5f6</p>
    <p>Version: 1.0</p>

```

14. Verify that the ALB distributes traffic across both tasks by making several requests and observing different hostnames:

```bash
for i in 1 2 3 4 5 6; do
  echo "--- Request $i ---"
  curl -s http://${ALB_DNS} | grep "Hostname"
  sleep 1
done
```

You should see two different hostnames alternating across requests, confirming that the ALB is distributing traffic to both Fargate tasks.

> **Tip:** The hostname displayed by the application is the ECS task ID. Each Fargate task gets a unique hostname, which makes it easy to verify that the load balancer is routing to different tasks.

### Step 6: Update the Application and Perform a Rolling Deployment (Semi-Guided)

**Goal:** Update the web application to display a new message, build and push a new container image with a `2.0` tag, register a new task definition revision, and update the ECS service to trigger a [rolling deployment](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html). Verify that the new version is deployed without downtime.

**Requirements:**

- Modify `server.js` to change the message from "Hello from ECS" to "Hello from ECS v2" and update the version display from "1.0" to "2.0"
- Build the updated image and tag it as `lab10-web-app:2.0`
- Push the `2.0` tag to your ECR repository
- Register a new revision of the `lab10-web-app` task definition that references the `2.0` image tag
- Update the ECS service to use the new task definition revision
- Verify that the rolling deployment replaces the old tasks with new ones

> **Hint:** Edit `server.js` in CloudShell using `sed` or recreate the file with `cat > server.js << 'EOF'`. Change the `MESSAGE` default value and the version string.

> **Hint:** After building and tagging the new image, push it the same way you did in Step 3: `docker tag lab10-web-app:2.0 ${ECR_URI}:2.0` followed by `docker push ${ECR_URI}:2.0`.

> **Hint:** To register a new task definition revision, you can retrieve the current task definition JSON, modify the image tag, and register it. Use `aws ecs describe-task-definition --task-definition lab10-web-app --query "taskDefinition"` to get the current definition. Alternatively, create a new JSON file with the updated image URI and use `aws ecs register-task-definition --cli-input-json file://...`.

> **Hint:** Update the service with `aws ecs update-service --cluster lab10-cluster --service lab10-service --task-definition lab10-web-app:<new-revision>`. ECS will automatically perform a rolling deployment, stopping old tasks and starting new ones. See the [update-service CLI reference](https://docs.aws.amazon.com/cli/latest/reference/ecs/update-service.html).

> **Hint:** Monitor the deployment progress with `aws ecs describe-services --cluster lab10-cluster --services lab10-service --query "services[0].deployments"`. You should see two deployments during the transition: the PRIMARY (new) and ACTIVE (old).

**Test your work** by repeatedly curling the ALB DNS name during the deployment. You should see responses gradually transition from version 1.0 to version 2.0:

```bash
for i in $(seq 1 20); do
  echo "--- Request $i ---"
  curl -s http://${ALB_DNS} | grep "Version"
  sleep 3
done
```

During the rolling update, you may see a mix of version 1.0 and 2.0 responses. Once the deployment completes, all responses should show version 2.0.

**Reference links:**
- [ECS rolling update deployments](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html)
- [Updating an ECS service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/update-service.html)
- [ECS deployment circuit breaker](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html)

### Step 7: Verify the Rolling Deployment in the Console and CloudWatch Logs (Guided)

In this step, you verify the deployment through the ECS console and review container logs in [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html).

1. In the AWS Management Console, navigate to **Elastic Container Service** using the search bar.

2. Choose **Clusters**, then choose **lab10-cluster**.

3. Choose the **Services** tab and select **lab10-service**.

4. On the service detail page, choose the **Deployments and events** tab. Review the deployment history:

   - You should see the most recent deployment with status **PRIMARY** and the task definition revision pointing to your `2.0` image.
   - If the rolling update is still in progress, you may see a second deployment with status **ACTIVE** for the previous revision.
   - The **Events** section shows a timeline of task start and stop events during the deployment.

5. Choose the **Tasks** tab. Verify that two tasks are running and both use the latest task definition revision.

6. Choose one of the running tasks to view its details. Note the following:

   - The **Last status** should be `RUNNING`
   - The **Task definition** should show the latest revision
   - The **Container** section shows the image URI with the `2.0` tag
   - The **Network** section shows the private IP address and the ENI assigned to the task

7. Now review the container logs. Navigate to **CloudWatch** using the search bar.

8. In the left navigation pane, choose **Logs**, then **Log groups**.

9. Choose the log group `/ecs/lab10-web-app`.

10. You should see multiple log streams, each prefixed with `web/`. Each log stream corresponds to a container instance. Choose the most recent log stream.

11. Review the log entries. You should see the application startup message:

```
Server running on port 3000
```

12. You can also view logs from the CLI:

```bash
LOG_GROUP="/ecs/lab10-web-app"

LATEST_STREAM=$(aws logs describe-log-streams \
  --log-group-name $LOG_GROUP \
  --order-by LastEventTime \
  --descending \
  --limit 1 \
  --query "logStreams[0].logStreamName" \
  --output text)

aws logs get-log-events \
  --log-group-name $LOG_GROUP \
  --log-stream-name $LATEST_STREAM \
  --query "events[*].message" \
  --output text
```

13. Confirm the final state of the deployment from the CLI:

```bash
aws ecs describe-services \
  --cluster lab10-cluster \
  --services lab10-service \
  --query "services[0].{Status:status,Running:runningCount,Desired:desiredCount,TaskDef:taskDefinition,Deployments:deployments[*].{Status:status,Running:runningCount,Desired:desiredCount,TaskDef:taskDefinition}}" \
  --output json
```

You should see a single PRIMARY deployment with `runningCount: 2` and `desiredCount: 2`, using the latest task definition revision.

14. Make a final request to the ALB to confirm the updated application is serving traffic:

```bash
curl http://${ALB_DNS}
```

Expected output:

```html

    <h1>Hello from ECS v2</h1>
    <p>Hostname: f6e5d4c3b2a1</p>
    <p>Version: 2.0</p>

```

> **Tip:** In production, enable the [deployment circuit breaker](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html) on your ECS service. The circuit breaker automatically rolls back a deployment if new tasks repeatedly fail to reach a healthy state, preventing a bad image from replacing all running tasks.

## Validation

Confirm that you have completed the lab successfully by verifying each of the following:

- [ ] The `lab10-web-app` ECR repository exists and contains images tagged `1.0` and `2.0`
- [ ] The `lab10-cluster` ECS cluster exists
- [ ] The `lab10-web-app` task definition has at least two revisions (one for each image version)
- [ ] The `lab10-service` ECS service is running with desired count 2 and running count 2
- [ ] Both running tasks use the latest task definition revision (the `2.0` image)
- [ ] The `lab10-alb` Application Load Balancer is active and accessible via its DNS name
- [ ] Accessing the ALB DNS name in a browser or with `curl` returns "Hello from ECS v2"
- [ ] Multiple requests to the ALB show different hostnames, confirming traffic distribution across tasks
- [ ] CloudWatch Logs group `/ecs/lab10-web-app` contains log streams with application startup messages

## Cleanup

Delete all resources created during this lab to avoid unexpected charges. Run the following commands in CloudShell.

1. Update the ECS service desired count to 0 and delete the service:

```bash
aws ecs update-service \
  --cluster lab10-cluster \
  --service lab10-service \
  --desired-count 0

echo "Waiting for tasks to drain..."
aws ecs wait services-stable --cluster lab10-cluster --services lab10-service

aws ecs delete-service \
  --cluster lab10-cluster \
  --service lab10-service
```

2. Delete the ECS cluster:

```bash
aws ecs delete-cluster --cluster lab10-cluster
```

3. Delete the ALB listener, target group, and load balancer:

```bash
LISTENER_ARN=$(aws elbv2 describe-listeners \
  --load-balancer-arn $ALB_ARN \
  --query "Listeners[0].ListenerArn" \
  --output text)

aws elbv2 delete-listener --listener-arn $LISTENER_ARN
aws elbv2 delete-target-group --target-group-arn $TG_ARN
aws elbv2 delete-load-balancer --load-balancer-arn $ALB_ARN
```

4. Wait for the ALB to finish deleting before removing security groups (this may take 1-2 minutes):

```bash
echo "Waiting for ALB to delete..."
aws elbv2 wait load-balancers-deleted --load-balancer-arns $ALB_ARN 2>/dev/null || sleep 60
```

5. Delete the security groups:

```bash
aws ec2 delete-security-group --group-id $ALB_SG
aws ec2 delete-security-group --group-id $ECS_SG
```

6. Delete all images from the ECR repository and then delete the repository:

```bash
aws ecr batch-delete-image \
  --repository-name lab10-web-app \
  --image-ids imageTag=1.0 imageTag=2.0

aws ecr delete-repository \
  --repository-name lab10-web-app \
  --region us-east-1
```

7. Deregister the task definitions:

```bash
for revision in $(aws ecs list-task-definitions \
  --family-prefix lab10-web-app \
  --query "taskDefinitionArns[]" \
  --output text); do
  aws ecs deregister-task-definition --task-definition $revision > /dev/null
  echo "Deregistered: $revision"
done
```

8. Delete the CloudWatch Logs log group:

```bash
aws logs delete-log-group --log-group-name /ecs/lab10-web-app
```

9. Clean up the local project directory:

```bash
rm -rf ~/lab10-web-app
```

> **Warning:** Fargate tasks incur charges based on vCPU and memory per second while running. The ALB incurs hourly charges plus data processing fees. If you skip the cleanup steps, these resources will continue to generate charges. ECR storage charges are minimal but will accumulate over time.

## Challenge

Extend your ECS deployment with the following enhancements:

1. **Enable the deployment circuit breaker.** Update the ECS service to enable the [deployment circuit breaker](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html) with automatic rollback. Then intentionally deploy a broken image (for example, an image tag that does not exist in ECR) and observe how the circuit breaker detects the failure and rolls back to the previous working revision.

2. **Add ECS service auto scaling.** Configure [target tracking scaling](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html) on your ECS service to maintain average CPU utilization at 50%. Set the minimum task count to 2 and the maximum to 4. Use `aws application-autoscaling register-scalable-target` and `aws application-autoscaling put-scaling-policy` to configure the policy.

3. **Add an ECR lifecycle policy.** Create a [lifecycle policy](https://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html) on your ECR repository that keeps only the 5 most recent tagged images and expires all untagged images older than 1 day. Use `aws ecr put-lifecycle-policy` with a JSON policy document.
