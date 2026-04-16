# Module 11: Infrastructure as Code with CloudFormation, SAM, and CDK

## Learning Objectives

By the end of this module, you will be able to:

- Build a CloudFormation template that provisions a Virtual Private Cloud (VPC), subnets, and an Amazon Elastic Compute Cloud (Amazon EC2) instance using declarative YAML syntax
- Construct a parameterized CloudFormation template that accepts environment-specific values and produces cross-stack outputs for reuse
- Integrate intrinsic functions such as `!Ref`, `!Sub`, `!GetAtt`, and `!Join` into CloudFormation templates to create dynamic resource configurations
- Troubleshoot failed CloudFormation stack operations by interpreting stack events, rollback behavior, and error messages
- Compare AWS CloudFormation, AWS Serverless Application Model (AWS SAM), and AWS Cloud Development Kit (AWS CDK) to differentiate when each tool is the best fit for a given workload
- Build a serverless application template using AWS SAM with Globals, event sources, and local testing through the SAM Command Line Interface (CLI)
- Differentiate between imperative and declarative approaches to Infrastructure as Code (IaC) and identify the trade-offs of each

## Prerequisites

- Completion of [Module 02: Identity and Access Management (IAM) and Security](../02-iam-and-security/README.md) (IAM roles and policies for CloudFormation service permissions)
- Completion of [Module 03: Networking Basics](../03-networking-basics/README.md) (VPC, subnets, route tables, and security groups that you will now define as code)
- Completion of [Module 04: Compute with Amazon EC2](../04-compute-ec2/README.md) (EC2 instances, launch templates, and Auto Scaling groups that you will codify in templates)
- Completion of [Module 05: Storage with Amazon S3](../05-storage-s3/README.md) (S3 buckets and bucket policies that you will define declaratively)
- Completion of [Module 06: Databases with Amazon RDS and DynamoDB](../06-databases-rds-dynamodb/README.md) (DynamoDB tables that you will provision through SAM templates)
- Completion of [Module 09: Serverless Computing with AWS Lambda](../09-serverless-lambda/README.md) (Lambda functions, API Gateway integrations, and event sources that SAM simplifies)
- An AWS account with console and CLI access
- AWS CLI installed and configured with a named profile

## Concepts

### Why Infrastructure as Code?

In Modules 03 through 10, you created AWS resources manually through the console and CLI. Manual provisioning works for learning, but it introduces serious problems in production environments. If you need to recreate the same VPC, EC2 instances, and Lambda functions in a second AWS Region or a staging account, you must repeat every step by hand. Manual processes are slow, error-prone, and impossible to audit reliably.

[Infrastructure as Code (IaC)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) solves these problems by defining your infrastructure in text files that you store in version control. Instead of clicking through the console, you write a template that describes the resources you need. A provisioning engine reads the template and creates, updates, or deletes resources to match the desired state.

IaC provides five core benefits:

| Benefit | Description |
|---------|-------------|
| Repeatability | Deploy the same infrastructure in any Region or account by running the same template. No manual steps to forget or reorder. |
| Version control | Store templates in Git alongside application code. Review changes in pull requests, track history, and roll back to previous versions. |
| Automation | Integrate template deployments into CI/CD pipelines (Module 12) to provision infrastructure automatically on every code push. |
| Drift detection | Compare the actual state of deployed resources against the template to identify manual changes that deviate from the defined configuration. |
| Consistency across environments | Use the same template with different parameter values to create identical development, staging, and production environments. |

> **Tip:** You already practiced version control with Git for application code. IaC extends the same workflow to infrastructure. Treat your templates as source code: review them, test them, and never modify production resources outside the template.

### CloudFormation Overview

[AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) is the native IaC service for AWS. You write a template in YAML or JSON that declares the AWS resources you want. CloudFormation reads the template, determines the order of resource creation based on dependencies, and provisions everything as a single unit called a [stack](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html).

The core concepts in CloudFormation are:

