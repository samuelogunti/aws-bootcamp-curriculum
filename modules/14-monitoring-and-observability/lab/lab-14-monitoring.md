# Lab 14: Monitoring with CloudWatch Metrics, Alarms, Logs Insights, and X-Ray

## Objective

Create a CloudWatch dashboard that monitors a Lambda function, configure alarms on error rate and latency, query Lambda logs using CloudWatch Logs Insights, enable X-Ray tracing on a Lambda function, and analyze the resulting traces.

## Architecture Diagram

This lab builds a monitoring stack around a Lambda function that you instrument and observe:

```
API Gateway (REST API)
    |
    v
Lambda function: bootcamp-monitored-function
    |  (X-Ray active tracing enabled)
    |
    ├── DynamoDB: bootcamp-monitoring-table (GetItem)
    └── CloudWatch Logs: /aws/lambda/bootcamp-monitored-function
            |
            v
        CloudWatch Logs Insights (queries)

CloudWatch Metrics
    ├── Lambda: Invocations, Duration, Errors, Throttles
    ├── Custom metric: BootcampApp/OrdersProcessed
    └── Alarms:
        ├── bootcamp-error-alarm (Lambda Errors > 1)
        └── bootcamp-latency-alarm (Lambda Duration p95 > 1000ms)

CloudWatch Dashboard: bootcamp-operations
    └── Widgets: Lambda metrics, alarm status, error rate, latency percentiles

AWS X-Ray
    └── Service map: API Gateway -> Lambda -> DynamoDB
    └── Traces: individual request paths with timing
```

## Prerequisites

