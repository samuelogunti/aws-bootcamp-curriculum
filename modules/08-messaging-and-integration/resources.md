# Module 08: Resources

## Official Documentation

### Amazon SQS

- [What is Amazon SQS?](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)
- [Amazon SQS Standard Queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/standard-queues.html)
- [Amazon SQS FIFO Queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-fifo-queues.html)
- [Amazon SQS Visibility Timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html)
- [Amazon SQS Short and Long Polling](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html)
- [Setting Up Long Polling in Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/best-practices-setting-up-long-polling.html)
- [Using Dead-Letter Queues in Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html)
- [Setting Up Dead-Letter Queue Retention in Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/setting-up-dead-letter-queue-retention.html)
- [Configuring Amazon SQS Queues (Overview)](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-overview.html)
- [Overview of Managing Access in Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-overview-of-managing-access.html)
- [Available CloudWatch Metrics for Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-available-cloudwatch-metrics.html)
- [Monitoring Amazon SQS Queues Using CloudWatch](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/monitoring-using-cloudwatch.html)

### Amazon SNS

- [What is Amazon SNS?](https://docs.aws.amazon.com/sns/latest/dg/welcome.html)
- [Creating an Amazon SNS Topic](https://docs.aws.amazon.com/sns/latest/dg/sns-create-topic.html)
- [Creating a Subscription to an Amazon SNS Topic](https://docs.aws.amazon.com/sns/latest/dg/sns-create-subscribe-endpoint-to-topic.html)
- [Amazon SNS Message Filtering](https://docs.aws.amazon.com/sns/latest/dg/sns-message-filtering.html)
- [Amazon SNS Subscription Filter Policies](https://docs.aws.amazon.com/sns/latest/dg/sns-subscription-filter-policies.html)
- [Subscribing an Amazon SQS Queue to an Amazon SNS Topic](https://docs.aws.amazon.com/sns/latest/dg/subscribe-sqs-queue-to-sns-topic.html)
- [Amazon SNS Dead-Letter Queues](https://docs.aws.amazon.com/sns/latest/dg/sns-dead-letter-queues.html)

### Amazon EventBridge

- [What is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html)
- [Event Bus Concepts in Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is-how-it-works-concepts.html)
- [Event Buses in Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-bus.html)
- [Creating Amazon EventBridge Event Patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html)
- [Event Bus Targets in Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-targets.html)
- [EventBridge Schema Registry](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-schema-registry.html)
- [EventBridge Schema Discovery](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-schema.html)
- [Using Dead-Letter Queues to Process Undelivered Events in EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rule-dlq.html)

### AWS Step Functions

- [What is Step Functions?](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html)
- [Learn About State Machines in Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-statemachines.html)
- [Discover Service Integration Patterns in Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/connect-to-resource.html)
- [Integrating Services with Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/integrate-services.html)

### Choosing Between Services

- [Amazon SQS, Amazon SNS, or Amazon EventBridge?](https://docs.aws.amazon.com/decision-guides/latest/sns-or-sqs-or-eventbridge/sns-or-sqs-or-eventbridge.html)
- [Choosing an AWS Application Integration Service](https://docs.aws.amazon.com/decision-guides/latest/application-integration-on-aws-how-to-choose/application-integration-on-aws-how-to-choose.html)

### AWS Tutorials

- [Send Fanout Event Notifications (SNS + SQS)](https://docs.aws.amazon.com/hands-on/latest/send-fanout-event-notifications/send-fanout-event-notifications.html)

## AWS Whitepapers

- [Implementing Microservices on AWS](https://docs.aws.amazon.com/whitepapers/latest/microservices-on-aws/microservices-on-aws.html): Covers communication mechanisms between microservices, including synchronous and asynchronous messaging patterns using SQS, SNS, and EventBridge. Students will revisit this whitepaper in Module 18 (Architecture Patterns).

## AWS FAQs

- [Amazon SQS FAQ](https://aws.amazon.com/sqs/faqs/)
- [Amazon SNS FAQ](https://aws.amazon.com/sns/faqs/)

## AWS Architecture References

- [Integrating Microservices by Using AWS Serverless Services (Messaging)](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-integrating-microservices/messaging.html): AWS Prescriptive Guidance on using SQS, SNS, and EventBridge for asynchronous communication in microservices architectures. Students will explore these patterns further in Module 09 (Serverless: Lambda) and Module 18 (Architecture Patterns).

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
