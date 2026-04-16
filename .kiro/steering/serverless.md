# Serverless Best Practices

## Function Design
- Keep functions small and single-purpose
- Separate business logic from handler code for testability
- Set memory and timeout appropriately — over-provisioning wastes money, under-provisioning causes failures
- Default timeout: 30s for API-backed functions, up to 15min for async processing
- Use ARM64 (Graviton) runtime for better price-performance

## Cold Starts
- Minimize package size — bundle only what's needed
- Use provisioned concurrency for latency-sensitive endpoints
- Avoid heavy initialization in the handler — move it outside the handler for connection reuse
- Prefer lightweight runtimes (Node.js, Python) for cold-start-sensitive paths

## Idempotency
- Design all functions to be idempotent — they may be invoked more than once
- Use idempotency keys for write operations
- Store processing state in DynamoDB or similar for deduplication

## Event-Driven Patterns
- Use SQS for decoupling and buffering between services
- Use SNS for fan-out to multiple consumers
- Use EventBridge for event routing with filtering rules
- Use Step Functions for orchestrating multi-step workflows
- Set up dead-letter queues (DLQs) for failed event processing

## Error Handling
- Configure DLQs or on-failure destinations for async invocations
- Implement structured error responses for synchronous invocations
- Use CloudWatch alarms on error metrics and DLQ depth
- Log enough context to reproduce failures

## Deployment
- Use SAM or CDK for defining serverless infrastructure
- Deploy with `sam build && sam deploy` or `cdk deploy`
- Use Lambda aliases and versions for safe deployments
- Implement canary or linear deployment preferences
- Test locally with `sam local invoke` before deploying

## Cost
- Monitor invocation count, duration, and memory usage
- Use Lambda Power Tuning to find optimal memory settings
- Prefer event-driven architectures over polling
- Use S3 event notifications instead of periodic scans
