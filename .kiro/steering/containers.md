# Containerization Best Practices

## Dockerfile
- Use multi-stage builds to keep final images small
- Start from minimal base images (`alpine`, `distroless`, `slim` variants)
- Pin base image versions — avoid `latest` tag
- Order instructions from least to most frequently changing to maximize layer caching
- Combine RUN commands to reduce layers
- Copy dependency manifests first, install, then copy source code

## Image Optimization
- Remove build tools, caches, and temp files in the same layer they're created
- Use `.dockerignore` to exclude unnecessary files (`.git`, `node_modules`, `__pycache__`)
- Target final image size under 200MB where possible
- Scan images for vulnerabilities (Trivy, Snyk, ECR scanning)

## Security
- Don't run containers as root — use a non-root `USER`
- Don't store secrets in images — inject at runtime via environment variables or secrets manager
- Use read-only file systems where possible (`--read-only`)
- Keep base images updated for security patches

## Health Checks
- Define `HEALTHCHECK` in Dockerfiles or in orchestrator config
- Health check endpoints should verify actual readiness (database connectivity, dependency availability)
- Set appropriate intervals, timeouts, and retry thresholds

## Container Orchestration
- Use ECS Fargate or EKS for production workloads
- Define resource limits (CPU, memory) for every container
- Use rolling deployments with health check gates
- Store container images in ECR with lifecycle policies to clean up old images
- Use task/pod anti-affinity for high availability

## Local Development
- Use `docker-compose` for local multi-service development
- Keep local and production Dockerfiles as similar as possible
- Mount source code as volumes for hot-reload during development
- Document the local setup in the README
