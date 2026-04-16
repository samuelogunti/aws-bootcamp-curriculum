# Lab 12: Building a CI/CD Pipeline with AWS CodePipeline

## Objective

Build an automated CI/CD pipeline using AWS CodePipeline that takes a SAM application from an S3 source bucket through CodeBuild (build and test) and CloudFormation (deploy), then extend the pipeline with a post-deployment test stage.

## Architecture Diagram

This lab builds a CI/CD pipeline using CodePipeline, CodeBuild, S3, and CloudFormation. The components and their relationships are as follows:

```
S3 Source Bucket (lab12-source-{account-id})
    |
    └── Upload: sam-app.zip (SAM project source code)
            |
            v
CodePipeline: lab12-pipeline
    |
    ├── Stage 1: Source
    │   └── Action: S3 Source (detects new sam-app.zip upload)
    │           |
    │           v
    │       Source Artifact (sam-app.zip contents)
    |
    ├── Stage 2: Build
    │   └── Action: CodeBuild (lab12-build-project)
    │           |
    │           ├── Runs: sam build
    │           ├── Runs: python -m pytest tests/
    │           ├── Runs: sam package
    │           v
    │       Build Artifact (packaged SAM template)
    |
    ├── Stage 3: Deploy
    │   └── Action: CloudFormation (CREATE_UPDATE)
    │           |
    │           v
    │       CloudFormation Stack: lab12-sam-app
    │           ├── Lambda Function (HelloWorldFunction)
    │           └── API Gateway (ServerlessRestApi)
    |
    └── Stage 4: Test
        └── Action: CodeBuild (lab12-test-project)
                |
                ├── Runs: curl against deployed API endpoint
                └── Validates: HTTP 200 response with expected body
```

You start by initializing a SAM application and creating a CodeBuild project that builds and tests it. You then create a CodePipeline that connects S3 (source), CodeBuild (build), and CloudFormation (deploy) into an automated workflow. You trigger the pipeline by uploading your source code to S3. You extend the pipeline with a test stage that runs integration tests against the deployed application. Finally, you monitor the pipeline execution and review build logs before cleaning up all resources.

## Prerequisites

- Completed [Lab 09: Building Serverless Applications with AWS Lambda](../../09-serverless-lambda/lab/lab-09-lambda.md) (Lambda functions and SAM concepts)
- Completed [Lab 11: Infrastructure as Code with CloudFormation and SAM](../../11-infrastructure-as-code/lab/lab-11-iac.md) (CloudFormation stacks and SAM templates)
- Completed [Module 12: CI/CD Pipelines](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI and SAM CLI installed locally)
- Python 3.13 available in your environment

## Duration

90 minutes

## Instructions

### Step 1: Create a SAM Application Using sam init (Guided)

In this step, you use the [AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-init.html) to initialize a Python hello-world application. This application serves as the codebase that your CI/CD pipeline will build, test, and deploy.

1. Open CloudShell by choosing the terminal icon in the navigation bar. Verify that the Region selector displays **US East (N. Virginia) us-east-1**.

2. Verify that the SAM CLI is installed:

```bash
sam --version
```

You should see output similar to `SAM CLI, version 1.x.x`.

3. Initialize a new SAM application:

```bash
sam init \
  --runtime python3.13 \
  --name lab12-sam-app \
  --app-template hello-world \
  --no-tracing \
  --no-application-insights \
  --no-structured-logging \
  --package-type Zip
```

4. Review the generated project structure:

```bash
ls -la lab12-sam-app/
```

You should see the following key files:

| File/Directory | Purpose |
|----------------|---------|
| `template.yaml` | SAM template defining the Lambda function and API Gateway |
| `hello_world/app.py` | Lambda function handler code |
| `hello_world/requirements.txt` | Python dependencies |
| `tests/` | Unit test directory |
| `events/` | Sample event payloads for local testing |

5. Review the SAM template:

```bash
cat lab12-sam-app/template.yaml
```

Note the `AWS::Serverless::Function` resource. SAM automatically creates an API Gateway endpoint when you define an `Events` property with an `Api` type. This is the same pattern you used in [Module 11](../../11-infrastructure-as-code/README.md).

6. Review the Lambda function code:

```bash
cat lab12-sam-app/hello_world/app.py
```

The function returns a JSON response with a "hello world" message. This is the code that your pipeline will build and deploy.

