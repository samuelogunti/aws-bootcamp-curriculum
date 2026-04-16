# Security Best Practices

## Secrets Management
- Never hardcode secrets, API keys, passwords, or tokens in source code
- Use environment variables or a secrets manager (e.g., AWS Secrets Manager, SSM Parameter Store)
- Add `.env` files to `.gitignore`
- Rotate credentials regularly and use short-lived tokens where possible

## Authentication & Authorization
- Use IAM roles over long-lived access keys
- Apply the principle of least privilege to all roles and policies
- Enforce MFA for human users
- Use OIDC federation for CI/CD pipelines instead of static credentials
- Validate and sanitize all user input on the server side

## Dependencies
- Pin dependency versions for reproducible builds
- Regularly audit dependencies for known vulnerabilities (`npm audit`, `pip audit`, `snyk`, etc.)
- Prefer well-maintained libraries with active security patching
- Remove unused dependencies

## Code
- Never log sensitive data (tokens, PII, passwords)
- Use parameterized queries to prevent SQL injection
- Encrypt data at rest and in transit (TLS 1.2+)
- Implement proper error handling — don't expose stack traces or internal details to end users
- Use Content Security Policy headers for web applications

## Git
- Never commit secrets — use pre-commit hooks (e.g., `git-secrets`, `trufflehog`) to prevent this
- Sign commits when possible
- Protect main/production branches with required reviews
