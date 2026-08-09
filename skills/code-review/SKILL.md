# Code Review

## Purpose

Provide a systematic approach for reviewing code for correctness, maintainability, security, accessibility, performance, testing quality, and architectural consistency.

A good code review should identify meaningful problems before they reach production.

Do not treat code review as a style audit.

---

# Core Review Principles

## Review for Problems, Not Preferences

Prioritize findings that can:

* Cause incorrect behavior.
* Introduce regressions.
* Create security vulnerabilities.
* Break accessibility.
* Cause meaningful performance problems.
* Make the system difficult to maintain.
* Violate important architectural boundaries.
* Make future changes unnecessarily risky.

Do not report personal preferences as defects.

If a concern is subjective, clearly identify it as a suggestion rather than a problem.

---

## Understand the Change First

Before reviewing code:

1. Understand the requested behavior.
2. Read the relevant surrounding code.
3. Identify the intended change.
4. Understand existing patterns.
5. Inspect related tests.
6. Identify architectural boundaries.
7. Identify applicable skills.
8. Review the complete change rather than isolated lines.

Do not review a changed line without understanding the context in which it operates.

---

# Review the Diff

Start with the actual change.

For each meaningful change, ask:

* What behavior changed?
* Why did it change?
* What assumptions does it introduce?
* What existing behavior could it affect?
* What code depends on it?
* What happens in edge cases?
* What happens when something fails?
* Does the change introduce a new trust boundary or modify an existing one?

Avoid expanding the review into unrelated parts of the codebase unless they are necessary to understand the change.

---

# Correctness First

The first priority is correctness.

Look for:

* Incorrect logic.
* Incorrect conditions.
* Missing edge cases.
* Incorrect state transitions.
* Race conditions.
* Incorrect async behavior.
* Incorrect error handling.
* Data corruption.
* Unexpected side effects.
* Incorrect assumptions about external data.
* Incorrect browser behavior.

A stylistically excellent implementation that is functionally incorrect is still a bad implementation.

---

# Regression Risk

Consider how the change interacts with existing behavior.

Look for:

* Changed public APIs.
* Changed component behavior.
* Changed state ownership.
* Changed event handling.
* Changed navigation.
* Changed error handling.
* Changed default behavior.
* Changed configuration.
* Changed dependency behavior.

Ask what existing users or consumers might experience differently.

---

# Edge Cases

Consider relevant edge cases based on the feature.

Examples include:

* Empty data.
* Missing data.
* Null or undefined values.
* Duplicate values.
* Large datasets.
* Invalid input.
* Rapid repeated interaction.
* Slow network.
* Failed network requests.
* Component unmounting.
* Browser resize.
* Navigation during asynchronous work.
* Permission changes.
* Unexpected API responses.

Do not invent arbitrary edge cases that have no relationship to the feature.

---

# Async and Concurrency

When reviewing asynchronous code, inspect:

* Race conditions.
* Stale state.
* Stale closures.
* Request cancellation.
* Out-of-order responses.
* Duplicate requests.
* Loading state transitions.
* Error handling.
* Component lifecycle.
* Cleanup.

Be suspicious of code that assumes asynchronous operations complete in the same order they started.

Do not recommend arbitrary delays as a synchronization mechanism.

---

# Security

When the change touches security-sensitive behavior, apply:

```text
skills/security/SKILL.md
```

Security-sensitive areas include:

* Authentication.
* Authorization.
* Sessions.
* Tokens.
* User-controlled input.
* Sensitive data.
* File uploads.
* External URL fetching.
* Database queries.
* Command execution.
* HTML rendering.
* Dependencies.
* CI/CD.
* Secrets.
* Security configuration.
* Privileged operations.

Do not duplicate the security skill's detailed rules here.

The code review should identify security concerns and evaluate them using the security skill.

Prioritize credible security findings based on impact and exploitability.

Never treat frontend-only authorization as sufficient protection for privileged operations.

---

# React Review

For React code, consider:

* Component responsibilities.
* State ownership.
* Prop design.
* Effect usage.
* Derived state.
* Component lifecycle.
* Remounting.
* Keys.
* Context updates.
* Event handling.
* Conditional rendering.
* Memoization.
* Async behavior.

Look for effects that are being used to derive state unnecessarily.

Look for state that duplicates existing information.

Look for components that have accumulated unrelated responsibilities.

Do not flag every re-render as a performance problem.

---

# TypeScript Review

Check whether types accurately represent the behavior.

Look for:

* `any` used to bypass type safety.
* Unsafe type assertions.
* Incorrect optionality.
* Overly broad types.
* Missing discriminated unions where they would prevent invalid states.
* Incorrect API models.
* Type definitions that contradict runtime behavior.

Prefer types that prevent invalid states rather than types that merely describe them after the fact.

Do not introduce complex generic types when a simpler type communicates the contract adequately.

---

# Testing Review

When behavior changes, determine whether the tests provide meaningful coverage.

Ask:

* Is the changed behavior tested?
* Is the important user-facing behavior covered?
* Are important edge cases covered?
* Does the test verify behavior rather than implementation details?
* Are asynchronous interactions tested correctly?
* Are mocks appropriate?
* Could the test pass while the real behavior is broken?

Do not require a test for every trivial line of code.

Prioritize risk and behavior.

---

# Regression Tests

For bug fixes, look for a regression test when appropriate.

A useful regression test should:

* Represent the previously broken behavior.
* Fail against the buggy implementation when practical.
* Pass with the fix.
* Remain meaningful after refactoring.

For security bugs, use the security skill to determine the appropriate security regression strategy.

Do not accept a test that merely increases coverage without protecting the actual bug.

---

# Accessibility Review

When the change affects UI or interaction, apply:

```text
skills/accessibility/SKILL.md
```

Consider:

* Semantic HTML.
* Accessible names.
* Keyboard interaction.
* Focus behavior.
* Focus order.
* Form labels.
* Error messages.
* ARIA states.
* Dialog behavior.
* Dynamic announcements.

Do not assume a component is accessible because it renders correctly.

Do not recommend ARIA when native HTML already provides the correct semantics.

---

# Performance Review

When the change has meaningful performance implications, apply:

```text
skills/performance/SKILL.md
```

Look for credible or measurable issues such as:

* Rendering very large datasets.
* Repeated expensive calculations.
* Excessive network requests.
* Large bundle additions.
* Blocking synchronous work.
* Unnecessary high-frequency event work.
* Significant memory retention.

Do not automatically recommend:

* `useMemo`.
* `useCallback`.
* `React.memo`.
* Lazy loading.
* Virtualization.

There should be a meaningful reason for each optimization.

---

# Architecture Review

When the change has meaningful architectural implications, apply:

```text
skills/architecture/SKILL.md
```

Look for:

* New circular dependencies.
* Infrastructure leaking into domain logic.
* UI components directly handling unrelated infrastructure concerns.
* Global state introduced unnecessarily.
* New abstractions without a clear purpose.
* Inconsistent module ownership.
* Duplication caused by unclear boundaries.

Do not demand architectural changes simply because another architecture might be theoretically cleaner.

---

# Maintainability

Consider whether the implementation will be understandable to another developer.

Look for:

* Complex control flow.
* Ambiguous naming.
* Hidden side effects.
* Excessive abstraction.
* Duplicated logic.
* Large functions with multiple responsibilities.
* Configuration that is difficult to understand.
* Comments that contradict the code.

Prefer clarity over cleverness.

Do not refactor code solely to make it look different.

---

# Naming

Names should communicate intent.

Review:

* Variables.
* Functions.
* Components.
* Hooks.
* Types.
* Constants.
* Modules.

Avoid names that are:

* Misleading.
* Overly generic.
* Ambiguous.
* Inconsistent with the domain.

Do not spend review effort changing a reasonable name merely because another name could also work.

---

# Comments

Comments should explain information that is not obvious from the code.

Useful comments may explain:

* Why an unusual implementation is necessary.
* An external constraint.
* A non-obvious browser behavior.
* A compatibility workaround.
* A business rule that is not obvious from the code.

Avoid comments that simply restate the code.

Do not remove useful context merely because the code could theoretically be refactored.

---

# Scope Discipline

Review whether the change is appropriately scoped.

Watch for:

* Unrelated refactoring.
* Unnecessary dependency upgrades.
* Large formatting changes.
* Renaming unrelated files.
* Architecture changes unrelated to the feature.
* Cleanup mixed into a bug fix.

Large unrelated changes make reviews harder and increase regression risk.

Prefer focused changes unless broader scope is explicitly justified.

---

# Review Findings

Each finding should communicate:

1. What is wrong.
2. Why it matters.
3. Where it occurs.
4. What behavior could result.
5. A reasonable direction for fixing it when useful.

Prefer specific findings.

Bad:

```text
"This could be improved."
```

Better:

```text
"This request can complete after the component has changed to a different user, allowing the stale response to overwrite the current state. Cancel or ignore the previous request when the user changes."
```

---

# Finding Severity

Use severity based on actual impact.

### Critical

The change introduces a severe security, data integrity, or system failure risk.

### High

The change is likely to cause serious incorrect behavior, major regression, or significant security/accessibility problems.

### Medium

The change creates a meaningful bug, maintainability problem, or reliability issue that should be addressed.

### Low

The issue has limited impact but is still worth correcting.

### Suggestion

An optional improvement that does not represent a defect.

Do not inflate severity to make a comment more persuasive.

---

# Avoid False Positives

Before reporting a finding:

* Verify the relevant code path.
* Check whether another layer already handles the problem.
* Check existing assumptions and contracts.
* Check whether the behavior is intentional.
* Check whether the concern is actually reachable.

Do not report hypothetical problems without a credible path to failure.

---

# Positive Feedback

Positive observations can be useful when they identify good patterns worth preserving.

Examples:

* Clear separation of responsibilities.
* Strong test coverage of a risky behavior.
* Good error handling.
* Appropriate abstraction.
* Accessible interaction design.
* Explicit security boundaries.

Do not fill a review with praise when the purpose is to identify actionable issues.

---

# Review Order

For efficiency, review in this order:

1. Correctness.
2. Security.
3. Regression risk.
4. Data and async behavior.
5. Accessibility.
6. Architecture.
7. Testing.
8. Performance.
9. Maintainability.
10. Style and minor improvements.

Do not spend significant time on formatting while a functional or security defect remains unresolved.

---

# Final Review

Before completing a review:

* Re-read the change as a whole.
* Verify each finding against the actual code.
* Remove speculative findings.
* Ensure severity is appropriate.
* Check whether the proposed fix is realistic.
* Confirm that findings are actionable.
* Identify important missing tests when relevant.
* Determine whether the change is safe to merge.

Do not approve a change simply because it compiles.

---

# Code Review Definition of Done

A code review is complete when:

* The change is understood.
* Relevant behavior has been evaluated.
* Functional correctness has been considered.
* Security implications have been evaluated when relevant.
* Regression risks have been considered.
* Accessibility implications have been considered when relevant.
* Performance implications have been considered when relevant.
* Testing quality has been evaluated.
* Architectural boundaries have been evaluated when relevant.
* Findings are specific and actionable.
* False positives have been minimized.
* Severity reflects actual impact.
* The reviewer can clearly state whether the change is safe to merge and why.
