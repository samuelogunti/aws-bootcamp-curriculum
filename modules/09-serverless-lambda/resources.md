# Module 09: Resources

## Official Documentation

### AWS Lambda Core

- [What is AWS Lambda?](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
- [Create Your First Lambda Function](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html)
- [Define Lambda Function Handler in Python](https://docs.aws.amazon.com/lambda/latest/dg/python-handler.html)
- [Building Lambda Functions with Python](https://docs.aws.amazon.com/lambda/latest/dg/lambda-python.html)
- [Understanding the Lambda Execution Environment Lifecycle](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html)
- [Lambda Runtimes](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html)
- [When to Use Lambda's OS-Only Runtimes (Custom Runtimes)](https://docs.aws.amazon.com/lambda/latest/dg/runtimes-provided.html)
- [Lambda Quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html)

### Lambda Permissions and IAM

- [Managing Permissions in AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/lambda-permissions.html)
- [Lambda Execution Role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)
- [Working with AWS Managed Policies in the Execution Role](https://docs.aws.amazon.com/lambda/latest/dg/permissions-managed-policies.html)
- [AWS Managed Policies for AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/security-iam-awsmanpol.html)
- [Viewing Resource-Based IAM Policies in Lambda](https://docs.aws.amazon.com/lambda/latest/dg/access-control-resource-based.html)

### Lambda Configuration

- [Working with Lambda Environment Variables](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html)
- [Securing Lambda Environment Variables](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars-encryption.html)
- [Configure Lambda Function Memory](https://docs.aws.amazon.com/lambda/latest/dg/configuration-memory.html)
- [Configure Lambda Function Timeout](https://docs.aws.amazon.com/lambda/latest/dg/configuration-timeout.html)
- [Configuring Reserved Concurrency](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html)

### Cold Starts and Performance

- [Configuring Provisioned Concurrency](https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html)
- [Improving Startup Performance with Lambda SnapStart](https://docs.aws.amazon.com/lambda/latest/dg/snapstart.html)

### Lambda Layers

- [Managing Lambda Dependencies with Layers](https://docs.aws.amazon.com/lambda/latest/dg/chapter-layers.html)

### Lambda Packaging and Deployment

- [Working with .zip File Archives for Python Lambda Functions](https://docs.aws.amazon.com/lambda/latest/dg/python-package.html)
- [Create a Lambda Function Using a Container Image](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html)
- [Deploy Python Lambda Functions with Container Images](https://docs.aws.amazon.com/lambda/latest/dg/python-image.html)

### Lambda Event Sources and Triggers

- [Lambda Event Source Mappings](https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventsourcemapping.html)
- [Invoking a Lambda Function Using an Amazon API Gateway Endpoint](https://docs.aws.amazon.com/lambda/latest/dg/services-apigateway.html)
- [Process Amazon S3 Event Notifications with Lambda](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html)
- [Using Lambda with Amazon SNS](https://docs.aws.amazon.com/lambda/latest/dg/with-sns.html)
- [Using Lambda with Amazon SQS](https://docs.aws.amazon.com/lambda/latest/dg/services-sqs-configure.html)
- [Using Lambda with Amazon DynamoDB Streams](https://docs.aws.amazon.com/lambda/latest/dg/services-dynamodb-eventsourcemapping.html)
- [Using Lambda with Amazon EventBridge](https://docs.aws.amazon.com/lambda/latest/dg/services-eventbridge.html)
- [Using Lambda with Amazon CloudWatch Events](https://docs.aws.amazon.com/lambda/latest/dg/services-cloudwatchevents.html)

### Lambda Monitoring

- [Sending Lambda Function Logs to CloudWatch Logs](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-cloudwatchlogs.html)
- [Working with Lambda Function Logs](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-logs.html)

### Amazon API Gateway

- [What is Amazon API Gateway?](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html)
- [Lambda Proxy Integrations in API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html)
- [Set Up Lambda Proxy Integration as a Simple Proxy](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-create-api-as-simple-proxy-for-lambda.html)
- [Deploying a REST API in Amazon API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-deploy-api.html)
- [Get Started with API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/getting-started.html)

### Amazon S3 Event Notifications

- [S3 Event Notification Configuration](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-enable-disable-notification-first-time.html)
- [S3 Event Message Structure](https://docs.aws.amazon.com/AmazonS3/latest/userguide/notification-content-structure.html)

### Amazon DynamoDB (Referenced in Lab)

- [Creating a DynamoDB Table (AWS CLI)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/getting-started-step-1.html)
- [Writing an Item to a DynamoDB Table](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.WriteItem.html)
- [Reading an Item from a DynamoDB Table](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.ReadItem.html)

### AWS Serverless Application Repository

- [What is the AWS Serverless Application Repository?](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/what-is-serverlessrepo.html)

## AWS Whitepapers

- [AWS Serverless Multi-Tier Architectures with Amazon API Gateway and AWS Lambda](https://docs.aws.amazon.com/whitepapers/latest/serverless-multi-tier-architectures-api-gateway-lambda/index.html): Covers architectural patterns for building multi-tier serverless applications using API Gateway and Lambda, including microservices patterns and serverless data storage options.
- [Security Overview of AWS Lambda](https://docs.aws.amazon.com/whitepapers/latest/security-overview-aws-lambda/lambda-functions-and-layers.html): Describes the security model for Lambda functions and layers, including isolation, execution environment security, and shared responsibility.

## AWS FAQs

- [AWS Lambda FAQ](https://aws.amazon.com/lambda/faqs/)
- [Amazon API Gateway FAQ](https://aws.amazon.com/api-gateway/faqs/)

## AWS Architecture References

- [Serverless Applications Lens (AWS Well-Architected Framework)](https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/welcome.html): Best practices for designing, deploying, and architecting serverless application workloads on AWS. Covers all six pillars of the Well-Architected Framework as they apply to serverless architectures.
- [Integrating Microservices by Using AWS Serverless Services (Messaging)](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-integrating-microservices/messaging.html): AWS Prescriptive Guidance on using Lambda with SQS, SNS, and EventBridge for asynchronous communication in microservices architectures. Extends the messaging patterns introduced in Module 08.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
