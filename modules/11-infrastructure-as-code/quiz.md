# Module 11: Quiz

1. Which of the following best describes why Infrastructure as Code (IaC) is preferred over manual provisioning for production environments?

   A) IaC eliminates the need for an AWS account by simulating resources locally
   B) IaC defines infrastructure in version-controlled text files, enabling repeatable deployments, audit trails, and consistency across environments
   C) IaC automatically reduces the cost of AWS resources by selecting the cheapest options
   D) IaC replaces the need for IAM roles and policies by embedding credentials directly in templates

2. A CloudFormation template contains the following sections: `AWSTemplateFormatVersion`, `Description`, `Parameters`, `Mappings`, `Conditions`, `Resources`, and `Outputs`. Which of these sections is the only one that is required in every CloudFormation template?

   A) `Parameters`
   B) `AWSTemplateFormatVersion`
   C) `Resources`
   D) `Outputs`

3. Match each CloudFormation intrinsic function to its purpose:

   | Function | Purpose |
   |----------|---------|
   | `!Ref` | _______ |
   | `!Sub` | _______ |
   | `!GetAtt` | _______ |
   | `!If` | _______ |

   Options:
   A) Returns one of two values based on a condition defined in the Conditions section
   B) Returns the value of a parameter or the physical ID of a resource
   C) Substitutes variables in a string using `${}` syntax
   D) Returns a specific attribute of a resource, such as an ARN or DNS name

4. True or False: When a CloudFormation stack creation fails, CloudFormation leaves all successfully created resources in place by default and only reports the failed resource.

5. A team maintains a CloudFormation template that defines a VPC, subnets, and an EC2 instance. They need to add a new security group to the template and want to verify exactly which resources will be added, modified, or replaced before applying the change. Which CloudFormation feature should they use?

   A) Drift detection
   B) Stack policies
   C) Change sets
   D) Resource import

6. Which of the following correctly describes the `Transform` declaration in a SAM template?

   A) It converts the SAM template from YAML to JSON before deployment
   B) It tells CloudFormation to process SAM-specific resource types (such as `AWS::Serverless::Function`) by expanding them into standard CloudFormation resources
   C) It encrypts all resource properties in the template before storing them in S3
   D) It enables the template to be used with Terraform instead of CloudFormation

7. A developer is writing a SAM template with three Lambda functions that all use the Python 3.12 runtime, a 30-second timeout, and the same `TABLE_NAME` environment variable. Which SAM template feature should the developer use to avoid repeating these settings in each function definition?

   A) The `Mappings` section
   B) The `Conditions` section
   C) The `Globals` section
   D) The `Metadata` section

8. Your team deploys a CloudFormation stack that creates a VPC, an RDS database, and several EC2 instances. Two weeks later, a developer modifies a security group rule directly through the AWS Management Console to unblock a testing issue. The team is now preparing a stack update with new template changes. What risk does this situation present, and what should the team do before applying the update?

   A) There is no risk. CloudFormation automatically detects and incorporates manual changes during updates.
   B) The stack has drifted from the template-defined state. The team should run drift detection to identify the manual change, then either update the template to reflect the intended state or revert the manual change before applying the stack update.
   C) The stack is permanently corrupted and must be deleted and recreated from scratch.
   D) The team should delete the security group manually and let CloudFormation recreate it during the update.

9. A startup is building a new project that includes a REST API backed by Lambda functions and a DynamoDB table, plus a shared VPC with public and private subnets used by multiple teams. The Lambda functions need scoped IAM permissions for DynamoDB access, and the team wants to test API endpoints locally before deploying. Which combination of IaC tools best fits these requirements?

   A) Use the AWS CDK for both the VPC and the serverless API
   B) Use CloudFormation for the shared VPC infrastructure and SAM for the serverless API, because SAM provides policy templates for scoped DynamoDB permissions and the SAM CLI enables local API testing with `sam local start-api`
   C) Use SAM for both the VPC and the serverless API
   D) Use CloudFormation for everything, because SAM and CDK add unnecessary complexity

10. Which of the following statements correctly describes the relationship between AWS CDK constructs and CloudFormation?

    A) CDK constructs bypass CloudFormation entirely and call AWS APIs directly to create resources
    B) CDK constructs are organized into three levels (L1, L2, L3), where L1 constructs map directly to CloudFormation resource types, L2 constructs add sensible defaults and convenience methods, and L3 constructs combine multiple resources into common architectural patterns. The CDK synthesizes all constructs into a CloudFormation template for deployment.
    C) CDK constructs are only available in TypeScript and cannot be used with Python or Java
    D) CDK L3 constructs are required for all deployments, and L1 and L2 constructs are deprecated

---

<details>
<summary>Answer Key</summary>

1. **B) IaC defines infrastructure in version-controlled text files, enabling repeatable deployments, audit trails, and consistency across environments**
   Infrastructure as Code solves the problems of manual provisioning by storing infrastructure definitions in text files alongside application code. This enables repeatable deployments across Regions and accounts, version-controlled change history with pull request reviews, integration with CI/CD pipelines for automated provisioning, drift detection to identify manual changes, and consistent environments using the same template with different parameter values.
   Further reading: [What is CloudFormation?](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)

2. **C) Resources**
   The `Resources` section is the only required section in a CloudFormation template. It declares the AWS resources (such as EC2 instances, S3 buckets, or VPCs) that CloudFormation will create and manage. All other sections (`AWSTemplateFormatVersion`, `Description`, `Parameters`, `Mappings`, `Conditions`, and `Outputs`) are optional, though commonly used in production templates.
   Further reading: [CloudFormation template sections](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html)

