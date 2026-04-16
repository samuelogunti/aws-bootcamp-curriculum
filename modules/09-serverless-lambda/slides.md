---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 09: Serverless Computing (Lambda)'
---

# Module 09: Serverless Computing with AWS Lambda

**Phase 3: Building Applications**
Estimated lecture time: 90 minutes

<!-- Speaker notes: Welcome to Module 09, the first module in Phase 3 (Building Applications). This module covers Lambda and serverless architecture. Breakdown: 10 min serverless overview, 15 min Lambda fundamentals, 10 min runtimes, 15 min event sources, 10 min IAM and config, 10 min cold starts, 10 min layers and packaging, 10 min API Gateway integration. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Build a Lambda function with a Python handler that processes event data
- Construct a serverless REST API with API Gateway, Lambda, and DynamoDB
- Integrate Lambda with multiple event sources (S3, SQS, SNS, DynamoDB Streams)
- Troubleshoot cold start latency and apply mitigation strategies
- Compare push-based triggers vs. poll-based event source mappings
- Differentiate .zip archives vs. container images for Lambda packaging
- Build reusable components using Lambda Layers

---

## Prerequisites and agenda

**Prerequisites:** Module 02 (IAM roles), Module 05 (S3 events), Module 06 (DynamoDB), Module 08 (SQS, SNS, EventBridge)

**Agenda:**
1. What is serverless computing?
2. Lambda fundamentals: handler, event, context
3. Supported runtimes
4. Event sources and triggers
5. IAM execution roles and configuration
6. Cold starts: causes and mitigation
7. Lambda Layers and packaging
8. API Gateway and Lambda: building REST APIs

---

# What is serverless computing?

<!-- Speaker notes: This section takes approximately 10 minutes. Connect to Module 04: EC2 is like owning a car (you maintain it), Lambda is like a taxi (you pay per ride). -->

---

## Serverless characteristics

- **No server management:** AWS handles provisioning, patching, scaling
- **Pay-per-invocation:** charged per request and execution duration
- **Automatic scaling:** from zero to thousands of concurrent executions
- **Event-driven:** functions run in response to events, not continuously

Well suited for: file processing, API requests, data transformation, scheduled tasks

> Serverless does not replace all compute. Long-running processes (over 15 min) or persistent connections are better on EC2 or containers.

---

# Lambda fundamentals

<!-- Speaker notes: This section takes approximately 15 minutes. Walk through the handler, event, and context with a live demo in the console. -->

---

## The handler function

The entry point Lambda calls when your function is invoked:

```python
def lambda_handler(event, context):
    name = event.get("name", "World")
    return {
        "statusCode": 200,
        "body": f"Hello, {name}"
    }
```

- **event:** JSON document with data to process (structure varies by source)
- **context:** runtime info (function name, request ID, remaining time)
- Handler setting format: `file_name.function_name`

---

## The execution environment lifecycle

1. **Init phase:** download code, initialize runtime, run code outside handler (runs once)
2. **Invoke phase:** call handler with event and context (repeats per invocation)
3. **Shutdown phase:** environment released after inactivity

Optimize by placing initialization outside the handler:

```python
import boto3

# Runs once during Init (connection reuse)
dynamodb = boto3.resource("dynamodb")
table = dynamodb.Table("Orders")

def lambda_handler(event, context):
    return table.get_item(Key={"OrderId": event["order_id"]})
```

---

## Quick check: execution environment reuse

A Lambda function initializes a database connection outside the handler. The function is invoked 100 times in rapid succession.

**How many times is the database connection created?**

<!-- Speaker notes: Answer: It depends on concurrency, but far fewer than 100. Lambda reuses execution environments for subsequent invocations (warm starts). If all 100 invocations are handled by 10 concurrent environments, the connection is created 10 times (once per environment during Init). This is why placing initialization outside the handler is a best practice. -->

---

# Supported runtimes

<!-- Speaker notes: This section takes approximately 10 minutes. Cover managed runtimes and container image packaging. -->

---

## Managed runtime options

