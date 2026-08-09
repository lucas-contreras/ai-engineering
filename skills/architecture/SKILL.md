# Architecture

## Purpose

Provide guidelines for designing, evaluating, and evolving software architecture.

Architecture should make the system easier to understand, change, test, and operate.

Prefer the simplest architecture that adequately supports the current requirements and foreseeable needs.

Do not introduce architectural complexity without a concrete problem that justifies it.

---

## Core Architecture Principles

### Architecture Serves the Problem

Architecture should emerge from:

* Business requirements.
* Domain complexity.
* Team needs.
* Change frequency.
* Scale.
* Reliability requirements.
* Performance requirements.
* Security requirements.
* Integration boundaries.
* Existing system constraints.

Do not choose an architecture because it is fashionable or because it is considered universally "best".

---

## Understand Before Designing

Before proposing architectural changes:

1. Inspect the existing architecture.
2. Identify major application boundaries.
3. Identify data flow.
4. Identify dependencies.
5. Identify state ownership.
6. Identify external integrations.
7. Identify existing conventions.
8. Identify the actual problem.
9. Identify constraints.
10. Evaluate the smallest viable architectural change.

Do not redesign the system before understanding why the current architecture is insufficient.

---

## Prefer Incremental Evolution

Existing systems should generally evolve incrementally.

When changing architecture:

* Preserve working behavior.
* Introduce boundaries gradually.
* Migrate one area at a time when practical.
* Avoid unnecessary rewrites.
* Keep intermediate states understandable.
* Remove obsolete architecture after migration is complete.

Do not recommend rewriting an existing system unless there is strong evidence that incremental evolution is insufficient.

---

## Separation of Concerns

Separate responsibilities when doing so improves:

* Understandability.
* Testability.
* Reuse.
* Independent change.
* Maintainability.

Common concerns may include:

* Presentation.
* UI state.
* Domain logic.
* Data access.
* External integrations.
* Configuration.
* Infrastructure.

Do not create a separate abstraction for every function or file merely to satisfy an abstract separation principle.

---

## Dependency Direction

Dependencies should flow toward stable abstractions and well-defined boundaries.

When useful, structure dependencies so that:

```text id="n2f7cz"
Presentation
     ↓
Application / Domain
     ↓
Infrastructure
```

The exact structure depends on the application.

Do not force every frontend application into a backend-style layered architecture.

The important principle is to prevent unstable implementation details from unnecessarily controlling higher-level business behavior.

---

## Domain Logic

Business rules should be placed where they can be understood and maintained.

Avoid burying significant domain rules inside:

* JSX.
* UI event handlers.
* API clients.
* Database-specific code.
* Infrastructure adapters.

Domain logic should not depend unnecessarily on presentation concerns.

At the same time, do not create a domain layer for trivial UI behavior that has no meaningful domain complexity.

---

## Application Boundaries

Define boundaries around meaningful responsibilities.

A good boundary should clarify:

* What the module owns.
* What it exposes.
* What it depends on.
* What it should not know about.

Avoid modules that expose their entire internal implementation.

Prefer small public interfaces over unrestricted access to internal structures.

---

## Modules

A module should have a coherent purpose.

Avoid modules that combine unrelated responsibilities simply because they are used by the same feature.

Avoid excessive fragmentation where every small function or component becomes its own architectural module.

The goal is meaningful cohesion, not maximum file count.

---

## Feature-Oriented Organization

For frontend applications with substantial feature complexity, consider organizing code around business capabilities or features rather than only technical types.

For example:

```text id="2w5tqa"
features/
  users/
  billing/
  authentication/
  reporting/
```

This can be preferable to structures that scatter one feature across:

```text id="5x0h1k"
components/
hooks/
services/
utils/
types/
```

However, follow the existing project conventions when they are working well.

Do not reorganize the entire repository merely to adopt feature-based structure.

---

## Component Boundaries

Component boundaries should reflect meaningful UI responsibilities.

A component should generally own:

* Its relevant presentation.
* Its local interaction behavior.
* State that naturally belongs to it.

Avoid components that become:

* Global state managers.
* API orchestration layers.
* Domain rule engines.
* Generic utility containers.

When a component accumulates unrelated responsibilities, consider introducing an appropriate boundary.

---

## State Architecture

Classify state before choosing where to store it.

