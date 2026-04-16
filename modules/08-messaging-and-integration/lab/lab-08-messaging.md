# Lab 08: SQS, SNS, Fan-Out, and Dead-Letter Queues

## Objective

Create and interact with Amazon SQS queues and Amazon SNS topics using the AWS CLI, set up the fan-out pattern by subscribing multiple queues to a single topic, and configure a dead-letter queue to capture failed messages.

## Architecture Diagram

This lab builds a messaging architecture using SQS and SNS. The components and their relationships are as follows:

```
AWS CLI (CloudShell)
    |
    ├── SQS Standard Queue: lab08-queue
    |       Long polling enabled (ReceiveMessageWaitTimeSeconds=20)
    |       Redrive policy --> lab08-dlq (maxReceiveCount=3)
    |
    ├── SQS Dead-Letter Queue: lab08-dlq
    |       Receives messages that fail processing 3 times
    |       Monitored via CloudWatch metric: ApproximateNumberOfMessagesVisible
    |
    ├── SNS Topic: lab08-topic
    |       |
    |       ├── Subscription: Email (your email address)
    |       ├── Subscription: SQS queue (lab08-fanout-queue-a)
    |       └── Subscription: SQS queue (lab08-fanout-queue-b)
    |
    ├── SQS Queue: lab08-fanout-queue-a
    |       Receives messages from lab08-topic (fan-out)
    |
    └── SQS Queue: lab08-fanout-queue-b
            Receives messages from lab08-topic (fan-out)
```

You start by creating a single SQS queue to practice sending, receiving, and deleting messages. You then configure long polling to reduce empty responses. Next, you create an SNS topic with an email subscription and publish a message. You then set up the fan-out pattern by subscribing two SQS queues to the SNS topic. Finally, you create a dead-letter queue, configure a redrive policy, and observe messages moving to the DLQ when they are not deleted after repeated receives.

## Prerequisites

- Completed [Lab 01: AWS Account Setup and Console Tour](../../01-cloud-fundamentals/lab/lab-01-aws-account-setup.md)
- Completed [Module 02: Identity and Access Management (IAM) and Security](../../02-iam-and-security/README.md) (understanding of IAM policies and resource-based policies)
- Completed [Module 08: Messaging and Integration Services](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)
- A valid email address you can access during the lab (for SNS email subscription confirmation)

## Duration

60 minutes

## Instructions

### Step 1: Create an SQS Standard Queue and Send a Message

In this step, you create an [Amazon SQS Standard queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/standard-queues.html) and practice the basic message lifecycle: send, receive, and delete.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as `bootcamp-admin`.
2. Verify that the Region selector in the top-right corner displays **US East (N. Virginia) us-east-1**.
3. Open CloudShell by choosing the terminal icon in the navigation bar. Wait for the shell to initialize.
4. Create a Standard SQS queue:

```bash
QUEUE_URL=$(aws sqs create-queue \
  --queue-name lab08-queue \
  --region us-east-1 \
  --query "QueueUrl" \
  --output text)
echo "Queue URL: $QUEUE_URL"
```

Expected output:

```
Queue URL: https://sqs.us-east-1.amazonaws.com/123456789012/lab08-queue
```

5. Verify the queue exists by listing your queues:

```bash
aws sqs list-queues --region us-east-1
```

6. Send a message to the queue:

```bash
aws sqs send-message \
  --queue-url $QUEUE_URL \
  --message-body "Order 1001: 3 widgets" \
  --region us-east-1
```

Expected output:

```json
{
    "MD5OfMessageBody": "a1b2c3d4e5f6...",
    "MessageId": "abcdef12-3456-7890-abcd-ef1234567890"
}
```

7. Receive the message from the queue:

```bash
aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --region us-east-1
```

Expected output:

```json
{
    "Messages": [
        {
            "MessageId": "abcdef12-3456-7890-abcd-ef1234567890",
            "ReceiptHandle": "AQEBwJnK...",
            "MD5OfBody": "a1b2c3d4e5f6...",
            "Body": "Order 1001: 3 widgets"
        }
    ]
}
```

8. Copy the `ReceiptHandle` value from the output. You need it to delete the message. Store it in a variable:

```bash
RECEIPT_HANDLE=$(aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --region us-east-1 \
  --query "Messages[0].ReceiptHandle" \
  --output text)
echo "Receipt Handle: $RECEIPT_HANDLE"
```

> **Tip:** If the output shows `None`, the message may still be hidden due to the [visibility timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html). Wait 30 seconds (the default visibility timeout) and try again.

