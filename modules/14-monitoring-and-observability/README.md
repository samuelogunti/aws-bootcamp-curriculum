# Module 14: Monitoring and Observability

## Learning Objectives

By the end of this module, you will be able to:

- Analyze the three pillars of observability (metrics, logs, traces) and evaluate how each contributes to diagnosing application issues on AWS
- Assess Amazon CloudWatch metrics (built-in and custom) and recommend appropriate metric configurations for monitoring EC2, Lambda, ECS, RDS, and ALB workloads
- Evaluate CloudWatch alarm configurations by comparing static threshold alarms, anomaly detection alarms, and composite alarms, and recommend the appropriate type for a given monitoring scenario
- Analyze application logs using CloudWatch Logs Insights queries to identify error patterns, latency outliers, and operational trends
- Assess distributed tracing data from AWS X-Ray to identify performance bottlenecks and failure points across multi-service architectures
- Recommend a CloudWatch dashboard design that surfaces the four golden signals (latency, traffic, errors, saturation) for a production workload
- Evaluate alerting strategies and justify alert configurations that minimize alert fatigue while ensuring critical issues are detected promptly
- Optimize logging costs by recommending appropriate log retention policies, log class selections, and structured logging formats

## Prerequisites

- Completion of [Module 04: Compute with Amazon EC2](../04-compute-ec2/README.md) (EC2 instances that generate CloudWatch metrics you will monitor)
- Completion of [Module 07: Load Balancing and DNS](../07-load-balancing-and-dns/README.md) (ALB metrics such as request count, latency, and HTTP error codes)
- Completion of [Module 09: Serverless Computing with AWS Lambda](../09-serverless-lambda/README.md) (Lambda functions that generate invocation, duration, and error metrics, and produce CloudWatch Logs)
- Completion of [Module 10: Containers and Amazon ECS](../10-containers-ecs/README.md) (ECS services with CPU and memory utilization metrics)
- Completion of [Module 13: Security in Depth](../13-security-in-depth/README.md) (CloudTrail and GuardDuty that feed into the monitoring ecosystem)
- Familiarity with all prior modules, as this module monitors infrastructure built throughout the bootcamp

## Concepts

### The Three Pillars of Observability

Observability is the ability to understand the internal state of a system by examining its external outputs. On AWS, observability rests on three pillars: metrics, logs, and traces. Each pillar answers a different question about your application's behavior.

| Pillar | What It Captures | Question It Answers | AWS Service |
|--------|-----------------|--------------------|----|
| Metrics | Numerical measurements over time (CPU utilization, request count, error rate) | "Is the system healthy right now?" | [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) |
| Logs | Timestamped records of discrete events (error messages, request details, state changes) | "What happened, and in what order?" | [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) |
| Traces | End-to-end request paths across multiple services | "Where is the bottleneck in this request?" | [AWS X-Ray](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html) |

Metrics tell you something is wrong (error rate spiked). Logs tell you what went wrong (a specific exception in a specific function). Traces tell you where it went wrong (the database call in the third microservice took 5 seconds instead of 50 milliseconds). Together, they provide a complete picture.

In previous modules, you built infrastructure (EC2, Lambda, ECS, RDS, ALB) that generates data across all three pillars. This module teaches you how to collect, analyze, and act on that data.

> **Tip:** Start with metrics for high-level health monitoring, drill into logs when you need to understand specific events, and use traces when you need to follow a request across service boundaries. This top-down approach is the most efficient way to diagnose issues.

### Amazon CloudWatch Metrics

[Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) is the metrics backbone on AWS. It collects time-series data points from AWS services and your applications, stores them, and makes them available for visualization and alarming. A metric is simply a named series of measurements over time (for example, the CPU percentage of an EC2 instance sampled every 5 minutes).

#### Built-in Metrics

AWS services automatically publish metrics to CloudWatch at no additional cost. These built-in metrics cover the most common monitoring needs:

| Service | Key Metrics | Default Period |
|---------|------------|----------------|
| EC2 | `CPUUtilization`, `NetworkIn`, `NetworkOut`, `StatusCheckFailed` | 5 minutes (basic), 1 minute (detailed) |
| Lambda | `Invocations`, `Duration`, `Errors`, `Throttles`, `ConcurrentExecutions` | 1 minute |
| ALB | `RequestCount`, `TargetResponseTime`, `HTTPCode_Target_5XX_Count`, `HealthyHostCount` | 1 minute |
| RDS | `CPUUtilization`, `FreeableMemory`, `ReadIOPS`, `WriteIOPS`, `DatabaseConnections` | 1 minute |
| ECS (Fargate) | `CPUUtilization`, `MemoryUtilization` (at service and task level) | 1 minute |
| SQS | `ApproximateNumberOfMessagesVisible`, `ApproximateAgeOfOldestMessage` | 5 minutes |

#### Custom Metrics

When built-in metrics do not cover your monitoring needs, you can publish [custom metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html) to CloudWatch. Custom metrics let you track application-specific measurements such as the number of orders processed, cache hit ratio, or queue processing latency.

You publish custom metrics using the `PutMetricData` API or the CloudWatch agent. Each custom metric has a namespace (a grouping name), a metric name, dimensions (key-value pairs that identify the metric source), and a value.

```bash
aws cloudwatch put-metric-data \
  --namespace "BootcampApp" \
  --metric-name "OrdersProcessed" \
  --dimensions "Environment=production,Service=order-api" \
  --value 42 \
  --unit Count \
  --region us-east-1
```

> **Tip:** Use metric dimensions to segment data by environment, service, or instance. This lets you filter and aggregate metrics in dashboards and alarms. For example, you can create a single `OrdersProcessed` metric with dimensions for `Environment=production` and `Environment=staging` instead of creating separate metrics for each environment.

#### Metric Math and Statistics

CloudWatch supports [metric math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html) expressions that combine multiple metrics into a single derived metric. For example, you can calculate the error rate as a percentage:

```
error_rate = (errors / invocations) * 100
```

CloudWatch also provides statistics for each metric: Average, Sum, Minimum, Maximum, SampleCount, and percentiles (p50, p90, p95, p99). Percentiles are especially important for latency monitoring because averages hide outliers. A p99 latency of 2 seconds means that 99% of requests complete in under 2 seconds, but 1% take longer.

### CloudWatch Alarms

[CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Alarms.html) monitor a metric and fire when it crosses a boundary you define. Each alarm lives in one of three states: OK (metric is within bounds), ALARM (metric has breached the threshold), or INSUFFICIENT_DATA (not enough data points to evaluate yet).

#### Static Threshold Alarms

A static threshold alarm triggers when a metric exceeds (or falls below) a fixed value for a specified number of evaluation periods. For example: "Trigger an alarm when the ALB 5XX error count exceeds 10 for 3 consecutive 1-minute periods."

#### Anomaly Detection Alarms

[Anomaly detection alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Anomaly_Detection.html) let CloudWatch learn what "normal" looks like for a metric and alert you when behavior deviates from that baseline. This is valuable for metrics with daily or weekly patterns (like traffic that peaks during business hours). Instead of guessing a fixed threshold, you set a sensitivity band and let the model flag outliers.

#### Composite Alarms

A composite alarm combines multiple alarms using Boolean logic (AND, OR, NOT). For example: "Trigger only when both the error rate alarm AND the latency alarm are in ALARM state." Composite alarms reduce alert noise by requiring multiple conditions to be true before triggering a notification.

#### Alarm Actions

When an alarm enters the ALARM state, it can trigger actions:

| Action | Use Case |
|--------|----------|
| Send an SNS notification | Email or SMS alert to the operations team |
| Trigger an Auto Scaling policy | Scale out EC2 or ECS tasks in response to high load |
| Execute a Systems Manager Automation | Automatically remediate the issue (restart a service, clear a cache) |
| Stop, terminate, or reboot an EC2 instance | Respond to instance health check failures |

> **Tip:** Configure alarms to trigger on symptoms (high error rate, high latency) rather than causes (high CPU). High CPU is not always a problem; it might mean the instance is efficiently processing a batch job. A high error rate, on the other hand, always indicates a problem that affects users.

### CloudWatch Logs

[Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) is where your application's event records live. It organizes data into log groups (a collection of streams sharing the same retention and access settings) and log streams (a sequence of events from one source, like a single Lambda invocation or container).