- **Template.** A YAML or JSON file that describes your desired AWS resources and their configuration. The template is the blueprint.
- **Stack.** A collection of AWS resources that CloudFormation creates and manages as a single unit. When you create a stack from a template, CloudFormation provisions all the resources defined in that template. When you delete the stack, CloudFormation deletes all the resources.
- **Resource.** An individual AWS component (such as an EC2 instance, an S3 bucket, or a DynamoDB table) declared in the template. Each resource has a logical name (used within the template) and a physical name (assigned by AWS at creation time).

Here is how CloudFormation works at a high level:

1. You write a template that declares the resources you need.
2. You submit the template to CloudFormation (through the console, CLI, or API).
3. CloudFormation analyzes the template, resolves dependencies between resources, and determines the creation order.
4. CloudFormation calls the underlying AWS APIs to create each resource.
5. If any resource fails to create, CloudFormation rolls back the entire stack to its previous state by default.

> **Tip:** CloudFormation is free to use. You pay only for the AWS resources that CloudFormation provisions (EC2 instances, S3 buckets, Lambda functions, and so on). There is no additional charge for the CloudFormation service itself.


### CloudFormation Template Anatomy

A [CloudFormation template](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html) is organized into sections. Only the `Resources` section is required. All other sections are optional but commonly used in production templates.

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: A sample template that creates a VPC and an EC2 instance

Parameters:
  EnvironmentType:
    Type: String
    Default: dev
    AllowedValues:
      - dev
      - staging
      - prod

Mappings:
  RegionAMI:
    us-east-1:
      HVM64: ami-0abcdef1234567890

Conditions:
  IsProduction: !Equals [!Ref EnvironmentType, prod]

Resources:
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      Tags:
        - Key: Name
          Value: !Sub "${EnvironmentType}-vpc"

Outputs:
  VPCId:
    Description: The ID of the VPC
    Value: !Ref MyVPC
    Export:
      Name: !Sub "${EnvironmentType}-vpc-id"
```

Each section serves a specific purpose:

| Section | Required | Purpose |
|---------|----------|---------|
| `AWSTemplateFormatVersion` | No | Identifies the template format version. The only valid value is `"2010-09-09"`. |
| `Description` | No | A text string that describes the template. Must follow the `AWSTemplateFormatVersion` section if both are present. |
| [`Parameters`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html) | No | Input values that you pass to the template at stack creation or update time. Parameters make templates reusable across environments. |
| `Mappings` | No | Static key-value lookup tables. Use mappings to select values based on a condition such as the AWS Region or environment type. |
| `Conditions` | No | Boolean expressions that control whether certain resources are created or whether certain property values are applied. |
| `Resources` | Yes | The AWS resources to create. Every template must have at least one resource. Each resource has a type (such as `AWS::EC2::Instance`) and properties. |
| [`Outputs`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html) | No | Values that you can view after stack creation, pass to other stacks, or return in API responses. Outputs are essential for cross-stack references. |

> **Tip:** Write templates in YAML rather than JSON. YAML supports comments, is more readable, and requires less punctuation. CloudFormation accepts both formats, but the AWS documentation and community examples predominantly use YAML.

### Intrinsic Functions

[Intrinsic functions](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference.html) are built-in functions that you use within CloudFormation templates to assign values that are available only at runtime. You cannot hardcode an EC2 instance ID or a VPC ID into your template because those values do not exist until CloudFormation creates the resources. Intrinsic functions solve this by referencing resources, substituting variables, and retrieving attributes dynamically.

#### Ref

[`Ref`](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference-ref.html) returns the value of a parameter or the physical ID of a resource. When you reference a parameter, `Ref` returns the parameter value. When you reference a resource, `Ref` returns the resource's primary identifier (for example, the instance ID of an EC2 instance or the name of an S3 bucket).

```yaml
Resources:
  MyBucket:
    Type: AWS::S3::Bucket

  MyFunction:
    Type: AWS::Lambda::Function
    Properties:
      Environment:
        Variables:
          BUCKET_NAME: !Ref MyBucket
```

#### Fn::Sub

[`Fn::Sub`](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference-sub.html) substitutes variables in a string. Variables are enclosed in `${}` and can reference parameters, resource logical names, or resource attributes. This function is useful for constructing Amazon Resource Names (ARNs), URLs, and descriptive tags.

```yaml
Outputs:
  BucketArn:
    Value: !Sub "arn:aws:s3:::${MyBucket}"
  ApiUrl:
    Value: !Sub "https://${MyApi}.execute-api.${AWS::Region}.amazonaws.com/prod"
