# Copilot Instructions

## Purpose

These instructions define how the AI assistant should work within this repository.

The repository contains reusable engineering skills under `skills/`.

Skills provide specialized guidance for specific engineering activities.

The assistant should apply the relevant skills based on the task rather than treating every skill as mandatory for every change.

---

# General Behavior

## Understand Before Changing

Before modifying code:

1. Understand the user's request.
2. Inspect the relevant project structure.
3. Read the relevant existing implementation.
4. Search for similar patterns already used in the project.
5. Identify relevant tests.
6. Identify applicable skills.
7. Make the smallest appropriate change.

Do not modify code based only on assumptions about the project.

Prefer existing project conventions over introducing new patterns.

---

## Preserve Existing Behavior

Unless the task explicitly requires behavioral changes:

* Preserve existing functionality.
* Avoid unrelated refactoring.
* Avoid unnecessary dependency changes.
* Avoid changing public APIs.
* Avoid changing architecture without justification.

A change should be as focused as reasonably possible.

---

## Evidence Over Assumptions

When making technical decisions:

* Inspect the codebase.
* Use available documentation.
* Use test results.
* Use compiler errors.
* Use runtime behavior.
* Use profiling data when performance is involved.
* Use security evidence and established security guidance when security is involved.

Do not invent APIs, project conventions, dependencies, or behavior.

If something cannot be verified, state the assumption clearly.

---

# Skill System

Skills are located under:

```text
skills/
├── core-development/
├── frontend/
├── testing/
├── debugging/
├── accessibility/
├── performance/
├── architecture/
├── security/
└── code-review/
```

Each skill contains detailed guidance for a specific engineering concern.

Apply skills contextually.

Do not duplicate the contents of a skill in this file.

---

# Skill Selection

## Always Apply

### Core Development

Apply:

```text
skills/core-development/SKILL.md
```

to all development tasks.

This provides the general engineering principles that apply regardless of technology.

---

## Frontend Development

Apply:

```text
skills/frontend/SKILL.md
```

when working on:

* UI components.
* React components.
* Frontend application logic.
* Client-side state.
* Forms.
* Browser interactions.
* Frontend TypeScript.
* Rendering behavior.

If the project uses a frontend framework other than React, adapt the principles to the framework instead of forcing React-specific patterns.

---

## Testing

Apply:

```text
skills/testing/SKILL.md
```

when:

* Writing tests.
* Modifying tests.
* Fixing test failures.
* Evaluating test coverage.
* Adding regression tests.
* Changing test infrastructure.

When the project uses Jest, React Testing Library, TypeScript, or `esbuild-jest`, follow the relevant guidance in the testing skill and existing project configuration.

Do not replace existing test tooling without an explicit reason.

---

## Debugging

Apply:

```text
skills/debugging/SKILL.md
```

when:

* Investigating bugs.
* Diagnosing unexpected behavior.
* Investigating runtime errors.
* Investigating browser-specific behavior.
* Investigating asynchronous issues.
* Investigating build or tooling failures.

Use a hypothesis-driven debugging process.

Do not make speculative changes simply to see whether the problem disappears.

---

## Accessibility

Apply:

```text
skills/accessibility/SKILL.md
```

when a change affects:

* UI interaction.
* Keyboard navigation.
* Focus.
* Forms.
* Semantic HTML.
* ARIA.
* Screen readers.
* Dialogs.
* Menus.
* Dynamic content.
* WCAG requirements.

For accessibility-sensitive changes, consider both automated validation and manual keyboard or screen-reader validation when appropriate.

---

## Performance

Apply:

```text
skills/performance/SKILL.md
```

when:

* Investigating performance problems.
* Optimizing rendering.
* Optimizing network behavior.
* Optimizing bundle size.
* Working with large lists or datasets.
* Investigating memory usage.
* Working with high-frequency events.
* Evaluating Core Web Vitals.

Do not introduce performance optimizations without evidence or a credible performance rationale.

Avoid adding memoization, lazy loading, virtualization, or similar techniques by default.

---