7. Ensure the unit tests work locally before building a pipeline around them:

```bash
cd lab12-sam-app
pip install -r tests/requirements.txt
python -m pytest tests/unit/ -v
cd ..
```

You should see the unit tests pass.

> **Tip:** Always verify that your application builds and tests pass locally before setting up a CI/CD pipeline. A pipeline that fails on its first run due to broken tests wastes time and makes it harder to diagnose pipeline configuration issues versus code issues.

### Step 2: Create a CodeBuild Project with a buildspec.yml (Guided)

In this step, you create a [buildspec.yml](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) file that tells CodeBuild how to build and test the SAM application, then create a CodeBuild project.

1. Create the buildspec.yml file in the root of the SAM project:

```bash
cat > lab12-sam-app/buildspec.yml << 'EOF'
version: 0.2

phases:
  install:
    runtime-versions:
      python: 3.13
    commands:
      - echo "Installing SAM CLI and dependencies..."
      - pip install aws-sam-cli
      - pip install -r tests/requirements.txt

  pre_build:
    commands:
      - echo "Running unit tests..."
      - python -m pytest tests/unit/ -v

  build:
    commands:
      - echo "Building SAM application..."
      - sam build
      - echo "Packaging SAM application..."
      - sam package --s3-bucket $S3_BUCKET --output-template-file packaged-template.yaml

  post_build:
    commands:
      - echo "Build completed on $(date)"

artifacts:
  files:
    - packaged-template.yaml
  discard-paths: yes
EOF
```

The buildspec defines four phases:
- **install**: installs the SAM CLI and test dependencies
- **pre_build**: runs unit tests (the build fails if tests fail)
- **build**: runs `sam build` to compile the application and `sam package` to upload artifacts to S3 and produce a packaged CloudFormation template
- **post_build**: logs the completion timestamp

The `artifacts` section tells CodeBuild to output the `packaged-template.yaml` file, which the deploy stage will use.

2. Create an S3 bucket for SAM packaging artifacts and pipeline artifacts:

```bash
ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)
ARTIFACT_BUCKET="lab12-artifacts-${ACCOUNT_ID}"

aws s3 mb s3://${ARTIFACT_BUCKET} --region us-east-1
echo "Artifact bucket: ${ARTIFACT_BUCKET}"
```

3. Create an IAM role for CodeBuild. This role grants CodeBuild permission to write logs, access S3, and perform SAM operations:

```bash
cat > /tmp/codebuild-trust-policy.json << 'EOF'
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "codebuild.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF

aws iam create-role \
  --role-name lab12-codebuild-role \
  --assume-role-policy-document file:///tmp/codebuild-trust-policy.json
```

4. Attach policies to the CodeBuild role:

```bash
cat > /tmp/codebuild-policy.json << EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:us-east-1:${ACCOUNT_ID}:*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:GetBucketLocation",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::${ARTIFACT_BUCKET}",
        "arn:aws:s3:::${ARTIFACT_BUCKET}/*"
      ]
    }
  ]
}
EOF

aws iam put-role-policy \
  --role-name lab12-codebuild-role \
  --policy-name lab12-codebuild-policy \
  --policy-document file:///tmp/codebuild-policy.json
```

5. Create the CodeBuild project:

```bash
CODEBUILD_ROLE_ARN=$(aws iam get-role \
  --role-name lab12-codebuild-role \
  --query "Role.Arn" \
  --output text)

aws codebuild create-project \
  --name lab12-build-project \
  --source type=CODEPIPELINE \
  --artifacts type=CODEPIPELINE \
  --environment type=LINUX_CONTAINER,computeType=BUILD_GENERAL1_SMALL,image=aws/codebuild/amazonlinux2-x86_64-standard:5.0,environmentVariables=["{name=S3_BUCKET,value=${ARTIFACT_BUCKET}}"] \
  --service-role $CODEBUILD_ROLE_ARN \
  --region us-east-1
```

This creates a CodeBuild project that:
- Reads source code from CodePipeline (not directly from a repository)
- Uses the Amazon Linux 2 standard image with Python support
- Passes the artifact bucket name as an environment variable for `sam package`
- Outputs build artifacts back to CodePipeline

> **Tip:** The `type=CODEPIPELINE` setting for both source and artifacts means this project is designed to run as part of a pipeline. CodePipeline provides the source input and collects the build output. You cannot run this project standalone without providing source artifacts manually.

