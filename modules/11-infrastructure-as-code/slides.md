---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 11: Infrastructure as Code'
---

# Module 11: Infrastructure as Code with CloudFormation, SAM, and CDK

**Phase 3: Building Applications**
Estimated lecture time: 90 minutes

<!-- Speaker notes: Welcome to Module 11. This module transitions students from manual provisioning to Infrastructure as Code. Total time: ~90 min. Breakdown: 15 min IaC concepts, 20 min CloudFormation, 15 min intrinsic functions, 10 min stacks/change sets, 15 min SAM, 10 min CDK, 5 min wrap-up. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Build a CloudFormation template that provisions a VPC, subnets, and an EC2 instance
- Construct parameterized templates with cross-stack outputs
- Integrate intrinsic functions (`!Ref`, `!Sub`, `!GetAtt`, `!Join`)
- Troubleshoot failed stack operations using events and rollback behavior
- Compare CloudFormation, SAM, and CDK for different workloads
- Build a serverless app template using SAM with local testing
- Differentiate imperative vs. declarative IaC approaches

---

## Prerequisites and agenda

**Prerequisites:** Modules 02 (IAM), 03 (VPC), 04 (EC2), 05 (S3), 06 (Databases), 09 (Lambda)

**Agenda:**
1. Why Infrastructure as Code?
2. CloudFormation overview and template anatomy
3. Intrinsic functions
4. Parameters, outputs, and cross-stack references
5. Creating, updating, and deleting stacks
6. Change sets and drift detection
7. AWS SAM and the SAM CLI
8. AWS CDK overview

---

# Why Infrastructure as Code?

<!-- Speaker notes: This section takes about 10 minutes. Ask students to recall how they created a VPC in Module 03 manually. Contrast that with a 20-line YAML file. -->

---

## The problem with manual provisioning

- Manual console clicks are slow and error-prone
- Impossible to reproduce exactly in another Region or account
- No audit trail for who changed what
- Inconsistencies between dev, staging, and production
- Cannot integrate with CI/CD pipelines

---

## Five benefits of IaC

| Benefit | Description |
|---------|-------------|
| Repeatability | Deploy the same infrastructure anywhere from one template |
| Version control | Store templates in Git; review changes in pull requests |
| Automation | Integrate deployments into CI/CD pipelines (Module 12) |
| Drift detection | Compare actual state against the template definition |
| Consistency | Same template, different parameters for each environment |

---

## Discussion: when does manual provisioning break down?

Your team manages 15 microservices across dev, staging, and production. Each service needs a VPC, subnets, security groups, an ALB, and an ECS cluster.

**How would you keep all three environments consistent without IaC?**

<!-- Speaker notes: Expected answer: You cannot reliably keep them consistent manually. Any manual change in one environment risks divergence. IaC templates ensure all environments are provisioned identically. Common wrong answer: "Just document the steps." Documentation drifts from reality over time. -->

---

# CloudFormation overview

<!-- Speaker notes: This section takes about 15 minutes. Cover the core concepts: template, stack, resource. Emphasize that CloudFormation is free; you pay only for the resources it creates. -->

---

## How CloudFormation works

1. You write a YAML template declaring desired resources
2. You submit the template to CloudFormation
3. CloudFormation resolves dependencies and determines creation order
4. CloudFormation calls AWS APIs to create each resource
5. If any resource fails, CloudFormation rolls back the entire stack

---

## Core concepts

- **Template:** A YAML or JSON file describing your AWS resources
- **Stack:** A collection of resources managed as a single unit
- **Resource:** An individual AWS component (EC2, S3, DynamoDB)
- Each resource has a logical name (in template) and physical name (assigned by AWS)

> CloudFormation is free. You pay only for the resources it provisions.

---

## Template anatomy

| Section | Required | Purpose |
|---------|----------|---------|
| `AWSTemplateFormatVersion` | No | Template format version (`"2010-09-09"`) |
| `Description` | No | Text describing the template |
| `Parameters` | No | Input values for reusability |
| `Mappings` | No | Static key-value lookup tables |
| `Conditions` | No | Boolean expressions for conditional resources |
| `Resources` | Yes | AWS resources to create |
| `Outputs` | No | Values to expose after stack creation |

