# API Design Best Practices

## REST Conventions
- Use nouns for resource URLs (`/users`, `/orders`), not verbs (`/getUsers`)
- Use HTTP methods correctly: GET (read), POST (create), PUT (full update), PATCH (partial update), DELETE (remove)
- Return appropriate status codes: 200 (OK), 201 (Created), 204 (No Content), 400 (Bad Request), 401 (Unauthorized), 403 (Forbidden), 404 (Not Found), 409 (Conflict), 500 (Internal Server Error)
- Use plural nouns for collection endpoints (`/users` not `/user`)
- Nest resources to express relationships (`/users/{id}/orders`)

## Versioning
- Version APIs from the start — retrofitting is painful
- Prefer URL path versioning (`/v1/users`) for simplicity
- Support at most two versions simultaneously — deprecate old versions with clear timelines
- Document breaking vs non-breaking changes

## Request & Response
- Use JSON as the default format
- Use consistent field naming (camelCase or snake_case — pick one and stick with it)
- Wrap collection responses in an object (`{ "data": [], "meta": {} }`) to allow future metadata
- Include pagination metadata: `page`, `pageSize`, `totalCount`, `nextToken`
- Return created/updated resources in response bodies

## Pagination
- Use cursor-based pagination for large or frequently changing datasets
- Use offset-based pagination only for small, stable datasets
- Always set a default and maximum page size
- Return a `nextToken` or `nextPage` link for easy traversal

## Error Responses
- Use a consistent error format across all endpoints:
  ```json
  {
    "error": {
      "code": "VALIDATION_ERROR",
      "message": "Human-readable description",
      "details": []
    }
  }
  ```
- Include a machine-readable error code alongside the human-readable message
- Never expose internal details (stack traces, SQL errors) in responses

## Rate Limiting
- Implement rate limiting on all public APIs
- Return `429 Too Many Requests` with `Retry-After` header
- Use API keys or tokens to identify callers for per-client limits
- Consider tiered rate limits based on plan or role

## Documentation
- Document all endpoints with OpenAPI/Swagger
- Include request/response examples for every endpoint
- Document authentication requirements, rate limits, and error codes
- Keep docs auto-generated from code where possible