```

`${AWS::Region}` and `${AWS::AccountId}` are [pseudo parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html) that CloudFormation provides automatically. You do not need to declare them.

#### Fn::GetAtt

[`Fn::GetAtt`](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference-getatt.html) returns the value of a specific attribute from a resource. While `Ref` returns the primary identifier, `GetAtt` can retrieve other attributes such as an ARN, a DNS name, or an endpoint URL.

```yaml
Outputs:
  BucketArn:
    Value: !GetAtt MyBucket.Arn
  BucketDomainName:
    Value: !GetAtt MyBucket.DomainName
```

Each resource type supports different attributes. Check the [CloudFormation resource reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html) for the attributes available on each resource type.

#### Fn::Join and Fn::Select

`Fn::Join` concatenates a list of values with a specified delimiter. `Fn::Select` returns a single value from a list by index.

```yaml
# Join example: produces "subnet-aaa,subnet-bbb,subnet-ccc"
SubnetList: !Join
  - ","
  - - !Ref SubnetA
    - !Ref SubnetB
    - !Ref SubnetC

# Select example: returns the first subnet from the list
FirstSubnet: !Select
  - 0
  - - !Ref SubnetA
    - !Ref SubnetB
    - !Ref SubnetC
```

#### Fn::If

`Fn::If` returns one of two values based on a condition defined in the `Conditions` section. Use it to set different property values for different environments.

```yaml
Conditions:
  IsProduction: !Equals [!Ref EnvironmentType, prod]

Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !If [IsProduction, m5.large, t3.micro]
```

#### Fn::ImportValue

`Fn::ImportValue` imports a value that was exported from another stack's `Outputs` section. This enables [cross-stack references](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html), where one stack exports a VPC ID and another stack imports it to launch resources into that VPC.

```yaml
Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      SubnetId: !ImportValue prod-public-subnet-id
```

#### Intrinsic Functions Summary

| Function | Short Form | Purpose |
|----------|-----------|---------|
| `Ref` | `!Ref` | Returns parameter value or resource physical ID |
| `Fn::Sub` | `!Sub` | Substitutes variables in a string |
| `Fn::GetAtt` | `!GetAtt` | Returns a specific attribute of a resource |
| `Fn::Join` | `!Join` | Concatenates values with a delimiter |
| `Fn::Select` | `!Select` | Returns one value from a list by index |
| `Fn::If` | `!If` | Returns one of two values based on a condition |
| `Fn::ImportValue` | N/A | Imports an exported value from another stack |


### Parameters and Outputs

#### Parameterizing Templates

[Parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html) make your templates reusable. Instead of hardcoding values like instance types, CIDR blocks, or environment names, you define parameters that accept input at stack creation time. This lets you use a single template for development, staging, and production by passing different parameter values.

Each parameter supports constraints that validate input before CloudFormation creates any resources:

```yaml
Parameters:
  InstanceType:
    Type: String
    Default: t3.micro
    AllowedValues:
      - t3.micro
      - t3.small
      - m5.large
    Description: EC2 instance type for the web server

  VpcCidr:
    Type: String
    Default: 10.0.0.0/16
    AllowedPattern: "^(\\d{1,3}\\.){3}\\d{1,3}/\\d{1,2}$"
    ConstraintDescription: Must be a valid CIDR block (e.g., 10.0.0.0/16)

  KeyPairName:
    Type: AWS::EC2::KeyPair::KeyName
    Description: Name of an existing EC2 key pair for SSH access
