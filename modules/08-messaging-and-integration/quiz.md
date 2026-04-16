# Module 08: Quiz

1. Which of the following best describes the primary benefit of loose coupling in a distributed architecture?

   A) Components share a single database for faster data access
   B) Components communicate through a messaging layer, so a failure in one component does not cascade to others
   C) Components are deployed together in a single package to reduce latency
   D) Components use synchronous HTTP calls to guarantee immediate responses

2. True or False: An Amazon SQS FIFO queue guarantees exactly-once processing and strict first-in, first-out message ordering, but it must have a queue name that ends with the `.fifo` suffix.

3. A consumer receives a message from an SQS queue but takes longer to process it than the configured visibility timeout. What happens to the message?

   A) The message is permanently deleted from the queue
   B) The message is moved to the dead-letter queue immediately
   C) The message becomes visible again in the queue and can be received by another consumer
   D) The message remains hidden until the consumer explicitly releases it

4. In your own words, explain the difference between long polling and short polling in Amazon SQS, and state which approach AWS recommends for most use cases.

5. An SNS topic has three subscriptions: an email endpoint, an SQS queue, and a Lambda function. When a message is published to the topic, which subscribers receive the message?

   A) Only the first subscriber that was created
   B) Only the SQS queue, because SQS is the most reliable endpoint
   C) All three subscribers receive a copy of the message
   D) The subscriber is selected at random for each message

6. Which of the following are valid Amazon SNS subscription protocols? (Select THREE.)

   A) Email
   B) Amazon SQS
   C) Amazon DynamoDB
   D) AWS Lambda
   E) Amazon Redshift

7. True or False: Amazon EventBridge can automatically receive events from AWS services (such as EC2 instance state changes) on the default event bus without requiring you to write any publishing code.

8. A development team wants to process a single uploaded file through three independent pipelines simultaneously: thumbnail generation, metadata extraction, and audit logging. Which integration pattern is most appropriate?

   A) Send the file path to a single SQS queue and have all three consumers poll the same queue
   B) Publish a notification to an SNS topic that fans out to three separate SQS queues, one per pipeline
   C) Create three separate EventBridge rules on three different custom event buses
   D) Use a Step Functions state machine with a single Task state

9. A message in an SQS queue has been received and returned to the queue three times because the consumer keeps failing to process it. The queue has a dead-letter queue configured with `maxReceiveCount` set to 3. What happens on the next receive attempt?

   A) The message is delivered to the consumer a fourth time
   B) The message is deleted from both the source queue and the dead-letter queue
   C) The message is moved to the dead-letter queue instead of being delivered to the consumer
   D) The source queue pauses all message delivery until the failed message is manually removed

10. Match each AWS Step Functions state type to its purpose by filling in the correct state type name for each description:

    a) Performs work by invoking an AWS service (such as a Lambda function): ___________
    b) Adds branching logic based on input values: ___________
    c) Pauses execution for a specified duration: ___________
    d) Runs multiple branches of work simultaneously: ___________

---

<details>
<summary>Answer Key</summary>

1. **B) Components communicate through a messaging layer, so a failure in one component does not cascade to others**
   Loose coupling places a messaging intermediary (a queue, topic, or event bus) between components. The producer does not need to know whether the consumer is available, which provides fault isolation, independent scaling, and deployment independence. Option A describes tight coupling through a shared database. Option C describes a monolithic deployment, not loose coupling. Option D describes synchronous communication, which is a characteristic of tight coupling.
   Further reading: [What is Amazon SQS?](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)

2. **True.**
   SQS FIFO queues guarantee strict first-in, first-out delivery and exactly-once processing by using message deduplication IDs to eliminate duplicates. FIFO queue names must end with the `.fifo` suffix. Standard queues, by contrast, provide best-effort ordering and at-least-once delivery with higher throughput.
   Further reading: [Amazon SQS FIFO queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-fifo-queues.html)

