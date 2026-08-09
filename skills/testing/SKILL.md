# Testing

## Purpose

Provide guidelines for writing reliable, maintainable, and meaningful automated tests.

Tests should increase confidence in the system's behavior, catch regressions, and document important expected behavior without becoming tightly coupled to implementation details.

These guidelines apply primarily to frontend and application testing, with specific guidance for Jest, React Testing Library, TypeScript, and esbuild-jest when those technologies are present in the project.

---

## Testing Philosophy

### Test Behavior, Not Implementation

Tests should verify what the user or consuming code can observe.

Prefer testing:

* Rendered output.
* User interactions.
* Public component behavior.
* Returned values.
* Observable state changes.
* Error handling.
* Integration between relevant units.

Avoid testing:

* Private implementation details.
* Internal variable names.
* Exact hook implementation.
* Internal state structure unless it is part of the public contract.
* Function calls that are irrelevant to observable behavior.

A test should remain useful if the implementation is refactored without changing the expected behavior.

---

## Before Writing Tests

Before adding or modifying tests:

1. Inspect the existing test structure.
2. Search for similar tests.
3. Identify existing test utilities and helpers.
4. Understand the component or function being tested.
5. Understand existing project conventions.
6. Determine whether the behavior is best covered by a unit, integration, or higher-level test.

Reuse existing testing patterns whenever they are appropriate.

Do not introduce a new testing approach when the project already has an established one without a clear reason.

---

## Test Structure

Tests should be easy to understand.

Prefer a structure that clearly communicates:

```text
Arrange
Act
Assert
```

Use descriptive test names that explain the expected behavior.

Prefer:

```text
renders an error message when the request fails
```

over:

```text
should work
```

Avoid test names that describe implementation details.

---

## Test Cases

Tests should prioritize meaningful behavior and important edge cases.

Consider:

* Normal behavior.
* Boundary conditions.
* Empty states.
* Invalid input.
* Error states.
* Loading states.
* User interactions.
* Permission or access conditions when applicable.
* Important asynchronous behavior.

Do not create tests solely to increase coverage numbers.

Coverage is a signal, not the objective.

---

## React Testing Library

When using React Testing Library:

### Prefer User-Observable Queries

Prefer queries in approximately this order:

1. Accessible role.
2. Accessible name.
3. Label.
4. Placeholder when appropriate.
5. Visible text.
6. Test ID as a last resort.

Prefer:

```text
getByRole
getByLabelText
getByText
```

over selectors based on implementation details.

Avoid querying by:

* CSS classes.
* Internal component structure.
* Generated DOM structure.
* Arbitrary attributes.

Use `data-testid` only when there is no meaningful user-facing or semantic way to identify the element.

### Accessibility-Aligned Queries

Prefer queries that reflect how a user or assistive technology identifies an element.

For example, prefer a button's accessible role and name over selecting a specific DOM element.

Testing-library queries should reinforce accessible UI rather than work around inaccessible markup.

---

## User Interactions

When simulating user behavior:

* Prefer `userEvent` for realistic interactions when available.
* Test the behavior produced by the interaction.
* Avoid directly manipulating implementation state.
* Avoid manually dispatching low-level events when a higher-level user interaction is available.

Prefer:

```text
user clicks the button
→ expected UI changes
```

over:

```text
invoke internal click handler
→ verify internal state
```

---

## Async Behavior

When testing asynchronous behavior:

* Wait for observable outcomes.
* Avoid arbitrary timeouts.
* Avoid race-condition-prone assertions.
* Use the appropriate async testing utilities.

Prefer waiting for the expected UI state rather than waiting for a fixed amount of time.

Avoid:

```text
sleep 1000ms
then assert
```

when a deterministic condition can be awaited.

---

## Mocking

### Mock Only What Needs Isolation

Do not mock dependencies automatically.

Before introducing a mock, determine:

* Why the dependency needs to be isolated.
* Whether using the real implementation is safe.
* Whether the dependency performs external I/O.
* Whether the mock improves determinism.
* Whether the mock makes the test less representative.

Prefer real implementations for simple, deterministic dependencies.

Mock external systems and boundaries when necessary.

Examples may include:

* Network requests.
* Browser APIs unavailable in the test environment.
* External services.
* Time when deterministic time is required.
* Non-deterministic external dependencies.

### Avoid Over-Mocking

Over-mocked tests can pass while the real application is broken.

Avoid mocking the system under test merely to make assertions easier.

Do not mock internal implementation details when the behavior can be tested directly.

---

## Jest

When Jest is the project's test runner:

* Follow the existing Jest configuration.
* Reuse existing setup files and utilities.
* Keep tests deterministic.
* Clean up mocks and test state appropriately.
* Avoid global state leaking between tests.
* Use focused assertions.

Do not introduce alternative test runners without a project-level decision.

### esbuild-jest

When the project uses `esbuild-jest`:

* Use the existing `esbuild-jest` configuration.
* Do not replace it with `jest-esbuild` unless the project explicitly decides to migrate.
* Do not introduce a second transformation strategy unnecessarily.
* Preserve the project's existing TypeScript and JSX transformation behavior.

