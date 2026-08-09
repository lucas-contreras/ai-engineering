# Backend

## Purpose

Provide practical engineering guidance for backend development, APIs, services, data access, validation, error handling, reliability, observability, and server-side architecture.

The goal is to build backend systems that are correct, secure, maintainable, observable, testable, and appropriate for their expected scale.

This skill is technology-agnostic.

Do not assume a specific backend framework, database, ORM, runtime, or deployment platform unless the project explicitly uses one.

---

# Core Principles

## Backend Owns Trust

The backend is responsible for enforcing:

* Authentication.
* Authorization.
* Business rules.
* Data validation.
* Resource ownership.
* Security boundaries.
* Data integrity.

Never rely on frontend behavior to enforce backend security or business rules.

The frontend may improve user experience by hiding unavailable actions, but the backend must independently validate every protected operation.

---

# Understand the Domain First

Before implementing backend functionality:

1. Understand the business operation.
2. Identify the relevant entities.
3. Identify relationships between entities.
4. Identify ownership and permissions.
5. Identify invariants.
6. Identify failure conditions.
7. Identify external dependencies.
8. Identify persistence requirements.
9. Identify expected concurrency.
10. Identify observability requirements.

Do not begin by designing endpoints before understanding the operation they represent.

---

# API Design

Design APIs around meaningful resources and operations.

Prefer consistent conventions for:

* URLs.
* HTTP methods.
* Status codes.
* Request bodies.
* Query parameters.
* Pagination.
* Filtering.
* Sorting.
* Errors.
* Authentication.
* Authorization.

Avoid creating endpoints solely around frontend implementation details.

The API should represent domain behavior rather than mirroring UI components.

---

# HTTP Semantics

Use HTTP methods according to their intended semantics.

Commonly:

```text
GET       Retrieve
POST      Create or execute a non-idempotent operation
PUT       Replace
PATCH     Partially modify
DELETE    Remove
```

Use status codes consistently.

For example:

```text
200 OK
201 Created
204 No Content
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
422 Unprocessable Content
429 Too Many Requests
500 Internal Server Error
```

Do not return `200 OK` for every possible outcome.

Do not use status codes merely because they make frontend handling convenient.

---

# Authentication vs Authorization

Keep authentication and authorization conceptually separate.

Authentication answers:

> Who is this actor?

Authorization answers:

> What is this actor allowed to do?

A valid identity does not automatically grant permission.

Every protected operation should verify authorization at the appropriate server-side boundary.

---

# Resource Ownership

When a request references a resource identifier, verify that the authenticated actor has permission to access that resource.

Do not assume:

```text id="e6v8r3"
GET /users/123/orders/456
```

is safe simply because the user is authenticated.

The backend must verify whether order `456` belongs to or is accessible by the requesting actor.

This applies to:

* Users.
* Orders.
* Documents.
* Files.
* Projects.
* Organizations.
* Administrative resources.

---

# Input Validation

Validate all externally supplied input.

Potential sources include:

* Request bodies.
* Path parameters.
* Query parameters.
* Headers.
* Cookies.
* Webhooks.
* External APIs.
* Message queues.
* Uploaded files.

Validation should verify:

* Required fields.
* Types.
* Format.
* Length.
* Allowed values.
* Relationships.
* Business constraints.

Do not assume frontend validation is sufficient.

---

# Validation Layers

Separate where appropriate:

### Structural validation

Does the input have the correct shape and types?

### Domain validation

Is the requested operation valid according to business rules?

### Authorization

Is this actor allowed to perform the operation?

These are different concerns.

For example:

```text id="f6s6j7"
Input:
"quantity": 5

Structural validation:
quantity is an integer

Domain validation:
quantity must be greater than zero

Authorization:
this user may modify this order
```

Do not collapse all validation into a single generic check when doing so obscures the domain rules.

---

# Business Logic

Business rules belong on trusted server-side boundaries.

Do not rely on clients to enforce rules such as:

* Maximum quantities.
* Ownership.
* Workflow transitions.
* Discounts.
* Permissions.
* Approval requirements.
* Resource limits.
* Financial constraints.

The backend should reject invalid state transitions.

---

# State Transitions

When entities have meaningful states, make transitions explicit.

For example:

```text id="4kr7ji"
draft
  ↓
submitted
  ↓
approved
  ↓
completed
```

Do not allow arbitrary updates to state fields when only certain transitions are valid.

Prefer explicit domain operations when they make the business rules clearer.

---

# Idempotency

Consider idempotency for operations that may be retried.

This is especially important for:

* Payments.
* Orders.
* Resource creation.
* External API calls.
* Message processing.
* Webhooks.

A client or infrastructure component may retry a request because of:

* Timeouts.
* Network failures.
* Connection resets.
* Load balancers.
* Queue redelivery.