#### Log Sources

AWS services automatically send logs to CloudWatch Logs:

| Service | Log Content | Log Group Naming |
|---------|------------|-----------------|
| Lambda | Function output (print/console.log statements, exceptions) | `/aws/lambda/<function-name>` |
| API Gateway | Access logs (request/response details) | Custom name |
| ECS (Fargate) | Container stdout/stderr | `/ecs/<task-definition-name>` |
| VPC Flow Logs | Network traffic records | Custom name |
| CloudTrail | API call records (when configured to deliver to CloudWatch Logs) | Custom name |

#### Structured Logging

Structured logging means writing log entries as JSON objects instead of unformatted text strings. JSON logs are machine-parseable, which makes them searchable and filterable in CloudWatch Logs Insights.

Unstructured log (hard to query):
```
2026-04-15 10:30:45 ERROR Failed to process order 12345: DynamoDB ConditionalCheckFailedException
```

Structured log (easy to query):
```json
{
  "timestamp": "2026-04-15T10:30:45Z",
  "level": "ERROR",
  "message": "Failed to process order",
  "orderId": "12345",
  "error": "ConditionalCheckFailedException",
  "service": "order-processor",
  "requestId": "abc-123-def"
}
```

With structured logs, you can query for all errors related to a specific order, a specific error type, or a specific service using CloudWatch Logs Insights.

#### CloudWatch Logs Insights

[CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) gives you a SQL-like [query language](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html) for searching and aggregating log data directly in the console. You can filter by field values, compute statistics, and visualize trends without exporting logs to a separate analytics tool.

Example: Find the top 10 most frequent error messages in the last hour:

```
fields @timestamp, @message
| filter @message like /ERROR/
| stats count(*) as errorCount by @message
| sort errorCount desc
| limit 10
```

Example: Calculate the p95 latency for a Lambda function:

```
filter @type = "REPORT"
| stats avg(@duration) as avgDuration,
        pct(@duration, 95) as p95Duration,
        max(@duration) as maxDuration
  by bin(5m)
```

#### Log Retention and Cost

CloudWatch Logs charges for data ingestion and storage. By default, log groups retain data indefinitely, which can become expensive over time. Set a [retention policy](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html) on each log group to automatically delete old logs:

| Retention Period | Use Case |
|-----------------|----------|
| 1 to 7 days | Development and testing environments |
| 30 days | Production application logs (most troubleshooting happens within 30 days) |
| 90 to 365 days | Compliance or audit logs that must be retained longer |
| Never expire | Logs that must be retained indefinitely (consider archiving to S3 instead) |

> **Tip:** For long-term log retention at lower cost, configure a CloudWatch Logs subscription to stream logs to Amazon S3. S3 storage is significantly cheaper than CloudWatch Logs storage for data you rarely query.

### AWS X-Ray: Distributed Tracing

[AWS X-Ray](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html) follows a request from the moment it enters your system (through API Gateway or an ALB) all the way through backend services (Lambda, ECS, EC2) and into downstream dependencies (DynamoDB, RDS, SQS, external APIs). Each trace shows the full journey of a single request, including how long each hop took and where errors occurred.

#### How X-Ray Works

