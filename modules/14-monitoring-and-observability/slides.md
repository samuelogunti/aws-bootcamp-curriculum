---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 14: Monitoring and Observability'
---

# Module 14: Monitoring and Observability

**Phase 4: Production Readiness**
Estimated lecture time: 75 to 90 minutes

<!-- Speaker notes: Welcome to Module 14. This module teaches students how to collect, analyze, and act on operational data from the infrastructure they built in previous modules. Breakdown: 10 min three pillars, 15 min CloudWatch metrics, 10 min alarms, 15 min CloudWatch Logs, 10 min X-Ray, 10 min golden signals and dashboards, 10 min alerting best practices, 5 min wrap-up. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Analyze the three pillars of observability (metrics, logs, traces)
- Assess CloudWatch metrics and recommend configurations for EC2, Lambda, ECS, RDS, ALB
- Evaluate CloudWatch alarm types (static, anomaly detection, composite)
- Analyze logs using CloudWatch Logs Insights queries
- Assess X-Ray traces to identify bottlenecks across multi-service architectures
- Recommend dashboard designs surfacing the four golden signals
- Evaluate alerting strategies that minimize alert fatigue
- Optimize logging costs with retention policies and structured logging

---

## Prerequisites and agenda

**Prerequisites:** Modules 04 (EC2), 07 (ALB), 09 (Lambda), 10 (ECS), 13 (Security)

**Agenda:**
1. The three pillars of observability
2. Amazon CloudWatch metrics (built-in and custom)
3. CloudWatch alarms
4. CloudWatch Logs and Logs Insights
5. AWS X-Ray: distributed tracing
6. The four golden signals
7. CloudWatch dashboards
8. Alerting best practices

---

# The three pillars of observability

<!-- Speaker notes: This section takes about 10 minutes. Use the car analogy: metrics are dashboard gauges, logs are the mechanic's diagnostic report, traces are GPS tracking a delivery route. -->

---

## Metrics, logs, and traces

| Pillar | What It Captures | Question It Answers | AWS Service |
|--------|-----------------|--------------------|----|
| Metrics | Numerical measurements over time | "Is the system healthy right now?" | CloudWatch |
| Logs | Timestamped records of discrete events | "What happened, and in what order?" | CloudWatch Logs |
| Traces | End-to-end request paths across services | "Where is the bottleneck?" | AWS X-Ray |

- Metrics tell you something is wrong (error rate spiked)
- Logs tell you what went wrong (a specific exception)
- Traces tell you where it went wrong (which service is slow)

---

# Amazon CloudWatch metrics

<!-- Speaker notes: This section takes about 15 minutes. Show a real CloudWatch dashboard if possible. Walk through each widget. -->

---

## Built-in metrics by service

| Service | Key Metrics | Default Period |
|---------|------------|----------------|
| EC2 | `CPUUtilization`, `NetworkIn/Out`, `StatusCheckFailed` | 5 min (basic) |
| Lambda | `Invocations`, `Duration`, `Errors`, `Throttles` | 1 min |
| ALB | `RequestCount`, `TargetResponseTime`, `HTTPCode_5XX` | 1 min |
| RDS | `CPUUtilization`, `FreeableMemory`, `DatabaseConnections` | 1 min |
| ECS | `CPUUtilization`, `MemoryUtilization` | 1 min |

---

## Custom metrics

```bash
aws cloudwatch put-metric-data \
  --namespace "BootcampApp" \
  --metric-name "OrdersProcessed" \
  --dimensions "Environment=production,Service=order-api" \
  --value 42 \
  --unit Count
```

- Use custom metrics for application-specific measurements
- Dimensions segment data by environment, service, or instance
- Use percentiles (p95, p99) for latency, not averages

---

## Discussion: averages vs. percentiles for latency

Your API has an average latency of 100ms. A customer reports that the API is "sometimes very slow." You check the p99 latency and find it is 5 seconds.

**Why does the average hide this problem? What metric should you alert on?**