Do not assume that a request is executed exactly once.

---

# Concurrency

Consider concurrent requests when multiple actors can modify the same resource.

Potential problems include:

* Lost updates.
* Duplicate operations.
* Race conditions.
* Inconsistent state.
* Double processing.
* Stale reads.

Use appropriate mechanisms such as:

* Transactions.
* Optimistic locking.
* Pessimistic locking.
* Unique constraints.
* Idempotency keys.
* Atomic operations.

Do not introduce locks or transactions without understanding the actual consistency requirement.

---

# Database Access

Keep database access deliberate and predictable.

Consider:

* Query efficiency.
* Indexes.
* Transactions.
* Constraints.
* Connection management.
* N+1 queries.
* Pagination.
* Data consistency.
* Migration safety.

Do not assume an ORM automatically produces efficient queries.

Inspect generated queries or execution plans when performance is relevant.

---

# Transactions

Use transactions when multiple operations must succeed or fail together.

For example:

```text id="g6x4t1"
Create order
+
Reserve inventory
+
Create order items
```

If the operation requires atomicity, ensure the database transaction actually covers the required operations.

Do not use transactions indiscriminately for every operation.

Understand transaction boundaries and isolation requirements.

---

# Database Constraints

Use the database to enforce important invariants when appropriate.

Examples:

* Unique values.
* Foreign keys.
* Non-null constraints.
* Valid relationships.
* Check constraints.

Application-level validation improves user-facing errors, but database constraints can provide a final integrity boundary.

Do not rely entirely on application code for invariants that the database can safely enforce.

---

# Migrations

Database migrations should be:

* Versioned.
* Reproducible.
* Reviewable.
* Safe to execute.
* Appropriate for the deployment strategy.

For production changes, consider compatibility between:

```text
old application
    ↓
migration
    ↓
new application
```

Avoid migrations that require the old and new application versions to be compatible only for an unrealistically short period.

---

# API Error Handling

Use consistent error responses.

A useful error response should communicate enough information for the client to handle the problem without exposing internal implementation details.

Consider a structure such as:

```text id="z91m0g"
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "The requested resource was not found"
  }
}
```

Use stable machine-readable error codes when the frontend needs to react differently to different failures.

Do not force clients to parse human-readable error messages to determine behavior.

---

# Error Categories

Distinguish between:

### Client errors

The request cannot be accepted because of the client's input, permissions, or state.

### Domain errors

The request is structurally valid but violates a business rule.

### Infrastructure errors

The application cannot complete the operation because a dependency or infrastructure component failed.

### Unexpected errors

The application encountered an unexpected condition.

Do not expose infrastructure details to clients merely because they are available in the exception.

---

# Exception Handling

Handle expected failures explicitly.

Do not use broad exception handling to hide programming errors.

Avoid patterns equivalent to:

```text id="k6l5gl"
try:
    doSomething()
catch:
    return success
```

An exception should not silently transform a failed operation into a successful response.

Unexpected errors should be observable and should fail safely.

---

# External Services

Treat external dependencies as unreliable.

Examples:

* Payment providers.
* Authentication providers.
* Cloud services.
* Email services.
* Third-party APIs.
* Databases.
* Message brokers.

Consider:

* Timeouts.
* Retries.
* Backoff.
* Circuit breaking where appropriate.
* Idempotency.
* Partial failure.
* Error translation.
* Observability.

Do not retry every error.

Distinguish transient failures from permanent failures.

---

# Timeouts

External calls should have deliberate timeout behavior.

An operation that can wait forever can consume:

* Threads.
* Connections.
* Memory.
* Request capacity.

Timeouts should be appropriate to the operation and infrastructure.

Do not simply use extremely large timeouts to avoid handling failures.

---

# Retries

Retries can amplify failures.

Before retrying, consider:

* Is the operation idempotent?
* Is the error transient?
* How many retries are appropriate?
* Is exponential backoff needed?
* Could retries overload the dependency?

Do not blindly retry operations that may execute twice.

---

# Pagination

Do not return unbounded collections.

For potentially large datasets, use pagination.

Consider:

* Page size.
* Maximum page size.
* Stable ordering.
* Cursor-based pagination when appropriate.
* Filtering.
* Sorting.

Avoid accepting arbitrary client-controlled limits without reasonable maximums.

---

# Large Payloads

Protect the application against unnecessarily large inputs.

Consider limits for:

* Request bodies.
* File uploads.
* Query parameters.
* Batch operations.
* API responses.

Do not assume that clients will always send reasonably sized payloads.

---

# Caching

Cache only when the consistency and invalidation strategy is understood.

Consider:

* Cache keys.
* Expiration.
* Invalidation.
* Stale data.
* Authorization boundaries.
* Sensitive information.

Never allow cached responses to cross user or authorization boundaries accidentally.

---

# Background Jobs

