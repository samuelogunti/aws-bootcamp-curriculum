# DevOps Best Practices

## Infrastructure as Code (IaC)
- Define all infrastructure in code (CloudFormation, CDK, Terraform, SAM)
- Never make manual changes to production infrastructure — all changes go through IaC
- Store IaC templates in version control alongside application code
- Use parameterized templates for environment-specific configuration
- Validate and lint templates before deployment (`cfn-lint`, `tflint`)

## CI/CD
- Automate build, test, and deploy pipelines
- Run linting, unit tests, and security scans on every PR
- Use separate stages: build → test → staging → production
- Implement automated rollback on deployment failure
- Keep build pipelines fast — cache dependencies where possible
- Tag releases with semantic versioning

## Environments
- Maintain parity between dev, staging, and production
- Use environment variables or config files for environment-specific values — never branch logic by environment name in code
- Isolate environments using separate AWS accounts or at minimum separate VPCs

## Monitoring & Observability
- Instrument applications with structured logging (JSON format preferred)
- Set up CloudWatch alarms for key metrics (error rates, latency, CPU/memory)
- Use distributed tracing (X-Ray, OpenTelemetry) for microservices
- Create dashboards for operational visibility
- Set up alerting with clear escalation paths

## Incident Response
- Document runbooks for common failure scenarios
- Automate recovery where possible (auto-scaling, health checks, self-healing)
- Conduct post-incident reviews and update runbooks accordingly