9. Delete the message from the queue using the receipt handle:

```bash
aws sqs delete-message \
  --queue-url $QUEUE_URL \
  --receipt-handle $RECEIPT_HANDLE \
  --region us-east-1
```

This command produces no output on success.

10. Verify the queue is empty by receiving again:

```bash
aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --region us-east-1
```

**Expected result:** No messages are returned (empty response or no `Messages` key), confirming the message was deleted.

> **Tip:** In a real application, a consumer receives a message, processes it, and then deletes it. If the consumer fails to delete the message before the visibility timeout expires, the message becomes visible again for another consumer to process. This provides automatic retry behavior.


### Step 2: Configure Long Polling

By default, SQS uses [short polling](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html), which returns immediately even if the queue is empty. [Long polling](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html) waits up to 20 seconds for a message to arrive, reducing empty responses and lowering costs.

1. Configure long polling on the queue by setting `ReceiveMessageWaitTimeSeconds` to 20:

```bash
aws sqs set-queue-attributes \
  --queue-url $QUEUE_URL \
  --attributes ReceiveMessageWaitTimeSeconds=20 \
  --region us-east-1
```

2. Verify the attribute was set:

```bash
aws sqs get-queue-attributes \
  --queue-url $QUEUE_URL \
  --attribute-names ReceiveMessageWaitTimeSeconds \
  --region us-east-1
```

Expected output:

```json
{
    "Attributes": {
        "ReceiveMessageWaitTimeSeconds": "20"
    }
}
```

3. Test long polling by receiving from the empty queue. Notice that the command pauses for up to 20 seconds before returning, instead of returning immediately:

```bash
aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --wait-time-seconds 20 \
  --region us-east-1
```

**Expected result:** The command waits approximately 20 seconds and then returns an empty response. With short polling, the command would have returned immediately.

4. Now send a message and receive it with long polling to see the difference:

```bash
aws sqs send-message \
  --queue-url $QUEUE_URL \
  --message-body "Order 1002: 5 gadgets" \
  --region us-east-1
```

```bash
RECEIPT_HANDLE=$(aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --wait-time-seconds 20 \
  --region us-east-1 \
  --query "Messages[0].ReceiptHandle" \
  --output text)
echo "Receipt Handle: $RECEIPT_HANDLE"
```

**Expected result:** The message is returned immediately because it is already in the queue. Long polling only waits when the queue is empty.

5. Delete the message to clean up:

```bash
aws sqs delete-message \
  --queue-url $QUEUE_URL \
  --receipt-handle $RECEIPT_HANDLE \
  --region us-east-1
```

> **Tip:** Always use long polling unless you have a specific reason to use short polling. Long polling queries all SQS servers, which eliminates false empty responses and reduces the number of API calls you pay for.

### Step 3: Create an SNS Topic and Subscribe an Email Endpoint