Use background processing for work that does not need to block the request when appropriate.

Examples:

* Sending email.
* Generating reports.
* Processing large files.
* Synchronizing external systems.
* Long-running computations.

Background jobs should consider:

* Retries.
* Idempotency.
* Failure handling.
* Dead-letter behavior.
* Observability.
* Concurrency.

Do not move work to background jobs merely to hide a slow operation without understanding the user-facing behavior.

---

# Queues and Messaging

When using asynchronous messaging:

Consider:

* Delivery semantics.
* Duplicate messages.
* Ordering.
* Retry behavior.
* Poison messages.
* Dead-letter queues.
* Idempotent consumers.

Assume that messages may be delivered more than once unless exactly-once behavior is explicitly guaranteed and appropriate.

---

# Observability

Backend systems should be observable.

Consider:

* Structured logs.
* Metrics.
* Traces.
* Request identifiers.
* Dependency latency.
* Error rates.
* Resource utilization.

Observability should help answer:

```text id="q36xjg"
What happened?
Where did it happen?
For which request?
For which user or tenant, when appropriate?
How long did it take?
Which dependency failed?
What was the outcome?
```

Do not log sensitive data unnecessarily.

For security-specific logging guidance, use:

```text id="5f5g9u"
skills/security/SKILL.md
```

---

# Logging

Prefer structured logging over arbitrary strings when the infrastructure supports it.

Useful fields may include:

* Request ID.
* Operation.
* Status.
* Duration.
* Dependency.
* Error category.
* Correlation ID.

Avoid logging:

* Passwords.
* Access tokens.
* Private keys.
* Session identifiers.
* Sensitive personal information.

Logs should not become a secondary data leak.

---

# Health Checks

Health endpoints should reflect their purpose.

Consider distinguishing:

### Liveness

Is the process alive?

### Readiness

Can the instance safely receive traffic?

Do not make liveness checks depend on every external dependency unless that is intentionally required.

A temporary database failure should not necessarily cause the process to be considered dead.

---

# Configuration

Keep environment-specific configuration outside application logic where appropriate.

Configuration may include:

* Database URLs.
* External service endpoints.
* Feature flags.
* Timeouts.
* Credentials.
* Resource limits.

Validate required configuration during startup when possible.

Fail fast for invalid critical configuration rather than discovering it during a production request.

---

# Secrets

Never hardcode secrets.

Do not place secrets in:

* Source code.
* Public configuration.
* Frontend bundles.
* Logs.
* Error messages.
* Git history.

Use appropriate secret-management mechanisms for the deployment environment.

For detailed security guidance, use:

```text id="x3b6k8"
skills/security/SKILL.md
```

---

# Security

Backend security must be treated as a first-class concern.

Apply:

```text id="5m1a0e"
skills/security/SKILL.md
```

when implementing or reviewing:

* Authentication.
* Authorization.
* Input handling.
* Database access.
* File uploads.
* External URL fetching.
* Sensitive data.
* Secrets.
* Sessions.
* Tokens.
* CORS.
* CSRF.
* SSRF.
* Dependencies.
* Privileged operations.

Do not duplicate detailed security rules inside this skill.

---

# API and Frontend Integration

The backend API is a contract between systems.

Consider:

* Stable response shapes.
* Explicit error codes.
* Backward compatibility.
* Nullable fields.
* Optional fields.
* Pagination contracts.
* Validation errors.
* Authentication state.
* Authorization failures.

Avoid changing response structures casually.

A small API change can break multiple frontend consumers.

---

# API Contract Evolution

When changing an API, consider existing clients.

Prefer additive changes when appropriate:

```text
Existing response:
{
  id,
  name
}

New response:
{
  id,
  name,
  description
}
```

Be careful when:

* Removing fields.
* Changing types.
* Renaming properties.
* Changing status codes.
* Changing error formats.
* Changing required request fields.

For breaking changes, consider versioning or a migration strategy.

---

# Monorepo Considerations

In a monorepo, backend code should have clear ownership boundaries.

For example:

```text id="k9qkq6"
apps/
├── web/
└── api/

packages/
├── shared/
├── types/
└── config/
```

Avoid turning `shared` into a dumping ground.

Only share code when it genuinely represents a stable cross-boundary concern.

---

# Sharing Types

Sharing types between frontend and backend can reduce contract drift.

However, do not assume that a TypeScript type is runtime validation.

For example:

```text id="yd2xym"
type CreateUserRequest = {
  email: string;
}
```

does not validate incoming HTTP data.

Runtime validation must still happen at the backend boundary.

Shared types can describe the contract, but they do not replace runtime validation.

---

# Monorepo Dependency Boundaries

Keep dependency direction explicit.

For example:

```text id="4o7n76"
web ───────→ shared
api ───────→ shared

shared ────X→ api
shared ────X→ web
```