| Runtime | Languages | Notes |
|---------|-----------|-------|
| Node.js | JavaScript, TypeScript | Includes AWS SDK v3 |
| Python | Python | Includes boto3 |
| Java | Java, Kotlin, Scala | Supports SnapStart |
| .NET | C#, F#, PowerShell | .NET 8 managed runtime |
| Ruby | Ruby | Includes AWS SDK for Ruby |

- Custom runtimes via `provided.al2023` (Go, Rust, C++)
- Container images up to 10 GB for full control

> Python and Node.js have the fastest cold starts. Java and .NET offer strong type systems.

---

# Event sources and triggers

<!-- Speaker notes: This section takes approximately 15 minutes. Draw push-based on one side and poll-based on the other. Have students categorize each service. -->

---

## Push-based triggers

The source service invokes Lambda directly:
- **API Gateway:** on HTTP requests
- **S3:** on object creation/deletion (Module 05)
- **SNS:** on message published to topic (Module 08)
- **EventBridge:** on matching event pattern
- **CloudWatch Events:** on schedule (cron/rate)

Permission model: resource-based policy on the function

---

## Poll-based event source mappings

Lambda polls the source for new records:
- **SQS:** polls queue for messages (Module 08)
- **DynamoDB Streams:** polls for table changes (Module 06)
- **Kinesis Data Streams:** polls for new data records

Permission model: execution role needs read access to the source

---

## Push vs. poll comparison

| Feature | Push-Based | Poll-Based |
|---------|-----------|------------|
| Who invokes Lambda | Source service | Lambda polls |
| Permission model | Resource-based policy | Execution role IAM policy |
| Batching | Typically single event | Configurable batch size |
| Error handling | Sync/async invocation type | Auto retries, failure destinations |
| Examples | API Gateway, S3, SNS | SQS, DynamoDB Streams |

---

## Discussion: choosing an invocation pattern

Scenario A: Process images immediately when uploaded to S3.
Scenario B: Drain a queue of order processing messages at a controlled rate.

**Which invocation pattern fits each scenario?**

<!-- Speaker notes: Answer: Scenario A is push-based (S3 event notification triggers Lambda directly on upload). Scenario B is poll-based (Lambda polls the SQS queue with configurable batch size and concurrency). The key distinction: push is immediate and event-driven, poll gives you control over processing rate and batching. -->

---

# IAM execution roles and configuration

<!-- Speaker notes: This section takes approximately 10 minutes. Connect to Module 02's IAM concepts. -->

---

## Execution role: least privilege

Every Lambda function needs an IAM execution role:

```json
{
    "Effect": "Allow",
    "Action": ["dynamodb:GetItem", "dynamodb:Query"],
    "Resource": "arn:aws:dynamodb:us-east-1:123456789012:table/Orders"
}
```

| Managed Policy | Permissions |
|----------------|-------------|
| AWSLambdaBasicExecutionRole | CloudWatch Logs only |
| AWSLambdaDynamoDBExecutionRole | DynamoDB Streams + Logs |
| AWSLambdaSQSQueueExecutionRole | SQS + Logs |

> AWSLambdaBasicExecutionRole only grants logging. Add permissions for every other service your function accesses.

---

## Configuration settings

| Setting | Range | Default | Impact |
|---------|-------|---------|--------|
| Memory | 128 MB to 10,240 MB | 128 MB | More memory = more CPU |
| Timeout | 1s to 900s | 3s | Max execution time |
| Reserved concurrency | 0 to account limit | Unreserved | Caps concurrent executions |
| Ephemeral storage | 512 MB to 10,240 MB | 512 MB | Temp disk in `/tmp` |

> Use Lambda Power Tuning to find the optimal memory setting. More memory often reduces cost.

---

# Cold starts

<!-- Speaker notes: This section takes approximately 10 minutes. Run a live demo showing cold start vs warm start duration difference. -->

---

## What causes cold starts?

- First invocation or after a period of inactivity
- Scaling up beyond available execution environments
- Code or configuration changes

Cold start duration depends on: runtime language, package size, initialization code, VPC configuration, and memory allocation.

---

## Mitigation strategies