```

CloudFormation provides [AWS-specific parameter types](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html) such as `AWS::EC2::KeyPair::KeyName`, `AWS::EC2::VPC::Id`, and `AWS::EC2::Subnet::Id`. These types validate that the provided value exists in your account before stack creation begins, catching errors early.

#### Cross-Stack References with Outputs

[Outputs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html) expose values from your stack that other stacks or users can consume. When you add an `Export` block to an output, the value becomes available to other stacks in the same Region and account through `Fn::ImportValue`.

A common pattern is to create a "network stack" that provisions the VPC and subnets, then export the subnet IDs. An "application stack" imports those IDs to launch EC2 instances or Lambda functions into the correct subnets.

Network stack outputs:

```yaml
Outputs:
  PublicSubnetId:
    Description: Public subnet for web-facing resources
    Value: !Ref PublicSubnet
    Export:
      Name: !Sub "${AWS::StackName}-public-subnet-id"

  PrivateSubnetId:
    Description: Private subnet for backend resources
    Value: !Ref PrivateSubnet
    Export:
      Name: !Sub "${AWS::StackName}-private-subnet-id"
```

Application stack that imports the values:

```yaml
Resources:
  WebServer:
    Type: AWS::EC2::Instance
    Properties:
      SubnetId: !ImportValue network-stack-public-subnet-id
      InstanceType: t3.micro
      ImageId: ami-0abcdef1234567890
```

> **Warning:** You cannot delete a stack that has exported values currently imported by another stack. You must first update or delete the importing stack. Plan your stack dependencies carefully.

### Creating, Updating, and Deleting Stacks

#### Creating a Stack

You create a stack by submitting a template to CloudFormation. You can do this through the [AWS Management Console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html), the AWS CLI, or the CloudFormation API.

Using the AWS CLI:

```bash
aws cloudformation create-stack \
    --stack-name my-vpc-stack \
    --template-body file://vpc-template.yaml \
    --parameters ParameterKey=EnvironmentType,ParameterValue=dev
```

CloudFormation processes the template, resolves dependencies, and creates resources in the correct order. You can monitor progress by viewing stack events in the console or with the CLI:

```bash
aws cloudformation describe-stack-events \
    --stack-name my-vpc-stack
```

#### Updating a Stack

When you modify your template and submit the update, CloudFormation compares the new template to the current stack state and determines which resources need to change. Updates fall into three categories:

| Update Type | Behavior | Example |
|-------------|----------|---------|
| No interruption | The resource is updated in place with no downtime. | Changing an EC2 instance tag |
| Some interruption | The resource experiences brief downtime during the update. | Changing an EC2 instance type (requires stop/start) |
| Replacement | CloudFormation creates a new resource, updates references, then deletes the old resource. | Changing an RDS database engine |

```bash
aws cloudformation update-stack \
    --stack-name my-vpc-stack \
    --template-body file://vpc-template-v2.yaml \
    --parameters ParameterKey=EnvironmentType,ParameterValue=staging
```

#### Rollback Behavior

If a stack creation or update fails, CloudFormation [rolls back](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stack-failure-options.html) to the last known stable state by default:

- **Create failure.** CloudFormation deletes all resources it created during the failed operation. The stack enters the `ROLLBACK_COMPLETE` state.
- **Update failure.** CloudFormation reverts all resources to their previous configuration. The stack returns to the `UPDATE_ROLLBACK_COMPLETE` state.

You can disable automatic rollback during development to inspect the partially created resources and diagnose the failure. In production, always keep rollback enabled to maintain a consistent state.

#### Deleting a Stack

When you delete a stack, CloudFormation deletes all resources in the stack in the reverse order of their creation:

```bash
aws cloudformation delete-stack \
    --stack-name my-vpc-stack
```

> **Warning:** Deleting a stack permanently removes all resources in that stack unless you set a `DeletionPolicy` of `Retain` or `Snapshot` on specific resources. For databases and S3 buckets containing important data, always set a `DeletionPolicy` to prevent accidental data loss.


### Change Sets: Previewing Changes Before Applying

A [change set](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets-samples.html) lets you preview how proposed changes to a stack will affect your running resources before you execute them. This is critical in production environments where an unexpected resource replacement could cause downtime or data loss.

The change set workflow has three steps:

1. **Create the change set.** Submit your updated template. CloudFormation compares it to the current stack and generates a list of proposed changes.
2. **Review the change set.** Examine each proposed change to understand whether resources will be added, modified, or replaced.
3. **Execute or discard.** If the changes are acceptable, execute the change set to apply them. If not, delete the change set and revise your template.

```bash
# Step 1: Create the change set
aws cloudformation create-change-set \
    --stack-name my-vpc-stack \
    --change-set-name add-private-subnet \
    --template-body file://vpc-template-v2.yaml

