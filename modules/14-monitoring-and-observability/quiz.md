# Module 14: Quiz

1. What are the three pillars of observability, and which AWS service primarily supports each?

2. A Lambda function processes API Gateway requests. The operations team wants to monitor the function's latency and be alerted when the worst-case response time (experienced by the slowest 1% of requests) exceeds 2 seconds. Which CloudWatch statistic should the team use for the alarm?

   A) Average, because it represents the typical response time.
   B) Maximum, because it captures the single slowest request.
   C) p99 (99th percentile), because it represents the latency experienced by the slowest 1% of requests.
   D) Sum, because it captures the total duration of all requests.

3. True or False: AWS X-Ray records every single request that passes through your application by default.

4. A company runs a microservices application with API Gateway, three Lambda functions, a DynamoDB table, and an SQS queue. Users report intermittent slow responses, but the team cannot determine which service is causing the delay. CloudWatch metrics show that overall error rates are normal. Which AWS service should the team use to identify the specific service causing the latency, and why?

   A) CloudWatch Logs, because log entries contain timing information for each service.
   B) AWS X-Ray, because it traces individual requests across all services and shows the time spent in each service, making it easy to identify the bottleneck.
   C) CloudWatch Metrics, because the `Duration` metric for each Lambda function shows which one is slow.
   D) AWS CloudTrail, because it records all API calls and their response times.

5. Which CloudWatch Logs Insights query finds the 10 most frequent error messages in a log group over the last hour?

   A) `fields @timestamp | filter @message like /ERROR/ | limit 10`
   B) `filter @message like /ERROR/ | stats count(*) as errorCount by @message | sort errorCount desc | limit 10`
   C) `display @message | search "ERROR" | top 10`
   D) `SELECT message, COUNT(*) FROM logs WHERE level = 'ERROR' GROUP BY message LIMIT 10`

6. An operations team receives 40 CloudWatch alarm notifications per day. Most are for brief CPU spikes on EC2 instances that resolve within minutes without any user impact. The team has started ignoring all alarm notifications. Which TWO changes should the team make to improve their alerting strategy? (Select TWO.)

   A) Increase the evaluation period for CPU alarms so that brief spikes do not trigger notifications (for example, require 3 consecutive periods above the threshold instead of 1).
   B) Remove the CPU alarms entirely and replace them with alarms on user-facing symptoms such as ALB 5XX error rate and response time.
   C) Decrease the alarm threshold to catch even smaller CPU spikes earlier.
   D) Send all alarm notifications to a shared email distribution list so more people see them.
   E) Disable all CloudWatch alarms and rely on manual dashboard checks instead.

7. What is the primary advantage of structured JSON logging over unstructured text logging in CloudWatch Logs?

   A) JSON logs are smaller in size and cost less to store.
   B) JSON logs are automatically parsed by CloudWatch Logs Insights, making fields searchable, filterable, and aggregatable without custom parsing.
   C) JSON logs are encrypted by default, while text logs are not.
   D) JSON logs are delivered to CloudWatch faster than text logs.

8. A solutions architect is designing a monitoring dashboard for a production web application. The application runs on ECS Fargate behind an ALB. The architect wants the dashboard to provide enough information to diagnose most production issues at a glance. Which four metrics should the architect prioritize on the dashboard, following the four golden signals framework?

   A) CPU utilization, memory utilization, disk I/O, network throughput
   B) ALB `TargetResponseTime` (latency), ALB `RequestCount` (traffic), ALB `HTTPCode_Target_5XX_Count` (errors), ECS `CPUUtilization` (saturation)
   C) Number of ECS tasks, number of ALB rules, number of target groups, number of security group rules
   D) CloudTrail event count, Config rule compliance percentage, GuardDuty finding count, Security Hub score

9. A Lambda function writes verbose debug logs in production, generating 50 GB of log data per month in CloudWatch Logs. The team wants to reduce logging costs without losing the ability to troubleshoot production issues. Which TWO approaches should the team implement? (Select TWO.)

   A) Set the Lambda function's log level to WARN or ERROR in production, so only important events are logged.
   B) Reduce the CloudWatch Logs retention period from "Never expire" to 30 days, and archive older logs to S3 if long-term retention is needed.
   C) Disable CloudWatch Logs entirely for the Lambda function.
   D) Increase the Lambda function's memory to reduce execution time and therefore reduce log volume.
   E) Switch from CloudWatch Logs to writing logs directly to DynamoDB for cheaper storage.

10. A development team enables X-Ray tracing on their API Gateway and Lambda function. The X-Ray service map shows that the Lambda function takes 200ms on average, but the DynamoDB `GetItem` subsegment takes 150ms of that time. The team wants to reduce the overall latency. Which approach should the team evaluate first?

    A) Increase the Lambda function's memory allocation to speed up the DynamoDB call.
    B) Enable DynamoDB Accelerator (DAX) to cache frequently accessed items and reduce read latency from milliseconds to microseconds.
    C) Replace DynamoDB with Amazon RDS, because relational databases are faster for single-item lookups.
    D) Increase the X-Ray sampling rate to capture more traces and get a more accurate latency measurement.

---

<details>
<summary>Answer Key</summary>