- Completed [Lab 09: Serverless Computing with Lambda](../../09-serverless-lambda/lab/lab-09-lambda.md) (Lambda function creation and API Gateway integration)
- Completed [Module 14: Monitoring and Observability](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

60 minutes

## Instructions

### Step 1: Create a Lambda Function with Structured Logging

In this step, you create a Lambda function that uses structured JSON logging and interacts with DynamoDB. This function will be the target of your monitoring setup.

1. Create a DynamoDB table named `bootcamp-monitoring-table` with a partition key `id` (String). Use on-demand capacity mode.

```bash
aws dynamodb create-table \
  --table-name bootcamp-monitoring-table \
  --attribute-definitions AttributeName=id,AttributeType=S \
  --key-schema AttributeName=id,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST \
  --region us-east-1
```

2. Create a Lambda execution role with permissions for DynamoDB, CloudWatch Logs, and X-Ray:

```bash
ROLE_ARN=$(aws iam create-role \
  --role-name bootcamp-monitoring-lambda-role \
  --assume-role-policy-document '{
    "Version": "2012-10-17",
    "Statement": [{
      "Effect": "Allow",
      "Principal": {"Service": "lambda.amazonaws.com"},
      "Action": "sts:AssumeRole"
    }]
  }' \
  --query "Role.Arn" --output text)
echo "Role ARN: $ROLE_ARN"
```

```bash
aws iam attach-role-policy \
  --role-name bootcamp-monitoring-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

```bash
aws iam attach-role-policy \
  --role-name bootcamp-monitoring-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonDynamoDBReadOnlyAccess
```

```bash
aws iam attach-role-policy \
  --role-name bootcamp-monitoring-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/AWSXRayDaemonWriteAccess
```

3. Create a file named `index.py` with the following Lambda function code that uses structured JSON logging:

```python
import json
import logging
import os
import time
import boto3

logger = logging.getLogger()
logger.setLevel(logging.INFO)

dynamodb = boto3.resource("dynamodb")
table = dynamodb.Table(os.environ.get("TABLE_NAME", "bootcamp-monitoring-table"))

def handler(event, context):
    start_time = time.time()
    request_id = context.aws_request_id

    logger.info(json.dumps({
        "level": "INFO",
        "message": "Request received",
        "requestId": request_id,
        "path": event.get("path", "/"),
        "method": event.get("httpMethod", "GET")
    }))

    try:
        item_id = event.get("queryStringParameters", {}).get("id", "default")
        response = table.get_item(Key={"id": item_id})
        item = response.get("Item", {"id": item_id, "status": "not found"})

        duration = (time.time() - start_time) * 1000
        logger.info(json.dumps({
            "level": "INFO",
            "message": "Request completed",
            "requestId": request_id,
            "durationMs": round(duration, 2),
            "itemFound": "Item" in response
        }))

        return {
            "statusCode": 200,
            "body": json.dumps(item, default=str)
        }
    except Exception as e:
        logger.error(json.dumps({
            "level": "ERROR",
            "message": "Request failed",
            "requestId": request_id,
            "error": str(e),
            "errorType": type(e).__name__
        }))
        return {
            "statusCode": 500,
            "body": json.dumps({"error": "Internal server error"})
        }
```

4. Package and deploy the function:

```bash
zip function.zip index.py
```

```bash
# Wait 10 seconds for the role to propagate
sleep 10

aws lambda create-function \
  --function-name bootcamp-monitored-function \
  --runtime python3.12 \
  --handler index.handler \
  --role $ROLE_ARN \
  --zip-file fileb://function.zip \
  --timeout 30 \
  --memory-size 256 \
  --environment "Variables={TABLE_NAME=bootcamp-monitoring-table}" \
  --tracing-config Mode=Active \
  --region us-east-1
```

**Expected result:** The function is created with X-Ray active tracing enabled.

5. Invoke the function a few times to generate metrics and logs:

```bash
for i in $(seq 1 10); do
  aws lambda invoke \
    --function-name bootcamp-monitored-function \
    --payload '{"queryStringParameters": {"id": "test-'$i'"}}' \
    --cli-binary-format raw-in-base64-out \
    /tmp/response.json \
    --region us-east-1
  echo "Invocation $i: $(cat /tmp/response.json)"
done
```

### Step 2: Create CloudWatch Alarms

In this step, you create two alarms: one for Lambda errors and one for high latency.

1. Create an error alarm that triggers when the function produces more than 1 error in a 5-minute period:

```bash
aws cloudwatch put-metric-alarm \
  --alarm-name bootcamp-error-alarm \
  --metric-name Errors \
  --namespace AWS/Lambda \
  --dimensions Name=FunctionName,Value=bootcamp-monitored-function \
  --statistic Sum \
  --period 300 \
  --evaluation-periods 1 \
  --threshold 1 \
  --comparison-operator GreaterThanThreshold \
  --treat-missing-data notBreaching \
  --alarm-description "Alarm when Lambda function errors exceed 1 in 5 minutes" \
  --region us-east-1
```

2. Create a latency alarm that triggers when the p95 duration exceeds 1000ms:

```bash
aws cloudwatch put-metric-alarm \
  --alarm-name bootcamp-latency-alarm \
  --metric-name Duration \
  --namespace AWS/Lambda \
  --dimensions Name=FunctionName,Value=bootcamp-monitored-function \
  --extended-statistic p95 \
  --period 300 \
  --evaluation-periods 1 \
  --threshold 1000 \
  --comparison-operator GreaterThanThreshold \
  --treat-missing-data notBreaching \
  --alarm-description "Alarm when Lambda p95 latency exceeds 1000ms" \
  --region us-east-1
```

3. Verify the alarms exist:

```bash
aws cloudwatch describe-alarms \
  --alarm-name-prefix bootcamp \
  --query "MetricAlarms[].{Name:AlarmName,State:StateValue}" \
  --output table \
  --region us-east-1
```

**Expected result:** Both alarms are listed with state `OK` or `INSUFFICIENT_DATA`.

### Step 3: Query Logs with CloudWatch Logs Insights

In this step, you use [CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) to query the structured logs your Lambda function produces. Logs Insights uses a purpose-built query language that lets you filter, aggregate, and visualize log data without exporting it to another tool.

Navigate to the CloudWatch console, choose **Logs Insights** in the left navigation pane, and select the log group `/aws/lambda/bootcamp-monitored-function`.

Run the following queries and analyze the results:

1. Find all log entries from the last hour:

```
fields @timestamp, @message
| sort @timestamp desc
| limit 20
```

2. Calculate the average and p95 duration from the structured logs:

```
filter @message like /Request completed/
| parse @message '"durationMs":*,' as duration
| stats avg(duration) as avgMs, pct(duration, 95) as p95Ms, count(*) as requests by bin(5m)
```

3. Find all error log entries:

```
filter @message like /ERROR/
| fields @timestamp, @message
| sort @timestamp desc
| limit 10
```

> **Hint:** If you do not see structured log fields parsed automatically, use the `parse` command to extract JSON fields from the `@message` field. CloudWatch Logs Insights automatically discovers JSON fields in log entries that are formatted as pure JSON objects.

After running these queries, experiment with writing your own query. For example, try to find all requests where `itemFound` is `false`.

### Step 4: Analyze X-Ray Traces

In this step, you examine the [X-Ray traces](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html) generated by your Lambda function.

Navigate to the X-Ray console (or the CloudWatch console under **X-Ray traces**) and explore the following:

1. **Service map.** View the service map to see the relationship between your Lambda function and DynamoDB. Note the average latency and request count on each node.

2. **Traces.** Choose a trace to see the detailed timeline. Identify:
   - The total request duration
   - The time spent in the Lambda initialization phase (cold start, if present)
   - The time spent in the DynamoDB `GetItem` call
   - Any subsegments created by the AWS SDK

3. **Trace filtering.** Use the filter expression to find traces with errors or high latency:
   - `service("bootcamp-monitored-function") { error = true }`
   - `service("bootcamp-monitored-function") { responsetime > 0.5 }`

> **Hint:** If the service map is empty, wait 2 to 3 minutes for X-Ray to process the trace data. X-Ray aggregates data in near-real-time, but there can be a short delay.

Analyze the traces and identify which portion of the request takes the most time. Is it the Lambda initialization, the function code, or the DynamoDB call?

### Step 5: Build a CloudWatch Dashboard

In this step, you create a [CloudWatch dashboard](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) that displays the four golden signals for your Lambda function.

Design and build a dashboard named `bootcamp-operations` that includes the following widgets:

- An alarm status widget showing the state of `bootcamp-error-alarm` and `bootcamp-latency-alarm`
- A line graph showing Lambda `Invocations` over time (traffic signal)
- A line graph showing Lambda `Duration` with p50, p95, and p99 statistics (latency signal)
- A line graph showing Lambda `Errors` over time (error signal)
- A number widget showing the current `ConcurrentExecutions` value (saturation signal)

> **Hint:** In the CloudWatch console, choose **Dashboards**, then **Create dashboard**. Add widgets using the **Add widget** button. For each metric widget, select the `AWS/Lambda` namespace, choose the `FunctionName` dimension, and select `bootcamp-monitored-function`. For the alarm status widget, choose the **Alarm status** widget type and select your two alarms.

After building the dashboard, review it and consider: does it give you enough information to diagnose a problem quickly? What additional widgets would you add for a production application?

### Step 6: Publish a Custom Metric

In this step, you publish a custom metric to CloudWatch and add it to your dashboard.

Publish a custom metric that tracks the number of "orders processed" (simulated):

```bash
for i in $(seq 1 5); do
  aws cloudwatch put-metric-data \
    --namespace "BootcampApp" \
    --metric-name "OrdersProcessed" \
    --dimensions "Environment=lab,Service=order-api" \
    --value $((RANDOM % 100 + 1)) \
    --unit Count \
    --region us-east-1
  sleep 2
done
```

After publishing the data points, add a widget to your `bootcamp-operations` dashboard that displays the `BootcampApp/OrdersProcessed` metric.

> **Hint:** When adding the widget, select the `BootcampApp` namespace (under **Custom namespaces**) instead of the `AWS/Lambda` namespace. Custom metrics may take 1 to 2 minutes to appear in the console after the first `PutMetricData` call.

## Validation

Confirm the following:

- [ ] The Lambda function `bootcamp-monitored-function` is deployed with X-Ray active tracing enabled
- [ ] Invoking the function produces structured JSON log entries in CloudWatch Logs
- [ ] Two CloudWatch alarms exist: `bootcamp-error-alarm` and `bootcamp-latency-alarm`
- [ ] CloudWatch Logs Insights queries return results from the function's log group
- [ ] The X-Ray service map shows the Lambda function and its DynamoDB dependency
- [ ] X-Ray traces show the request timeline with subsegments for DynamoDB calls
- [ ] A CloudWatch dashboard named `bootcamp-operations` displays Lambda metrics and alarm status
- [ ] A custom metric `BootcampApp/OrdersProcessed` appears in the CloudWatch console

## Cleanup

Delete all resources created in this lab to avoid charges:

1. **Delete the CloudWatch dashboard:**
   - Navigate to the [CloudWatch console](https://console.aws.amazon.com/cloudwatch/).
   - Choose **Dashboards**, select `bootcamp-operations`, and choose **Delete**.

2. **Delete the CloudWatch alarms:**

```bash
aws cloudwatch delete-alarms \
  --alarm-names bootcamp-error-alarm bootcamp-latency-alarm \
  --region us-east-1
```

3. **Delete the Lambda function:**

```bash
aws lambda delete-function \
  --function-name bootcamp-monitored-function \
  --region us-east-1
```

4. **Delete the DynamoDB table:**

```bash
aws dynamodb delete-table \
  --table-name bootcamp-monitoring-table \
  --region us-east-1
```

5. **Delete the IAM role and policies:**

```bash
aws iam detach-role-policy \
  --role-name bootcamp-monitoring-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

aws iam detach-role-policy \
  --role-name bootcamp-monitoring-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonDynamoDBReadOnlyAccess

aws iam detach-role-policy \
  --role-name bootcamp-monitoring-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/AWSXRayDaemonWriteAccess

aws iam delete-role --role-name bootcamp-monitoring-lambda-role
```

6. **Delete the CloudWatch Logs log group:**

```bash
aws logs delete-log-group \
  --log-group-name /aws/lambda/bootcamp-monitored-function \
  --region us-east-1
```

7. **Clean up the local files:**

```bash
rm -f function.zip index.py /tmp/response.json
```

> **Warning:** CloudWatch custom metrics cannot be deleted manually. They expire automatically after 15 months of no new data points. The custom metric namespace `BootcampApp` will disappear on its own after you stop publishing data.

## Challenge (Optional)

Extend this lab by implementing the following:

1. Create an anomaly detection alarm on the Lambda `Duration` metric instead of a static threshold. Let CloudWatch learn the baseline for 24 hours, then test whether it detects an artificially introduced latency spike (add a `time.sleep(2)` to the Lambda function code).

2. Create a composite alarm that triggers only when both the error alarm AND the latency alarm are in ALARM state simultaneously. Test it by modifying the Lambda function to both throw errors and introduce latency.

3. Set up a CloudWatch Logs subscription filter that streams all ERROR-level log entries to a Lambda function, which then sends a notification to an SNS topic. This creates a real-time error notification pipeline.

These challenges combine monitoring concepts with Lambda, SNS, and alarm configuration from previous modules.
