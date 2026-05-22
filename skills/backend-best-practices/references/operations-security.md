# Operations, Security, And Scale

Use this reference when backend work crosses into reliability, background processing, security hardening, distributed systems, or deployment maturity.

## 7. Asynchronous Systems And Background Processing

Use queues when work is expensive, slow, flaky, or not required before the user receives a response.

Typical candidates:

1. Transactional email.
2. Webhook fan-out.
3. File and image processing.
4. Report generation.
5. AI inference or embedding pipelines.
6. Retryable third-party sync jobs.

Focus areas:

1. Message brokers: Redis, RabbitMQ, Kafka where justified.
2. Retries with backoff and jitter.
3. Dead-letter queues and poison-message handling.
4. Concurrency limits and rate shaping.
5. Scheduled jobs, cron workflows, backups, cleanup tasks.

Agent guidance:

1. Return `202 Accepted` when the result is asynchronous.
2. Make jobs idempotent.
3. Include retry ceilings and failure visibility.
4. Persist enough job context to resume, debug, or replay safely.

## 8. Search Architectures And Webhooks

Search:

1. Use dedicated search engines such as Elasticsearch for full-text relevance, analyzers, tokenization, shards, and autocomplete.
2. Do not force transactional databases to behave like search engines for complex search workloads.

Webhooks:

1. Verify signatures.
2. Deduplicate events where the provider can retry.
3. Acknowledge quickly, then process asynchronously when possible.
4. Persist delivery status and retry behavior.

## 9. Error Engineering And Observability

Error handling:

1. Classify errors into validation, auth, not found, conflict, dependency failure, timeout, and internal errors.
2. Map them to stable HTTP responses.
3. Log rich context without leaking secrets or PII.

Graceful shutdown:

1. Trap `SIGTERM` and `SIGINT`.
2. Stop accepting new traffic.
3. Wait for in-flight requests and jobs up to a timeout.
4. Close database pools, cache clients, consumers, and producers.
5. Exit with clear logs about shutdown status.

Three pillars:

1. Logs: structured JSON, levels, event names, correlation IDs.
2. Metrics: latency, throughput, saturation, error rate, queue depth, retries, cache hit rate, DB pool usage.
3. Traces: spans around handlers, DB queries, queue hops, and downstream API calls.

Agent guidance:

1. Inject `trace_id` at the edge.
2. Propagate it through jobs and outbound requests.
3. Prefer consistent event names and dimensions over ad hoc logging.
4. Distinguish operational alerts from debugging signals so on-call noise stays manageable.

## 10. Backend Security Engineering

Authentication options:

1. Sessions for browser-heavy apps.
2. JWT or opaque tokens for stateless APIs.
3. OAuth 2.0 or OIDC when delegating identity.
4. API keys for controlled service integrations.

Authorization:

1. RBAC for role-driven access.
2. ABAC for policy-rich environments.
3. ReBAC for graph or relationship-heavy products.

Cryptography and secrets:

1. Use `bcrypt` or `Argon2` for password hashing.
2. Encrypt data in transit and at rest where required.
3. Keep secrets out of source code and logs.

Minimum defenses:

1. Input validation and output encoding.
2. SQL and NoSQL injection prevention.
3. CSRF protection for cookie-based auth.
4. Safe CORS defaults.
5. Rate limiting and brute-force protection.
6. Path traversal, insecure deserialization, and timing attack awareness.

## 11. Enterprise Distributed Systems And Scale

Introduce these only when justified:

1. Circuit breakers for unstable downstream services.
2. Retries with deadlines and idempotency.
3. Bulkheads to prevent resource starvation.
4. Pub/Sub and event-driven designs.
5. Kafka for durable high-volume event streams.
6. CQRS when read and write workloads diverge sharply.
7. Sharding and read replicas when the data tier truly needs them.
8. Sagas for distributed transaction compensation.

Important warning:

1. Advanced patterns reduce one class of pain while adding another.
2. Do not introduce them before the current bottleneck is measurable.
3. If you cannot explain the failure mode the pattern solves, you probably should not add it.

## 12. Testing, Code Quality, And Delivery

Testing matrix:

1. Unit tests for domain logic and adapters.
2. Integration tests for DB, cache, broker, and third-party boundaries.
3. Contract tests for APIs and webhook payloads.
4. E2E tests for critical workflows.
5. Load and stress tests for bottlenecks.
6. Security checks for auth and validation boundaries.

Quality gates:

1. Linters and formatters.
2. Complexity control.
3. Static analysis and type checking.
4. Migration review.

Delivery:

1. Containerize where appropriate.
2. Automate CI and CD.
3. Use blue-green, rolling, or canary deployment when risk justifies it.
4. Follow twelve-factor principles for config, logs, and stateless processes.