---

## A minimal CloudFormation template

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: A VPC with DNS support

Resources:
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      Tags:
        - Key: Name
          Value: bootcamp-vpc
```

> Write templates in YAML rather than JSON for readability and comment support.

---

# Intrinsic functions

<!-- Speaker notes: This section takes about 15 minutes. Build a template live, starting with hardcoded values, then refactoring to use !Ref, !Sub, and !GetAtt. -->

---

## Intrinsic functions summary

| Function | Short Form | Purpose |
|----------|-----------|---------|
| `Ref` | `!Ref` | Returns parameter value or resource physical ID |
| `Fn::Sub` | `!Sub` | Substitutes variables in a string |
| `Fn::GetAtt` | `!GetAtt` | Returns a specific attribute of a resource |
| `Fn::Join` | `!Join` | Concatenates values with a delimiter |
| `Fn::Select` | `!Select` | Returns one value from a list by index |
| `Fn::If` | `!If` | Returns one of two values based on a condition |

---

## Ref and Sub in action

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

Outputs:
  ApiUrl:
    Value: !Sub "https://${MyApi}.execute-api.${AWS::Region}.amazonaws.com/prod"
```

---

## Quick check: what does each function return?

Given a stack with an S3 bucket named `DataBucket`:

1. `!Ref DataBucket` returns ___
2. `!GetAtt DataBucket.Arn` returns ___
3. `!Sub "arn:aws:s3:::${DataBucket}"` returns ___

<!-- Speaker notes: Answers: 1) The bucket name (physical ID). 2) The full ARN of the bucket. 3) The same ARN, constructed via string substitution. This reinforces that Ref returns the primary ID while GetAtt returns specific attributes. -->

---

# Parameters and outputs

<!-- Speaker notes: This section takes about 10 minutes. Show how parameters make templates reusable across environments. -->

---

## Parameterizing templates

```yaml
Parameters:
  EnvironmentType:
    Type: String
    Default: dev
    AllowedValues: [dev, staging, prod]
  InstanceType:
    Type: String
    Default: t3.micro
    AllowedValues: [t3.micro, t3.small, m5.large]
```

- Parameters accept input at stack creation time
- Constraints validate input before any resources are created
- AWS-specific types (`AWS::EC2::KeyPair::KeyName`) verify existence

---

## Cross-stack references with outputs

```yaml
# Network stack exports:
Outputs:
  PublicSubnetId:
    Value: !Ref PublicSubnet
    Export:
      Name: !Sub "${AWS::StackName}-public-subnet-id"

# Application stack imports:
Resources:
  WebServer:
    Type: AWS::EC2::Instance
    Properties:
      SubnetId: !ImportValue network-stack-public-subnet-id
```

> You cannot delete a stack that has exported values imported by another stack.

---

# Creating, updating, and deleting stacks

<!-- Speaker notes: This section takes about 5 minutes. Cover the three update types and rollback behavior. -->

---

## Stack update types

| Update Type | Behavior | Example |
|-------------|----------|---------|
| No interruption | Updated in place, no downtime | Changing an EC2 tag |
| Some interruption | Brief downtime during update | Changing an EC2 instance type |
| Replacement | New resource created, old deleted | Changing an RDS database engine |

> Always set a `DeletionPolicy` of `Retain` or `Snapshot` on databases and S3 buckets with important data.

---

## Change sets: preview before you apply

1. **Create** the change set with your updated template
2. **Review** each proposed change (Add, Modify, Remove)
3. **Execute** if acceptable, or discard and revise

```bash
aws cloudformation create-change-set \
    --stack-name my-vpc-stack \
    --change-set-name add-private-subnet \
    --template-body file://vpc-template-v2.yaml
```

> Use change sets for every stack update, even in development.

---

## Drift detection

- Manual changes outside CloudFormation cause "drift"
- Drift detection compares actual state vs. template definition
- Resources reported as `IN_SYNC`, `MODIFIED`, or `DELETED`
- Run drift detection before applying stack updates

---

# AWS SAM

<!-- Speaker notes: This section takes about 15 minutes. Show a SAM template side by side with the equivalent raw CloudFormation. Count the lines to make the simplification tangible. -->

---