# Step 2: Review the change set
aws cloudformation describe-change-set \
    --stack-name my-vpc-stack \
    --change-set-name add-private-subnet

# Step 3: Execute the change set
aws cloudformation execute-change-set \
    --stack-name my-vpc-stack \
    --change-set-name add-private-subnet
```

The `describe-change-set` output shows each resource change with its action (`Add`, `Modify`, or `Remove`) and whether the modification requires replacement. Always review change sets before executing them in production.

> **Tip:** CloudFormation [best practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html) recommend using change sets for every stack update, even in development. This habit prevents surprises when you move to production workflows.

### Drift Detection

Over time, team members or automated processes may modify resources outside of CloudFormation (for example, changing a security group rule through the console). These manual changes cause the actual resource configuration to diverge from the template definition. This divergence is called drift.

[Drift detection](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/detect-drift-stack.html) compares the current configuration of each resource in a stack against the expected configuration defined in the template. CloudFormation reports whether each resource is `IN_SYNC`, `MODIFIED`, or `DELETED`.

```bash
# Initiate drift detection
aws cloudformation detect-stack-drift \
    --stack-name my-vpc-stack

# Check drift detection status
aws cloudformation describe-stack-drift-detection-status \
    --stack-drift-detection-id <detection-id>

# View drift results for the stack
aws cloudformation describe-stack-resource-drifts \
    --stack-name my-vpc-stack
```

When you detect drift, you have two options: update the template to match the actual state (if the manual change was intentional) or update the stack to revert the resource to the template-defined state.

> **Tip:** Run drift detection regularly, especially before applying stack updates. Updating a stack that has drifted resources can produce unexpected results because CloudFormation assumes the current state matches the previous template.

### AWS Serverless Application Model (AWS SAM)

The [AWS Serverless Application Model (AWS SAM)](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam-overview.html) is an open-source framework that extends CloudFormation with a simplified syntax for defining serverless applications. SAM templates are CloudFormation templates with additional resource types and a `Globals` section that reduces repetition.

In Module 09, you built Lambda functions, API Gateway endpoints, and DynamoDB tables manually. SAM lets you define all of those resources in a single template with significantly less YAML than raw CloudFormation.

#### SAM Template Syntax

A [SAM template](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html) uses the `Transform: AWS::Serverless-2016-10-31` declaration to tell CloudFormation to process SAM-specific resource types. SAM introduces three key resource types:

| SAM Resource Type | Equivalent CloudFormation Resources |
|-------------------|-------------------------------------|
| `AWS::Serverless::Function` | Lambda function, IAM execution role, event source mapping, CloudWatch Logs log group |
| `AWS::Serverless::Api` | API Gateway REST API, deployment, stage |
| `AWS::Serverless::SimpleTable` | DynamoDB table with a single-attribute primary key |

Here is a SAM template that defines a Lambda function triggered by an API Gateway endpoint, with a DynamoDB table for storage:

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Transform: AWS::Serverless-2016-10-31
Description: A serverless API with Lambda and DynamoDB

Globals:
  Function:
    Runtime: python3.12
    Timeout: 30
    MemorySize: 256
    Environment:
      Variables:
        TABLE_NAME: !Ref OrdersTable

Resources:
  GetOrderFunction:
    Type: AWS::Serverless::Function
    Properties:
      Handler: app.get_order
      CodeUri: src/
      Events:
        GetOrder:
          Type: Api
          Properties:
            Path: /orders/{id}
            Method: get
      Policies:
        - DynamoDBReadPolicy:
            TableName: !Ref OrdersTable

  OrdersTable:
    Type: AWS::Serverless::SimpleTable
    Properties:
      PrimaryKey:
        Name: OrderId
        Type: String

Outputs:
  ApiUrl:
    Description: API Gateway endpoint URL
    Value: !Sub "https://${ServerlessRestApi}.execute-api.${AWS::Region}.amazonaws.com/Prod"
```

Notice how much SAM simplifies compared to raw CloudFormation. The `AWS::Serverless::Function` resource automatically creates the IAM execution role, configures the API Gateway integration, and sets up CloudWatch Logs. You do not need to define those resources separately.