## Architecture

Apply:

```text
skills/architecture/SKILL.md
```

when:

* Introducing new architectural boundaries.
* Restructuring modules.
* Changing state architecture.
* Introducing significant abstractions.
* Changing dependency direction.
* Changing API boundaries.
* Migrating architectural patterns.
* Making decisions that affect multiple features or layers.

Do not introduce architectural complexity without a concrete problem that justifies it.

Prefer incremental evolution over unnecessary rewrites.

---

## Security

Apply:

```text
skills/security/SKILL.md
```

when a change affects:

* Authentication.
* Authorization.
* Sessions.
* Tokens.
* User-controlled input.
* Sensitive data.
* Database queries.
* Command execution.
* HTML rendering.
* File uploads.
* External URL fetching.
* CORS.
* CSRF.
* Dependencies.
* CI/CD.
* Secrets.
* Security configuration.
* Privileged operations.
* Trust boundaries.

Use OWASP Top 10:2025 as a primary reference for common web application security risks.

Security controls must not depend solely on frontend behavior.

When security-sensitive functionality is involved, evaluate both the normal flow and failure/abuse paths.

---

## Code Review

Apply:

```text
skills/code-review/SKILL.md
```

when:

* Reviewing a pull request.
* Reviewing a diff.
* Evaluating a proposed implementation.
* Looking for regressions.
* Evaluating maintainability.
* Performing a pre-merge review.

Prioritize correctness, security, regression risk, accessibility, architecture, testing, and performance over stylistic preferences.

When security-sensitive code is reviewed, also apply:

```text
skills/security/SKILL.md
```

---

# Combining Skills

Multiple skills may apply to the same task.

Apply all relevant skills when their concerns overlap.

Examples:

### New React Feature

Apply:

```text
core-development
frontend
testing
accessibility
```

Add:

```text
performance
```

when performance is relevant.

Add:

```text
architecture
```

when the feature introduces meaningful architectural decisions.

Add:

```text
security
```

when authentication, authorization, sensitive data, external input, or other security boundaries are involved.

---

### Bug Fix

Apply:

```text
core-development
debugging
```

Add:

```text
testing
```

when a regression test is appropriate.

Add other skills when the bug involves their domain.

---

### Security Bug

Apply:

```text
core-development
debugging
security
testing
```

Add:

```text
architecture
```

when the vulnerability results from an architectural weakness.

---

### Performance Bug

Apply:

```text
core-development
debugging
performance
```

Add:

```text
frontend
```

when the problem involves frontend rendering or React behavior.

---

### Accessibility Bug

Apply:

```text
core-development
debugging
accessibility
testing
```

when automated or regression testing is relevant.

---

### Architectural Change

Apply:

```text
core-development
architecture
```

Add:

```text
frontend
testing
performance
accessibility
security
```

when the change affects those areas.

---

### Code Review

Apply:

```text
core-development
code-review
```

Then apply specialized skills based on the changed code.

Security-sensitive changes should additionally use:

```text
security
```

---

# Technology Awareness

Skills describe engineering principles, not mandatory technologies.

Before applying technology-specific guidance:

1. Identify the technologies actually used by the project.
2. Inspect existing configuration.
3. Follow established project conventions.
4. Apply the relevant skill principles using the project's technology.

Do not assume that every project uses React, Jest, TypeScript, or any other specific technology.

When a skill contains technology-specific guidance, use it only when that technology is actually present.

---

# Existing Patterns

Before introducing a new pattern:

1. Search the repository for existing implementations.
2. Determine why the existing pattern is used.
3. Reuse it when appropriate.
4. Improve it only when the task requires improvement.

Do not introduce competing patterns without a clear reason.

Consistency with the existing codebase is generally preferable to introducing a theoretically superior pattern.

---

# Dependencies

Before adding a dependency:

* Check whether the functionality already exists.
* Check whether the project already has an equivalent dependency.
* Consider maintenance and compatibility.
* Consider bundle and runtime impact where relevant.
* Consider security implications.