3. **Correct matching: `!Ref` = B, `!Sub` = C, `!GetAtt` = D, `!If` = A**
   `!Ref` returns the value of a parameter or the physical ID of a resource (for example, an EC2 instance ID). `!Sub` substitutes variables enclosed in `${}` within a string, which is useful for constructing ARNs, URLs, and tags. `!GetAtt` retrieves a specific attribute from a resource, such as an S3 bucket ARN or an EC2 instance public IP address. `!If` evaluates a condition from the `Conditions` section and returns one of two values based on the result.
   Further reading: [Intrinsic function reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference.html)

4. **False.**
   When a stack creation fails, CloudFormation rolls back the entire stack by default. It deletes all resources that were successfully created during the failed operation, and the stack enters the `ROLLBACK_COMPLETE` state. This default behavior ensures that you do not end up with a partially provisioned stack in an inconsistent state. You can optionally disable automatic rollback during development to inspect partially created resources and diagnose the failure, but in production, rollback should always remain enabled.
   Further reading: [Choose how to handle failures when provisioning resources](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stack-failure-options.html)

5. **C) Change sets**
   A change set lets you preview how proposed changes to a stack will affect running resources before you execute them. You create a change set by submitting your updated template, then review the list of proposed changes to see which resources will be added, modified, or replaced. If the changes are acceptable, you execute the change set. If not, you discard it and revise the template. Drift detection (A) identifies manual changes to deployed resources. Stack policies (B) protect specific resources from being updated. Resource import (D) brings existing resources under CloudFormation management.
   Further reading: [Example change sets for CloudFormation stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets-samples.html)

6. **B) It tells CloudFormation to process SAM-specific resource types (such as `AWS::Serverless::Function`) by expanding them into standard CloudFormation resources**
   The `Transform: AWS::Serverless-2016-10-31` declaration in a SAM template instructs CloudFormation to use the SAM transform macro. This macro processes SAM-specific resource types (such as `AWS::Serverless::Function`, `AWS::Serverless::Api`, and `AWS::Serverless::SimpleTable`) and expands them into the equivalent standard CloudFormation resources (Lambda functions, IAM roles, API Gateway REST APIs, DynamoDB tables, and so on). Without this declaration, CloudFormation would not recognize SAM resource types.
   Further reading: [AWS SAM template anatomy](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html)

7. **C) The Globals section**
   The `Globals` section in a SAM template defines properties that apply to all resources of a given type. By setting `Runtime`, `Timeout`, and `Environment` variables under `Globals > Function`, all `AWS::Serverless::Function` resources in the template inherit those values automatically. Individual functions can override any global property by specifying it explicitly. This reduces duplication and keeps the template concise when multiple functions share the same configuration.
   Further reading: [Globals section of the AWS SAM template](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy-globals.html)

8. **B) The stack has drifted from the template-defined state. The team should run drift detection to identify the manual change, then either update the template to reflect the intended state or revert the manual change before applying the stack update.**
   When someone modifies a CloudFormation-managed resource outside of the template (for example, through the console or CLI), the actual resource configuration diverges from the template definition. This is called drift. Updating a drifted stack can produce unexpected results because CloudFormation assumes the current state matches the previous template. The team should run drift detection before the update to identify which resources have changed and by how much. They can then decide whether to incorporate the manual change into the template or revert it. CloudFormation does not automatically detect or incorporate manual changes (A is incorrect), and the stack is not permanently corrupted (C is incorrect).
   Further reading: [Detect drift on an entire CloudFormation stack](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/detect-drift-stack.html)

9. **B) Use CloudFormation for the shared VPC infrastructure and SAM for the serverless API, because SAM provides policy templates for scoped DynamoDB permissions and the SAM CLI enables local API testing with `sam local start-api`**
   This scenario has two distinct workloads with different requirements. The shared VPC is general networking infrastructure used by multiple teams, making it a good fit for a standalone CloudFormation template with exported outputs. The serverless API (Lambda, API Gateway, DynamoDB) benefits from SAM because SAM provides policy templates like `DynamoDBReadPolicy` that generate scoped IAM permissions automatically, and the SAM CLI supports local testing with `sam local invoke` and `sam local start-api`. Using CDK for both (A) would work but adds unnecessary complexity for the VPC and does not provide built-in local API testing. Using SAM for the VPC (C) is possible but SAM is optimized for serverless resources, not general networking. Using only CloudFormation (D) would require writing verbose IAM policies manually and would not support local testing.
   Further reading: [AWS SAM policy templates](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-policy-templates.html), [sam local start-api](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-local-start-api.html)

10. **B) CDK constructs are organized into three levels (L1, L2, L3), where L1 constructs map directly to CloudFormation resource types, L2 constructs add sensible defaults and convenience methods, and L3 constructs combine multiple resources into common architectural patterns. The CDK synthesizes all constructs into a CloudFormation template for deployment.**
    The CDK uses an imperative approach where you write infrastructure in a programming language (TypeScript, Python, Java, C#, or Go). The CDK CLI synthesizes your code into a standard CloudFormation template, which CloudFormation then deploys. L1 constructs (CFN Resources) are direct one-to-one mappings to CloudFormation resource types with no added defaults. L2 constructs (Curated Constructs) provide higher-level abstractions with sensible defaults and convenience methods like `bucket.grantRead(fn)`. L3 constructs (Patterns) combine multiple resources into opinionated architectural patterns like `LambdaRestApi`. The CDK does not bypass CloudFormation (A is incorrect), supports multiple languages (C is incorrect), and all construct levels are actively supported (D is incorrect).
    Further reading: [AWS CDK Constructs](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html)

</details>

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
