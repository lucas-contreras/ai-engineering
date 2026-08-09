# Core Development

## Purpose

Provide general software engineering principles that should apply to any development task, regardless of language, framework, or project.

These guidelines prioritize correctness, maintainability, simplicity, and minimal unintended impact.

---

## General Principles

### Understand Before Changing

Before modifying code:

- Inspect the relevant code and surrounding context.
- Understand existing patterns and conventions.
- Identify dependencies and consumers that may be affected.
- Do not assume the requested change is isolated without verifying it.

For non-trivial tasks, briefly explain the intended approach before making significant changes.

### Prefer Simple Solutions

- Prefer the simplest solution that correctly solves the problem.
- Do not introduce abstractions without a concrete reason.
- Avoid unnecessary design patterns, layers, wrappers, or indirection.
- Do not optimize for hypothetical future requirements.
- Prefer existing project patterns over introducing new ones.

Complexity should be justified by an actual requirement.

### Minimize Scope

- Change only what is necessary to solve the task.
- Do not refactor unrelated code while implementing a feature or fixing a bug.
- Avoid broad formatting changes.
- Avoid unrelated dependency upgrades.
- Do not rename or reorganize files unless it contributes directly to the task.

A small, focused change is preferable to a broad cleanup.

### Preserve Existing Behavior

Unless explicitly requested:

- Do not change unrelated behavior.
- Do not remove existing functionality.
- Do not weaken validation or error handling.
- Do not remove or modify tests simply to make them pass.
- Treat existing behavior as intentional until there is evidence otherwise.

### Reuse Before Creating

Before creating something new:

1. Search the codebase for existing implementations.
2. Check whether an existing utility, component, helper, or abstraction can be reused.
3. Extend existing functionality when appropriate.
4. Create something new only when reuse would make the design worse.

Avoid duplicate implementations.

---

## Type Safety

When working in a typed language:

- Prefer strong, explicit types where they improve correctness.
- Avoid `any` or equivalent escape hatches unless there is a clear technical reason.
- Do not silence type errors without understanding their cause.
- Prefer narrowing and safe abstractions over unsafe casts.
- Keep type definitions close to the domain they represent when appropriate.

Type safety should help prevent bugs, not merely satisfy the compiler.

---

## Error Handling

- Handle errors at the appropriate boundary.
- Do not silently swallow errors.
- Preserve useful error information.
- Do not add defensive error handling everywhere without understanding the failure mode.
- Avoid catching errors only to rethrow them unchanged unless there is a meaningful reason.

Distinguish between:

- Expected failures that should be handled.
- Unexpected failures that should surface clearly.
- Programmer errors that should be fixed rather than hidden.

---

## Dependencies

Before adding a dependency:

- Check whether the project already provides equivalent functionality.
- Consider whether the dependency is justified by the problem.
- Prefer existing dependencies when they are appropriate.
- Avoid adding a dependency for trivial functionality.
- Consider maintenance, bundle size, security, and compatibility implications.

Do not introduce dependencies casually.

---

## Validation

After making changes:

1. Run the most relevant tests.
2. Run type checking when applicable.
3. Run linting or formatting checks when applicable.
4. Verify the changed behavior.
5. Check for unintended side effects.

Do not assume code is correct merely because it compiles.

If validation cannot be performed, explicitly state what could not be verified.

---

## Debugging Mindset

When something fails:

- Do not immediately apply the first plausible fix.
- Identify the actual failure.
- Investigate the root cause.
- Form a hypothesis.
- Validate the hypothesis.
- Make the smallest appropriate change.
- Re-run the relevant validation.

Prefer root-cause fixes over symptom suppression.

---

## Communication

When explaining work:

- Be concise and technically precise.
- State important assumptions.
- Explain meaningful trade-offs.
- Distinguish facts from assumptions.
- Do not claim that code was tested if it was not.
- Do not claim that a command succeeded unless it was actually executed.

For complex changes, summarize:

1. What changed.
2. Why it changed.
3. How it was validated.
4. Any remaining risks or limitations.

---

## Decision Making

When multiple solutions are valid:

- Prefer the solution that best fits existing project conventions.
- Consider maintainability and readability.
- Consider complexity introduced versus complexity removed.
- Consider performance only when it is relevant to the actual problem.
- Consider future extensibility only when there is evidence it will matter.

Do not optimize for theoretical perfection.

---

## Anti-Patterns

Avoid:

- Over-engineering.
- Premature optimization.
- Unnecessary abstractions.
- Large unrelated refactors.
- Duplicate utilities.
- Unnecessary dependencies.
- Suppressing errors without understanding them.
- Changing tests to accommodate incorrect behavior.
- Guessing about project behavior instead of inspecting the code.
- Claiming validation that did not happen.

---

## Definition of Done

A development task is not considered complete merely because the implementation exists.

Before considering the task complete:

- The requested behavior is implemented.
- Existing behavior is preserved unless intentionally changed.
- Relevant tests exist or have been updated.
- Relevant validation has been executed.
- No obvious unrelated regressions were introduced.
- The implementation follows the project's existing conventions.
- Important assumptions or limitations are communicated.