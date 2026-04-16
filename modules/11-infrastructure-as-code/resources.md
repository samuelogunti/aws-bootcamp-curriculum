# Module 11: Resources

## Official Documentation

### AWS CloudFormation Core

- [What is CloudFormation?](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)
- [CloudFormation Template Sections](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html)
- [CloudFormation Template Format (YAML and JSON)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-formats.html)
- [Managing AWS Resources as a Single Unit with CloudFormation Stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html)
- [CloudFormation Best Practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html)

### CloudFormation Parameters and Outputs

- [CloudFormation Template Parameters Syntax](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html)
- [CloudFormation Template Outputs Syntax](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html)
- [Refer to Resource Outputs in Another CloudFormation Stack (Cross-Stack References)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/walkthrough-crossstackref.html)
- [Pseudo Parameter Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html)

### CloudFormation Intrinsic Functions

- [Intrinsic Function Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference.html)
- [Ref](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference-ref.html)
- [Fn::Sub](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference-sub.html)
- [Fn::GetAtt](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference-getatt.html)
- [Fn::ImportValue](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference-importvalue.html)
- [Condition Functions](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference-conditions.html)

### CloudFormation Stack Operations

- [Choose How to Handle Failures When Provisioning Resources (Rollback Behavior)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stack-failure-options.html)
- [Example Change Sets for CloudFormation Stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets-samples.html)
- [Delete a Change Set for a CloudFormation Stack](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets-delete.html)
- [Detect Drift on an Entire CloudFormation Stack](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/detect-drift-stack.html)
- [Importing Existing Resources into a Stack](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resource-import-existing-stack.html)
- [Troubleshooting CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/troubleshooting.html)

### CloudFormation Resource Types (Used in Lab)

- [AWS::EC2::VPC](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/aws-resource-ec2-vpc.html)
- [AWS::EC2::Subnet](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/aws-resource-ec2-subnet.html)
- [AWS::EC2::InternetGateway](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/aws-resource-ec2-internetgateway.html)
- [AWS::EC2::SecurityGroup](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/aws-resource-ec2-securitygroup.html)
- [AWS::EC2::Instance](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/aws-resource-ec2-instance.html)

### AWS Serverless Application Model (SAM)

- [What is the AWS Serverless Application Model (AWS SAM)?](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)
- [AWS SAM Template Anatomy](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html)
- [Globals Section of the AWS SAM Template](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy-globals.html)
- [AWS::Serverless::Function](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-function.html)
- [AWS::Serverless::SimpleTable](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-simpletable.html)
- [AWS::Serverless::Api](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-api.html)
- [AWS SAM Policy Templates](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-policy-templates.html)
- [Install the SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html)

### SAM CLI Commands

- [AWS SAM CLI Core Commands](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-corecommands.html)
- [sam init](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-init.html)
- [sam build](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-build.html)
- [sam local invoke](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-local-invoke.html)
- [sam local start-api](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-local-start-api.html)
- [sam deploy](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-deploy.html)
- [Introduction to Deploying with AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-deploy.html)
- [AWS SAM CLI Configuration File](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-config.html)

### AWS Cloud Development Kit (CDK)

- [Getting Started with the AWS CDK](https://docs.aws.amazon.com/cdk/v2/guide/getting-started.html)
- [AWS CDK Constructs](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html)
- [AWS CDK CLI Reference](https://docs.aws.amazon.com/cdk/v2/guide/cli.html)

### Infrastructure as Code Concepts

- [Infrastructure as Code (IaC) for Serverless Applications](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-iac.html)
- [Choosing an Infrastructure as Code Tool for Your Organization](https://docs.aws.amazon.com/prescriptive-guidance/latest/choose-iac-tool/introduction.html)

## AWS Whitepapers

- [Choosing an IaC Tool](https://docs.aws.amazon.com/prescriptive-guidance/latest/choose-iac-tool/choose-tool.html): Guidance on evaluating AWS CloudFormation, AWS CDK, Terraform, and Pulumi based on team skills, workload complexity, and organizational requirements.
- [Benefits of Implementing IaC](https://docs.aws.amazon.com/prescriptive-guidance/latest/choose-iac-tool/benefits.html): Covers the core benefits of Infrastructure as Code including repeatability, version control, automation, and consistency across environments.

## AWS FAQs

- [AWS CloudFormation FAQ](https://aws.amazon.com/cloudformation/faqs/): Covers CloudFormation pricing (free to use, pay only for provisioned resources), template limits, stack operations, change sets, drift detection, and integration with other AWS services.

## AWS Architecture References

- [CloudFormation Best Practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html): Comprehensive guidance on organizing stacks by lifecycle, using cross-stack references, adopting IaC practices, managing change sets, implementing stack policies, and securing templates.
- [Reviewing IaC Tools for the AWS Cloud](https://docs.aws.amazon.com/prescriptive-guidance/latest/choose-iac-tool/iac-tools.html): Detailed comparison of five IaC tools available for provisioning and managing AWS resources, including feature matrices and decision criteria.
