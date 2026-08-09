# Frontend Development

## Purpose

Provide frontend engineering guidelines for building maintainable, predictable, accessible, and performant user interfaces.

These guidelines focus on application structure, component design, state management, React patterns, TypeScript usage, browser behavior, and user experience.

Testing, accessibility, and performance have dedicated skills and should be applied when relevant.

---

## General Frontend Principles

### Understand the Existing Application

Before implementing or modifying a frontend feature:

* Inspect the existing component structure.
* Identify established patterns.
* Look for existing components, hooks, utilities, and state management solutions.
* Understand how data flows through the relevant area.
* Check how similar functionality is already implemented.

Prefer consistency with the existing application over introducing a theoretically superior pattern.

---

## Component Design

### Single Responsibility

Components should have a clear responsibility.

Avoid components that simultaneously:

* Fetch multiple unrelated data sources.
* Manage complex business logic.
* Control application state.
* Contain large amounts of presentation logic.
* Handle unrelated user interactions.

When a component becomes difficult to understand, consider extracting meaningful responsibilities.

Do not split components merely because they are large. Extract when the separation improves clarity, reuse, testing, or maintainability.

### Composition Over Duplication

Prefer composing existing components over duplicating UI implementations.

Before creating a new component:

1. Search for an existing component with similar responsibilities.
2. Determine whether it can be reused.
3. Determine whether it can be extended safely.
4. Create a new component only when the responsibilities are meaningfully different.

Avoid creating generic components that have no clear reuse case.

### Component APIs

Component APIs should be:

* Small.
* Predictable.
* Explicit.
* Easy to understand.

Avoid excessive boolean props that create many implicit states.

For example, prefer a meaningful variant or state model over combinations such as `isSmall`, `isCompact`, `isPrimary`, `isDisabled`, and `isLoading` when those flags create ambiguous combinations.

---

## React

### Functional Components

Prefer functional components and React's current composition patterns.

Avoid unnecessary abstraction around simple components.

### Hooks

Use hooks to encapsulate reusable behavior and stateful logic.

Custom hooks should:

* Have a clear responsibility.
* Expose a minimal API.
* Avoid leaking implementation details.
* Be reusable only when reuse provides value.

Do not create a custom hook solely to move a few lines of code to another file.

### Effects

Treat `useEffect` as a synchronization mechanism, not as a general-purpose way to run code after rendering.

Before adding an effect, determine whether the behavior can instead be expressed through:

* Derived values.
* Event handlers.
* Memoized calculations when justified.
* State initialization.
* Existing application lifecycle mechanisms.

Avoid effects that only derive state from other state or props when the value can be calculated directly.

### Derived State

Prefer deriving values from existing state and props instead of storing duplicate state.

Avoid situations where two pieces of state can become inconsistent because one is derived from the other.

### State Ownership

Keep state as close as reasonably possible to the components that use it.

Lift state only when multiple components genuinely need to share it.

Do not introduce global state for local concerns.

Before introducing or expanding global state:

* Identify why local state is insufficient.
* Identify which components require the state.
* Consider whether the state represents application state or UI-local state.

### Controlled vs Uncontrolled Components

Choose controlled or uncontrolled behavior based on the requirements.

Do not make a component controlled solely because it is theoretically more flexible.

Avoid unnecessary synchronization between internal and external state.

---

## TypeScript in Frontend

Use TypeScript to make component contracts and application behavior safer.

### Props

Prefer explicit prop types that communicate the component's API.

Avoid overly generic props such as `Record<string, unknown>` when a meaningful domain type can be defined.

### Avoid `any`

Do not use `any` to bypass type errors.

If an external API or legacy boundary requires unsafe typing, isolate the unsafe boundary and provide a typed interface around it.

### Unions

Prefer discriminated unions when a component or domain has distinct states.

When states are mutually exclusive, model them as mutually exclusive types instead of several optional properties.

### Type Assertions

Avoid unnecessary type assertions.

A type assertion should not be used merely to make the compiler stop reporting an error.

Investigate why the type is not being inferred correctly first.

---

## Event Handling

Keep event handlers focused on the behavior they represent.

Avoid placing large amounts of business logic directly inside JSX event handlers.

When logic becomes complex or reusable, move it to an appropriate function, hook, or domain layer.

Do not create unnecessary wrapper functions when an existing handler can be passed directly.

---

## Data Flow

Make data flow easy to follow.

Prefer parent-to-child props when that is sufficient.

Avoid passing data through many unrelated layers when an established state or composition mechanism provides a clearer solution.

At the same time, do not introduce context or global state simply to avoid passing a small number of props.

---

## Business Logic

Keep significant business logic separate from presentation when doing so improves clarity and maintainability.

A component should primarily describe:

* What the UI renders.
* What user interactions occur.
* Which state it consumes.

Complex domain rules should not become buried inside JSX.

However, avoid creating excessive layers for trivial logic.

