---
name: backend-best-practices
description: Build, review, or refactor backend systems using production-grade engineering defaults. Use this skill whenever the user asks for backend APIs, services, controllers, repositories, database access, queues, caching, auth, observability, scaling, idempotency, or "best practices" for server-side systems, even if they do not explicitly ask for architecture help.
license: CC-BY-4.0
---

# Backend Best Practices

Use this skill to keep backend work boring in the right ways: layered, observable, resilient, secure, and efficient under real traffic. Prefer first-principles designs over fashionable complexity. Add advanced distributed patterns only when the workload actually needs them.

## Core Principle

Optimize for correctness first, operability second, performance third, and abstraction last. Many backend failures come from getting this order backwards.

## Outcomes

Aim for these outcomes in the result you produce:

1. Clear separation between transport, business logic, and persistence.
2. Explicit contracts for inputs, outputs, and failure modes.
3. Efficient database and cache behavior under load.
4. Safe operational behavior during shutdowns, retries, and partial failures.
5. Security controls that are on by default, not bolted on later.
6. Enough tests, logging, and metrics to diagnose production issues.

## When To Use

Use this skill when the user wants any of the following:

1. A new backend service, API, worker, webhook consumer, or internal platform service.
2. Refactoring of controllers, services, repositories, schemas, or data flows.
3. Help with caching, queues, retries, rate limits, observability, graceful shutdowns, or performance tuning.
4. Production hardening for authentication, authorization, validation, error handling, or deployment.
5. Review of backend code for scalability, reliability, or maintainability.

Do not over-apply advanced microservice patterns to simple CRUD apps. Start with a modular monolith unless the problem explicitly requires distributed boundaries.

## Working Style

Follow this default sequence:

1. Identify the business capability, external API contract, and operational constraints.
2. Choose the simplest architecture that meets expected scale and failure requirements.
3. Define contracts first: request schema, response schema, domain types, errors, and persistence boundaries.
4. Implement strict layering.
5. Add resilience, observability, and security before polishing abstractions.
6. Verify with tests that cover success, failure, and retry behavior.

If key constraints are missing, infer reasonable defaults from the workload rather than stalling. State those assumptions explicitly in the result.

## Task Modes

Choose one of these modes early and tailor the response to it:

1. Build: produce architecture, contracts, code structure, and key implementation details.
2. Review: inspect existing backend code for correctness, security, reliability, and scaling issues.
3. Refactor: move tangled code toward strict layering and clearer boundaries without changing behavior.
4. Harden: add missing operational safeguards such as auth, rate limits, logging, metrics, timeouts, graceful shutdown, and background jobs.
5. Scale: improve performance, query behavior, caching, concurrency control, queue usage, or deployment topology.

When the user's request mixes modes, prioritize them in this order:

1. Correctness and data integrity.
2. Security.
3. Reliability.
4. Performance.
5. Code cleanliness.

## Non-Negotiable Rules

1. Keep controllers thin. They handle HTTP semantics, validation, auth context, and mapping to service calls.
2. Keep business logic in services. Services must not depend on HTTP framework objects.
3. Keep repositories focused on data access. Do not leak ORM entities or raw database concerns into controllers.
4. Validate at the boundary. Normalize and sanitize input before it reaches business logic.
5. Use explicit DTOs or domain types for service inputs and outputs.
6. Design for cancellation, timeouts, and graceful shutdown.
7. Offload slow or non-critical work to background jobs when synchronous handling would block user-facing latency.
8. Prevent N+1 queries, unbounded loops, missing indexes, and cache stampedes.
9. Emit structured logs with request or trace IDs.
10. Return safe errors to clients and detailed diagnostics to logs, not the reverse.

## Architecture Defaults

Default to a strict 3-layer backend:

1. Presentation layer: routes, controllers, middleware, request validation, HTTP status mapping.
2. Business layer: services, domain rules, orchestration, transactions, idempotency logic.
3. Data layer: repositories, query builders, migrations, cache adapters, broker adapters.

Prefer dependency inversion:

1. Services depend on interfaces or stable abstractions, not framework internals.
2. Repositories hide storage details.
3. External providers such as Stripe, Redis, S3, email, and Kafka sit behind adapters.

If the user asks for a structure proposal, produce a file tree that reflects the layering.

## Framework Adaptation

Map the same architecture to the stack the user is already using:

1. Node.js or Express or NestJS:
   controllers are route handlers, services hold use-case logic, repositories wrap Prisma, TypeORM, SQL, or external clients.
2. Python or FastAPI or Django:
   routers or views handle transport, services hold workflows, repositories isolate ORM or SQL access.
3. Go:
   handlers own HTTP, services own use cases, repositories hide `database/sql`, ORM, or external clients.
4. Java or Spring Boot:
   controllers own web layer, services own transactions and domain logic, repositories own JPA or query access.
5. .NET:
   controllers own HTTP, application services own orchestration, repositories or data-access services hide EF Core or SQL concerns.

Preserve project conventions when they are already clean. Do not force a new folder layout unless the user asked for restructuring.

## Implementation Checklist

For any non-trivial backend task, consciously check these areas:

1. API contract: versioning, request and response schemas, status codes, pagination, idempotency, and OpenAPI when relevant.
2. Validation: required fields, enums, nested objects, normalization, payload limits, and semantic checks.
3. Persistence: indexes, transactions, uniqueness guarantees, connection pooling, and migration safety.
4. Performance: batching, eager loading, cache strategy, compression, streaming, and backpressure.
5. Security: authn, authz, rate limiting, CORS, secret handling, secure defaults, and OWASP basics.
6. Operations: timeouts, retries, circuit breaking when needed, graceful shutdowns, health endpoints, and config hygiene.
7. Observability: structured logs, metrics, traces, and correlation IDs.
8. Testing: unit, integration, contract, and failure-path coverage.