<!-- Speaker notes: Expected answer: Averages are dominated by the majority of fast requests. If 99% of requests take 50ms and 1% take 5 seconds, the average is ~100ms but 1% of users have a terrible experience. Alert on p95 or p99 latency to catch tail latency issues. This is why the four golden signals recommend percentiles for latency. -->

---

# CloudWatch alarms

<!-- Speaker notes: This section takes about 10 minutes. Cover the three alarm types and alarm actions. -->

---

## Alarm types

| Type | How It Works | Best For |
|------|-------------|----------|
| Static threshold | Triggers when metric exceeds a fixed value | Known thresholds (error count > 10) |
| Anomaly detection | ML-based baseline; triggers on deviation | Metrics with time-of-day patterns |
| Composite | Combines alarms with AND/OR/NOT logic | Reducing noise (require multiple conditions) |

---

## Alarm actions

| Action | Use Case |
|--------|----------|
| SNS notification | Email or SMS alert to the ops team |
| Auto Scaling policy | Scale out EC2 or ECS tasks |
| Systems Manager Automation | Auto-remediate (restart service, clear cache) |
| EC2 actions | Stop, terminate, or reboot an instance |

> Alert on symptoms (high error rate, high latency), not causes (high CPU). High CPU during a batch job is expected.

---

# CloudWatch Logs

<!-- Speaker notes: This section takes about 15 minutes. Pause after Logs Insights for a hands-on exercise. Give students a sample query to modify. -->

---

## Structured logging

Unstructured (hard to query):
```
2026-04-15 10:30:45 ERROR Failed to process order 12345
```

Structured JSON (easy to query):
```json
{
  "timestamp": "2026-04-15T10:30:45Z",
  "level": "ERROR",
  "message": "Failed to process order",
  "orderId": "12345",
  "service": "order-processor"
}
```

- JSON logs are machine-parseable and searchable in Logs Insights
- Include: timestamp, level, message, service, requestId

---

## CloudWatch Logs Insights queries

Find the top 10 most frequent errors:

```
fields @timestamp, @message
| filter @message like /ERROR/
| stats count(*) as errorCount by @message
| sort errorCount desc
| limit 10
```

Calculate p95 latency for a Lambda function:

```
filter @type = "REPORT"
| stats avg(@duration) as avgDuration,
        pct(@duration, 95) as p95Duration
  by bin(5m)
```

---

## Log retention and cost

| Retention Period | Use Case |
|-----------------|----------|
| 1 to 7 days | Development and testing environments |
| 30 days | Production app logs (most troubleshooting is within 30 days) |
| 90 to 365 days | Compliance or audit logs |
| Never expire | Consider archiving to S3 instead for lower cost |

> For long-term retention, stream logs to S3. S3 storage is significantly cheaper than CloudWatch Logs storage.

---

## Quick check: which log retention?

Your Lambda function processes payment transactions. Regulations require you to retain transaction logs for one year. The function generates 10 GB of logs per month.

**What retention strategy minimizes cost while meeting compliance?**

<!-- Speaker notes: Answer: Set CloudWatch Logs retention to 30 days for operational troubleshooting. Configure a subscription filter to stream logs to S3 for long-term retention (1 year). S3 Standard-IA or Glacier is much cheaper than CloudWatch Logs storage for data you rarely query. Use Athena for ad-hoc queries on the archived logs. -->

---

# AWS X-Ray: distributed tracing

<!-- Speaker notes: This section takes about 10 minutes. Show the trace diagram to illustrate how a request flows through multiple services. -->

---

## How X-Ray works

```
Client request
    |
    v
API Gateway (segment: 15ms)
    |
    v
Lambda function (segment: 200ms)
    |
    +--> DynamoDB GetItem (subsegment: 8ms)
    +--> S3 PutObject (subsegment: 45ms)
    +--> SNS Publish (subsegment: 12ms)
```

- Each service records a segment; X-Ray assembles the full trace
- The service map shows latency, error rates, and request volume
- X-Ray uses sampling to control costs (configurable rate)

---

## Enabling X-Ray