## SAM simplifies serverless templates

| SAM Resource Type | What It Creates Automatically |
|-------------------|-------------------------------|
| `AWS::Serverless::Function` | Lambda function, IAM role, event source, CloudWatch Logs |
| `AWS::Serverless::Api` | API Gateway REST API, deployment, stage |
| `AWS::Serverless::SimpleTable` | DynamoDB table with single-attribute key |

- SAM templates use `Transform: AWS::Serverless-2016-10-31`
- The `Globals` section sets defaults for all functions

---

## SAM template example

```yaml
Transform: AWS::Serverless-2016-10-31
Globals:
  Function:
    Runtime: python3.12
    Timeout: 30
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
```

---

## SAM CLI workflow

| Command | Purpose |
|---------|---------|
| `sam init` | Scaffold a new SAM project |
| `sam build` | Install dependencies and prepare artifacts |
| `sam local invoke` | Test a function locally in Docker |
| `sam local start-api` | Start a local API Gateway simulator |
| `sam deploy --guided` | Package and deploy to AWS |

---

## Discussion: CloudFormation vs. SAM for a serverless API

You need to deploy a Lambda function behind API Gateway with a DynamoDB table. In raw CloudFormation, this requires separate resources for the function, role, API, deployment, stage, and table.

**How many resources does the equivalent SAM template need?**

<!-- Speaker notes: Answer: Two resources (AWS::Serverless::Function with an Api event, and AWS::Serverless::SimpleTable). SAM auto-generates the IAM role, API Gateway resources, and CloudWatch Logs group. This is the key value proposition of SAM: less YAML, same result. -->

---

# AWS CDK overview

<!-- Speaker notes: This section takes about 10 minutes. Keep it conceptual. Students do not need to write CDK code in this module. The goal is awareness of the imperative approach. -->

---

## Imperative vs. declarative IaC

| Approach | Tool | How You Define Infrastructure |
|----------|------|-------------------------------|
| Declarative | CloudFormation, SAM | Write YAML describing the desired state |
| Imperative | CDK | Write TypeScript/Python code that generates the state |

- CDK uses constructs at three levels: L1 (raw CFN), L2 (curated), L3 (patterns)
- `bucket.grantRead(fn)` generates the correct IAM policy automatically
- CDK synthesizes to CloudFormation under the hood

---

## Choosing your IaC tool

| Criteria | CloudFormation | SAM | CDK |
|----------|---------------|-----|-----|
| Syntax | YAML/JSON | YAML (extended) | TypeScript, Python, Java |
| Best for | General infrastructure | Serverless apps | Complex, reusable patterns |
| Local testing | No | `sam local invoke` | CDK assertions (unit tests) |
| Learning curve | Moderate | Low | Higher |

> These tools are not mutually exclusive. Many teams use SAM for serverless and CloudFormation for networking.

---

## Key takeaways

- IaC eliminates manual provisioning by defining resources in version-controlled templates, ensuring repeatability and consistency
- CloudFormation is the foundational IaC service on AWS; every resource from Modules 03-10 can be defined declaratively
- Always use change sets to preview stack updates before executing them in production
- SAM extends CloudFormation with simplified syntax for serverless apps; the SAM CLI enables local testing before deployment
- Choose your IaC tool based on workload type and team skills: CloudFormation for general infra, SAM for serverless, CDK for complex patterns

---

## Lab preview: CloudFormation VPC and SAM serverless API

**What you will do:**
- Write a CloudFormation template that provisions a VPC with subnets
- Use parameters for environment-specific values
- Build a SAM template for a serverless API with Lambda and DynamoDB
- Test locally with `sam local invoke` and deploy to AWS

**Duration:** 60 minutes
**Key services:** CloudFormation, SAM, Lambda, API Gateway, DynamoDB

<!-- Speaker notes: Remind students to have Docker installed for sam local commands. The lab has 4 guided steps and 3 semi-guided steps. Encourage students to use change sets when updating their stacks. -->

---

# Questions?

Review `modules/11-infrastructure-as-code/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions at this point: "Can I import existing resources?" (Yes, via resource import.) "How do I handle secrets?" (Use SSM Parameter Store or Secrets Manager dynamic references, never hardcode.) -->
