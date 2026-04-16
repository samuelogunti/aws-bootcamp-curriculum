# Lab 09: Building Serverless Applications with AWS Lambda

## Objective

Build a serverless REST API that reads and writes items to an Amazon DynamoDB table using AWS Lambda functions fronted by Amazon API Gateway, then configure an Amazon S3 event trigger to invoke a Lambda function on object upload.

## Architecture Diagram

This lab builds a serverless application using Lambda, API Gateway, DynamoDB, and S3. The components and their relationships are as follows:

```
Client (Browser / curl)
    |
    v
API Gateway (REST API: lab09-api)
    |
    ├── POST /items  -->  Lambda: lab09-items-function
    |                         |
    |                         v
    |                     DynamoDB Table: lab09-items
    |                         (Partition key: itemId, type String)
    |
    └── GET /items/{id}  -->  Lambda: lab09-items-function
                                  |
                                  v
                              DynamoDB Table: lab09-items


S3 Bucket: lab09-uploads-{account-id}
    |
    └── Event Notification (s3:ObjectCreated:*)
            |
            v
        Lambda: lab09-s3-processor
            |
            v
        CloudWatch Logs (log bucket name and object key)
```

You start by creating a simple Lambda function in the console and testing it with a sample event. You then create an IAM execution role with the permissions Lambda needs. Next, you create a DynamoDB table and build a Lambda function that reads and writes items. You integrate the function with API Gateway to expose it as a REST API. You add a second route for retrieving individual items. You then configure an S3 bucket to trigger a separate Lambda function on object upload. Finally, you review Lambda invocation logs in Amazon CloudWatch.

## Prerequisites

- Completed [Lab 02: IAM Users, Groups, and Roles](../../02-iam-and-security/lab/lab-02-iam-users-groups-roles.md) (understanding of IAM roles and policies)
- Completed [Lab 05: Amazon S3 Storage](../../05-storage-s3/lab/lab-05-s3-storage.md) (S3 bucket creation and event notifications)
- Completed [Lab 06: Databases with Amazon RDS and DynamoDB](../../06-databases-rds-dynamodb/lab/lab-06-databases.md) (DynamoDB table creation and item operations)
- Completed [Module 09: Serverless Computing with AWS Lambda](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

90 minutes

## Instructions

### Step 1: Create a Simple Lambda Function in the Console (Guided)

In this step, you create a basic [Lambda function](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html) using the AWS Management Console and test it with a sample event.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as `bootcamp-admin`.

2. Verify that the Region selector in the top-right corner displays **US East (N. Virginia) us-east-1**.

3. In the search bar at the top, type **Lambda** and select **Lambda** from the results.

4. Choose **Create function**.

5. Select **Author from scratch** and configure the following settings:

   | Setting | Value |
   |---------|-------|
   | Function name | `lab09-hello` |
   | Runtime | Python 3.13 |
   | Architecture | x86_64 |
   | Execution role | Create a new role with basic Lambda permissions |

6. Choose **Create function**. Wait for the console to display the function overview page.

7. In the **Code source** section, replace the default code with the following:

```python
import json

def lambda_handler(event, context):
    name = event.get("name", "World")
    return {
        "statusCode": 200,
        "body": json.dumps({"message": f"Hello, {name}"})
    }
```

8. Choose **Deploy** to save the function code.

9. Choose **Test**. In the **Configure test event** dialog, configure the following:

   | Setting | Value |
   |---------|-------|
   | Event name | `testHello` |
   | Event sharing settings | Private |
   | Template | hello-world |

10. Replace the event JSON with:

```json
{
  "name": "Lambda Student"
}
```

11. Choose **Save**, then choose **Test** again.

12. Review the **Execution results** tab. You should see output similar to:

```json
{
  "statusCode": 200,
  "body": "{\"message\": \"Hello, Lambda Student\"}"
}
```

13. Note the **Duration**, **Billed Duration**, **Memory Size**, and **Max Memory Used** values in the execution summary. These metrics help you understand Lambda pricing and performance.

> **Tip:** The function you just created uses the `AWSLambdaBasicExecutionRole` managed policy, which only grants permission to write logs to CloudWatch. You will create a custom execution role with additional permissions in the next step.

### Step 2: Create an IAM Execution Role for Lambda (Guided)

Every Lambda function needs an [execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) that grants it permission to access AWS services. In this step, you create a role with permissions for DynamoDB and CloudWatch Logs.

1. Open CloudShell by choosing the terminal icon in the navigation bar.

2. Create a trust policy file that allows the Lambda service to assume the role:

```bash
cat > /tmp/lambda-trust-policy.json << 'EOF'
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "lambda.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
EOF
```

3. Create the IAM role:

```bash
aws iam create-role \
  --role-name lab09-lambda-role \
  --assume-role-policy-document file:///tmp/lambda-trust-policy.json \
  --query "Role.Arn" \
  --output text
```

Expected output (your account ID will differ):

```
arn:aws:iam::123456789012:role/lab09-lambda-role
```

4. Save the role ARN for later use:

```bash
ROLE_ARN=$(aws iam get-role \
  --role-name lab09-lambda-role \
  --query "Role.Arn" \
  --output text)
echo "Role ARN: $ROLE_ARN"
```

5. Attach the `AWSLambdaBasicExecutionRole` managed policy for CloudWatch Logs permissions:

```bash
aws iam attach-role-policy \
  --role-name lab09-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

6. Create a custom inline policy that grants DynamoDB read and write access to a table named `lab09-items`:

```bash
ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)

