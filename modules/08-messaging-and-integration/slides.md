---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 08: Messaging and Integration'
---

# Module 08: Messaging and Integration Services

**Phase 2: Core Services**
Estimated lecture time: 75 minutes

<!-- Speaker notes: Welcome to Module 08, the last module in Phase 2. This module covers SQS, SNS, EventBridge, and Step Functions. Breakdown: 10 min loose coupling, 15 min SQS, 10 min SNS, 10 min EventBridge, 10 min fan-out pattern, 10 min DLQs, 5 min Step Functions, 5 min decision framework and Q&A. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Demonstrate the benefits of loose coupling and asynchronous communication
- Configure SQS queues (Standard and FIFO) with visibility timeout and retention
- Implement SNS topics with multiple subscription types and message filtering
- Use EventBridge to route events using event buses, rules, and patterns
- Set up a fan-out pattern (SNS to multiple SQS queues)
- Configure dead-letter queues (DLQs) for failed messages
- Demonstrate how Step Functions orchestrates multi-step workflows
- Use a decision framework to choose SQS, SNS, or EventBridge

---

## Prerequisites and agenda

**Prerequisites:** Module 02 (IAM), Module 04 (EC2 as producers/consumers), Module 05 (S3 event notifications)

**Agenda:**
1. Tight coupling vs. loose coupling
2. Amazon SQS: message queues
3. Amazon SNS: publish/subscribe
4. Amazon EventBridge: event routing
5. Fan-out pattern (SNS + SQS)
6. Dead-letter queues
7. AWS Step Functions
8. Choosing the right integration service

---

# Why decouple?

<!-- Speaker notes: This section takes approximately 10 minutes. Draw a tightly coupled architecture on the whiteboard, then show what happens when the backend fails. Introduce a queue between components. -->

---

## Tight coupling vs. loose coupling

**Tight coupling:** components call each other directly
- If one fails, the failure cascades
- Changes require coordinated deployments
- Scaling one component does not help if the other is the bottleneck

**Loose coupling:** components communicate through a messaging layer
- **Fault isolation:** messages wait in the queue until the consumer recovers
- **Independent scaling:** scale producers and consumers separately
- **Async processing:** producer sends and moves on immediately

---

# Amazon SQS

<!-- Speaker notes: This section takes approximately 15 minutes. Cover the basic workflow, queue types, visibility timeout, and long polling. -->

---

## How SQS works

1. **Send:** producer sends a message to the queue (up to 256 KB)
2. **Receive:** consumer polls the queue and receives messages
3. **Delete:** consumer deletes the message after processing

SQS is pull-based: consumers poll the queue to retrieve messages.

```bash
aws sqs send-message \
    --queue-url https://sqs.us-east-1.amazonaws.com/123456789012/my-queue \
    --message-body "Order 12345 placed"
```

---

## Standard vs. FIFO queues

| Feature | Standard Queue | FIFO Queue |
|---------|----------------|------------|
| Ordering | Best-effort | Strict FIFO guaranteed |
| Delivery | At-least-once | Exactly-once |
| Throughput | Nearly unlimited | Up to 3,000 msg/s with batching |
| Deduplication | None | Built-in |
| Queue name | Any valid name | Must end with `.fifo` |

> Start with Standard unless you need strict ordering or exactly-once processing.

---

## Visibility timeout and long polling

- **Visibility timeout:** hides a message from other consumers while one processes it (default 30s, max 12h)
- Set it longer than your expected processing time
- **Long polling:** waits up to 20s for messages before returning empty (reduces costs)
- **Short polling:** returns immediately, may miss messages

| Polling Type | Wait Time | Empty Responses | Cost |
|-------------|-----------|-----------------|------|
| Short | 0 seconds | Frequent | Higher |
| Long | 1-20 seconds | Rare | Lower |

> Always use long polling unless you have a specific reason not to.

---

## Quick check: SQS visibility timeout

A consumer receives a message and begins processing. The visibility timeout is 30 seconds, but processing takes 60 seconds.

**What happens?**

<!-- Speaker notes: Answer: After 30 seconds, the visibility timeout expires and the message becomes visible again. Another consumer can pick it up, leading to duplicate processing. The fix: set the visibility timeout to at least 60 seconds (with buffer). This is a common misconfiguration that causes duplicate processing in production. -->

---

# Amazon SNS

<!-- Speaker notes: This section takes approximately 10 minutes. Cover topics, subscriptions, and message filtering. -->

---

## SNS: publish/subscribe messaging

- Push-based: messages delivered to subscribers automatically
- Publisher sends to a topic; all subscribers receive a copy
- Supports email, SQS, Lambda, HTTP/HTTPS, SMS subscriptions

```bash
aws sns create-topic --name my-topic
```

---

## SNS message filtering

- Attach a filter policy to a subscription
- Subscriber receives only messages matching the filter
- Reduces need for subscribers to discard irrelevant messages

```json
{
    "order_priority": ["high"]
}
```

This subscriber receives only messages where `order_priority` is `"high"`.

---

## SQS vs. SNS

| Feature | Amazon SQS | Amazon SNS |
|---------|-----------|-----------|
| Model | Pull-based (queue) | Push-based (topic) |
| Consumers | One per message | Multiple subscribers |
| Persistence | Yes (up to 14 days) | No (immediate delivery) |
| Use case | Point-to-point, work queues | Fan-out, notifications |

> SQS and SNS are complementary. A common pattern combines both for buffered fan-out.

---

# Amazon EventBridge

<!-- Speaker notes: This section takes approximately 10 minutes. Cover event buses, rules, and event patterns. -->