When a test fails because of transformation or module behavior, inspect the existing Jest and esbuild configuration before changing the test itself.

---

## Mock Lifecycle

Mocks should not leak between tests.

Use the appropriate Jest cleanup mechanism according to the project's conventions.

Understand the difference between:

* Clearing mock call history.
* Resetting mock implementations.
* Restoring original implementations.

Do not use the strongest reset mechanism by default if it removes configuration required by other tests.

---

## Timers

When testing time-dependent behavior:

* Use Jest's fake timers when appropriate.
* Keep time deterministic.
* Restore timer behavior after the test.
* Avoid unnecessary real-time delays.

Do not make tests slower simply to simulate time passing.

---

## Network Requests

When testing components that depend on network requests:

* Prefer the project's established request mocking strategy.
* Test loading, success, empty, and error states where relevant.
* Avoid coupling tests to real external services.
* Keep request behavior deterministic.

Do not make unit or component tests dependent on external network availability.

---

## Test Isolation

Each test should be understandable and independently executable.

Avoid tests that depend on:

* Execution order.
* Shared mutable state.
* Previous test results.
* External services.
* Developer-specific environment state.

Clean up resources created by tests, including:

* Event listeners.
* Timers.
* DOM state.
* Subscriptions.
* Mocks.
* Temporary resources.

---

## Assertions

Assertions should be specific enough to detect meaningful regressions without being unnecessarily brittle.

Prefer assertions about important behavior.

Avoid asserting the entire DOM tree when only one or two pieces of behavior matter.

Avoid snapshot tests for large dynamic structures unless snapshots provide meaningful value.

A test should fail for a meaningful regression, not for an irrelevant implementation change.

---

## Snapshots

Use snapshots selectively.

Before adding a snapshot, ask:

* Does the output have meaningful structural stability?
* Will the snapshot clearly communicate an intended change?
* Is reviewing snapshot changes practical?

Avoid large snapshots that are difficult to understand and easy to approve without inspection.

Prefer explicit assertions when they communicate behavior more clearly.

---

## Coverage

Coverage should be used to identify untested areas, not as the primary measure of test quality.

High coverage does not guarantee meaningful tests.

Do not:

* Add meaningless assertions solely for coverage.
* Test trivial implementation details solely to increase coverage.
* Remove useful tests because coverage thresholds are already satisfied.

Prioritize important behavior and risk.

---

## Regression Tests

When fixing a bug:

1. Reproduce or understand the failure.
2. Identify the root cause.
3. Add a regression test that fails before the fix when practical.
4. Implement the fix.
5. Verify that the regression test passes.
6. Run relevant existing tests.

The regression test should protect the behavior that was previously broken, not merely reproduce the implementation used in the fix.

---

## Test Duplication

Avoid repetitive setup when existing helpers provide meaningful reuse.

However, do not create abstractions that hide important test behavior.

Test code should remain explicit enough that a developer can understand what each test is verifying.

Prefer small, focused helpers over large test frameworks built inside the test suite.

---

## Test Naming

Test names should describe behavior and expected outcomes.

Good:

```text
displays an error message when saving fails
disables the submit button while saving
allows the user to remove a selected item
```

Avoid:

```text
calls handleSubmit
sets loading to true
renders component correctly
```

unless the implementation detail itself is part of the contract.

---

## Unit vs Integration Tests

Choose the smallest test scope that provides meaningful confidence.

### Unit Tests

Use when behavior can be tested independently and isolation provides value.

Examples:

* Pure functions.
* Data transformations.
* Validation logic.
* Small utilities.

### Integration Tests

Use when multiple pieces must work together.

Examples:

* Component + state management.
* Component + API boundary.
* User interaction + resulting UI state.
* Multiple components participating in one workflow.

Do not force every behavior into a unit test if integration provides stronger confidence.

---

## Test Maintenance

When production code changes:

* Update tests when behavior intentionally changes.
* Preserve tests that still represent valid behavior.
* Remove obsolete tests when the behavior they describe no longer exists.
* Do not modify tests merely to make an incorrect implementation pass.

When a test becomes difficult to maintain, investigate whether it is testing the wrong abstraction level.

---

## Test Failures

When a test fails:

1. Read the failure carefully.
2. Determine whether the failure is in the test, implementation, or environment.
3. Reproduce the behavior.
4. Inspect relevant code.
5. Form a hypothesis.
6. Make the smallest appropriate change.
7. Re-run the relevant tests.

Do not automatically modify the test to match the current implementation.

---

## Definition of Done

A testing task is complete when:

* Relevant behavior is covered.
* Tests describe observable behavior.
* Important edge cases are considered.
* Tests are deterministic.
* Mocks are limited to appropriate boundaries.
* Tests do not rely on external services unless explicitly intended.
* Existing project testing conventions are followed.
* Relevant tests have been executed.
* Failures have been investigated rather than blindly suppressed.
* The test suite remains maintainable.
