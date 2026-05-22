# Review Checklist

Use this checklist when reviewing backend code, design docs, or generated implementations.

## Architecture

1. Are controllers thin and transport-only?
2. Is business logic isolated in services?
3. Are repositories the only layer touching persistence details?
4. Are external integrations behind adapters?

## API And Validation

1. Are request and response contracts explicit?
2. Are status codes semantically correct?
3. Is validation done before business logic?
4. Are pagination, filtering, and versioning sensible?
5. Are idempotency and retry semantics defined for transactional endpoints?

## Database And Cache

1. Any N+1 queries or hidden ORM loops?
2. Are indexes aligned to real filter and sort paths?
3. Are transactions scoped tightly?
4. Is cache invalidation defined?
5. Is the cache strategy justified?
6. Are uniqueness guarantees enforced in the database where correctness depends on them?

## Reliability

1. Are timeouts, retries, and cancellation handled?
2. Are background jobs used where synchronous work is too slow?
3. Is shutdown graceful?
4. Are webhooks and jobs idempotent?
5. Are downstream failures isolated well enough to avoid cascading outages?

## Security

1. Are authn and authz enforced at the correct boundary?
2. Are secrets protected?
3. Are CORS and rate limits configured?
4. Are internal errors hidden from clients?
5. Are common injection paths blocked?

## Observability

1. Are logs structured?
2. Is a request or trace ID propagated?
3. Are key metrics measurable?
4. Are downstream calls traceable?

## Testing And Delivery

1. Are happy path and failure path tests present?
2. Are integration tests covering DB, cache, queue, or external boundaries?
3. Would CI catch schema, migration, or contract regressions?
4. Is deployment strategy safe for the change risk?
5. Is there at least one test that would catch the most likely production incident here?