#### The Globals Section

The [Globals section](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy-globals.html) defines properties that apply to all resources of a given type. In the example above, every `AWS::Serverless::Function` in the template inherits the `Runtime`, `Timeout`, `MemorySize`, and `Environment` settings from Globals. Individual functions can override any global property by specifying it explicitly.

Globals reduce duplication when your template contains multiple Lambda functions that share the same runtime, timeout, and environment variables.

#### SAM Policy Templates

SAM provides [policy templates](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-policy-templates.html) that generate scoped IAM policies for common access patterns. Instead of writing a full IAM policy document, you reference a template name and pass the required parameters:

| Policy Template | Permissions Granted |
|-----------------|---------------------|
| `DynamoDBReadPolicy` | `GetItem`, `Query`, `Scan`, `BatchGetItem` on a specific table |
| `DynamoDBCrudPolicy` | Full CRUD operations on a specific table |
| `S3ReadPolicy` | `GetObject`, `ListBucket` on a specific bucket |
| `SQSPollerPolicy` | `ReceiveMessage`, `DeleteMessage`, `GetQueueAttributes` on a specific queue |

These policy templates follow the principle of least privilege by scoping permissions to the specific resource you reference.


### SAM CLI: Local Development and Deployment

The [SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-corecommands.html) is a command-line tool that helps you build, test, and deploy SAM applications. It uses Docker to simulate the Lambda execution environment on your local machine, so you can test functions before deploying to AWS.

#### Core SAM CLI Commands

| Command | Purpose |
|---------|---------|
| [`sam init`](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-init.html) | Scaffold a new SAM project from a template. Prompts you for runtime, project name, and application template. |
| [`sam build`](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-build.html) | Build your application. Installs dependencies, compiles code (if needed), and prepares deployment artifacts in the `.aws-sam/build/` directory. |
| [`sam local invoke`](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-local-invoke.html) | Invoke a single Lambda function locally with a test event. Runs the function in a Docker container that matches the Lambda execution environment. |
| [`sam local start-api`](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-local-start-api.html) | Start a local HTTP server that simulates API Gateway. Send HTTP requests to test your API endpoints locally. |
| [`sam deploy`](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-deploy.html) | Package and deploy your application to AWS. Uploads artifacts to S3, transforms the SAM template into CloudFormation, and creates or updates the stack. |

#### Typical SAM Workflow

```bash
# 1. Create a new SAM project
sam init --runtime python3.12 --name my-api --app-template hello-world

# 2. Build the application
cd my-api
sam build

# 3. Test a function locally with a sample event
sam local invoke HelloWorldFunction --event events/event.json

# 4. Start a local API server and test with curl
sam local start-api
# In another terminal:
curl http://127.0.0.1:3000/hello

# 5. Deploy to AWS
sam deploy --guided
```

The `--guided` flag on `sam deploy` walks you through configuration options (stack name, Region, IAM capabilities) and saves your choices to a `samconfig.toml` file. Subsequent deployments use the saved configuration automatically.

> **Tip:** The `sam local invoke` and `sam local start-api` commands require Docker to be installed and running on your machine. Docker provides the container images that simulate the Lambda execution environment. Install Docker Desktop before using these commands.

### AWS Cloud Development Kit (AWS CDK)

