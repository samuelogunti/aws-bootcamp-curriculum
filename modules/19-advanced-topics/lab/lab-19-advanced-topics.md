# Lab 19: Extending Your Architecture with Advanced AWS Services

## Objective

Extend the bootcamp architecture by adding CloudFront for global content delivery, building a Step Functions workflow for multi-step processing, and querying operational data in S3 using Amazon Athena.

## Architecture Diagram

This lab does not prescribe a single architecture. You choose at least two of the three exercises below and integrate them with the infrastructure from previous modules.

```
Exercise options:
    ├── Exercise A: CloudFront + S3
    |   └── CloudFront distribution --> S3 bucket (static website from Module 05)
    |       └── Origin Access Control (OAC) for secure access
    |
    ├── Exercise B: Step Functions + Lambda
    |   └── State machine orchestrating multiple Lambda functions
    |       └── Parallel processing, error handling, Choice states
    |
    └── Exercise C: Athena + S3
        └── Athena queries on CloudTrail logs or application data in S3
            └── Parquet conversion for cost optimization
```

## Prerequisites

- Completed all modules from Phase 1 through Phase 4 (Modules 01 through 16)
- Completed [Module 17: The AWS Well-Architected Framework](../../17-well-architected-framework/README.md)
- Completed [Module 18: Architecture Patterns on AWS](../../18-architecture-patterns/README.md)
- Completed [Module 19: Advanced Topics](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

90 minutes

## Goal

Choose at least two of the three exercises below and implement them. For each exercise, document your design decisions, the AWS services used, and how the exercise integrates with the architecture from previous modules. Write an Architecture Decision Record (ADR) for one significant decision you made.

## Constraints

- You must complete at least two of the three exercises.
- Each exercise must integrate with resources from previous modules (not standalone).
- You must write at least one ADR documenting a design decision (for example, why you chose a specific CloudFront caching behavior, why you chose Standard vs. Express Step Functions workflow, or why you chose Parquet over CSV for Athena queries).
- All resources must be created in `us-east-1`.
- You must include cleanup steps for every resource you create.

## Exercise A: Add CloudFront to an S3 Static Website

Design and implement a CloudFront distribution that serves the S3 static website from [Module 05](../../05-storage-s3/README.md) (or create a new S3 bucket with sample HTML files). Configure Origin Access Control (OAC) so the S3 bucket remains private.

Your implementation should include:

- A CloudFront distribution with an S3 origin
- Origin Access Control configured to restrict S3 access to CloudFront only
- A custom error page (for example, a 404.html page)
- Verification that the content is accessible through the CloudFront domain name but not through the S3 bucket URL directly

> **Hint:** After creating the distribution, update the S3 bucket policy to allow access only from the CloudFront distribution using the OAC. Remove any existing public access permissions. Test by accessing the CloudFront URL (should work) and the S3 URL (should return Access Denied).

Reference: [Restrict Access to an S3 Origin](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html)

## Exercise B: Build a Step Functions Workflow

Design and implement a Step Functions state machine that orchestrates a multi-step data processing workflow. The workflow should include at least three states and demonstrate at least two of the following: sequential execution, parallel execution, a Choice state (branching), and error handling with Retry/Catch.

Suggested workflow: an order processing pipeline that:

1. Validates the order (Lambda function that checks required fields)
2. Processes payment (Lambda function that simulates a payment API call)
3. In parallel: updates inventory (Lambda function) and sends confirmation (Lambda function that publishes to SNS)
4. Handles payment failures with a Catch block that routes to an error-handling Lambda

> **Hint:** Use the Step Functions Workflow Studio in the console to design the state machine visually. Define the Amazon States Language (ASL) definition, create the required Lambda functions, and test the workflow with sample input. Use the execution history to verify that each state executed correctly.

Reference: [Creating a Step Functions State Machine with Lambda](https://docs.aws.amazon.com/step-functions/latest/dg/tutorial-creating-lambda-state-machine.html)

## Exercise C: Query S3 Data with Amazon Athena

Set up Amazon Athena to query data stored in S3. Use CloudTrail logs (from [Module 13](../../13-security-in-depth/README.md)) or create a sample dataset. Demonstrate the cost and performance difference between querying CSV data and querying the same data in Parquet format.

Your implementation should include:

- An Athena database and table definition pointing to S3 data
- At least three SQL queries that answer operational questions (for example: "Which IAM user made the most API calls last week?", "Which S3 buckets were accessed most frequently?", "Were there any DeleteBucket calls?")
- A comparison of query cost (data scanned) between CSV and Parquet formats for the same query

> **Hint:** Create a Glue Data Catalog database and table using the Athena console or CLI. For the Parquet comparison, use a Glue ETL job or a Lambda function to convert a CSV file to Parquet, create a second Athena table pointing to the Parquet data, and run the same query against both tables. Compare the "Data scanned" metric.

Reference: [Amazon Athena User Guide](https://docs.aws.amazon.com/athena/latest/ug/what-is.html)

## Deliverables

1. **Implementation evidence** for at least two exercises:
   - Screenshots, CLI output, or descriptions showing the resources created and working
   - For Exercise A: CloudFront distribution URL serving content, S3 direct access denied
   - For Exercise B: Step Functions execution history showing successful workflow completion
   - For Exercise C: Athena query results and data scanned comparison

2. **Architecture Decision Record (ADR)** for one design decision:
   - Follow the ADR template from the Module 19 README
   - Include: context, decision, alternatives considered, and consequences

3. **Integration notes** describing how each exercise connects to previous module resources

## Validation

Confirm the following (for the exercises you completed):

**Exercise A:**
- [ ] A CloudFront distribution exists with an S3 origin
- [ ] OAC is configured and the S3 bucket denies direct public access
- [ ] Content is accessible through the CloudFront domain name
- [ ] Direct S3 URL access returns Access Denied

**Exercise B:**
- [ ] A Step Functions state machine exists with at least three states
- [ ] The workflow demonstrates at least two of: sequential, parallel, Choice, Retry/Catch
- [ ] A successful execution is visible in the execution history
- [ ] An error-handling path has been tested (simulate a failure)

**Exercise C:**
- [ ] An Athena database and table exist pointing to S3 data
- [ ] At least three SQL queries return meaningful results
- [ ] A data scanned comparison between CSV and Parquet is documented

**All exercises:**
- [ ] At least one ADR is written following the template

## Cleanup

Delete all resources created in this lab:

**Exercise A cleanup:**
1. Disable the CloudFront distribution (set to "Disabled" in the console or CLI).
2. Wait for the distribution to reach the "Deployed" state with "Disabled" status.
3. Delete the CloudFront distribution.
4. Remove the OAC-specific bucket policy from the S3 bucket (restore the original policy if needed).
5. Delete the OAC in the CloudFront console.

**Exercise B cleanup:**
1. Delete the Step Functions state machine.
2. Delete the Lambda functions created for the workflow.
3. Delete the IAM roles created for Step Functions and Lambda.
4. Delete any SNS topics created for notifications.

**Exercise C cleanup:**
1. Drop the Athena tables and database.
2. Delete the S3 bucket containing Athena query results.
3. Delete any Parquet files created for the comparison.
4. If you created a Glue ETL job, delete it.

> **Warning:** CloudFront distributions can take 15 to 30 minutes to disable. You must wait for the distribution to be fully disabled before you can delete it. Do not leave distributions running, as they incur charges for data transfer.

## Challenge (Optional)

Extend this lab with the following advanced exercises:

1. Add a Lambda@Edge function to your CloudFront distribution that adds security headers (Content-Security-Policy, X-Frame-Options, Strict-Transport-Security) to every response. Verify the headers using browser developer tools.

2. Design a multi-account architecture for the bootcamp application using AWS Organizations. Create an OU structure diagram, define SCPs for each OU, and write a CloudFormation StackSet template that deploys a baseline configuration (CloudTrail, Config, GuardDuty) to all member accounts.

3. Build an Athena-based dashboard by creating a set of saved queries that answer key operational questions. Schedule a Lambda function to run these queries daily and publish the results to an SNS topic for the operations team.