cat > /tmp/lambda-dynamodb-policy.json << EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:GetItem",
                "dynamodb:Query",
                "dynamodb:Scan"
            ],
            "Resource": "arn:aws:dynamodb:us-east-1:${ACCOUNT_ID}:table/lab09-items"
        }
    ]
}
EOF

aws iam put-role-policy \
  --role-name lab09-lambda-role \
  --policy-name lab09-dynamodb-access \
  --policy-document file:///tmp/lambda-dynamodb-policy.json
```

7. Verify the attached policies:

```bash
aws iam list-attached-role-policies --role-name lab09-lambda-role
aws iam list-role-policies --role-name lab09-lambda-role
```

You should see `AWSLambdaBasicExecutionRole` in the attached policies and `lab09-dynamodb-access` in the inline policies.

> **Tip:** This role follows the principle of least privilege from [Module 02](../../02-iam-and-security/README.md). The function can only perform the four DynamoDB actions it needs, and only on the specific table it will use.

### Step 3: Create a DynamoDB Table and Build a Lambda Function (Semi-Guided)

**Goal:** Create a DynamoDB table named `lab09-items` and build a Lambda function that can write new items to the table (via POST) and read all items from the table (via GET).

**Table schema:**

| Setting | Value |
|---------|-------|
| Table name | `lab09-items` |
| Partition key | `itemId` (String) |
| Billing mode | On-demand (PAY_PER_REQUEST) |

**Function requirements:**

- Function name: `lab09-items-function`
- Runtime: Python 3.13
- Execution role: `lab09-lambda-role` (created in Step 2)
- The handler must inspect the incoming event to determine the HTTP method
- For POST requests, generate a unique `itemId` (use Python's `uuid` module), read the item `name` and `description` from the request body, and write the item to DynamoDB using `put_item`
- For GET requests (without a path parameter), scan the table and return all items
- Return a JSON response with the appropriate `statusCode`, `headers` (including `Content-Type: application/json`), and `body`

> **Hint:** You created a DynamoDB table using the AWS CLI in [Lab 06](../../06-databases-rds-dynamodb/lab/lab-06-databases.md). The command uses `aws dynamodb create-table` with `--billing-mode PAY_PER_REQUEST`.

> **Hint:** To create the Lambda function from the CLI, write your handler code to a file, zip it, and use `aws lambda create-function` with the `--zip-file` parameter. See the [Lambda deployment package documentation](https://docs.aws.amazon.com/lambda/latest/dg/python-package.html) for the exact syntax.

> **Hint:** When API Gateway sends a request to Lambda using [proxy integration](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html), the event object contains `httpMethod`, `body`, `pathParameters`, and other HTTP request details. Use `event["httpMethod"]` to route between GET and POST logic.

> **Hint:** Initialize the DynamoDB resource outside the handler function for [connection reuse](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html) across invocations.

**Test your function** after creating it by invoking it from the CLI with a simulated API Gateway POST event:

```bash
cat > /tmp/test-post-event.json << 'EOF'
{
  "httpMethod": "POST",
  "body": "{\"name\": \"Test Item\", \"description\": \"Created in Lab 09\"}"
}
EOF