| Service | How to Enable | What It Traces |
|---------|--------------|----------------|
| Lambda | Enable "Active tracing" in config | Invocation, SDK calls to AWS services |
| API Gateway | Enable tracing on the stage | Request routing, integration latency |
| ECS | Add X-Ray daemon as a sidecar | Application requests, SDK calls |
| ALB | Automatically propagates trace headers | Request routing to targets |

> For Lambda, enabling active tracing is a single configuration change. No code changes needed for AWS SDK calls.

---

# The four golden signals

<!-- Speaker notes: This section takes about 5 minutes. These are the minimum metrics for any user-facing system. -->

---

## Latency, traffic, errors, saturation

| Signal | What It Measures | CloudWatch Examples |
|--------|-----------------|---------------------|
| Latency | Time to serve a request | ALB `TargetResponseTime`, Lambda `Duration` |
| Traffic | Volume of requests | ALB `RequestCount`, Lambda `Invocations` |
| Errors | Rate of failed requests | ALB `HTTPCode_5XX`, Lambda `Errors` |
| Saturation | How full a resource is | EC2 `CPUUtilization`, RDS `DatabaseConnections` |

- Check signals in order: errors, latency, traffic, saturation
- This systematic approach leads to faster diagnosis

---

## Discussion: designing a monitoring dashboard

You operate a serverless API (API Gateway, Lambda, DynamoDB) serving 1,000 requests per minute. You need a CloudWatch dashboard for the operations team.

**Which widgets would you include to cover all four golden signals?**

<!-- Speaker notes: Expected answer: Latency: Lambda Duration p95/p99 and API Gateway latency. Traffic: API Gateway RequestCount. Errors: Lambda Errors count and API Gateway 5XX count. Saturation: Lambda ConcurrentExecutions (vs. account limit), DynamoDB ConsumedReadCapacityUnits. Also include an alarm status widget at the top for at-a-glance health. -->

---

## Alerting best practices

| Principle | Description |
|-----------|-------------|
| Alert on symptoms, not causes | High error rate, not CPU spike |
| Every alert must be actionable | If no action possible, remove the alert |
| Use severity levels | Critical (page), Warning (ticket), Info (dashboard) |
| Avoid alert fatigue | Review frequency monthly; tune or remove noisy alerts |
| Include context | What is wrong, which resource, dashboard link, runbook link |

> Start with 5 high-signal alerts the team trusts, not 50 alerts everyone ignores.

---

## Key takeaways

- Observability rests on three pillars: metrics (is it healthy?), logs (what happened?), traces (where is the bottleneck?). Use all three together.
- CloudWatch provides built-in metrics for most services; publish custom metrics for app-specific measurements; use percentiles, not averages, for latency
- Configure alarms on symptoms (error rate, latency), not causes (CPU); use composite alarms to reduce noise
- Use structured JSON logging so Logs Insights can parse, filter, and aggregate efficiently
- X-Ray traces requests across service boundaries; enable it on Lambda, API Gateway, and ECS for multi-service architectures

---

## Lab preview: monitoring a serverless application

**What you will do:**
- Deploy a Lambda function with structured JSON logging
- Create CloudWatch alarms on error rate and latency
- Write Logs Insights queries to find error patterns
- Enable X-Ray tracing and analyze traces
- Build a CloudWatch dashboard with the four golden signals

**Duration:** 60 minutes
**Key services:** CloudWatch, CloudWatch Logs, X-Ray, Lambda

<!-- Speaker notes: The lab has 3 guided steps and 3 semi-guided steps. Prepare a Lambda function that occasionally throws errors so students have real data to query. Remind students to set log retention policies to avoid ongoing charges. -->

---

# Questions?

Review `modules/14-monitoring-and-observability/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions: "CloudWatch Logs vs. CloudTrail?" (Logs stores app-generated data; CloudTrail records AWS API calls.) "Do I need X-Ray if I have metrics and logs?" (X-Ray is essential for multi-service architectures where you need to trace a request across boundaries.) -->