In this step, you create an [Amazon SNS topic](https://docs.aws.amazon.com/sns/latest/dg/sns-create-topic.html), subscribe your email address, confirm the subscription, and publish a test message.

1. Create an SNS topic:

```bash
TOPIC_ARN=$(aws sns create-topic \
  --name lab08-topic \
  --region us-east-1 \
  --query "TopicArn" \
  --output text)
echo "Topic ARN: $TOPIC_ARN"
```

Expected output:

```
Topic ARN: arn:aws:sns:us-east-1:123456789012:lab08-topic
```

2. Subscribe your email address to the topic. Replace `your-email@example.com` with your actual email address:

```bash
aws sns subscribe \
  --topic-arn $TOPIC_ARN \
  --protocol email \
  --notification-endpoint your-email@example.com \
  --region us-east-1
```

Expected output:

```json
{
    "SubscriptionArn": "pending confirmation"
}
```

> **Warning:** The subscription status shows "pending confirmation" because email subscriptions require you to confirm from your inbox. SNS does not deliver messages to unconfirmed email subscriptions.

3. Check your email inbox for a message from "AWS Notifications" with the subject "AWS Notification - Subscription Confirmation". Open the email and click the **Confirm subscription** link.

4. After confirming, verify the subscription is active:

```bash
aws sns list-subscriptions-by-topic \
  --topic-arn $TOPIC_ARN \
  --region us-east-1
```

Expected output (partial):

```json
{
    "Subscriptions": [
        {
            "SubscriptionArn": "arn:aws:sns:us-east-1:123456789012:lab08-topic:abcd1234-...",
            "Owner": "123456789012",
            "Protocol": "email",
            "Endpoint": "your-email@example.com",
            "TopicArn": "arn:aws:sns:us-east-1:123456789012:lab08-topic"
        }
    ]
}
```

5. Publish a test message to the topic:

```bash
aws sns publish \
  --topic-arn $TOPIC_ARN \
  --subject "Lab 08 Test" \
  --message "This is a test message from the Module 08 messaging lab." \
  --region us-east-1
```

Expected output:

```json
{
    "MessageId": "12345678-abcd-efgh-ijkl-123456789012"
}
```

6. Check your email inbox. You should receive an email with the subject "Lab 08 Test" and the message body "This is a test message from the Module 08 messaging lab."

**Expected result:** The email arrives in your inbox within a few seconds, confirming that SNS successfully delivered the message to your email subscription.


### Step 4: Set Up the Fan-Out Pattern (SNS to Multiple SQS Queues)

The [fan-out pattern](https://docs.aws.amazon.com/sns/latest/dg/subscribe-sqs-queue-to-sns-topic.html) uses an SNS topic to deliver a single message to multiple SQS queues simultaneously. Each queue feeds a different consumer that processes the message independently. In this step, you create two SQS queues, subscribe both to the SNS topic, and verify that a single published message reaches both queues.

1. Create two SQS queues for the fan-out:

```bash
FANOUT_QUEUE_A_URL=$(aws sqs create-queue \
  --queue-name lab08-fanout-queue-a \
  --region us-east-1 \
  --query "QueueUrl" \
  --output text)
echo "Fanout Queue A URL: $FANOUT_QUEUE_A_URL"
```

```bash
FANOUT_QUEUE_B_URL=$(aws sqs create-queue \
  --queue-name lab08-fanout-queue-b \
  --region us-east-1 \
  --query "QueueUrl" \
  --output text)
echo "Fanout Queue B URL: $FANOUT_QUEUE_B_URL"
```

2. Retrieve the ARN for each queue. SNS subscriptions require the queue ARN, not the URL:

```bash
FANOUT_QUEUE_A_ARN=$(aws sqs get-queue-attributes \
  --queue-url $FANOUT_QUEUE_A_URL \
  --attribute-names QueueArn \
  --region us-east-1 \
  --query "Attributes.QueueArn" \
  --output text)
echo "Fanout Queue A ARN: $FANOUT_QUEUE_A_ARN"
```

```bash
FANOUT_QUEUE_B_ARN=$(aws sqs get-queue-attributes \
  --queue-url $FANOUT_QUEUE_B_URL \
  --attribute-names QueueArn \
  --region us-east-1 \
  --query "Attributes.QueueArn" \
  --output text)
echo "Fanout Queue B ARN: $FANOUT_QUEUE_B_ARN"
```

3. Get your AWS account ID (you need it for the queue policy):

```bash
ACCOUNT_ID=$(aws sts get-caller-identity \
  --query "Account" \
  --output text)
echo "Account ID: $ACCOUNT_ID"
```

4. Grant the SNS topic permission to send messages to each SQS queue. Without this [resource-based policy](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-overview-of-managing-access.html), SNS cannot deliver messages to the queues.

Set the policy on Queue A:

```bash
aws sqs set-queue-attributes \
  --queue-url $FANOUT_QUEUE_A_URL \
  --attributes '{
    "Policy": "{\"Version\":\"2012-10-17\",\"Statement\":[{\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"sns.amazonaws.com\"},\"Action\":\"sqs:SendMessage\",\"Resource\":\"'"$FANOUT_QUEUE_A_ARN"'\",\"Condition\":{\"ArnEquals\":{\"aws:SourceArn\":\"'"$TOPIC_ARN"'\"}}}]}"
  }' \
  --region us-east-1
```

Set the policy on Queue B:

```bash
aws sqs set-queue-attributes \
  --queue-url $FANOUT_QUEUE_B_URL \
  --attributes '{
    "Policy": "{\"Version\":\"2012-10-17\",\"Statement\":[{\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"sns.amazonaws.com\"},\"Action\":\"sqs:SendMessage\",\"Resource\":\"'"$FANOUT_QUEUE_B_ARN"'\",\"Condition\":{\"ArnEquals\":{\"aws:SourceArn\":\"'"$TOPIC_ARN"'\"}}}]}"
  }' \
  --region us-east-1
```

> **Tip:** The `Condition` element restricts access so that only the specified SNS topic can send messages to the queue. This follows the principle of least privilege you learned in [Module 02](../../02-iam-and-security/README.md).

5. Subscribe both queues to the SNS topic:

```bash
aws sns subscribe \
  --topic-arn $TOPIC_ARN \
  --protocol sqs \
  --notification-endpoint $FANOUT_QUEUE_A_ARN \
  --region us-east-1
```

```bash
aws sns subscribe \
  --topic-arn $TOPIC_ARN \
  --protocol sqs \
  --notification-endpoint $FANOUT_QUEUE_B_ARN \
  --region us-east-1
```

> **Tip:** Unlike email subscriptions, SQS subscriptions are confirmed automatically when the queue policy allows the SNS topic to send messages. You do not need to manually confirm SQS subscriptions.

6. Verify all subscriptions on the topic:

```bash
aws sns list-subscriptions-by-topic \
  --topic-arn $TOPIC_ARN \
  --region us-east-1
```

**Expected result:** You see three subscriptions: one email and two SQS queues.

7. Publish a message to the SNS topic:

```bash
aws sns publish \
  --topic-arn $TOPIC_ARN \
  --subject "Fan-out Test" \
  --message "Order 1003: fan-out to multiple queues" \
  --region us-east-1
```

8. Wait a few seconds, then check both queues for the message:

```bash
echo "--- Queue A ---"
aws sqs receive-message \
  --queue-url $FANOUT_QUEUE_A_URL \
  --region us-east-1 \
  --query "Messages[0].Body" \
  --output text
```

```bash
echo "--- Queue B ---"
aws sqs receive-message \
  --queue-url $FANOUT_QUEUE_B_URL \
  --region us-east-1 \
  --query "Messages[0].Body" \
  --output text
```

**Expected result:** Both queues contain a copy of the message. The message body is a JSON envelope from SNS that includes the original message in the `Message` field. This confirms the fan-out pattern is working: one published message was delivered to both queues independently.

9. You should also receive the same message in your email inbox, since the email subscription is still active on the topic.

> **Tip:** In a production architecture, each queue would feed a different consumer. For example, Queue A might process order fulfillment while Queue B feeds an analytics pipeline. Each consumer processes messages at its own pace without affecting the other.


### Step 5: Create a Dead-Letter Queue and Configure the Redrive Policy

A [dead-letter queue (DLQ)](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html) captures messages that fail processing after a specified number of attempts. In this step, you create a DLQ, attach a redrive policy to the source queue, and demonstrate a message moving to the DLQ by receiving it multiple times without deleting it.

1. Create the dead-letter queue:

```bash
DLQ_URL=$(aws sqs create-queue \
  --queue-name lab08-dlq \
  --region us-east-1 \
  --query "QueueUrl" \
  --output text)
echo "DLQ URL: $DLQ_URL"
```

2. Retrieve the DLQ ARN:

```bash
DLQ_ARN=$(aws sqs get-queue-attributes \
  --queue-url $DLQ_URL \
  --attribute-names QueueArn \
  --region us-east-1 \
  --query "Attributes.QueueArn" \
  --output text)
echo "DLQ ARN: $DLQ_ARN"
```

3. Configure the [redrive policy](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html) on the source queue (`lab08-queue`). Set `maxReceiveCount` to 3, meaning a message that is received 3 times without being deleted is moved to the DLQ:

```bash
aws sqs set-queue-attributes \
  --queue-url $QUEUE_URL \
  --attributes '{
    "RedrivePolicy": "{\"deadLetterTargetArn\":\"'"$DLQ_ARN"'\",\"maxReceiveCount\":\"3\"}"
  }' \
  --region us-east-1
```

4. Verify the redrive policy is set:

```bash
aws sqs get-queue-attributes \
  --queue-url $QUEUE_URL \
  --attribute-names RedrivePolicy \
  --region us-east-1
```

Expected output:

```json
{
    "Attributes": {
        "RedrivePolicy": "{\"deadLetterTargetArn\":\"arn:aws:sqs:us-east-1:123456789012:lab08-dlq\",\"maxReceiveCount\":\"3\"}"
    }
}
```

5. To demonstrate the DLQ in action, first set a short visibility timeout on the source queue so you do not have to wait long between receives:

```bash
aws sqs set-queue-attributes \
  --queue-url $QUEUE_URL \
  --attributes VisibilityTimeout=5 \
  --region us-east-1
```

6. Send a "poison message" to the source queue (a message you will intentionally not delete):

```bash
aws sqs send-message \
  --queue-url $QUEUE_URL \
  --message-body "Poison message: this will fail processing" \
  --region us-east-1
```

7. Receive the message three times without deleting it. After each receive, the visibility timeout hides the message for 5 seconds. Wait at least 6 seconds between each receive:

```bash
echo "--- Receive 1 ---"
aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --region us-east-1 \
  --query "Messages[0].Body" \
  --output text
```

Wait 6 seconds:

```bash
sleep 6
```

```bash
echo "--- Receive 2 ---"
aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --region us-east-1 \
  --query "Messages[0].Body" \
  --output text
```

Wait 6 seconds:

```bash
sleep 6
```

```bash
echo "--- Receive 3 ---"
aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --region us-east-1 \
  --query "Messages[0].Body" \
  --output text
```

**Expected result:** Each receive returns the same message body: "Poison message: this will fail processing". After the third receive, the message has reached the `maxReceiveCount` of 3.

8. Wait 6 seconds, then try to receive from the source queue again:

```bash
sleep 6
```

```bash
echo "--- Receive 4 (should be empty) ---"
aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --region us-east-1
```

**Expected result:** The source queue returns an empty response. The message has been moved to the DLQ.

9. Check the dead-letter queue for the message:

```bash
aws sqs receive-message \
  --queue-url $DLQ_URL \
  --region us-east-1 \
  --query "Messages[0].Body" \
  --output text
```

**Expected result:** The DLQ contains the poison message: "Poison message: this will fail processing". This confirms that SQS automatically moved the message to the DLQ after it was received 3 times without being deleted.

> **Warning:** In production, always configure a DLQ for every SQS queue. Without a DLQ, poison messages cycle through the queue indefinitely, wasting compute resources and delaying the processing of healthy messages.

### Step 6: Monitor the DLQ Using CloudWatch Metrics

[Amazon CloudWatch](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-available-cloudwatch-metrics.html) automatically collects metrics for every SQS queue. The `ApproximateNumberOfMessagesVisible` metric shows how many messages are available for retrieval. Monitoring this metric on your DLQ helps you detect failures quickly.

1. Check the current number of messages in the DLQ using the SQS API:

```bash
aws sqs get-queue-attributes \
  --queue-url $DLQ_URL \
  --attribute-names ApproximateNumberOfMessagesVisible \
  --region us-east-1
```

Expected output:

```json
{
    "Attributes": {
        "ApproximateNumberOfMessagesVisible": "1"
    }
}
```

2. You can also view this metric in the CloudWatch console. In the console search bar, type `CloudWatch` and select **CloudWatch** from the results.

3. In the left navigation pane, choose **Metrics**, then **All metrics**.

4. In the **Browse** tab, choose **SQS**.

5. Choose **Queue Metrics**.

6. Find the row where **QueueName** is `lab08-dlq` and **MetricName** is `ApproximateNumberOfMessagesVisible`. Select the checkbox to graph it.

**Expected result:** The graph shows a data point of 1, representing the poison message that was moved to the DLQ.

7. Query the metric from the CLI using `cloudwatch get-metric-statistics`:

```bash
aws cloudwatch get-metric-statistics \
  --namespace AWS/SQS \
  --metric-name ApproximateNumberOfMessagesVisible \
  --dimensions Name=QueueName,Value=lab08-dlq \
  --start-time $(date -u -d '10 minutes ago' +%Y-%m-%dT%H:%M:%S 2>/dev/null || date -u -v-10M +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 300 \
  --statistics Maximum \
  --region us-east-1
```

> **Tip:** The `date` command syntax differs between Linux and macOS. The command above includes both variants separated by `||`. In CloudShell (Amazon Linux), the `-d` flag is used.

Expected output (partial):

```json
{
    "Label": "ApproximateNumberOfMessagesVisible",
    "Datapoints": [
        {
            "Timestamp": "2024-01-15T10:00:00Z",
            "Maximum": 1.0,
            "Unit": "Count"
        }
    ]
}
```

8. In a production environment, you would create a [CloudWatch alarm](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/monitoring-using-cloudwatch.html) on this metric to notify you when messages appear in the DLQ. For example, an alarm that triggers when `ApproximateNumberOfMessagesVisible` is greater than 0 for 1 evaluation period would alert you immediately when a message fails processing.

> **Tip:** SQS metrics are reported to CloudWatch at 5-minute intervals. If you do not see data points immediately, wait a few minutes and query again.

## Validation

Confirm the following:

- [ ] You created an SQS Standard queue (`lab08-queue`) and successfully sent, received, and deleted a message
- [ ] Long polling is configured on `lab08-queue` with `ReceiveMessageWaitTimeSeconds` set to 20
- [ ] You created an SNS topic (`lab08-topic`) with a confirmed email subscription and received a test email
- [ ] Two fan-out queues (`lab08-fanout-queue-a` and `lab08-fanout-queue-b`) are subscribed to the SNS topic, and both received a copy of a published message
- [ ] A dead-letter queue (`lab08-dlq`) is configured with a redrive policy on `lab08-queue` (maxReceiveCount=3)
- [ ] A poison message was moved to the DLQ after being received 3 times without deletion
- [ ] You viewed the `ApproximateNumberOfMessagesVisible` metric for the DLQ in CloudWatch (console or CLI)


## Cleanup

Delete all resources created in this lab to avoid charges. SQS and SNS are pay-per-use services with generous free tiers, but cleaning up is good practice.

1. Delete the SNS subscriptions. First, list all subscriptions on the topic:

```bash
aws sns list-subscriptions-by-topic \
  --topic-arn $TOPIC_ARN \
  --region us-east-1 \
  --query "Subscriptions[].SubscriptionArn" \
  --output text
```

2. Delete each subscription (replace the ARN with each value from the output above):

```bash
SUBSCRIPTION_ARNS=$(aws sns list-subscriptions-by-topic \
  --topic-arn $TOPIC_ARN \
  --region us-east-1 \
  --query "Subscriptions[].SubscriptionArn" \
  --output text)

for SUB_ARN in $SUBSCRIPTION_ARNS; do
  if [ "$SUB_ARN" != "PendingConfirmation" ]; then
    aws sns unsubscribe --subscription-arn $SUB_ARN --region us-east-1
    echo "Unsubscribed: $SUB_ARN"
  fi
done
```

3. Delete the SNS topic:

```bash
aws sns delete-topic \
  --topic-arn $TOPIC_ARN \
  --region us-east-1
echo "Deleted topic: $TOPIC_ARN"
```

4. Delete all SQS queues created in this lab:

```bash
aws sqs delete-queue --queue-url $QUEUE_URL --region us-east-1
echo "Deleted: lab08-queue"
```

```bash
aws sqs delete-queue --queue-url $DLQ_URL --region us-east-1
echo "Deleted: lab08-dlq"
```

```bash
aws sqs delete-queue --queue-url $FANOUT_QUEUE_A_URL --region us-east-1
echo "Deleted: lab08-fanout-queue-a"
```

```bash
aws sqs delete-queue --queue-url $FANOUT_QUEUE_B_URL --region us-east-1
echo "Deleted: lab08-fanout-queue-b"
```

5. Verify all queues are deleted:

```bash
aws sqs list-queues \
  --queue-name-prefix lab08 \
  --region us-east-1
```

**Expected result:** No queues with the `lab08` prefix are returned.

> **Warning:** SQS queues may take up to 60 seconds to fully delete. If you see the queues listed immediately after deletion, wait a minute and check again.

## Challenge (Optional)

Extend this lab by completing the following tasks using only concepts and services covered up to and including Module 08:

1. **FIFO queue with deduplication.** Create an SQS FIFO queue (the queue name must end with `.fifo`). Send two messages with the same `MessageDeduplicationId` and `MessageGroupId`. Verify that SQS delivers only one copy of the message, demonstrating the [exactly-once processing](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-fifo-queues.html) guarantee of FIFO queues.

2. **SNS message filtering.** Add a [filter policy](https://docs.aws.amazon.com/sns/latest/dg/sns-message-filtering.html) to one of the fan-out queue subscriptions so that it receives only messages with a specific attribute value. Publish messages with different attributes and verify that the filtered subscription receives only matching messages while the unfiltered subscription receives all messages.

3. **CloudWatch alarm on the DLQ.** Create a CloudWatch alarm that triggers when `ApproximateNumberOfMessagesVisible` on the DLQ exceeds 0. Subscribe your email to the alarm's SNS notification topic. Send a poison message to the source queue, let it move to the DLQ, and verify that you receive an alarm notification email.

> **Tip:** For the FIFO queue challenge, remember to set `FifoQueue=true` and `ContentBasedDeduplication=true` (or provide a `MessageDeduplicationId` with each message) when creating the queue. FIFO queue names must end with the `.fifo` suffix.