aws lambda invoke \
  --function-name lab09-items-function \
  --cli-binary-format raw-in-base64-out \
  --payload file:///tmp/test-post-event.json \
  /tmp/response.json

cat /tmp/response.json
```

You should see a response with `statusCode` 200 (or 201) and a body containing the new item's `itemId`.

Then test a GET request:

```bash
cat > /tmp/test-get-event.json << 'EOF'
{
  "httpMethod": "GET",
  "pathParameters": null
}
EOF

aws lambda invoke \
  --function-name lab09-items-function \
  --cli-binary-format raw-in-base64-out \
  --payload file:///tmp/test-get-event.json \
  /tmp/response.json

cat /tmp/response.json
```

You should see a response containing the item you created.

**Reference links:**
- [Creating a DynamoDB table (AWS CLI)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/getting-started-step-1.html)
- [Lambda Python handler](https://docs.aws.amazon.com/lambda/latest/dg/python-handler.html)
- [Lambda deployment packages (Python)](https://docs.aws.amazon.com/lambda/latest/dg/python-package.html)
- [Boto3 DynamoDB Table resource](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.WriteItem.html)


### Step 4: Create an API Gateway REST API with Lambda Integration (Guided)

In this step, you create an [Amazon API Gateway REST API](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-rest-api.html) with Lambda proxy integration to expose your function as an HTTP endpoint.

1. Create the REST API:

```bash
API_ID=$(aws apigateway create-rest-api \
  --name lab09-api \
  --description "Lab 09 Serverless API" \
  --endpoint-configuration types=REGIONAL \
  --region us-east-1 \
  --query "id" \
  --output text)
echo "API ID: $API_ID"
```

2. Get the root resource ID (this represents the `/` path):

```bash
ROOT_ID=$(aws apigateway get-resources \
  --rest-api-id $API_ID \
  --query "items[?path=='/'].id" \
  --output text)
echo "Root Resource ID: $ROOT_ID"
```

3. Create the `/items` resource:

```bash
ITEMS_ID=$(aws apigateway create-resource \
  --rest-api-id $API_ID \
  --parent-id $ROOT_ID \
  --path-part items \
  --query "id" \
  --output text)
echo "Items Resource ID: $ITEMS_ID"
```

4. Create a POST method on `/items` with Lambda proxy integration:

```bash
aws apigateway put-method \
  --rest-api-id $API_ID \
  --resource-id $ITEMS_ID \
  --http-method POST \
  --authorization-type NONE

ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)
FUNCTION_ARN="arn:aws:lambda:us-east-1:${ACCOUNT_ID}:function:lab09-items-function"

aws apigateway put-integration \
  --rest-api-id $API_ID \
  --resource-id $ITEMS_ID \
  --http-method POST \
  --type AWS_PROXY \
  --integration-http-method POST \
  --uri "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/${FUNCTION_ARN}/invocations"
```

5. Create a GET method on `/items` with the same Lambda proxy integration:

```bash
aws apigateway put-method \
  --rest-api-id $API_ID \
  --resource-id $ITEMS_ID \
  --http-method GET \
  --authorization-type NONE

aws apigateway put-integration \
  --rest-api-id $API_ID \
  --resource-id $ITEMS_ID \
  --http-method GET \
  --type AWS_PROXY \
  --integration-http-method POST \
  --uri "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/${FUNCTION_ARN}/invocations"
```

> **Tip:** Notice that `--integration-http-method` is always POST for Lambda proxy integration, regardless of the API method. This is because API Gateway always uses POST to invoke the Lambda function. The actual HTTP method from the client is passed inside the event object.

6. Grant API Gateway permission to invoke your Lambda function. Without this [resource-based policy](https://docs.aws.amazon.com/lambda/latest/dg/access-control-resource-based.html), API Gateway receives an "Access Denied" error when it tries to call Lambda:

```bash
aws lambda add-permission \
  --function-name lab09-items-function \
  --statement-id apigateway-post \
  --action lambda:InvokeFunction \
  --principal apigateway.amazonaws.com \
  --source-arn "arn:aws:execute-api:us-east-1:${ACCOUNT_ID}:${API_ID}/*/POST/items"

