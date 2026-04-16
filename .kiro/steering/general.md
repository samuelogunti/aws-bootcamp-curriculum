# General Engineering Best Practices

## Code Quality
- Write clear, self-documenting code — use meaningful names for variables, functions, and classes
- Keep functions small and focused on a single responsibility
- Prefer composition over inheritance
- Don't repeat yourself (DRY), but don't over-abstract prematurely either
- Add comments for "why", not "what" — the code should explain the what

## Code Review
- Every change should be reviewed before merging
- Keep PRs small and focused — one concern per PR
- Include context in PR descriptions: what changed, why, and how to test
- Review for correctness, readability, security, and performance

## Testing
- Write tests alongside code, not as an afterthought
- Aim for meaningful coverage, not 100% line coverage
- Use unit tests for business logic, integration tests for service boundaries
- Mock external dependencies in unit tests
- Run tests in CI on every push

## Documentation
- Keep a README with setup instructions, architecture overview, and contribution guidelines
- Document API contracts (OpenAPI, GraphQL schema)
- Keep documentation close to the code it describes
- Update docs when behavior changes — stale docs are worse than no docs

## Error Handling
- Handle errors explicitly — don't swallow exceptions silently
- Use structured error types with clear messages
- Log errors with enough context to debug (request ID, user context, stack trace)
- Return user-friendly error messages to clients, detailed errors to logs

## Performance
- Measure before optimizing — profile to find actual bottlenecks
- Use caching strategically (CDN, in-memory, database query cache)
- Paginate large result sets
- Use async/non-blocking I/O for I/O-bound workloads
- Set timeouts on all external calls
