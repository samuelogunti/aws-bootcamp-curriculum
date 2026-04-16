# Logging & Observability Best Practices

## Structured Logging
- Use JSON-formatted logs for machine parseability
- Include consistent fields in every log entry: `timestamp`, `level`, `message`, `service`, `requestId`
- Use correlation IDs to trace requests across services
- Propagate correlation IDs through HTTP headers (`X-Request-Id` or `X-Correlation-Id`)

## Log Levels
- **ERROR**: Something failed and needs attention (unhandled exceptions, failed external calls)
- **WARN**: Something unexpected happened but the system recovered (retries, fallbacks)
- **INFO**: Significant business events (user created, order placed, deployment started)
- **DEBUG**: Detailed diagnostic info (request/response payloads, internal state) — disabled in production

## What to Log
- Request entry and exit (method, path, status code, duration)
- Authentication and authorization decisions
- External service calls (target, duration, success/failure)
- Business-critical events and state transitions
- Errors with full context (input, stack trace, affected resource)

## What NOT to Log
- Passwords, tokens, API keys, or secrets
- Full credit card numbers or SSNs
- Personal health information
- Raw request bodies containing PII — redact or mask sensitive fields

## Metrics
- Track the four golden signals: latency, traffic, errors, saturation
- Use percentiles (p50, p95, p99) for latency — averages hide outliers
- Set up CloudWatch custom metrics for business KPIs
- Alert on anomalies, not just thresholds

## Distributed Tracing
- Instrument all service boundaries with tracing (AWS X-Ray, OpenTelemetry)
- Include trace IDs in log entries for cross-referencing
- Trace external calls (HTTP, database, cache, queue)
- Sample traces in high-traffic environments to control costs

## Alerting
- Alert on symptoms (high error rate), not causes (CPU spike)
- Avoid alert fatigue — every alert should be actionable
- Include runbook links in alert notifications
- Use severity levels: critical (page), warning (ticket), info (dashboard)