| Strategy | How It Helps | Trade-off |
|----------|-------------|-----------|
| Provisioned concurrency | Pre-warms environments | Pay for idle environments |
| Minimize package size | Faster download and extract | Careful dependency management |
| SnapStart (Java only) | Snapshot-based restore | Java runtimes only |
| Init outside handler | Reuse across invocations | None (always do this) |
| Lightweight runtime | Python/Node.js start faster | Language choice constraints |

> For most workloads, cold starts add 100-500ms (Python/Node.js). Use provisioned concurrency for latency-sensitive APIs.

---

# Lambda Layers and packaging

<!-- Speaker notes: This section takes approximately 10 minutes. Cover layers for code sharing and the two packaging formats. -->

---

## Lambda Layers

- Package libraries and dependencies separately from function code
- Extracted to `/opt` in the execution environment
- Share across multiple functions (up to 5 layers per function)
- Smaller deployment packages = faster deployments and cold starts

---

## Packaging: .zip vs. container images

| Feature | .zip Archive | Container Image |
|---------|-------------|-----------------|
| Max size | 250 MB (unzipped) | 10 GB |
| Build tool | zip, pip, npm | Docker |
| Registry | Lambda service or S3 | Amazon ECR |
| Custom OS libraries | Limited | Full control |
| Best for | Standard dependencies | Large deps, container workflows |

> Start with .zip for simplicity. Use container images when dependencies exceed the size limit.

---

# API Gateway and Lambda

<!-- Speaker notes: This section takes approximately 10 minutes. Show the full serverless API architecture. -->

---

## Building a serverless REST API

```
Client --> API Gateway --> Lambda --> DynamoDB
                                 --> CloudWatch Logs
```

Lambda proxy integration passes the full HTTP request as the event:

```python
def lambda_handler(event, context):
    method = event["httpMethod"]
    path = event["path"]
    return {
        "statusCode": 200,
        "headers": {"Content-Type": "application/json"},
        "body": json.dumps({"message": f"{method} {path}"})
    }
```

> API Gateway has a 29-second timeout for Lambda integrations. Design API functions to complete within this limit.

---

## Think about it: serverless trade-offs

Your team is deciding between EC2 with Auto Scaling and Lambda for a new REST API that receives 1,000 requests per second during business hours and near zero at night.

**What are the trade-offs of each approach?**

<!-- Speaker notes: Lambda: zero cost at night, automatic scaling, no servers to manage, but 29s timeout limit and potential cold starts. EC2: more control, no timeout limit, but you pay for instances even at night (unless you scale to zero, which takes time), and you manage patching and scaling. For this traffic pattern (high variability, zero at night), Lambda is likely the better fit. The key insight is that Lambda excels when traffic is variable and you want to pay only for actual usage. -->

---

## Key takeaways

- Lambda runs code in response to events with no servers to manage, scaling automatically and charging only for actual compute time.
- Every function needs an IAM execution role following least privilege, granting only the specific actions on specific resources the function needs.
- Cold starts add latency to new execution environments; mitigate with small packages, init outside the handler, and provisioned concurrency for latency-sensitive workloads.
- Lambda integrates via push-based triggers (S3, SNS, API Gateway) and poll-based event source mappings (SQS, DynamoDB Streams).
- API Gateway + Lambda + DynamoDB creates a fully serverless REST API with no infrastructure to provision and pay-per-request pricing.

---

## Lab preview: building serverless applications

**Objective:** Create Lambda functions triggered by API Gateway, S3, and SQS; build a serverless API with DynamoDB backend

**Key services:** AWS Lambda, API Gateway, DynamoDB, S3, SQS, IAM

**Duration:** 60 minutes

<!-- Speaker notes: This is the first Phase 3 lab with semi-guided steps. Students will create a Lambda function with a Python handler, connect it to API Gateway, create a DynamoDB table, and wire up S3 and SQS triggers. Steps 1-4 are guided; steps 5-7 provide the goal and hints but let students figure out the implementation. Remind students about the Free Tier limits for Lambda. -->

---

# Questions?

Review `modules/09-serverless-lambda/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions involve Lambda vs EC2 selection, handling secrets, and VPC timeout issues. Transition to the lab when ready. -->
