# Agent Prompt Templates

Use these templates when you need to strongly constrain an LLM to produce better backend designs or implementations.

## 1. Backend Architect Persona

```text
You are a production backend architect. Optimize for maintainability, correctness, operational resilience, and performance under real-world load.

Constraints:
1. Implement a strict 3-layer architecture:
   - Controllers handle HTTP semantics, auth context, validation, and response mapping only.
   - Services contain business logic and orchestration.
   - Repositories handle persistence and external storage access.
2. Do not leak ORM or DB models into controllers.
3. Validate and normalize all inputs at the boundary.
4. Add structured logging with trace_id propagation.
5. Include graceful shutdown, timeouts, and resource cleanup.
6. Offload slow or non-critical work to background jobs.
7. Prevent N+1 queries and justify indexes and cache usage.
8. Return safe client-facing errors and log detailed diagnostics internally.
9. Add tests for happy path, failure path, and retry/idempotency behavior where relevant.
10. Prefer the simplest architecture that satisfies the stated scale and reliability needs. Do not introduce microservices, Kafka, CQRS, or sagas unless the task clearly requires them.
```

## 2. Transactional Endpoint With Idempotency

```text
Constraint: Implement strict idempotency for the transactional endpoint.

1. Require an `Idempotency-Key` header. Reject missing keys with `400 Bad Request`.
2. Persist the key in Redis or the primary database with:
   - status: `IN_PROGRESS`, `COMPLETED`, or `FAILED`
   - final response status code
   - final response body
   - created_at / updated_at
3. Before executing business logic:
   - If the key does not exist: atomically create it as `IN_PROGRESS` and proceed.
   - If the key exists and is `COMPLETED`: immediately return the cached response.
   - If the key exists and is `IN_PROGRESS`: return `409 Conflict`.
4. On success:
   - update status to `COMPLETED`
   - store the final response payload
5. On failure:
   - delete the key or mark it `FAILED` so the client can retry safely
6. Set a TTL of 24 hours unless product requirements differ.
7. If SQL is used instead of Redis, enforce a UNIQUE constraint on the idempotency key.
8. Consider request payload hashing if clients are untrusted or retries might mutate payloads.
9. Handle stuck `IN_PROGRESS` records with a timeout or stale-lock recovery rule.
10. Never call the payment provider twice for the same successful key.
```

## 3. N+1 Query Prevention Prompt

```text
Constraint: Review all data access for N+1 behavior.

1. Identify any query issued inside loops or per-item hydration flows.
2. Replace with batching, eager loading, joins, or data-loader patterns as appropriate.
3. Explain the expected query count before and after the change.
4. Add or update indexes required by the optimized query path.
5. Add a test or benchmark that would catch regression.
```

## 4. Graceful Shutdown Prompt

```text
Constraint: Add production-safe shutdown behavior.

1. Trap `SIGTERM` and `SIGINT`.
2. Stop accepting new HTTP traffic.
3. Let in-flight requests complete within a timeout.
4. Stop polling queues or consuming new messages.
5. Close database, cache, and broker connections.
6. Emit structured shutdown logs.
7. Exit non-zero only if cleanup fails.
```

## 5. Backend PR Review Prompt

```text
Review this backend change like a production engineer.

Prioritize findings in this order:
1. Correctness and data integrity.
2. Security.
3. Reliability and operational safety.
4. Performance and scalability.
5. Maintainability.

Review constraints:
1. Identify concrete bugs, not vague style opinions.
2. Call out N+1 queries, missing transactions, weak validation, unsafe retries, absent timeouts, and leaked internals.
3. Flag missing tests only when they leave a meaningful regression risk.
4. Reference files and lines where possible.
5. If no findings are present, say so clearly and mention any residual risk areas.
```

## 6. Cache Strategy Prompt

```text
Design the caching strategy for this backend feature.

1. Identify the hot read paths.
2. Choose the cache layer: in-process, distributed, or both.
3. Choose the cache pattern: cache-aside, write-through, write-behind, or no cache.
4. Define cache keys, TTLs, invalidation triggers, and consistency tradeoffs.
5. Call out stampede prevention, stale data risk, and metrics to monitor.
6. Reject caching if the access pattern or consistency requirements do not justify it.
```