aws lambda add-permission \
  --function-name lab09-items-function \
  --statement-id apigateway-get \
  --action lambda:InvokeFunction \
  --principal apigateway.amazonaws.com \
  --source-arn "arn:aws:execute-api:us-east-1:${ACCOUNT_ID}:${API_ID}/*/GET/items"
```

7. Deploy the API to a stage named `dev`:

```bash
aws apigateway create-deployment \
  --rest-api-id $API_ID \
  --stage-name dev
```

8. Construct the invoke URL and test the API:

```bash
API_URL="https://${API_ID}.execute-api.us-east-1.amazonaws.com/dev"
echo "API URL: $API_URL"
```

9. Test the POST endpoint:

```bash
curl -X POST "${API_URL}/items" \
  -H "Content-Type: application/json" \
  -d '{"name": "API Item", "description": "Created via API Gateway"}'
```

You should see a JSON response with the new item's `itemId`.

10. Test the GET endpoint:

```bash
curl "${API_URL}/items"
```

You should see a JSON array containing all items in the table.

> **Tip:** If you receive a `{"message": "Internal server error"}` response, check the CloudWatch Logs for your Lambda function. The most common causes are missing permissions in the execution role or a malformed response format. Lambda proxy integration requires the response to include `statusCode`, `headers`, and `body` fields.

### Step 5: Add a GET /items/{id} Route (Semi-Guided)

**Goal:** Add a new API Gateway resource at `/items/{id}` that accepts GET requests and returns a single item from DynamoDB by its `itemId`. Update your Lambda function handler to support this new route.

**Requirements:**

- Create a new resource under `/items` with a path parameter `{id}`
- Create a GET method on the new resource with Lambda proxy integration to `lab09-items-function`
- Add a resource-based policy so API Gateway can invoke the function for this new route
- Update the Lambda function code to handle GET requests where `pathParameters` is not null
- When `pathParameters` contains an `id` key, use `get_item` to retrieve the specific item from DynamoDB
- Return a 404 response if the item is not found
- Redeploy the API to the `dev` stage after making changes

> **Hint:** Use `aws apigateway create-resource` with `--path-part "{id}"` (including the curly braces) to create a path parameter resource. See the [API Gateway path parameter documentation](https://docs.aws.amazon.com/apigateway/latest/developerguide/integrating-api-with-aws-services-lambda.html).

> **Hint:** When a request comes in for `/items/abc123`, API Gateway sets `event["pathParameters"]` to `{"id": "abc123"}`. Check whether `event.get("pathParameters")` is not None and contains the `"id"` key to distinguish between "get all items" and "get one item."

> **Hint:** To update your function code, modify your Python file, re-zip it, and use `aws lambda update-function-code --function-name lab09-items-function --zip-file fileb://...`. See the [update-function-code CLI reference](https://docs.aws.amazon.com/cli/latest/reference/lambda/update-function-code.html).

> **Hint:** After adding the new resource and method, you must create a new deployment with `aws apigateway create-deployment` to make the changes live.

**Test your work** by retrieving an item ID from the GET /items response, then requesting it individually:

```bash
curl "${API_URL}/items/{paste-an-itemId-here}"
```

You should see a JSON response with the single item. Test with a non-existent ID to verify you get a 404 response.

