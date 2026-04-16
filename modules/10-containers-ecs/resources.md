# Module 10: Resources

## Official Documentation

### Amazon ECS Core

- [What is Amazon Elastic Container Service?](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)
- [Amazon ECS Clusters](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/clusters.html)
- [Amazon ECS Task Definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)
- [Amazon ECS Services](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html)
- [Example Amazon ECS Task Definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/example_task_definitions.html)
- [Amazon ECS Launch Types and Capacity Providers](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/capacity-launch-type-comparison.html)

### AWS Fargate

- [Architect for AWS Fargate for Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html)
- [Amazon ECS Task Definition Differences for Fargate](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/fargate-tasks-services.html)
- [Fargate Platform Versions for Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/platform-fargate.html)

### Container Images and Docker

- [Creating a Container Image for Use on Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-container-image.html)

### Amazon ECR

- [What is Amazon ECR?](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)
- [Amazon ECR Concepts and Components](https://docs.aws.amazon.com/AmazonECR/latest/userguide/concept-and-components.html)
- [Automate the Cleanup of Images by Using Lifecycle Policies in Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html)
- [Scan Images for Software Vulnerabilities in Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-scanning.html)
- [Using Amazon ECR Images with Amazon ECS](https://docs.aws.amazon.com/AmazonECR/latest/userguide/ECR_on_ECS.html)
- [Moving an Image Through Its Lifecycle in Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/getting-started-cli.html)

### ECS IAM Roles

- [Amazon ECS Task Execution IAM Role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html)
- [Amazon ECS Task IAM Role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html)
- [IAM Roles for Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-iam-role-overview.html)

### ECS Deployments

- [Deploy Amazon ECS Services by Replacing Tasks (Rolling Update)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-ecs.html)
- [How the Amazon ECS Deployment Circuit Breaker Detects Failures](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-circuit-breaker.html)
- [Amazon ECS Deployment Failure Detection](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-failure-detection.html)
- [Updating an Amazon ECS Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/update-service.html)

### ECS Service Auto Scaling

- [Automatically Scale Your Amazon ECS Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html)
- [Create a Target Tracking Scaling Policy for Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/target-tracking-create-policy.html)

### ECS Load Balancing and Service Discovery

- [Use Load Balancing to Distribute Amazon ECS Service Traffic](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-load-balancing.html)
- [Use an Application Load Balancer for Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/alb.html)
- [Use Service Discovery to Connect Amazon ECS Services with DNS Names](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-discovery.html)

### ECS Security

- [Amazon ECS Task and Container Security Best Practices](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security-tasks-containers.html)

### Application Load Balancer

- [What is an Application Load Balancer?](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)
- [Target Groups for Your Application Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)
- [Health Checks for Application Load Balancer Target Groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html)

### Secrets Management (Referenced in Security Section)

- [What is AWS Secrets Manager?](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)
- [What is AWS Systems Manager Parameter Store?](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)

### Amazon EKS (Referenced in Comparison)

- [What is Amazon EKS?](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)

### AWS Lambda (Referenced in Comparison)

- [What is AWS Lambda?](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)

### Amazon CloudWatch Logs (Referenced in Lab)

- [What is Amazon CloudWatch Logs?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)

### AWS CloudShell (Referenced in Lab)

- [What is AWS CloudShell?](https://docs.aws.amazon.com/cloudshell/latest/userguide/welcome.html)

### AWS CLI References (Used in Lab)

- [create-service (AWS CLI ECS Reference)](https://docs.aws.amazon.com/cli/latest/reference/ecs/create-service.html)
- [update-service (AWS CLI ECS Reference)](https://docs.aws.amazon.com/cli/latest/reference/ecs/update-service.html)

## AWS Whitepapers

- [Containers on AWS](https://docs.aws.amazon.com/whitepapers/latest/containers-on-aws/containers-on-aws.html): Covers container orchestration and compute options on AWS, including Amazon ECS, Amazon EKS, AWS Fargate, and AWS App Runner. Provides guidance on networking, security, and key considerations for running container workloads.
- [Overview of Deployment Options on AWS: Rolling Deployments](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/rolling-deployments.html): Describes rolling deployment strategies across AWS services, including how Amazon ECS replaces containers incrementally during service updates.

## AWS FAQs

- [Amazon ECS FAQ](https://aws.amazon.com/ecs/faqs/): Covers ECS pricing, Fargate integration, task definitions, service scheduling, and cluster management.
- [Amazon ECR FAQ](https://aws.amazon.com/ecr/faqs/): Covers ECR image storage, lifecycle policies, image scanning, and integration with ECS and EKS.

## AWS Architecture References

- [Best Practices: Running Your Application with Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/bestpracticesguide/application.html): Covers container image best practices, task definition configuration, and ECS service design patterns for production workloads.
- [Configuring Service Auto Scaling (ECS Best Practices Guide)](https://docs.aws.amazon.com/AmazonECS/latest/bestpracticesguide/capacity-autoscaling.html): Guidance on choosing the right scaling metric based on application resource utilization patterns, including CPU, memory, and concurrency-based scaling.
- [Amazon ECS Best Practices](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-best-practices.html): Comprehensive best practices covering networking, Fargate security, container images, clusters, tasks, services, and security for Amazon ECS workloads.