Do not add dependencies for trivial functionality that can reasonably be implemented using existing project capabilities.

When adding a dependency with meaningful security implications, apply the security skill.

---

# Refactoring

When refactoring:

* Preserve observable behavior.
* Keep the change focused.
* Avoid unrelated cleanup.
* Prefer incremental changes.
* Keep tests meaningful.
* Validate behavior after significant changes.

Do not combine a feature implementation, architectural migration, dependency upgrade, and broad cleanup unless explicitly requested.

---

# Debugging Workflow

When a problem is reported:

```text
Understand
    ↓
Reproduce
    ↓
Inspect
    ↓
Form hypothesis
    ↓
Validate hypothesis
    ↓
Identify root cause
    ↓
Implement focused fix
    ↓
Add regression coverage when appropriate
    ↓
Validate
```

Do not skip directly from symptom to code modification when the cause is unclear.

---

# Security Workflow

When a security-sensitive problem is reported:

```text
Understand the feature
        ↓
Identify trust boundaries
        ↓
Identify attacker-controlled inputs
        ↓
Identify authentication requirements
        ↓
Identify authorization requirements
        ↓
Identify abuse cases
        ↓
Evaluate relevant security risks
        ↓
Implement the smallest appropriate control
        ↓
Test allowed and forbidden behavior
        ↓
Review failure paths
        ↓
Validate dependencies and configuration
```

Do not assume that hiding functionality in the frontend constitutes a security control.

---

# Performance Workflow

When performance is involved:

```text
Identify suspected bottleneck
        ↓
Measure
        ↓
Establish baseline
        ↓
Optimize
        ↓
Measure again
        ↓
Validate trade-offs
```

Do not optimize based solely on theoretical concerns.

---

# Accessibility Workflow

When accessibility is involved:

```text
Understand interaction
        ↓
Inspect semantic structure
        ↓
Evaluate keyboard behavior
        ↓
Evaluate focus behavior
        ↓
Evaluate accessible name / state
        ↓
Test with appropriate tools
        ↓
Validate regression risk
```

Do not assume that automated accessibility checks are sufficient for complex interactions.

---

# Testing Workflow

When implementing behavior:

1. Determine what behavior needs confidence.
2. Identify existing tests.
3. Choose the appropriate test level.
4. Test observable behavior.
5. Cover meaningful edge cases.
6. Keep tests deterministic.
7. Run relevant validation.

Do not write tests solely to increase coverage.

---

# Communication

When explaining a change:

* Be concise.
* State what changed.
* Explain important decisions.
* Mention relevant validation.
* Mention important limitations or assumptions.
* Do not claim validation that was not performed.

When reporting a bug:

* Distinguish symptoms from root cause.
* Explain the evidence.
* Explain the fix.
* Explain how it was validated.

---

# Final Validation

Before considering a development task complete:

* Verify the requested behavior.
* Review the changed code.
* Run relevant tests.
* Check for type errors when applicable.
* Check for lint or build issues when applicable.
* Consider accessibility when UI is involved.
* Consider performance when relevant.
* Consider security when relevant.
* Consider architectural impact when relevant.
* Remove temporary debugging code.
* Avoid unrelated changes.

The assistant should be able to explain why the implementation is correct and what validation supports that conclusion.

---

# Priority Order

When concerns conflict, prioritize:

1. Correctness.
2. Security.
3. User-facing behavior.
4. Accessibility.
5. Reliability.
6. Maintainability.
7. Performance.
8. Developer convenience.
9. Stylistic preferences.

Do not sacrifice correctness, security, or accessibility for superficial simplicity or minor performance gains.

---

# Final Principle

Prefer:

```text
Understand the problem
        ↓
Inspect the existing system
        ↓
Identify applicable skills
        ↓
Choose the simplest appropriate solution
        ↓
Implement intentionally
        ↓
Validate behavior
        ↓
Leave the codebase better than before
```

Avoid:

```text
Assume
  ↓
Change code
  ↓
Add abstractions
  ↓
Add dependencies
  ↓
Add workarounds
  ↓
Hope it works
```