### Step 3: Create a CodePipeline with Source, Build, and Deploy Stages (Semi-Guided)

**Goal:** Create an [AWS CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html) with three stages: Source (S3), Build (CodeBuild), and Deploy (CloudFormation). The pipeline should automatically build and deploy your SAM application whenever you upload new source code to the S3 source bucket.

**Requirements:**

- Create an S3 bucket named `lab12-source-{your-account-id}` to serve as the pipeline source. Enable versioning on this bucket (CodePipeline requires versioning on S3 source buckets).
- Create an IAM role named `lab12-pipeline-role` for CodePipeline with permissions to:
  - Read from the source S3 bucket
  - Read and write to the artifact S3 bucket
  - Start CodeBuild builds and get build results
  - Perform CloudFormation operations (CreateStack, UpdateStack, DescribeStacks, DeleteStack)
  - Pass IAM roles to CloudFormation (iam:PassRole)
- Create an IAM role named `lab12-cloudformation-role` for CloudFormation to use when deploying the SAM application. This role needs permissions to create Lambda functions, API Gateway resources, IAM roles (for the Lambda execution role), and CloudFormation operations. Attach the `AdministratorAccess` managed policy for lab simplicity.
- Create the pipeline named `lab12-pipeline` with three stages:
  - **Source stage**: S3 source action that watches for `sam-app.zip` in the source bucket. Use `S3` as the action provider with `PollForSourceChanges` set to `true`.
  - **Build stage**: CodeBuild action using `lab12-build-project`. The input artifact is the source output, and the build produces an output artifact.
  - **Deploy stage**: CloudFormation action using the `CREATE_UPDATE` action mode. Use the `packaged-template.yaml` from the build output as the template file. Set the stack name to `lab12-sam-app`. Pass the CloudFormation role ARN so CloudFormation assumes that role during deployment. Add the `CAPABILITY_IAM` and `CAPABILITY_AUTO_EXPAND` capabilities.

> **Hint:** Enable S3 bucket versioning with `aws s3api put-bucket-versioning --bucket BUCKET_NAME --versioning-configuration Status=Enabled`. CodePipeline uses version IDs to track which source revision triggered each execution.

> **Hint:** The pipeline definition is a JSON structure passed to `aws codepipeline create-pipeline`. Review the [CodePipeline pipeline structure reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html) for the exact format. The key fields are `pipeline.name`, `pipeline.roleArn`, `pipeline.artifactStore`, and `pipeline.stages`.

> **Hint:** For the S3 source action configuration, set `S3Bucket` and `S3ObjectKey` in the action's `configuration` block. The `S3ObjectKey` should be `sam-app.zip`. See the [S3 source action reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-S3.html).

> **Hint:** For the CloudFormation deploy action, use `ActionMode: CREATE_UPDATE`, `StackName: lab12-sam-app`, `TemplatePath: BuildOutput::packaged-template.yaml`, and `RoleArn` pointing to your CloudFormation role. See the [CloudFormation action reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-CloudFormation.html).

> **Hint:** The pipeline's `artifactStore` should reference the artifact bucket (`lab12-artifacts-{account-id}`) with type `S3`. This is where CodePipeline stores artifacts passed between stages.

**Verify your pipeline** by checking that it appears in the CodePipeline console:

```bash
aws codepipeline get-pipeline --name lab12-pipeline --query "pipeline.stages[*].name"
```

You should see three stages: `Source`, `Build`, and `Deploy`.

