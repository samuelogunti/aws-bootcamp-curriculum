# Architecture Patterns & Best Practices

## Choosing an Architecture
- Start with a modular monolith — split into microservices only when you have a clear need
- Microservices add operational complexity — justify the trade-off with team scale, deployment independence, or scaling requirements
- Use event-driven architecture for decoupling and async workflows
- Apply domain-driven design (DDD) to identify service boundaries

## Microservices
- Each service owns its data — no shared databases
- Communicate via APIs (sync) or events (async) — prefer async where possible
- Keep services independently deployable and testable
- Use API gateways for routing, auth, and rate limiting at the edge
- Implement circuit breakers for inter-service calls

## Event-Driven Architecture
- Use events for state changes that other services need to know about
- Design events as immutable facts: `OrderPlaced`, `PaymentProcessed`
- Use a schema registry to enforce event contracts
- Plan for eventual consistency — not all systems need real-time sync
- Implement idempotent consumers to handle duplicate events

## CQRS (Command Query Responsibility Segregation)
- Separate read and write models when read and write patterns differ significantly
- Use it for read-heavy systems where query optimization matters
- Don't apply CQRS everywhere — it adds complexity and is overkill for simple CRUD

## Domain-Driven Design
- Identify bounded contexts to define clear service boundaries
- Use ubiquitous language — align code terminology with business terminology
- Keep domain logic in the domain layer, free of infrastructure concerns
- Use aggregates to enforce consistency boundaries

## API Gateway Pattern
- Use API Gateway (AWS API Gateway, AppSync) as the single entry point
- Handle cross-cutting concerns at the gateway: auth, rate limiting, logging, CORS
- Use request/response transformation to decouple client and backend formats
- Implement caching at the gateway for frequently accessed, stable data

## Resilience Patterns
- **Retry with backoff**: Retry transient failures with exponential backoff and jitter
- **Circuit breaker**: Stop calling a failing dependency after repeated failures
- **Bulkhead**: Isolate resources so one failing component doesn't take down everything
- **Timeout**: Set timeouts on all external calls — fail fast rather than hang
- **Fallback**: Provide degraded functionality when a dependency is unavailable

## Data Consistency
- Use transactions within a single service/database
- Use sagas (orchestration or choreography) for cross-service consistency
- Accept eventual consistency where possible — it simplifies architecture significantly
- Use idempotency keys to safely retry operations
