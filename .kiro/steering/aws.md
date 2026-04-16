# AWS Best Practices

## IAM
- Use IAM roles, not access keys, for services and applications
- Apply least-privilege policies — start with zero permissions and add only what's needed
- Use AWS Organizations with SCPs for guardrails across accounts
- Prefer managed policies over inline policies for reusability
- Enable CloudTrail in all regions for audit logging

## Networking
- Use VPCs with public and private subnets
- Place databases, caches, and internal services in private subnets
- Use security groups as the primary network firewall — default deny all inbound
- Enable VPC Flow Logs for network troubleshooting and auditing
- Use VPC endpoints for AWS service access to avoid public internet traversal

## Compute
- Prefer serverless (Lambda, Fargate) for event-driven and variable workloads
- Right-size EC2 instances — use Compute Optimizer recommendations
- Use auto-scaling groups with health checks
- Prefer ARM-based instances (Graviton) for better price-performance

## Storage & Databases
- Enable versioning and encryption on S3 buckets
- Block public access on S3 buckets by default
- Use lifecycle policies to transition infrequently accessed data to cheaper storage classes
- Enable automated backups and point-in-time recovery for databases
- Use read replicas for read-heavy workloads

## Cost Management
- Tag all resources with at minimum: `Project`, `Environment`, `Owner`
- Set up AWS Budgets with alerts
- Use Savings Plans or Reserved Instances for predictable workloads
- Review Cost Explorer monthly for anomalies
- Clean up unused resources (unattached EBS volumes, idle load balancers, old snapshots)

## Resilience
- Design for multi-AZ availability at minimum
- Use health checks and circuit breakers
- Implement retries with exponential backoff and jitter for AWS API calls
- Test failure scenarios regularly (chaos engineering)
- Define and test RTO/RPO for critical systems
