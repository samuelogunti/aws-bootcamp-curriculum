# Database & Data Modeling Best Practices

## Schema Design
- Start normalized, denormalize only when you have measured performance needs
- Use meaningful table and column names — avoid abbreviations
- Always include `created_at` and `updated_at` timestamps
- Use UUIDs or ULIDs for public-facing IDs — keep auto-increment IDs internal
- Define foreign key constraints to enforce referential integrity

## Migrations
- Use a migration tool (Flyway, Alembic, Knex, Prisma Migrate) — never apply DDL manually
- Make migrations idempotent where possible
- Keep migrations small and reversible
- Test migrations against a copy of production data before deploying
- Never modify a migration that has already been applied — create a new one

## Indexing
- Index columns used in WHERE, JOIN, and ORDER BY clauses
- Use composite indexes for multi-column queries — column order matters
- Avoid over-indexing — each index adds write overhead
- Monitor slow query logs and add indexes based on actual usage patterns
- Use partial indexes for filtered queries on large tables

## Connection Management
- Use connection pooling (PgBouncer, RDS Proxy, HikariCP)
- Set appropriate pool sizes — too many connections can overwhelm the database
- Close connections properly — use try-with-resources or equivalent patterns
- Set query timeouts to prevent long-running queries from blocking resources

## Performance
- Use EXPLAIN/ANALYZE to understand query plans before optimizing
- Avoid SELECT * — fetch only the columns you need
- Paginate large result sets at the database level (LIMIT/OFFSET or keyset pagination)
- Use read replicas to offload read-heavy traffic
- Cache frequently accessed, rarely changing data (Redis, ElastiCache)

## DynamoDB Specific
- Design access patterns first, then model tables around them
- Use single-table design when access patterns are well-defined
- Choose partition keys for even data distribution
- Use GSIs sparingly — each adds storage and write cost
- Enable point-in-time recovery and on-demand backups

## Data Safety
- Enable automated backups with appropriate retention
- Test restore procedures regularly — backups are useless if you can't restore
- Use soft deletes for user-facing data when possible
- Encrypt data at rest and in transit
- Mask or redact PII in non-production environments