The [AWS Cloud Development Kit (AWS CDK)](https://docs.aws.amazon.com/cdk/v2/guide/getting-started.html) takes a fundamentally different approach to IaC. Instead of writing declarative YAML templates, you define infrastructure using a general-purpose programming language such as TypeScript, Python, Java, C#, or Go. The CDK synthesizes your code into a CloudFormation template, which it then deploys.

#### Imperative vs. Declarative

CloudFormation and SAM use a declarative approach: you describe the desired end state, and the engine figures out how to get there. The CDK uses an imperative approach: you write code that constructs the desired state using programming constructs like loops, conditionals, and functions.

| Approach | Tool | How You Define Infrastructure |
|----------|------|-------------------------------|
| Declarative | CloudFormation, SAM | Write YAML/JSON describing the desired state |
| Imperative | CDK | Write TypeScript/Python/Java code that generates the desired state |

The imperative approach offers several advantages for complex infrastructure: you can use loops to create multiple similar resources, share logic through functions and classes, use your IDE's autocomplete and type checking, and write unit tests for your infrastructure code.

#### Constructs

[Constructs](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html) are the building blocks of CDK applications. Every CDK resource is a construct. Constructs come in three levels of abstraction:

| Level | Name | Description | Example |
|-------|------|-------------|---------|
| L1 | CFN Resources | Direct one-to-one mapping to CloudFormation resource types. No defaults or convenience methods. | `CfnBucket` |
| L2 | Curated Constructs | Higher-level abstractions with sensible defaults, convenience methods, and built-in best practices. | `Bucket` (with encryption enabled by default) |
| L3 | Patterns | Opinionated combinations of multiple resources that implement common architectures. | `LambdaRestApi` (creates API Gateway + Lambda integration) |

Here is a CDK example in TypeScript that creates an S3 bucket and a Lambda function with permission to read from the bucket:

```typescript
import * as cdk from 'aws-cdk-lib';
import * as s3 from 'aws-cdk-lib/aws-s3';
import * as lambda from 'aws-cdk-lib/aws-lambda';

export class MyStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string) {
    super(scope, id);

    const bucket = new s3.Bucket(this, 'DataBucket', {
      versioned: true,
      encryption: s3.BucketEncryption.S3_MANAGED,
      removalPolicy: cdk.RemovalPolicy.DESTROY,
    });

    const fn = new lambda.Function(this, 'ProcessorFunction', {
      runtime: lambda.Runtime.PYTHON_3_12,
      handler: 'app.handler',
      code: lambda.Code.fromAsset('lambda/'),
      environment: {
        BUCKET_NAME: bucket.bucketName,
      },
    });

    bucket.grantRead(fn);
  }
}
```

Notice the `bucket.grantRead(fn)` call. This single line generates the correct IAM policy that allows the Lambda function to read from the bucket. The CDK handles the IAM policy creation, role attachment, and resource ARN references automatically. In raw CloudFormation, this would require writing a separate IAM policy resource with the correct actions and resource ARN.

#### CDK Synthesis and Deployment

The CDK CLI converts your code into a CloudFormation template through a process called synthesis:

```bash
# Synthesize the CloudFormation template
cdk synth

# Deploy the stack
cdk deploy

# Compare local changes to deployed stack
cdk diff
```

The `cdk synth` command outputs the generated CloudFormation template to the `cdk.out/` directory. You can inspect this template to understand exactly what CloudFormation resources the CDK will create. The `cdk diff` command shows the differences between your local code and the currently deployed stack, similar to a change set.

> **Tip:** The CDK is a good fit when your team is comfortable with TypeScript or Python and your infrastructure is complex enough to benefit from programming constructs (loops, conditionals, shared libraries). For simpler infrastructure or teams that prefer declarative templates, CloudFormation or SAM may be more straightforward.

### Choosing Between CloudFormation, SAM, and CDK

All three tools ultimately produce CloudFormation stacks. SAM and CDK are higher-level abstractions that generate CloudFormation templates. The right choice depends on your workload type, team skills, and infrastructure complexity.

| Criteria | CloudFormation | SAM | CDK |
|----------|---------------|-----|-----|
| Syntax | Declarative YAML/JSON | Declarative YAML (extended) | Imperative (TypeScript, Python, Java, C#, Go) |
| Best for | General AWS infrastructure | Serverless applications | Complex infrastructure with reusable patterns |
| Learning curve | Moderate (YAML syntax, resource types) | Low (simplified serverless syntax) | Higher (requires programming language knowledge) |
| Local testing | No built-in local testing | `sam local invoke`, `sam local start-api` | No built-in local testing (use CDK assertions for unit tests) |
| IAM handling | Manual policy definitions | Policy templates for common patterns | Grant methods (`bucket.grantRead(fn)`) |
| Template size | Can become verbose for large stacks | Compact for serverless resources | Generated template can be large, but source code is concise |
| IDE support | YAML linting, schema validation | YAML linting, schema validation | Full IDE support (autocomplete, type checking, refactoring) |
| When to choose | You need full control over all resource types, or your team prefers declarative templates | You are building serverless applications with Lambda, API Gateway, and DynamoDB | Your infrastructure is complex, you want to share patterns across teams, or you prefer writing code over YAML |

> **Tip:** These tools are not mutually exclusive. Many teams use SAM for serverless workloads and CloudFormation for networking and shared infrastructure. The CDK can also generate SAM-compatible templates for serverless resources.


## Instructor Notes

**Estimated lecture time:** 90 minutes (plus 60 minutes for the lab)

**Common student questions:**

- Q: When should I use CloudFormation directly instead of SAM or CDK?
  A: Use CloudFormation directly when you need full control over every resource property, when your infrastructure is not serverless-focused, or when your team prefers working with declarative YAML. SAM is purpose-built for serverless workloads and reduces boilerplate significantly. CDK is best when your infrastructure is complex enough to benefit from programming constructs. All three produce CloudFormation stacks under the hood.

- Q: What happens if I manually change a resource that CloudFormation manages?
  A: The resource drifts from the template-defined state. CloudFormation does not automatically detect or correct drift. You must run [drift detection](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/detect-drift-stack.html) to identify changes. The next stack update may overwrite your manual changes or fail if the actual state conflicts with the expected state. The best practice is to make all changes through the template.

- Q: Can I import existing resources into a CloudFormation stack?
  A: Yes. CloudFormation supports [resource import](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resource-import-existing-stack.html), which lets you bring manually created resources under CloudFormation management. You add the resource to your template with a `DeletionPolicy` of `Retain`, then run an import operation. This is useful for migrating from manual provisioning to IaC without recreating resources.

- Q: How do I handle secrets in CloudFormation templates?
  A: Never hardcode secrets in templates. Use the `AWS::SSM::Parameter::Value` parameter type to reference values stored in AWS Systems Manager Parameter Store, or use dynamic references to retrieve secrets from AWS Secrets Manager at deployment time. This keeps sensitive values out of your template files and version control.

**Teaching tips:**

- Start the lecture by asking students to recall how they created a VPC in Module 03. Walk through the manual steps, then show the equivalent CloudFormation template. The contrast between 15 minutes of clicking and a 20-line YAML file makes the value of IaC immediately clear.
- When covering intrinsic functions, build a template live on screen. Start with hardcoded values, then refactor step by step to use `!Ref`, `!Sub`, and `!GetAtt`. Students understand functions better when they see the progression from static to dynamic.
- Pause after the CloudFormation section (before SAM) for questions. Students often need time to absorb the template anatomy and intrinsic functions before adding SAM on top.
- When demonstrating SAM, show the SAM template side by side with the equivalent raw CloudFormation template. Counting the lines of YAML in each version makes the simplification tangible.
- For the CDK section, keep it brief and conceptual. Students do not need to write CDK code in this module. The goal is awareness of the imperative approach so they can make informed tool choices in future projects.

**Pause points:**

- After "CloudFormation Template Anatomy": pause for questions about template sections and YAML syntax.
- After "Intrinsic Functions": pause for a live demo building a template with `!Ref` and `!Sub`.
- After "Change Sets": pause to demonstrate creating and reviewing a change set in the console.
- After "SAM CLI": pause for a live demo of `sam init`, `sam build`, and `sam local invoke`.

## Key Takeaways

- Infrastructure as Code eliminates manual provisioning by defining all resources in version-controlled templates, ensuring repeatability, auditability, and consistency across environments.
- CloudFormation is the foundational IaC service on AWS. Every resource you created manually in Modules 03 through 10 can be defined declaratively in a CloudFormation template.
- Always use change sets to preview stack updates before executing them in production. A single unexpected resource replacement can cause downtime or data loss.
- AWS SAM extends CloudFormation with a simplified syntax for serverless applications, reducing boilerplate for Lambda functions, API Gateway endpoints, and DynamoDB tables. The SAM CLI enables local testing before deployment.
- Choose your IaC tool based on workload type and team skills: CloudFormation for general infrastructure, SAM for serverless workloads, and CDK for complex infrastructure that benefits from programming language features.