Consider:

### Local UI State

Examples:

* Modal visibility.
* Selected tab.
* Expanded section.
* Temporary form input.

Prefer local state.

### Shared Feature State

Examples:

* Filters shared across feature components.
* Feature-level selections.
* Multi-component UI workflows.

Use an appropriate feature-level mechanism.

### Global Application State

Examples:

* Authenticated user context.
* Application-wide configuration.
* State genuinely consumed across unrelated features.

Use global state only when the state is genuinely global.

### Server State

Examples:

* API resources.
* Cached remote data.
* Backend-derived state.

Treat server state separately from local UI state when the architecture supports it.

Do not copy every API response into a global client-side store.

---

## API Boundaries

Keep external APIs behind clear boundaries when doing so provides value.

Consider isolating:

* HTTP details.
* Serialization.
* Authentication headers.
* Error mapping.
* External API-specific data shapes.

The rest of the application should not need to understand every detail of an external service.

Avoid excessive wrapper layers that simply rename every API function without adding meaningful behavior.

---

## Data Transformation

External data should be transformed when the external representation should not leak into the rest of the application.

For example:

```text id="7g1f6n"
External API
    ↓
Adapter / Mapper
    ↓
Application model
    ↓
UI
```

Do not introduce mappings purely for theoretical purity.

Use them when they protect the application from external contracts or clarify domain behavior.

---

## Dependency Injection

Use dependency injection when it solves a real problem such as:

* Replacing implementations.
* Testing boundaries.
* Managing infrastructure dependencies.
* Supporting multiple environments.

Do not introduce a dependency injection framework merely because dependency injection is considered a best practice.

Simple function parameters or module boundaries are often sufficient.

---

## Abstractions

Create abstractions around meaningful variation or complexity.

Good reasons include:

* Multiple implementations exist.
* External systems need isolation.
* A domain concept has meaningful behavior.
* A complex operation needs a stable interface.
* A boundary improves testability.

Bad reasons include:

* "We may need it someday."
* "Clean architecture says we need an interface."
* "This function is more than 20 lines."
* "Every service should have an interface."

Prefer concrete implementations until meaningful variation exists.

---

## Reuse

Reuse should follow actual common behavior.

Do not create a shared abstraction merely because two things look similar.

Before sharing code, determine whether:

* The behavior is genuinely the same.
* The behavior is expected to evolve together.
* The abstraction has a coherent responsibility.

If two implementations are likely to evolve differently, keeping them separate may be preferable.

---

## Coupling

Identify unnecessary coupling between modules.

Common forms include:

* Shared mutable state.
* Circular dependencies.
* Direct knowledge of internal implementation.
* Shared low-level utilities with unclear ownership.
* UI components depending directly on infrastructure details.

Reduce coupling when it prevents independent change or makes behavior difficult to understand.

Do not eliminate all coupling.

Some coupling is necessary and healthy when it represents a real relationship between concepts.

---

## Cohesion

Prefer modules whose contents belong together conceptually.

High cohesion generally means:

* Related behavior is located together.
* Responsibilities are understandable.
* Changes to one concept are localized.

When a module repeatedly changes for unrelated reasons, consider whether its responsibilities should be separated.

---

## Circular Dependencies

Avoid circular dependencies between modules.

When a cycle exists:

1. Identify why the dependency exists.
2. Determine whether one dependency can move to a lower-level abstraction.
3. Consider extracting shared behavior.
4. Consider changing ownership.
5. Remove the cycle without introducing unnecessary abstraction.

Do not solve every circular dependency by creating a generic "common" module.

---

## Error Boundaries

Architect systems so that failures occur at understandable boundaries.

Consider:

* API failures.
* Validation failures.
* Component rendering failures.
* Authentication failures.
* Permission failures.
* Infrastructure failures.

Map low-level errors into meaningful application behavior when appropriate.

Do not expose infrastructure-specific errors directly to users unless they are meaningful to them.

---

## Configuration

Separate configuration from application logic when appropriate.

Consider:

* Environment-specific values.
* Feature flags.
* API endpoints.
* Runtime configuration.
* Build-time configuration.

Do not hardcode environment-specific behavior throughout the codebase.

Avoid creating a configuration abstraction that merely wraps a few constants without providing meaningful value.

---

## Feature Flags