---

## EventBridge: event-driven routing

- Serverless event bus for connecting applications using events
- Receives events from AWS services, SaaS partners, and custom apps
- Routes events to targets based on rules and event patterns

Event pattern example (EC2 state changes):

```json
{
    "source": ["aws.ec2"],
    "detail-type": ["EC2 Instance State-change Notification"],
    "detail": {
        "state": ["stopped", "terminated"]
    }
}
```

---

## EventBridge vs. SNS

| Feature | Amazon SNS | Amazon EventBridge |
|---------|-----------|-------------------|
| Model | Pub/sub (topic) | Event routing (rules) |
| Filtering | Basic attribute filtering | Rich pattern matching |
| AWS service events | Must publish explicitly | Automatic from 100+ services |
| Schema support | No | Yes (schema registry) |

> Use SNS for simple fan-out. Use EventBridge for complex routing and AWS service events.

---

# Fan-out pattern

<!-- Speaker notes: This section takes approximately 10 minutes. Draw the fan-out architecture on the whiteboard. -->

---

## SNS + SQS fan-out

```
Producer --> SNS Topic --> SQS Queue A --> Consumer A (fulfillment)
                       --> SQS Queue B --> Consumer B (analytics)
                       --> SQS Queue C --> Consumer C (notifications)
```

Benefits over direct delivery:
- **Buffering:** messages wait if a consumer is temporarily down
- **Independent rates:** each consumer processes at its own pace
- **Per-consumer error handling:** each queue has its own DLQ

> The fan-out pattern is one of the most common integration patterns on AWS.

---

## Discussion: designing an event-driven architecture

When a customer uploads a profile photo to S3, three things must happen: generate a thumbnail, update the user database, and send a confirmation email.

**How would you design this using SNS and SQS?**

<!-- Speaker notes: Expected answer: Configure S3 event notification to publish to an SNS topic on object creation. Subscribe three SQS queues to the topic (one for thumbnail generation, one for database update, one for email). Each queue feeds an independent consumer. If the thumbnail service is slow, it does not block the email or database update. Each queue can have its own DLQ for error handling. This is the classic fan-out pattern. -->

---

# Dead-letter queues

<!-- Speaker notes: This section takes approximately 10 minutes. Explain why DLQs are critical and how the redrive policy works. -->

---

## Why DLQs matter

- A "poison message" that fails repeatedly blocks the queue
- DLQ captures messages after N failed processing attempts
- Main queue continues processing other messages normally
- You inspect the DLQ to diagnose and fix failures

```bash
aws sqs set-queue-attributes \
    --queue-url https://sqs.../my-queue \
    --attributes '{
      "RedrivePolicy": "{\"deadLetterTargetArn\":\"arn:aws:sqs:...:my-dlq\",\"maxReceiveCount\":\"3\"}"
    }'
```

> Always configure a DLQ for every production SQS queue. Monitor with CloudWatch alarms.

---

# AWS Step Functions

<!-- Speaker notes: This section takes approximately 5 minutes. Brief overview of state machines and when to use them. -->

---

## Orchestrating multi-step workflows

- Coordinates multiple AWS services into workflows
- Defined as a state machine with states and transitions
- Built-in retry, error handling, and branching logic

| State Type | Purpose |
|-----------|---------|
| Task | Invoke a service (Lambda, DynamoDB, ECS) |
| Choice | Branch based on input values |
| Parallel | Run multiple branches simultaneously |
| Wait | Pause for a specified time |

> Think of Step Functions as the conductor of an orchestra, ensuring each service plays its part in the right order.

---

# Choosing the right service

<!-- Speaker notes: This section takes approximately 5 minutes. Walk through the decision table. -->

---

## Integration service decision framework

| Requirement | Service |
|-------------|---------|
| One producer, one consumer | SQS |
| One event, multiple consumers | SNS (+ SQS) |
| Route events by content | EventBridge |
| React to AWS service events | EventBridge |
| Strict message ordering | SQS FIFO |
| Coordinate multi-step workflows | Step Functions |
| Notifications to humans | SNS |

> Many architectures combine multiple services. Choose the combination that fits your requirements.

---

## Key takeaways

- Loose coupling through messaging (SQS, SNS, EventBridge) improves fault isolation, independent scaling, and deployment flexibility.
- Use SQS for point-to-point buffering; choose Standard for throughput or FIFO for strict ordering and exactly-once processing.
- Use SNS for fan-out where one event reaches multiple subscribers. Combine SNS with SQS for buffered, independent error handling per consumer.
- Use EventBridge for content-based event routing, AWS service events, and SaaS integrations with richer pattern matching than SNS.
- Always configure dead-letter queues for production messaging to capture failed messages and prevent poison messages from blocking processing.

---

## Lab preview: messaging with SQS and SNS

**Objective:** Create an SQS queue with long polling, set up an SNS topic with email subscription, build a fan-out pattern, and configure a DLQ

**Key services:** Amazon SQS, Amazon SNS, CloudWatch, IAM

**Duration:** 45 minutes

<!-- Speaker notes: Students will create a Standard queue with long polling, send and receive messages via CLI, create an SNS topic with email subscription, subscribe an SQS queue to the topic for fan-out, configure a DLQ with maxReceiveCount of 3, and set up a CloudWatch alarm on the DLQ. Remind students to confirm the email subscription. -->

---

# Questions?

Review `modules/08-messaging-and-integration/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions involve when to use SQS vs SNS and how EventBridge differs from SNS. This completes Phase 2. Transition to the lab when ready. -->