Avoid importing backend implementation details into frontend packages.

Do not share server-only modules with browser code.

Be especially careful with:

* Database clients.
* Secrets.
* Filesystem access.
* Server configuration.
* Node-only APIs.
* Internal services.

A shared package should not accidentally make server-only code available to the browser bundle.

---

# Backend Testing

Backend behavior should be tested at appropriate levels.

Consider:

### Unit tests

For isolated domain logic.

### Integration tests

For interactions between application components and dependencies.

### API tests

For request/response contracts and behavior.

### End-to-end tests

For important complete workflows.

Do not force every behavior into an end-to-end test.

Prefer the lowest test level that provides meaningful confidence.

---

# Test Important Failures

Do not test only successful requests.

Consider:

* Invalid input.
* Missing authentication.
* Missing authorization.
* Resource not found.
* Duplicate resources.
* Conflicting updates.
* Dependency failure.
* Timeout.
* Invalid state transition.
* Rate limiting.
* Unexpected external responses.

A backend is defined as much by how it fails as by how it succeeds.

---

# Performance

Do not optimize backend code without understanding the bottleneck.

When performance matters, investigate:

* Database queries.
* Network calls.
* Serialization.
* CPU usage.
* Memory usage.
* Connection pools.
* Cache behavior.
* Queue throughput.
* Request concurrency.

Measure before and after significant optimizations.

For detailed performance guidance, use:

```text id="h4h2kw"
skills/performance/SKILL.md
```

---

# N+1 Queries

Be alert to repeated database queries caused by iterating over related data.

For example:

```text id="q4o5qf"
Fetch 100 users
    ↓
Fetch orders for user 1
Fetch orders for user 2
Fetch orders for user 3
...
```

This may create significant unnecessary database traffic.

Use appropriate techniques such as:

* Joins.
* Batching.
* Eager loading.
* Data loaders.
* Aggregation.

Choose the solution based on the actual data access pattern.

---

# Resource Management

Ensure resources are properly released.

Consider:

* Database connections.
* File handles.
* Streams.
* Timers.
* Workers.
* Event listeners.
* Transactions.

Leaks may only appear under sustained load.

---

# Graceful Shutdown

Backend services should handle shutdown intentionally.

Consider:

1. Stop accepting new work.
2. Allow active work to complete where appropriate.
3. Close connections.
4. Stop background workers.
5. Flush important telemetry.
6. Exit cleanly.

Do not terminate the process while critical operations are still running unless the environment requires immediate termination.

---

# API Definition of Done

A backend feature is complete when:

* The domain behavior is understood.
* Authentication requirements are clear.
* Authorization is enforced.
* Input is validated.
* Business rules are enforced server-side.
* Errors are handled consistently.
* External dependencies have appropriate failure behavior.
* Database operations are correct.
* Transactions are used where required.
* Concurrency concerns have been considered.
* Sensitive information is protected.
* Relevant security risks have been evaluated.
* Observability is sufficient.
* Appropriate tests exist.
* API compatibility has been considered.
* Performance is appropriate for the expected workload.

---

# Backend Review Checklist

Before considering backend code complete, ask:

### API

* Is the API contract clear?
* Are HTTP semantics appropriate?
* Are status codes meaningful?
* Are errors consistent?
* Is backward compatibility considered?

### Validation

* Is external input validated?
* Are domain rules enforced?
* Is runtime validation present?

### Authorization

* Is authentication required where appropriate?
* Is authorization checked?
* Is resource ownership verified?
* Could an ID be manipulated to access another resource?

### Data

* Are database constraints appropriate?
* Are transactions needed?
* Could concurrent operations produce inconsistent state?
* Are queries efficient enough?
* Could an N+1 query occur?

### Reliability

* What happens when a dependency fails?
* Are timeouts configured?
* Are retries safe?
* Are operations idempotent where required?

### Observability

* Can failures be diagnosed?
* Are important operations observable?
* Are logs structured?
* Are sensitive values excluded?

### Security

* Are secrets protected?
* Is untrusted input handled safely?
* Could injection occur?
* Could SSRF occur?
* Could CSRF occur?
* Could authorization be bypassed?
* Are dependencies trustworthy?

### Testing

* Is the successful path tested?
* Are important failure paths tested?
* Are authorization boundaries tested?
* Are important domain rules tested?
* Is regression coverage appropriate?

---

# Backend Definition of Done

A backend implementation should not be considered complete merely because the endpoint returns the expected response.

It is complete when the system also behaves correctly under:

```text id="i0gvq8"
Valid input
Invalid input
Unauthorized access
Missing resources
Concurrent operations
Dependency failures
Timeouts
Retries
Unexpected errors
Large inputs
Repeated requests
Production configuration
```

The backend should be correct under both normal operation and realistic failure conditions.