The goal is clear separation of responsibilities, not maximum abstraction.

---

## Forms

Forms should have:

* Clear state ownership.
* Explicit validation behavior.
* Predictable submission behavior.
* Appropriate loading and error states.
* Clear user feedback.

Avoid duplicating validation rules between multiple layers when a shared source of truth is practical.

Do not allow invalid states to be represented ambiguously.

---

## Loading, Error, and Empty States

User-facing asynchronous operations should consider relevant states:

* Initial/loading.
* Success.
* Empty result.
* Error.
* Retry when appropriate.

Do not assume that successful data retrieval always means there is content to display.

Do not show loading indicators indefinitely when the operation has already completed or failed.

---

## Conditional Rendering

Keep conditional rendering readable.

When conditions become complex:

* Extract meaningful boolean expressions.
* Use early returns when appropriate.
* Extract a component when the UI represents a distinct responsibility.

Avoid deeply nested conditional expressions.

Do not use clever one-liners at the expense of readability.

---

## Lists and Keys

When rendering collections:

* Use stable, meaningful keys.
* Prefer domain identifiers.
* Avoid array indexes as keys when list items can be reordered, inserted, or removed.

Do not generate random keys during rendering.

Understand that changing a React key can intentionally reset component state.

---

## Memoization

Do not add `useMemo`, `useCallback`, or `React.memo` by default.

Memoization should have a reason, such as:

* Preventing demonstrably expensive recalculation.
* Preventing unnecessary child renders when the optimization matters.
* Maintaining referential stability required by another optimization or API.

Prefer simple code until there is a meaningful reason to optimize.

Performance-specific decisions should follow the performance skill.

---

## Rendering Behavior

Be aware of React rendering behavior.

When investigating rendering problems:

* Determine what causes the render.
* Identify which state or props changed.
* Check whether the component actually needs to re-render.
* Inspect parent renders before assuming the child is the problem.

Do not fix rendering problems by blindly adding memoization.

---

## UI State

Distinguish between different types of state.

### Local UI State

Examples:

* Modal open/closed.
* Selected tab.
* Input value.
* Expanded section.

Prefer local component state.

### Shared UI State

Examples:

* Shared filters.
* Selection used by multiple components.
* UI state spanning a feature.

Use the simplest appropriate shared mechanism.

### Server State

Examples:

* API responses.
* Remote resources.
* Cached backend data.

Treat server state differently from local UI state when the application architecture provides appropriate mechanisms.

Do not automatically copy server data into local state.

---

## Browser and Platform Behavior

Do not assume browser behavior.

When implementing browser-dependent functionality:

* Understand the relevant browser APIs.
* Consider lifecycle and cleanup.
* Consider resize, scroll, focus, visibility, and navigation behavior where applicable.
* Clean up subscriptions, listeners, timers, and observers.

Avoid leaking event listeners or other resources.

---

## Responsive UI

Do not implement responsiveness by adding arbitrary breakpoints without understanding the layout.

Prefer layouts that adapt naturally when possible.

When breakpoints are necessary:

* Use existing project conventions.
* Keep breakpoint behavior predictable.
* Consider intermediate viewport sizes.
* Consider keyboard and touch interaction where relevant.

Do not assume that a UI that works at desktop and mobile extremes works at all sizes.

---

## Reuse and Abstraction

Before introducing a reusable abstraction, identify the repeated problem.

Good abstraction:

* Multiple components have the same meaningful behavior.
* The shared behavior is understood.
* The abstraction reduces meaningful duplication.

Bad abstraction:

* A single component has some reusable-looking code.
* Future reuse is only hypothetical.
* The abstraction adds complexity without a current benefit.

Prefer duplication over a poorly designed abstraction when the duplication is small and the requirements are not yet understood.

---

## Dependency Awareness

Before adding a frontend dependency:

* Search the existing project.
* Check whether equivalent functionality already exists.
* Consider bundle impact.
* Consider maintenance and compatibility.
* Follow project conventions.

Do not introduce a library simply because it provides a convenient wrapper around a small amount of functionality.

---

## Refactoring

When refactoring frontend code:

* Preserve observable behavior unless change is intentional.
* Keep the refactor focused.
* Avoid combining unrelated feature work with large refactors.
* Prefer incremental improvements.
* Ensure existing tests and validation remain meaningful.

A refactor should improve the code without silently changing product behavior.

---

## Frontend Definition of Done

Before considering a frontend implementation complete:

* The UI behavior matches the requirement.
* Existing project patterns are followed.
* Component responsibilities are clear.
* State ownership is appropriate.
* Data flow is understandable.
* TypeScript types are meaningful.
* Loading, error, and empty states are handled where relevant.
* Browser resources are cleaned up where necessary.
* No unnecessary abstractions or dependencies were introduced.
* Relevant testing and accessibility checks are applied through their respective skills.
* Relevant validation has been performed.