1. **The three pillars are metrics, logs, and traces. Metrics are supported by Amazon CloudWatch (numerical measurements over time). Logs are supported by Amazon CloudWatch Logs (timestamped event records). Traces are supported by AWS X-Ray (end-to-end request paths across services).**
   Metrics answer "is the system healthy?" Logs answer "what happened?" Traces answer "where is the bottleneck?" Together, they provide complete observability.
   Further reading: [What Is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html), [AWS X-Ray](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html)

2. **C) p99 (99th percentile)**
   The p99 statistic represents the latency at which 99% of requests are faster. This means 1% of requests (the slowest) exceed this value. It is the correct statistic for monitoring worst-case latency experienced by real users. Average hides outliers. Maximum captures a single extreme data point that may not be representative. Sum is the total duration, not a latency measurement.
   Further reading: [CloudWatch Statistics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Statistics-definitions.html)

3. **False.**
   X-Ray uses sampling to control costs and overhead. By default, it records the first request each second and 5% of additional requests. You can customize the sampling rate, but X-Ray does not record every request by default.
   Further reading: [X-Ray Sampling](https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html)

4. **B) AWS X-Ray**
   X-Ray traces individual requests across all services in the application (API Gateway, Lambda, DynamoDB, SQS). The service map shows the average latency at each node, and individual traces show the exact time spent in each service for a specific request. This makes it straightforward to identify which service is causing the intermittent slowness. CloudWatch Logs contain timing information but require manual correlation across multiple log groups. CloudWatch Metrics show per-service averages but do not trace individual requests. CloudTrail records API calls for auditing, not application request latency.
   Further reading: [What Is AWS X-Ray?](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html)

5. **B) `filter @message like /ERROR/ | stats count(*) as errorCount by @message | sort errorCount desc | limit 10`**
   This query filters for log entries containing "ERROR," groups them by message content, counts occurrences, sorts by frequency (descending), and returns the top 10. Option A filters and limits but does not aggregate by message. Option C uses invalid Logs Insights syntax. Option D uses SQL syntax, which is not the Logs Insights query language.
   Further reading: [CloudWatch Logs Insights Query Syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html)

6. **A, B**
   Increasing the evaluation period (A) prevents brief, self-resolving CPU spikes from triggering alarms. Requiring 3 consecutive periods above the threshold means only sustained issues generate notifications. Replacing CPU alarms with symptom-based alarms (B) focuses alerts on user impact (errors, latency) rather than infrastructure metrics that may not indicate a problem. Decreasing the threshold (C) would generate even more alerts. Sending to more people (D) spreads the noise without reducing it. Disabling all alarms (E) eliminates monitoring entirely.
   Further reading: [Using Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Alarms.html)

7. **B) JSON logs are automatically parsed by CloudWatch Logs Insights**
   CloudWatch Logs Insights automatically discovers and indexes fields in JSON-formatted log entries. This means you can filter by specific fields (`level`, `requestId`, `error`), aggregate values (`stats avg(durationMs)`), and search for specific patterns without writing custom parsing logic. Unstructured text logs require regex-based parsing, which is slower and more error-prone. JSON logs are not inherently smaller, encrypted differently, or delivered faster than text logs.
   Further reading: [CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html)

8. **B) ALB TargetResponseTime, ALB RequestCount, ALB HTTPCode_Target_5XX_Count, ECS CPUUtilization**
   These four metrics map directly to the four golden signals: latency (TargetResponseTime), traffic (RequestCount), errors (5XX count), and saturation (CPUUtilization). Together, they provide a comprehensive view of the application's health. Option A focuses only on infrastructure metrics (saturation) without covering latency, traffic, or errors. Option C tracks configuration counts, not operational health. Option D tracks security posture, not application performance.
   Further reading: [CloudWatch Dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)

9. **A, B**
   Setting the log level to WARN or ERROR (A) eliminates verbose debug output in production, significantly reducing log volume. Most troubleshooting requires only error and warning messages; debug logs are for development. Reducing the retention period to 30 days (B) automatically deletes old logs, reducing storage costs. For compliance or long-term analysis, archive logs to S3 (which is much cheaper than CloudWatch Logs storage). Disabling logs entirely (C) eliminates troubleshooting capability. Increasing memory (D) may reduce execution time slightly but does not reduce the volume of log statements. Writing to DynamoDB (E) is more expensive and less practical than CloudWatch Logs for log storage.
   Further reading: [Working with Log Groups](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html)

10. **B) Enable DynamoDB Accelerator (DAX)**
    The X-Ray trace shows that 150ms of the 200ms total is spent in the DynamoDB GetItem call. DAX is an in-memory cache for DynamoDB that reduces read latency from single-digit milliseconds to microseconds for cached items. If the function reads the same items frequently, DAX would dramatically reduce the DynamoDB portion of the latency. Increasing Lambda memory (A) provides more CPU but does not speed up network calls to DynamoDB. Replacing DynamoDB with RDS (C) would likely increase latency, not decrease it, for single-item lookups. Increasing the sampling rate (D) improves trace coverage but does not reduce latency.
    Further reading: [DynamoDB Accelerator (DAX)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.html)

</details>

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