**Reference links:**
- [CodePipeline pipeline structure reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)
- [S3 source action reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-S3.html)
- [CodeBuild action reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-CodeBuild.html)
- [CloudFormation action reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-CloudFormation.html)
- [create-pipeline CLI reference](https://docs.aws.amazon.com/cli/latest/reference/codepipeline/create-pipeline.html)

### Step 4: Trigger the Pipeline by Uploading Source Code to S3 (Guided)

In this step, you package your SAM application as a ZIP file and upload it to the S3 source bucket. This triggers the pipeline to start its first execution.

1. Package the SAM application into a ZIP file:

```bash
cd lab12-sam-app
zip -r ../sam-app.zip . -x ".aws-sam/*" "__pycache__/*" "*.pyc"
cd ..
```

2. Upload the ZIP file to the source bucket:

```bash
SOURCE_BUCKET="lab12-source-${ACCOUNT_ID}"
aws s3 cp sam-app.zip s3://${SOURCE_BUCKET}/sam-app.zip
```

3. Verify the upload triggered a pipeline execution:

```bash
sleep 10
aws codepipeline list-pipeline-executions \
  --pipeline-name lab12-pipeline \
  --query "pipelineExecutionSummaries[0].{Status:status,StartTime:startTime}" \
  --output table
```

You should see an execution with status `InProgress` or `Succeeded`.

4. Monitor the pipeline execution in the AWS Management Console:

   a. Navigate to **CodePipeline** using the search bar.

   b. Choose **lab12-pipeline** from the pipeline list.

   c. Watch each stage transition from "In Progress" (blue) to "Succeeded" (green). The Source stage completes first, followed by Build, then Deploy.

   d. If any stage shows "Failed" (red), choose the **Details** link on the failed action to see the error message.

5. Wait for the pipeline to complete all three stages. The Build stage typically takes 3 to 5 minutes. You can check the status from the CLI:

```bash
aws codepipeline get-pipeline-state \
  --name lab12-pipeline \
  --query "stageStates[*].{Stage:stageName,Status:latestExecution.status}" \
  --output table
```

Expected output when the pipeline completes:

```
----------------------------
|    GetPipelineState      |
+----------+--------------+
|  Stage   |   Status     |
+----------+--------------+
|  Source  |  Succeeded   |
|  Build   |  Succeeded   |
|  Deploy  |  Succeeded   |
+----------+--------------+
```

6. After the Deploy stage succeeds, retrieve the API endpoint from the CloudFormation stack outputs:

```bash
aws cloudformation describe-stacks \
  --stack-name lab12-sam-app \
  --query "Stacks[0].Outputs[?OutputKey=='HelloWorldApi'].OutputValue" \
  --output text
```

7. Test the deployed application:

```bash
API_ENDPOINT=$(aws cloudformation describe-stacks \
  --stack-name lab12-sam-app \
  --query "Stacks[0].Outputs[?OutputKey=='HelloWorldApi'].OutputValue" \
  --output text)

curl $API_ENDPOINT
```

You should see a JSON response with a "hello world" message. This confirms that the pipeline successfully built, packaged, and deployed your SAM application.

> **Tip:** If the Build stage fails, check the CodeBuild logs (covered in Step 6). Common issues include missing permissions on the CodeBuild role, incorrect S3 bucket names in environment variables, or test failures in the pre_build phase.

### Step 5: Add a Test Stage to the Pipeline (Semi-Guided)

**Goal:** Add a fourth stage to the pipeline that runs integration tests against the deployed application. The test stage should execute after the Deploy stage and verify that the API endpoint returns the expected response.

**Requirements:**

- Create a new CodeBuild project named `lab12-test-project` that runs integration tests
- Create a `testspec.yml` file (a buildspec for the test project) in your SAM application that:
  - Retrieves the API endpoint URL from the CloudFormation stack outputs using the AWS CLI
  - Sends an HTTP request to the endpoint using `curl`
  - Validates that the response contains the expected "hello world" message
  - Exits with a non-zero code if the validation fails (which causes CodeBuild to report failure)
- The test project should use the same CodeBuild role (`lab12-codebuild-role`). You will need to add permissions for `cloudformation:DescribeStacks` to the role so the test can retrieve the stack outputs.
- Update the pipeline to add a new stage named `Test` after the Deploy stage. The Test stage should use the `lab12-test-project` CodeBuild project.
- Re-upload the source ZIP (with the new testspec.yml included) to trigger a full pipeline execution through all four stages.

> **Hint:** Create a separate buildspec file named `testspec.yml` for the test project. When creating the CodeBuild project, specify the buildspec file location using the `buildspecOverride` or by setting `buildspec` in the project source configuration. Alternatively, set the `buildspec` path in the project definition: `--source type=CODEPIPELINE,buildspec=testspec.yml`.

> **Hint:** In the testspec.yml, retrieve the API URL with:
> ```bash
> API_URL=$(aws cloudformation describe-stacks \
>   --stack-name lab12-sam-app \
>   --query "Stacks[0].Outputs[?OutputKey=='HelloWorldApi'].OutputValue" \
>   --output text)
> ```
> Then use `curl` and check the response. A simple validation approach:
> ```bash
> RESPONSE=$(curl -s $API_URL)
> echo "$RESPONSE" | grep -q "hello world" || exit 1
> ```

> **Hint:** To update the pipeline, use `aws codepipeline get-pipeline` to retrieve the current pipeline definition, add the new Test stage to the `stages` array, remove the `metadata` field from the JSON, and use `aws codepipeline update-pipeline` to apply the change. See the [update-pipeline CLI reference](https://docs.aws.amazon.com/cli/latest/reference/codepipeline/update-pipeline.html).

> **Hint:** Add the `cloudformation:DescribeStacks` permission to the CodeBuild role policy so the test project can read stack outputs. Update the existing inline policy using `aws iam put-role-policy`.

**Verify your work** by checking the pipeline now has four stages:

```bash
aws codepipeline get-pipeline --name lab12-pipeline --query "pipeline.stages[*].name"
```

Expected output:

```json
["Source", "Build", "Deploy", "Test"]
```

After re-uploading the source ZIP, monitor the pipeline and confirm all four stages succeed.

**Reference links:**
- [CodeBuild buildspec reference](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)
- [update-pipeline CLI reference](https://docs.aws.amazon.com/cli/latest/reference/codepipeline/update-pipeline.html)
- [CodePipeline stage structure](https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)
- [CloudFormation describe-stacks CLI](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/describe-stacks.html)

### Step 6: Monitor Pipeline Execution and Review CodeBuild Logs (Guided)

In this step, you explore the monitoring and troubleshooting tools available for your pipeline. Understanding how to read build logs and pipeline execution history is essential for diagnosing failures in production pipelines.

1. Navigate to **CodePipeline** in the AWS Management Console.

2. Choose **lab12-pipeline**. The pipeline visualization shows each stage with its current status. Green indicates success, blue indicates in progress, and red indicates failure.

3. Choose the **History** tab to see all pipeline executions. Each execution shows:
   - The execution ID
   - The trigger source (S3 upload in this case)
   - The status of each stage
   - The start and end times

4. Return to the pipeline view and choose **Details** on the Build stage action. This opens the CodeBuild build details.

5. In the CodeBuild console, review the [build logs](https://docs.aws.amazon.com/codebuild/latest/userguide/monitoring-builds.html). The logs show the output of each phase defined in your buildspec.yml:

   - **INSTALL phase**: SAM CLI and dependency installation
   - **PRE_BUILD phase**: Unit test execution and results
   - **BUILD phase**: `sam build` and `sam package` output
   - **POST_BUILD phase**: Completion timestamp

6. Scroll through the build log and locate the test results from the PRE_BUILD phase. You should see output similar to:

```
tests/unit/test_handler.py::test_lambda_handler PASSED
```

7. Check the **Build details** section for timing information:

   | Metric | Description |
   |--------|-------------|
   | Build duration | Total time from start to finish |
   | Queued duration | Time spent waiting for a build environment |
   | Phases | Time breakdown for each buildspec phase |

8. Navigate back to CodePipeline and choose **Details** on the Deploy stage action. This opens the CloudFormation stack events, where you can see each resource that was created or updated during deployment.

9. You can also review build logs from the CLI:

```bash
BUILD_IDS=$(aws codebuild list-builds-for-project \
  --project-name lab12-build-project \
  --query "ids[0]" \
  --output text)

aws codebuild batch-get-builds \
  --ids $BUILD_IDS \
  --query "builds[0].{Status:buildStatus,StartTime:startTime,Duration:phases[?phaseType=='BUILD'].durationInSeconds|[0]}" \
  --output table
```

10. View the CloudWatch Log group for the build project:

```bash
aws logs describe-log-groups \
  --log-group-name-prefix "/aws/codebuild/lab12" \
  --query "logGroups[*].logGroupName" \
  --output table
```

> **Tip:** In production pipelines, set up [Amazon SNS notifications](https://docs.aws.amazon.com/codepipeline/latest/userguide/detect-state-changes-cloudwatch-ct.html) for pipeline state changes. You can receive alerts when a stage fails, so you do not need to watch the console manually. Use Amazon EventBridge rules to trigger notifications on pipeline execution state changes.

### Step 7: Clean Up All Resources (Guided)

Delete all resources created during this lab to avoid unexpected charges. Run the following commands in CloudShell.

1. Delete the CloudFormation stack deployed by the pipeline:

```bash
aws cloudformation delete-stack --stack-name lab12-sam-app
aws cloudformation wait stack-delete-complete --stack-name lab12-sam-app
echo "Stack deleted."
```

2. Delete the CodePipeline:

```bash
aws codepipeline delete-pipeline --name lab12-pipeline
```

3. Delete the CodeBuild projects:

```bash
aws codebuild delete-project --name lab12-build-project
aws codebuild delete-project --name lab12-test-project
```

4. Empty and delete the S3 buckets:

```bash
ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)

aws s3 rm s3://lab12-source-${ACCOUNT_ID} --recursive
aws s3api delete-bucket --bucket lab12-source-${ACCOUNT_ID}

aws s3 rm s3://lab12-artifacts-${ACCOUNT_ID} --recursive
aws s3api delete-bucket --bucket lab12-artifacts-${ACCOUNT_ID}
```

5. Delete the local ZIP file:

```bash
rm -f sam-app.zip
```

6. Detach policies and delete the CodeBuild IAM role:

```bash
aws iam delete-role-policy \
  --role-name lab12-codebuild-role \
  --policy-name lab12-codebuild-policy

aws iam delete-role --role-name lab12-codebuild-role
```

7. Detach policies and delete the CodePipeline IAM role:

```bash
aws iam delete-role-policy \
  --role-name lab12-pipeline-role \
  --policy-name lab12-pipeline-policy

aws iam delete-role --role-name lab12-pipeline-role
```

8. Detach policies and delete the CloudFormation IAM role:

```bash
aws iam detach-role-policy \
  --role-name lab12-cloudformation-role \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

aws iam delete-role --role-name lab12-cloudformation-role
```

9. Delete the CloudWatch Log groups created by CodeBuild:

```bash
aws logs delete-log-group --log-group-name /aws/codebuild/lab12-build-project 2>/dev/null
aws logs delete-log-group --log-group-name /aws/codebuild/lab12-test-project 2>/dev/null
```

10. Remove the local SAM application directory:

```bash
rm -rf lab12-sam-app
```

> **Warning:** If you skip the cleanup steps, the S3 buckets with stored objects will incur storage charges. The CodePipeline itself costs approximately $1 per active pipeline per month. CloudFormation stacks with active Lambda functions and API Gateway endpoints may incur charges from invocations.

## Validation

Confirm that you have completed the lab successfully by verifying each of the following:

- [ ] The SAM application was initialized with `sam init` and unit tests pass locally
- [ ] The `buildspec.yml` file defines install, pre_build, build, and post_build phases
- [ ] The `lab12-build-project` CodeBuild project exists and is configured with the correct environment and role
- [ ] The `lab12-source-{account-id}` S3 bucket exists with versioning enabled
- [ ] The `lab12-pipeline` CodePipeline has Source, Build, Deploy, and Test stages
- [ ] Uploading `sam-app.zip` to the source bucket triggers a pipeline execution
- [ ] The Build stage runs unit tests and packages the SAM application
- [ ] The Deploy stage creates or updates the `lab12-sam-app` CloudFormation stack
- [ ] The deployed API endpoint returns a "hello world" JSON response when called with `curl`
- [ ] The Test stage runs integration tests that validate the deployed API
- [ ] All four pipeline stages show "Succeeded" status
- [ ] CodeBuild logs show test results and build phase timing
- [ ] All resources are deleted during cleanup

## Challenge

Extend your CI/CD pipeline with the following enhancements:

1. **Add a manual approval stage.** Insert a manual approval action between the Deploy and Test stages. Configure it with an SNS topic that sends an email notification when approval is needed. The pipeline should pause until you approve or reject the execution in the console. This simulates a production deployment gate where a team lead reviews the staging deployment before running integration tests. See the [manual approval action documentation](https://docs.aws.amazon.com/codepipeline/latest/userguide/approvals.html).

2. **Implement a code change and observe the pipeline.** Modify the Lambda function in `hello_world/app.py` to return a different message (for example, "hello from CI/CD pipeline"). Update the integration test in `testspec.yml` to validate the new message. Re-zip and upload the source to S3. Watch the pipeline execute all stages and verify the deployed API returns the updated message. This demonstrates the full CI/CD feedback loop: change code, push, build, test, deploy, validate.

3. **Add a linting step.** Install `flake8` in the buildspec install phase and add a linting command in the pre_build phase before the unit tests. Configure flake8 to check the `hello_world/` directory. If the linter finds issues, the build should fail before tests even run. This demonstrates the "fail fast" principle in CI/CD pipelines.
