# Foundation

Use this reference for core backend concepts that should shape most systems before advanced scaling concerns appear.

## 1. HTTP Protocols And Network Dynamics

Focus areas:

1. Request lifecycle: DNS, load balancers, reverse proxies, firewalls, app server ingress.
2. HTTP semantics: correct use of `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, headers, status codes, caching directives, and content negotiation.
3. CORS: simple vs preflight requests, `OPTIONS`, origin policy, safe header configuration.
4. Protocol evolution: HTTP/1.1 vs HTTP/2 vs HTTP/3, multiplexing, connection reuse, and head-of-line blocking tradeoffs.
5. Transport optimization: TLS, keep-alive, gzip, brotli, streaming, chunked transfer.

Agent guidance:

1. Respect HTTP semantics instead of tunneling everything through `POST`.
2. Add compression and pagination on payload-heavy endpoints.
3. Use streaming for large downloads, uploads, or server-side event flows.

## 2. Presentation Layer And API Design

Focus areas:

1. Routing patterns: static, dynamic, nested, wildcard, regex, and route precedence.
2. API versioning: URI, header, query, media type; deprecation and retirement plans.
3. Serialization: JSON as default, Protobuf or Avro for internal high-throughput systems.
4. Validation: JSON Schema, nested object validation, nullability, enums, time zones, and date parsing.
5. OpenAPI-first design for external APIs.

Agent guidance:

1. Define request and response shapes first.
2. Limit fields to what clients actually need.
3. Prefer cursor pagination for large or changing datasets.
4. Treat time zones and timestamps explicitly.
5. Use deterministic sort order for paginated resources.
6. Prefer partial-update semantics only when the merge behavior is unambiguous.

## 3. Request Lifecycle And Middleware

Recommended middleware order:

1. Request ID or trace ID injection.
2. Access logging.
3. Security headers.
4. CORS.
5. Rate limiting.
6. Authentication.
7. Authorization.
8. Body parsing with payload limits.
9. Validation and normalization.
10. Route handler.
11. Global error handling.

Agent guidance:

1. Short-circuit early on auth, validation, and rate-limit failures.
2. Propagate request-scoped context through async flows.
3. Honor deadlines, abort signals, and timeouts.

## 4. Decoupled Code Architecture

Core rules:

1. Controllers own transport concerns only.
2. Services own orchestration, validation beyond syntax, transactions, idempotency, and policies.
3. Repositories own data access patterns and mapping to storage.
4. Adapters wrap external systems such as cache, broker, payments, storage, and email.

Useful design principles:

1. Single Responsibility Principle for each layer.
2. Dependency Inversion so higher-level rules do not depend on frameworks.
3. Separation of concerns between syntax validation and domain validation.

Validation categories:

1. Syntactic: shape, required fields, types, size limits.
2. Semantic: business rules, state transitions, permissions, invariants.
3. Safety: sanitization, normalization, injection defense, canonicalization.

## 5. Database Operations And Performance

Focus areas:

1. SQL vs NoSQL tradeoffs.
2. ACID and transaction boundaries.
3. Schema design, normalization, uniqueness, foreign keys, and indexing.
4. Query plans, join costs, eager loading, batching, and pagination.
5. Connection pooling, concurrency control, and migration safety.

Common backend failures to catch:

1. N+1 queries.
2. Missing composite indexes for real filter and sort patterns.
3. Long transactions holding locks across network calls.
4. Table scans hidden behind ORM convenience methods.
5. Unbounded `SELECT *` behavior on hot endpoints.
6. Offset pagination on very large tables where keyset pagination is more stable.
7. Missing idempotency or uniqueness controls on transactional tables.

Agent guidance:

1. Batch where possible.
2. Keep transactions tight and local.
3. Never call third-party APIs inside a database transaction unless the business process was explicitly designed for it.
4. Explain why each new index exists.
5. Call out read paths, write paths, and consistency expectations separately.

## 6. Caching Topologies

Patterns:

1. Cache-aside for read-heavy data.
2. Write-through for stronger consistency needs.
3. Write-behind only with explicit tolerance for asynchronous persistence risks.
4. Read-through only when cache infrastructure already supports it cleanly.

Layers:

1. L1 in-process cache for low-latency hot reads in a single instance.
2. L2 distributed cache such as Redis for multi-instance deployments.

Operational concerns:

1. TTL selection.
2. Eviction policy: LRU, LFU, FIFO.
3. Cache invalidation triggers.
4. Stampede protection with locks, jitter, or stale-while-revalidate.

Agent guidance:

1. Name the cache key format.
2. Define what causes invalidation.
3. Avoid caching secrets or authorization decisions without very careful scoping.