When you enable X-Ray tracing on a service, the X-Ray SDK (or the service's built-in integration) generates a trace ID for each incoming request. As the request passes through each service, the trace ID is propagated in HTTP headers. Each service records a segment (its portion of the request) and sends it to X-Ray. X-Ray assembles the segments into a complete trace.

```
Client request
    |
    v
API Gateway (segment: 15ms)
    |
    v
Lambda function (segment: 200ms)
    |
    ├── DynamoDB GetItem (subsegment: 8ms)
    ├── S3 PutObject (subsegment: 45ms)
    └── SNS Publish (subsegment: 12ms)
```

#### Service Map

The [X-Ray service map](https://docs.aws.amazon.com/xray/latest/devguide/xray-console-servicemap.html) is a visual representation of your application's architecture, generated automatically from trace data. Each node represents a service, and the edges show the connections between services. The map displays:

- Average latency for each service
- Error rates and fault rates
- Request volume (thickness of the edges)
- Health status (green for healthy, yellow for errors, red for faults)

The service map is the fastest way to identify which service in a multi-service architecture is causing performance problems.

#### Enabling X-Ray

X-Ray integrates with several AWS services:

| Service | How to Enable | What It Traces |
|---------|--------------|----------------|
| Lambda | Enable "Active tracing" in the function configuration | Function invocation, SDK calls to AWS services |
| API Gateway | Enable tracing on the stage | Request routing, integration latency |
| ECS | Add the X-Ray daemon as a sidecar container | Application requests, SDK calls |
| ALB | Automatically propagates trace headers | Request routing to targets |

For [Lambda](https://docs.aws.amazon.com/lambda/latest/dg/services-xray.html), enabling active tracing is a single configuration change. Lambda automatically instruments the AWS SDK calls your function makes, so you see subsegments for DynamoDB, S3, SQS, and other service calls without writing any tracing code.

> **Tip:** X-Ray uses sampling to control costs. By default, it records the first request each second and 5% of additional requests. You can customize the sampling rate based on your needs. For debugging a specific issue, temporarily increase the sampling rate to capture more traces.

### The Four Golden Signals

The four golden signals, defined by Google's Site Reliability Engineering (SRE) book, are the most important metrics for monitoring any user-facing system:

| Signal | What It Measures | CloudWatch Metric Examples |
|--------|-----------------|---------------------------|
| Latency | Time to serve a request | ALB `TargetResponseTime`, Lambda `Duration`, custom p95/p99 metrics |
| Traffic | Volume of requests | ALB `RequestCount`, Lambda `Invocations`, API Gateway `Count` |
| Errors | Rate of failed requests | ALB `HTTPCode_Target_5XX_Count`, Lambda `Errors`, custom error count |
| Saturation | How full a resource is | EC2 `CPUUtilization`, RDS `DatabaseConnections`, ECS `MemoryUtilization` |

A well-designed monitoring dashboard surfaces all four signals for each critical service. When an incident occurs, you check the signals in order: Are errors elevated? Is latency high? Has traffic changed? Are resources saturated? This systematic approach leads to faster diagnosis.

> **Tip:** Use percentiles (p95, p99) for latency instead of averages. An average latency of 100ms might hide the fact that 1% of requests take 5 seconds. The p99 latency reveals the worst-case experience for your users.

### CloudWatch Dashboards

[CloudWatch dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) are customizable pages in the CloudWatch console that display metrics, alarms, and logs in a single view. Dashboards use [widgets](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-and-work-with-widgets.html) to visualize data: line graphs for time-series metrics, number widgets for current values, alarm status widgets for health at a glance, and log widgets for recent log entries.

#### Dashboard Design Principles

A production dashboard should follow these principles:

1. **Organize by service or workflow.** Group related metrics together (for example, all ALB metrics in one row, all Lambda metrics in another).
2. **Show the four golden signals.** Every dashboard should include latency, traffic, errors, and saturation for the monitored service.
3. **Use consistent time ranges.** Set all widgets to the same time range so you can correlate events across metrics.
4. **Include alarm status.** Add an alarm status widget at the top of the dashboard so you can see at a glance whether any alarms are firing.
5. **Link to related dashboards.** Use text widgets with links to drill-down dashboards for specific services.

#### Cross-Account and Cross-Region Dashboards

CloudWatch supports [cross-account observability](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Unified-Cross-Account.html), which lets you create dashboards that display metrics from multiple AWS accounts and Regions in a single view. This is essential for organizations that use multi-account architectures.

### Alerting Best Practices

Effective alerting is the bridge between monitoring and incident response. Poorly configured alerts create noise (too many false positives) or silence (missing real incidents). Follow these principles:

| Principle | Description |
|-----------|-------------|
| Alert on symptoms, not causes | Alert when users are affected (high error rate, high latency), not when a resource metric changes (CPU spike). A CPU spike during a batch job is expected; a 5XX error spike is always a problem. |
| Every alert must be actionable | If the on-call engineer cannot take a specific action in response to an alert, the alert should not exist. Remove or downgrade non-actionable alerts to dashboard-only metrics. |
| Use severity levels | Critical alerts page the on-call engineer immediately. Warning alerts create a ticket for next-business-day review. Info alerts appear on dashboards only. |
| Avoid alert fatigue | Too many alerts desensitize the team. Review alert frequency monthly and tune thresholds or remove alerts that fire frequently without indicating real problems. |
| Include context in notifications | Alert messages should include: what is wrong, which resource is affected, a link to the relevant dashboard, and a link to the runbook for remediation. |

> **Tip:** Start with a small number of high-signal alerts (error rate, latency p99, health check failures) and add more only when you identify gaps. It is better to have 5 alerts that the team trusts and acts on than 50 alerts that everyone ignores.

## Instructor Notes

**Estimated lecture time:** 75 to 90 minutes

**Common student questions:**

- Q: What is the difference between CloudWatch Logs and CloudTrail?
  A: CloudWatch Logs stores application-generated log data (Lambda function output, container stdout, custom application logs). CloudTrail records AWS API calls (who called which API, when, from where). They serve different purposes: CloudWatch Logs helps you debug application behavior, while CloudTrail helps you audit AWS account activity. In [Module 13](../13-security-in-depth/README.md), you learned about CloudTrail for security auditing. This module focuses on CloudWatch Logs for operational monitoring.

- Q: Do I need X-Ray if I already have CloudWatch metrics and logs?
  A: Metrics and logs are sufficient for monitoring single-service applications. X-Ray becomes essential when your application spans multiple services (API Gateway, Lambda, DynamoDB, SQS, another Lambda). Without tracing, you can see that a request failed, but you cannot easily determine which service in the chain caused the failure. X-Ray shows the complete request path and pinpoints the bottleneck.

- Q: How much does CloudWatch cost? Can monitoring become expensive?
  A: CloudWatch pricing has several components: metrics (free for built-in, charged for custom and high-resolution), alarms (per alarm per month), logs (per GB ingested and stored), and dashboards (per dashboard per month). The most common cost driver is log ingestion. Lambda functions that log verbose output at high invocation rates can generate significant log volume. Control costs by setting appropriate log retention periods, using log-level filtering (log only WARN and ERROR in production), and archiving old logs to S3.

- Q: What are the "four golden signals" and why are they important?
  A: The four golden signals (latency, traffic, errors, saturation) are the minimum set of metrics you need to monitor any user-facing service. They come from Google's Site Reliability Engineering practices. If you monitor only these four signals, you will catch most production issues. Latency tells you if the service is slow. Traffic tells you if demand has changed. Errors tell you if requests are failing. Saturation tells you if resources are running out.

**Teaching tips:**

- Start the lecture by showing a real CloudWatch dashboard (create one in advance with sample data). Walk through each widget and explain what it shows. This gives students a concrete visual before diving into concepts.
- When explaining the three pillars, use an analogy: metrics are like the dashboard gauges in a car (speed, fuel, temperature), logs are like the mechanic's diagnostic report (detailed event records), and traces are like GPS tracking a delivery truck's route (the path through multiple stops).
- Pause after the CloudWatch Logs Insights section for a hands-on exercise. Give students a sample Logs Insights query and ask them to modify it to answer a different question (for example, "find all requests with latency over 1 second").
- The alerting best practices section is a good discussion topic. Ask students: "You receive 50 alerts per day. How do you decide which ones to keep and which to remove?" This leads to a practical conversation about alert fatigue and signal-to-noise ratio.
- Emphasize that monitoring is not a one-time setup. Dashboards and alerts must evolve as the application changes. Encourage students to review their monitoring configuration as part of every deployment.

## Key Takeaways

- Observability rests on three pillars: metrics (is the system healthy?), logs (what happened?), and traces (where is the bottleneck?). Use all three together for complete visibility.
- CloudWatch provides built-in metrics for most AWS services; publish custom metrics for application-specific measurements and use percentiles (p95, p99) instead of averages for latency.
- Configure CloudWatch alarms on symptoms (error rate, latency) rather than causes (CPU utilization), and use composite alarms to reduce alert noise.
- Use structured JSON logging so that CloudWatch Logs Insights can parse, filter, and aggregate your log data efficiently.
- AWS X-Ray traces requests across service boundaries; enable it on Lambda, API Gateway, and ECS to identify performance bottlenecks in multi-service architectures.