Use feature flags when functionality needs controlled rollout, experimentation, or environment-specific activation.

When using flags:

* Define ownership.
* Define expected lifecycle.
* Remove obsolete flags.
* Avoid deeply nested conditional behavior.
* Keep default behavior explicit.

Feature flags should not become permanent hidden architecture.

---

## Scalability

Architecture should consider future growth without designing for hypothetical scale.

When evaluating scalability, consider:

* Data volume.
* Request volume.
* Concurrent users.
* Rendering complexity.
* Team size.
* Deployment frequency.
* Number of features.
* Number of integrations.

Do not introduce distributed systems, microservices, complex caching, or other infrastructure solely because the application might someday become large.

---

## Performance and Architecture

Architectural decisions can affect performance.

Consider:

* Data flow.
* Rendering boundaries.
* Network requests.
* Bundle boundaries.
* State subscriptions.
* Serialization.
* Caching.
* Server/client responsibilities.

Do not use architecture as a substitute for profiling.

When performance is the motivation for architectural change, provide evidence that the architecture contributes meaningfully to the bottleneck.

Use the performance skill for measurement and optimization.

---

## Testing and Architecture

Architecture should make important behavior testable.

Consider whether boundaries allow:

* Unit testing of pure logic.
* Integration testing of feature workflows.
* Isolation of external dependencies.
* Deterministic behavior.

Do not distort the architecture solely to make trivial tests easier.

The goal is testable behavior, not maximum mockability.

---

## Accessibility and Architecture

Accessibility requirements should influence architectural decisions when they affect:

* Component APIs.
* Focus management.
* Navigation.
* Dialog behavior.
* Form architecture.
* Design system components.
* Interaction patterns.

Do not create abstractions that make accessible behavior difficult to implement or verify.

Follow the accessibility skill for implementation-specific requirements.

---

## Security Boundaries

Architecture should clearly identify security-sensitive boundaries.

Consider:

* Authentication.
* Authorization.
* User input.
* External integrations.
* Sensitive data.
* Client/server trust boundaries.
* Token handling.

Never treat client-side checks as sufficient authorization for protected operations.

Security-sensitive architectural decisions should preserve server-side enforcement.

---

## Migration Strategy

When replacing an architectural pattern:

1. Define the desired target state.
2. Identify the current state.
3. Identify the smallest migration boundary.
4. Define compatibility requirements.
5. Migrate incrementally when practical.
6. Validate behavior after each meaningful step.
7. Remove obsolete paths.

Avoid creating two competing architectures indefinitely.

A migration is incomplete until the old path is intentionally retired or documented as a supported legacy boundary.

---

## Architectural Trade-offs

Every meaningful architectural decision should consider trade-offs.

When comparing approaches, evaluate:

* Complexity.
* Maintainability.
* Performance.
* Testability.
* Scalability.
* Developer experience.
* Operational cost.
* Migration cost.
* Risk.

Do not describe an architecture as universally superior.

State why it is appropriate for the specific problem.

---

## Architectural Decision Records

For significant architectural decisions, consider documenting:

```text id="svt7ol"
Context
Decision
Alternatives considered
Trade-offs
Consequences
```

Use ADRs when a decision is important enough that future developers may need to understand why it was made.

Do not create an ADR for trivial implementation choices.

---

## Architecture Review Checklist

Before approving a significant architectural change, ask:

* What concrete problem does this solve?
* Why is the current architecture insufficient?
* What alternatives were considered?
* What complexity does this introduce?
* What dependencies does it create?
* Does it improve or worsen coupling?
* Does it improve cohesion?
* How does it affect testing?
* How does it affect performance?
* How does it affect accessibility?
* How does it affect security?
* How difficult is migration?
* How will obsolete architecture be removed?
* Is the complexity justified by the problem?

---

## Architecture Definition of Done

An architectural change is complete when:

* The underlying problem is clearly understood.
* The proposed architecture addresses the actual problem.
* Existing conventions have been considered.
* The solution does not introduce unnecessary complexity.
* Responsibilities and boundaries are clear.
* Dependencies flow intentionally.
* Coupling is understood.
* Important trade-offs are documented.
* Migration is appropriately scoped.
* Testing and validation strategies are clear.
* Performance, accessibility, and security implications have been considered where relevant.
* Obsolete architecture has been removed or intentionally retained.