## Decision Heuristics

Use these heuristics to keep designs practical:

1. Prefer a modular monolith before microservices.
2. Prefer synchronous request handling only for work that must complete before the response.
3. Prefer queues for email, image processing, report generation, AI inference, webhooks, and fan-out work.
4. Prefer cache-aside for read-heavy paths unless consistency requirements clearly demand write-through.
5. Prefer Redis for distributed caching, rate limiting, and idempotency coordination.
6. Prefer database-enforced uniqueness for critical invariants.
7. Prefer read replicas, sharding, CQRS, Kafka, or sagas only when scale, team topology, or consistency needs justify them.

## Required Output Shape

When producing backend code or design guidance, include the following where relevant:

1. The chosen architecture and why it matches the problem.
2. API and data contracts.
3. Operational considerations: timeouts, retries, shutdown, background jobs, and observability.
4. Security and performance considerations.
5. Tests or a concrete testing plan.

Prefer concise, decision-oriented outputs over encyclopedia dumps. The best answer usually includes one recommended design, why it wins, and what tradeoffs were rejected.

## Response Templates

Use these structures when helpful.

### Build Template

1. Goal and assumptions.
2. Recommended architecture.
3. API contract.
4. Data model and repository responsibilities.
5. Operational controls.
6. Security notes.
7. Testing plan.

### Review Template

1. Findings ordered by severity with file references.
2. Risks if left unchanged.
3. Minimal fix direction.
4. Residual gaps or missing tests.

### Refactor Template

1. Current smell.
2. Target boundary split.
3. Concrete code movement plan.
4. Regression risks.
5. Verification plan.

### Scale Template

1. Bottleneck hypothesis.
2. Evidence or expected symptom.
3. Recommended fix.
4. Tradeoffs.
5. Metrics to watch after rollout.

If the user asks for a review, prioritize findings in this order:

1. Correctness and data integrity bugs.
2. Security issues.
3. Reliability and scaling risks.
4. Maintainability and clarity problems.

## Reusable Constraints

Use these constraints directly in generated specs or prompts:

1. "Implement a strict 3-layer architecture. Controllers validate HTTP and call Services. Services handle logic. Services call Repositories for data. Do not leak DB models into the presentation layer."
2. "Include graceful shutdown. Stop accepting new connections, allow in-flight work to finish within a timeout, and close database and broker connections cleanly."
3. "Offload non-critical or slow operations to a background task queue and return `202 Accepted` when appropriate."
4. "Write the OpenAPI contract first for externally consumed APIs."
5. "Inject a `trace_id` at request entry and propagate it through logs, metrics, traces, jobs, and downstream calls."
6. "Prevent N+1 queries, add the required indexes, and justify the caching strategy."
7. "Implement generalized error handling that hides stack traces from clients while logging structured diagnostics."
8. "For transactional endpoints, require idempotency keys, persist request state atomically, and make retries safe."

## Reference Map

Read only the reference files you need:

1. [references/foundation.md](references/foundation.md) for HTTP, API design, middleware, layered architecture, databases, and caching.
2. [references/operations-security.md](references/operations-security.md) for queues, shutdowns, observability, security, distributed systems, and delivery.
3. [references/agent-prompts.md](references/agent-prompts.md) for reusable prompt templates, including backend architect and idempotency-key constraints.
4. [references/review-checklist.md](references/review-checklist.md) for build and review checklists.

## What Good Looks Like

A strong backend result usually has these traits:

1. Thin handlers, explicit services, and bounded repositories.
2. Schema validation at ingress and typed outputs at egress.
3. Indexed queries, no hidden N+1 behavior, and bounded pagination.
4. Background processing for expensive work.
5. Idempotent handling for transactional operations.
6. Structured logs, trace IDs, health checks, and shutdown handling.
7. Tests that cover both happy paths and ugly edge cases.

## Anti-Pattern Smells

Treat these as red flags:

1. Controllers opening transactions or calling payment gateways directly.
2. Services returning framework response objects.
3. Repositories encoding business rules instead of persistence logic.
4. Cron jobs or workers without idempotency or retry ceilings.
5. Caches with no invalidation story.
6. Pagination endpoints with unstable ordering.
7. "Temporary" debug logging that leaks secrets, tokens, or personal data.
8. Timeouts missing on outbound HTTP, DB, or broker calls.

## Fast Examples

Use examples like these to steer implementations:

1. Bad:
   a route handler validates input, loads three tables, applies pricing logic, calls Stripe, sends email, and returns JSON.
   Good:
   controller validates and maps input, service orchestrates pricing and payment, repositories fetch and persist data, email is queued.
2. Bad:
   loop over 100 orders and query customer details 100 times.
   Good:
   batch-fetch customers once and map in memory.
3. Bad:
   synchronous image processing inside `POST /upload`.
   Good:
   store metadata, enqueue work, return `202 Accepted`, expose job status if needed.
4. Bad:
   retrying a checkout request with no deduplication.
   Good:
   require `Idempotency-Key`, persist state, return cached success on repeat.

## What To Avoid

1. Fat controllers that mix routing, validation, business logic, and SQL.
2. Blocking I/O or CPU-heavy work inside latency-sensitive request handlers.
3. ORM-heavy code that hides query count or fetches entire tables.
4. Returning raw internal exceptions or database error messages to clients.
5. Adding Kafka, sagas, CQRS, or service meshes without a concrete need.
6. Trusting client input, retries, or network behavior.