3. **C) The message becomes visible again in the queue and can be received by another consumer**
   The visibility timeout is the period during which a message is hidden from other consumers after one consumer receives it. If the consumer does not delete the message before the visibility timeout expires, the message becomes visible again for another consumer to process. This provides automatic retry behavior. The message is not deleted (A) or moved to the DLQ immediately (B). The consumer does not need to explicitly release it (D); the timeout handles this automatically.
   Further reading: [Amazon SQS visibility timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html)

4. **Sample answer:** Short polling returns immediately, even if the queue is empty, and queries only a subset of SQS servers. This can result in frequent empty responses and higher costs due to more API calls. Long polling waits up to 20 seconds for a message to arrive before returning and queries all SQS servers, which eliminates false empty responses and reduces costs. AWS recommends long polling for most use cases because it is more cost-effective and provides more reliable message retrieval.
   Further reading: [Amazon SQS short and long polling](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html)

5. **C) All three subscribers receive a copy of the message**
   Amazon SNS uses a publish/subscribe (pub/sub) model. When a message is published to a topic, SNS delivers a copy of the message to every confirmed subscription on that topic, regardless of the endpoint type. This is the fundamental difference between SNS (one-to-many push) and SQS (one-to-one pull).
   Further reading: [What is Amazon SNS?](https://docs.aws.amazon.com/sns/latest/dg/welcome.html)

6. **A, B, D**
   Amazon SNS supports several subscription protocols, including Email (A), Amazon SQS (B), and AWS Lambda (D). Other supported protocols include HTTP/HTTPS, SMS, and platform application (mobile push). Amazon DynamoDB (C) and Amazon Redshift (E) are not valid SNS subscription protocols. To send data to DynamoDB or Redshift, you would use an intermediary such as a Lambda function subscribed to the topic.
   Further reading: [Creating a subscription to an Amazon SNS topic](https://docs.aws.amazon.com/sns/latest/dg/sns-create-subscribe-endpoint-to-topic.html)

7. **True.**
   The default event bus in every AWS account automatically receives events from AWS services. For example, EC2 instance state changes, S3 object creation events, and IAM policy changes are all emitted to the default event bus without any publishing code. You create EventBridge rules with event patterns to match and route these events to targets.
   Further reading: [What is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html), [Event bus concepts in Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is-how-it-works-concepts.html)

8. **B) Publish a notification to an SNS topic that fans out to three separate SQS queues, one per pipeline**
   The fan-out pattern uses an SNS topic to deliver a single message to multiple SQS queues simultaneously. Each queue feeds an independent consumer that processes the message at its own pace. Option A is incorrect because a single SQS queue delivers each message to only one consumer, not all three. Option C adds unnecessary complexity with multiple event buses. Option D uses a single Task state, which does not run three pipelines in parallel (a Parallel state would be needed, but the fan-out pattern is simpler for independent consumers).
   Further reading: [Subscribing an Amazon SQS queue to an Amazon SNS topic](https://docs.aws.amazon.com/sns/latest/dg/subscribe-sqs-queue-to-sns-topic.html)

9. **C) The message is moved to the dead-letter queue instead of being delivered to the consumer**
   When a message has been received the number of times specified by `maxReceiveCount` (3 in this case) without being deleted, SQS automatically moves it to the configured dead-letter queue on the next receive attempt. This prevents "poison messages" from cycling through the queue indefinitely and blocking the processing of other messages. You should monitor the DLQ using the `ApproximateNumberOfMessagesVisible` CloudWatch metric to detect failures.
   Further reading: [Using dead-letter queues in Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html)

10. **Answers:**
    a) **Task** state: performs work by invoking an AWS service such as a Lambda function, running an ECS task, or inserting a DynamoDB item.
    b) **Choice** state: adds branching logic based on input values, routing the workflow to different processing paths.
    c) **Wait** state: pauses execution for a specified time or until a specific timestamp.
    d) **Parallel** state: runs multiple branches of work simultaneously and waits for all branches to complete before continuing.
    Other Step Functions state types include Pass (transforms data), Succeed (marks successful completion), and Fail (marks the workflow as failed).
    Further reading: [Learn about state machines in Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-statemachines.html)

</details>
