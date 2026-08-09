# Debugging

## Purpose

Provide a systematic approach for investigating, diagnosing, and resolving software defects.

The goal of debugging is to identify and correct the root cause of a problem while minimizing unintended changes.

Do not treat debugging as a trial-and-error exercise.

---

## Core Debugging Principles

### Investigate Before Changing

Do not modify code immediately after seeing an error.

First:

1. Understand the reported behavior.
2. Identify where the failure occurs.
3. Inspect the relevant code.
4. Trace the execution or data flow.
5. Form one or more hypotheses.
6. Validate the most likely hypothesis.
7. Implement the smallest appropriate fix.
8. Verify the result.

The first plausible explanation is not necessarily the root cause.

---

## Reproduce the Problem

Whenever possible, reproduce the problem before fixing it.

Determine:

* What action triggers the problem.
* What input or state is required.
* Whether the behavior is deterministic.
* Whether the problem occurs in all environments.
* Whether the problem depends on timing, browser, device, or configuration.
* Whether the problem can be reduced to a smaller scenario.

If reproduction is not possible, clearly distinguish observed facts from assumptions.

Do not claim that a bug has been reproduced when it has not.

---

## Understand the Symptom

Separate the symptom from the cause.

For example:

```text
Symptom:
The menu disappears when the window is resized.

Possible causes:
- Event listener is not registered correctly.
- Listener is attached to the wrong target.
- Component is unmounted.
- State is reset during re-render.
- Resize handling contains stale state.
- CSS changes visibility.
```

Do not assume the first visible failure is the root cause.

---

## Trace the Data Flow

When debugging application behavior, trace:

```text
Input
  ↓
Event
  ↓
Handler
  ↓
State / Data
  ↓
Business Logic
  ↓
Rendering
  ↓
User-visible Result
```

Identify where the expected behavior diverges from the actual behavior.

For asynchronous operations, also consider:

```text
Request
  ↓
Loading
  ↓
Response / Error
  ↓
State Update
  ↓
Render
```

---

## Trace Control Flow

Understand which code paths are actually executed.

Do not assume that a function is responsible for a problem simply because its name appears related to the behavior.

Check:

* Callers.
* Conditions.
* Early returns.
* Error paths.
* Event handlers.
* Effects.
* Cleanup functions.
* Async callbacks.
* State transitions.

Follow the execution path that produces the observed behavior.

---

## Hypothesis-Driven Debugging

For non-trivial problems, explicitly form a hypothesis.

A useful hypothesis should have:

```text
Cause
↓
Expected evidence
↓
Validation method
```

Example:

```text
Hypothesis:
The component loses its state because its React key changes.

Evidence:
The component remounts when the parent state changes.

Validation:
Inspect the key value and verify mount/unmount behavior.
```

Only implement a fix after the hypothesis has sufficient evidence.

If the hypothesis is disproven, update the investigation rather than forcing the evidence to fit it.

---

## Root Cause Analysis

Prefer fixing the root cause over suppressing the symptom.

### Symptom Fix

```text
Error occurs
↓
Add conditional
↓
Error disappears
```

### Root Cause Fix

```text
Error occurs
↓
Identify invalid state
↓
Understand why invalid state exists
↓
Prevent invalid state
↓
Error disappears
```

A defensive check can still be appropriate when invalid input is expected, but do not use defensive checks to hide an underlying programming error.

---

## Minimal Fix

Once the root cause is understood:

* Make the smallest change that correctly fixes it.
* Preserve unrelated behavior.
* Avoid opportunistic refactoring.
* Avoid changing architecture unless the architecture is directly responsible for the defect.
* Avoid modifying multiple unrelated files without justification.

A debugging task is not an excuse for a broad cleanup.

---

## Regression Prevention

After identifying a bug:

* Determine why existing tests did not catch it.
* Add or improve a regression test when appropriate.
* Ensure the test represents the broken behavior.
* Verify the test fails against the buggy behavior when practical.
* Verify the fix makes the test pass.

If no test is appropriate, explain why.

---

## Logs and Diagnostics

Use diagnostic information deliberately.

Useful sources may include:

* Error messages.
* Stack traces.
* Browser console.
* Network requests.
* Network responses.
* React warnings.
* Build output.
* Test failures.
* Runtime state.
* Performance traces.
* Browser developer tools.

Do not add permanent logging merely to investigate a temporary issue unless the logging provides lasting operational value.

Do not ignore warnings because the application appears to work.

Warnings often identify the underlying problem.

---

## Browser Debugging

For browser-related issues, inspect the actual browser behavior rather than relying only on source code.

When relevant, inspect:

* DOM.
* Computed styles.
* Event listeners.
* Network activity.
* Console errors.
* Focus state.
* Layout.
* Viewport dimensions.
* Storage.
* Browser APIs.
* Rendering behavior.

When a problem occurs only in a specific browser, verify browser-specific behavior before introducing a generic workaround.

---

## React Debugging

When debugging React behavior, consider:

* Component mounting and unmounting.
* State updates.
* Prop changes.
* Parent re-renders.
* Effect execution.
* Effect cleanup.
* Context updates.
* Component keys.
* Conditional rendering.
* Strict Mode behavior.
* Stale closures.
* Async state updates.

Do not assume that a render means the component's meaningful state changed.

Do not assume that `useEffect` runs only once unless its dependency behavior actually guarantees it.

When state appears to "reset", investigate whether the component was remounted.

---

## Async Debugging

For asynchronous bugs, establish the lifecycle:

```text
Started
  ↓
Pending
  ↓
Resolved / Rejected
  ↓
State update
  ↓
Render
```

Investigate:

* Race conditions.
* Stale closures.
* Requests completing out of order.
* Cancellation.
* Component unmounting.
* State updates after unmount.
* Loading state transitions.
* Error handling.
* Retry behavior.

Do not fix asynchronous problems with arbitrary delays.

Avoid `setTimeout`-based workarounds unless the timing behavior is itself intentional and understood.

---

## State Bugs

When state behaves unexpectedly:

1. Identify the state owner.
2. Identify every place that can modify it.
3. Identify what triggers each modification.
4. Identify whether multiple sources of truth exist.
5. Check whether state is derived unnecessarily.
6. Check whether updates depend on stale values.
7. Check whether the component has been remounted.

Prefer eliminating conflicting sources of truth over adding synchronization logic.

---

## Event and Listener Bugs

When debugging event-related behavior:

* Identify the event target.
* Verify the listener is registered.
* Verify the listener is removed appropriately.
* Check whether multiple listeners are being registered.
* Check listener dependencies and closures.
* Check whether the target changes.
* Check whether component lifecycle affects registration.

Be particularly careful with:

* `resize`.
* `scroll`.
* `keydown`.
* `click`.
* `pointer` events.
* `visibilitychange`.
* `online` / `offline`.

Do not add duplicate listeners as a workaround for an unclear lifecycle problem.

---

## Layout and CSS Bugs

When the problem appears visual:

1. Inspect the DOM.
2. Inspect computed styles.
3. Identify which rule determines the behavior.
4. Check inheritance and specificity.
5. Check containing blocks and positioning.
6. Check dimensions and overflow.
7. Check responsive rules.
8. Check whether JavaScript is changing the layout.

Do not immediately modify CSS based on appearance alone.

Understand which rule is producing the result.

---

## Build and Tooling Bugs

When a problem occurs during build, test, bundling, or development tooling:

* Identify the exact command that fails.
* Read the complete error.
* Determine whether the failure occurs during parsing, transformation, resolution, bundling, execution, or environment setup.
* Inspect relevant configuration.
* Check dependency versions.
* Check whether the problem is project-specific or tooling-wide.

Do not modify application code to fix a tooling problem.

When the project uses `esbuild-jest`, inspect the Jest and esbuild configuration before replacing the transformation setup.

---

## Dependency-Related Bugs

When a dependency appears responsible:

* Identify the dependency version.
* Check how it is being used.
* Determine whether the problem exists in the dependency or in the integration.
* Search the codebase for other usages.
* Check whether the project already contains a workaround.
* Consider compatibility with the current environment.

Do not upgrade or downgrade dependencies as the first debugging step.

A dependency change should have evidence supporting it.

---

## Environment-Specific Bugs

If a problem occurs only in a particular environment, compare:

* Runtime versions.
* Browser versions.
* Operating system.
* Environment variables.
* Build configuration.
* Dependency versions.
* Feature flags.
* Network conditions.
* Production/development differences.

Avoid introducing environment-specific workarounds until the environmental difference is understood.

---

## Failed Fixes

When a proposed fix does not work:

* Do not stack another workaround on top of it immediately.
* Re-evaluate the original hypothesis.
* Determine what the failed fix tells us.
* Return to the observed behavior.
* Form a new hypothesis based on evidence.

A failed fix is diagnostic information.

---

## When to Stop Investigating

Do not endlessly investigate a problem when the cause is sufficiently understood.

Once:

* The root cause is identified.
* The fix is clear.
* The change is appropriately scoped.
* Relevant validation is available.

Implement the fix and verify it.

Avoid speculative refactoring after the defect has been resolved.

---

## Debugging Communication

When reporting a debugging result, clearly distinguish:

### Observed

What was directly verified.

### Root Cause

Why the problem occurred.

### Fix

What was changed.

### Validation

What was tested or verified.

### Remaining Risk

Anything that could not be fully verified.

Do not present assumptions as facts.

---

## Debugging Definition of Done

A debugging task is complete when:

* The failure has been reproduced or sufficiently understood.
* The root cause has been identified.
* The fix addresses the root cause.
* The change is appropriately scoped.
* Existing behavior is preserved where appropriate.
* A regression test has been added or the reason for not adding one is understood.
* Relevant validation has been performed.
* No temporary debugging code remains.
* Important limitations or remaining risks are communicated.