**Reference links:**
- [API Gateway resource with path parameters](https://docs.aws.amazon.com/apigateway/latest/developerguide/integrating-api-with-aws-services-lambda.html)
- [Lambda proxy integration input format](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html#api-gateway-simple-proxy-for-lambda-input-format)
- [Boto3 DynamoDB get_item](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.ReadItem.html)

### Step 6: Configure an S3 Event Trigger for Lambda (Semi-Guided)

**Goal:** Create an S3 bucket and a new Lambda function. Configure the bucket to invoke the Lambda function whenever a new object is uploaded. The function should log the bucket name and object key to CloudWatch Logs.

**Requirements:**

- Create an S3 bucket named `lab09-uploads-{your-account-id}` (append your account ID to ensure uniqueness)
- Create a new Lambda function named `lab09-s3-processor` using the `lab09-lambda-role` execution role
- The handler should extract the bucket name and object key from the S3 event record and print them using `print()` (Lambda automatically sends print output to CloudWatch Logs)
- Add a resource-based policy to the Lambda function that allows S3 to invoke it
- Configure an S3 event notification on the bucket for `s3:ObjectCreated:*` events that triggers the Lambda function
- Upload a test file to the bucket and verify the function was invoked by checking CloudWatch Logs

> **Hint:** The S3 event structure places the bucket name at `event["Records"][0]["s3"]["bucket"]["name"]` and the object key at `event["Records"][0]["s3"]["object"]["key"]`. Review the [S3 event message structure](https://docs.aws.amazon.com/AmazonS3/latest/userguide/notification-content-structure.html) for the full schema.

> **Hint:** Before configuring the S3 notification, you must grant S3 permission to invoke your function. Use `aws lambda add-permission` with `--principal s3.amazonaws.com` and `--source-arn arn:aws:s3:::lab09-uploads-{your-account-id}`. See [Using Lambda with S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html).

> **Hint:** Configure the bucket notification using `aws s3api put-bucket-notification-configuration`. The configuration JSON includes a `LambdaFunctionConfigurations` array. See the [S3 notification configuration documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-enable-disable-notification-first-time.html).

> **Hint:** After uploading a test file, wait 10 to 15 seconds for the function to execute. Then check CloudWatch Logs using `aws logs filter-log-events --log-group-name /aws/lambda/lab09-s3-processor --start-time $(date -d '5 minutes ago' +%s000)` to see your function's output.

**Test your work** by uploading a file:

```bash
echo "Hello from Lab 09" > /tmp/test-upload.txt
aws s3 cp /tmp/test-upload.txt s3://lab09-uploads-${ACCOUNT_ID}/test-upload.txt
```

Then check CloudWatch Logs to confirm the function logged the bucket name and object key.

**Reference links:**
- [Using Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html)
- [S3 event notification configuration](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-enable-disable-notification-first-time.html)
- [S3 event message structure](https://docs.aws.amazon.com/AmazonS3/latest/userguide/notification-content-structure.html)
- [Lambda resource-based policy for S3](https://docs.aws.amazon.com/lambda/latest/dg/access-control-resource-based.html)

### Step 7: Monitor Lambda Invocations in CloudWatch Logs (Guided)

Every Lambda function automatically sends logs to [Amazon CloudWatch Logs](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-cloudwatchlogs.html). In this step, you review the logs for your functions to understand Lambda's logging behavior and invocation metrics.

1. In the AWS Management Console, navigate to **CloudWatch** using the search bar.

2. In the left navigation pane, choose **Logs**, then **Log groups**.

3. You should see log groups for each Lambda function you created:
   - `/aws/lambda/lab09-hello`
   - `/aws/lambda/lab09-items-function`
   - `/aws/lambda/lab09-s3-processor`

4. Choose the log group `/aws/lambda/lab09-items-function`.

5. Choose the most recent log stream. Each log stream corresponds to an execution environment instance.

6. Review the log entries. Each invocation produces three types of log lines:

   - **START**: marks the beginning of an invocation, includes the request ID
   - **END**: marks the end of an invocation
   - **REPORT**: summarizes the invocation with duration, billed duration, memory size, max memory used, and init duration (if it was a cold start)

7. Look for a REPORT line that includes `Init Duration`. This indicates a cold start. Compare it to a REPORT line without `Init Duration` (a warm start) and note the difference in total duration.

8. You can also view logs from the CLI. Run the following command to see recent log events for the S3 processor function:

```bash
LOG_GROUP="/aws/lambda/lab09-s3-processor"

aws logs describe-log-streams \
  --log-group-name $LOG_GROUP \
  --order-by LastEventTime \
  --descending \
  --limit 1 \
  --query "logStreams[0].logStreamName" \
  --output text
```

9. Use the log stream name from the previous command to retrieve the log events:

```bash
LOG_STREAM=$(aws logs describe-log-streams \
  --log-group-name $LOG_GROUP \
  --order-by LastEventTime \
  --descending \
  --limit 1 \
  --query "logStreams[0].logStreamName" \
  --output text)

aws logs get-log-events \
  --log-group-name $LOG_GROUP \
  --log-stream-name $LOG_STREAM \
  --query "events[*].message" \
  --output text
```

You should see the bucket name and object key that your S3 processor function logged, along with the START, END, and REPORT lines.

10. Navigate back to the **Log groups** list and choose `/aws/lambda/lab09-hello`. Review the logs from your initial test in Step 1. Note how the `aws_request_id` in the START line matches the request ID in the REPORT line, allowing you to correlate log entries for a single invocation.

> **Tip:** In production, you would set up [CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) on Lambda metrics such as `Errors`, `Throttles`, and `Duration` to get notified when something goes wrong. You will explore CloudWatch alarms in depth in [Module 14](../../14-monitoring-and-observability/README.md).

## Validation

Confirm that you have completed the lab successfully by verifying each of the following:

- [ ] The `lab09-hello` function exists and returns a 200 response when tested with a name in the event
- [ ] The `lab09-lambda-role` IAM role exists with `AWSLambdaBasicExecutionRole` attached and a `lab09-dynamodb-access` inline policy
- [ ] The `lab09-items` DynamoDB table exists with `itemId` as the partition key
- [ ] The `lab09-items-function` Lambda function can create items (POST) and retrieve all items (GET)
- [ ] The API Gateway REST API `lab09-api` is deployed to the `dev` stage
- [ ] `curl POST /items` creates a new item and returns it with an `itemId`
- [ ] `curl GET /items` returns all items from the table
- [ ] `curl GET /items/{id}` returns a single item or a 404 if not found
- [ ] The `lab09-uploads-{account-id}` S3 bucket triggers `lab09-s3-processor` on object upload
- [ ] Uploading a file to the S3 bucket produces a log entry in CloudWatch Logs with the bucket name and object key
- [ ] CloudWatch Logs contain START, END, and REPORT entries for each Lambda invocation

## Cleanup

Delete all resources created during this lab to avoid unexpected charges. Run the following commands in CloudShell:

1. Delete the API Gateway REST API:

```bash
aws apigateway delete-rest-api --rest-api-id $API_ID
```

2. Remove the S3 bucket notification and delete the bucket:

```bash
aws s3 rm s3://lab09-uploads-${ACCOUNT_ID} --recursive
aws s3api delete-bucket --bucket lab09-uploads-${ACCOUNT_ID}
```

3. Delete the Lambda functions:

```bash
aws lambda delete-function --function-name lab09-hello
aws lambda delete-function --function-name lab09-items-function
aws lambda delete-function --function-name lab09-s3-processor
```

4. Delete the DynamoDB table:

```bash
aws dynamodb delete-table --table-name lab09-items
```

5. Detach policies and delete the IAM role:

```bash
aws iam detach-role-policy \
  --role-name lab09-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

aws iam delete-role-policy \
  --role-name lab09-lambda-role \
  --policy-name lab09-dynamodb-access

aws iam delete-role --role-name lab09-lambda-role
```

6. Delete the CloudWatch Log groups:

```bash
aws logs delete-log-group --log-group-name /aws/lambda/lab09-hello
aws logs delete-log-group --log-group-name /aws/lambda/lab09-items-function
aws logs delete-log-group --log-group-name /aws/lambda/lab09-s3-processor
```

7. Delete the `lab09-hello` execution role that was auto-created by the console:

```bash
HELLO_ROLE_NAME=$(aws iam list-roles \
  --query "Roles[?starts_with(RoleName, 'lab09-hello')].RoleName" \
  --output text)

if [ -n "$HELLO_ROLE_NAME" ]; then
  aws iam detach-role-policy \
    --role-name $HELLO_ROLE_NAME \
    --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
  aws iam delete-role --role-name $HELLO_ROLE_NAME
  echo "Deleted role: $HELLO_ROLE_NAME"
fi
```

> **Warning:** If you skip the cleanup steps, the Lambda functions themselves will not incur charges while idle (Lambda charges only for invocations). However, the DynamoDB table in on-demand mode, the S3 bucket with stored objects, and CloudWatch Logs with retained data may incur small charges over time.

## Challenge

Extend your serverless API with the following enhancements:

1. **Add a DELETE /items/{id} route.** Create a new method on the `/items/{id}` resource that deletes an item from DynamoDB. Update your Lambda handler to support the DELETE HTTP method using `delete_item`. Test it by creating an item, deleting it, and confirming it no longer appears in GET /items.

2. **Add input validation.** Modify the POST handler to return a 400 response if the request body is missing the `name` field. Return a clear error message in the response body explaining what is required.

3. **Use environment variables.** Instead of hardcoding the DynamoDB table name in your Lambda function code, pass it as an [environment variable](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html). Update the function configuration using `aws lambda update-function-configuration --environment` and modify your handler to read the table name from `os.environ`.
