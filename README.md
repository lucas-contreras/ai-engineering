# AI Engineering

Reusable AI engineering skills and instructions for software development.

This repository provides a portable set of engineering skills designed to improve how AI coding assistants reason about software development tasks.

The goal is to avoid maintaining a completely different set of AI instructions for every project.

---

## What is this repository?

This repository contains two main concepts:

### Skills

Skills contain specialized engineering knowledge and workflows.

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

Each skill focuses on a specific engineering concern.

For example:

* `frontend` → frontend development practices.
* `testing` → testing strategy and test quality.
* `debugging` → structured debugging.
* `security` → application security and OWASP-based reasoning.
* `architecture` → architectural decisions and boundaries.
* `code-review` → systematic code review.

Skills are intentionally reusable across projects and technologies.

---

## Templates

The repository also contains templates for integrating the skills with AI coding assistants.

```text
templates/
└── copilot-instructions.md
```

The template defines:

* How the AI should approach development tasks.
* Which skills apply to different situations.
* How multiple skills should be combined.
* General engineering principles.
* Validation expectations.

The template should normally be adapted to the target project rather than copied blindly.

---

# Using the Repository in Another Project

There are two different ways to use these skills.

## Option 1 — Copy the skills into the project

This is the simplest approach.

Clone or download this repository:

```bash
git clone <repository-url>
```

Then copy the required skills into your project.

For example:

```text
my-project/
│
├── .github/
│   └── copilot-instructions.md
│
├── .ai/
│   └── skills/
│       ├── core-development/
│       ├── frontend/
│       ├── testing/
│       └── security/
│
└── src/
```

The exact directory can be adapted to the AI tool being used.

The important part is that the assistant can access the skill files.

---

# Option 2 — Use this repository as the source of truth

For multiple projects, the preferred approach is to keep this repository as the canonical source.

```text
ai-engineering
        │
        ├── core-development
        ├── frontend
        ├── testing
        ├── debugging
        ├── accessibility
        ├── performance
        ├── architecture
        ├── security
        └── code-review
                  │
          ┌───────┼────────┐
          ↓       ↓        ↓
       Project A Project B Project C
```

Each project can consume the skills it needs.

This makes improvements reusable across projects.

---

# Recommended Project Integration

For GitHub Copilot, create:

```text
.github/
└── copilot-instructions.md
```

Start from:

```text
templates/copilot-instructions.md
```

Then adapt it to the project.

For example:

```text
my-react-project/
│
├── .github/
│   └── copilot-instructions.md
│
├── skills/
│   ├── core-development/
│   ├── frontend/
│   ├── testing/
│   └── accessibility/
│
├── src/
└── package.json
```

The project-specific instructions should explain where the skills are located and which technologies the project uses.

---

# Important: Skills Are Not Technology Profiles

A skill describes an engineering concern.

For example:

```text
skills/frontend/
```

should contain principles applicable to frontend development.

It should not become:

```text
skills/frontend-react-vite-typescript-jest/
```

Technology-specific knowledge should be introduced separately.

For example, a future structure could be:

```text
profiles/
├── react/
├── vue/
├── svelte/
├── node/
└── typescript/
```

This separation keeps the engineering skills reusable.

---

# Example

Suppose a project uses:

```text
React
TypeScript
Vite
Jest
React Testing Library
```

A suitable integration could use:

```text
core-development
frontend
testing
accessibility
performance
security
```

The AI should not automatically apply every skill to every task.

For example:

### Adding a React component

Use:

```text
core-development
frontend
testing
```

Add:

```text
accessibility
```

if the component involves user interaction.

Add:

```text
security
```

if the component handles untrusted input, authentication, authorization, or sensitive data.

---

### Fixing a race condition

Use:

```text
core-development
debugging
testing
```

---

### Fixing an authorization vulnerability

Use:

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

if the vulnerability comes from a broader architectural problem.

---

### Reviewing a pull request

Use:

```text
core-development
code-review
```

Then add the specialized skills based on the changed code.

---

# Updating Skills

The repository should be treated as the source of truth.

When a skill improves:

```text
skills/security/SKILL.md
```

update it here first.

Then propagate the updated skill to projects that consume it.

Do not independently modify copies in multiple projects unless the project intentionally requires a local variation.

---

# Project-Specific Overrides

A project may have requirements that should not be added to the global skills.

For example:

```text
This project uses Redux Toolkit for global state.
```

or:

```text
All tests must use esbuild-jest.
```

or:

```text
This application must support Firefox ESR.
```

These belong in the project's own instructions.

The global skill should remain technology-agnostic whenever possible.

---

# Suggested Structure for Consuming Projects

A project can eventually look like:

```text
my-project/
│
├── .github/
│   └── copilot-instructions.md
│
├── .ai/
│   └── skills/
│       ├── core-development/
│       ├── frontend/
│       ├── testing/
│       └── security/
│
├── src/
├── tests/
├── package.json
└── README.md
```

The `.ai/skills/` directory is optional.

Its purpose is simply to make the project's AI context explicit and version-controlled.

---

# Keeping Skills Synchronized

If multiple projects consume the same skills, avoid manually copying changes whenever possible.

Possible approaches include:

### Git Submodule

```bash
git submodule add <repository-url> .ai/ai-engineering
```

This keeps the engineering repository as a separate Git repository.

The project can then reference a specific commit.

Advantages:

* Versioned.
* Explicit.
* Reproducible.
* Easy to update.

Disadvantage:

* Git submodules add workflow complexity.

---

### Git Subtree

The skills can be imported into the project while still allowing synchronization with the source repository.

This provides a more integrated project structure but requires a little more Git knowledge.

---

### Copy on Demand

For small projects, simply copy the relevant skills.

This is completely acceptable.

The important part is that the canonical version remains in:

```text
ai-engineering
```

---

# Versioning

Treat the skills as a versioned engineering system.

For example:

```text
v1.0.0
v1.1.0
v1.2.0
```

A project can document which version it uses:

```text
AI Engineering Skills: v1.2.0
```

This becomes useful when a skill changes significantly.

---

# Recommended Update Workflow

When improving the system:

```text
Identify recurring problem
        ↓
Determine which skill owns the problem
        ↓
Update the skill
        ↓
Test it against real development tasks
        ↓
Update copilot-instructions if activation changed
        ↓
Commit
        ↓
Tag a version when appropriate
        ↓
Update consuming projects
```

Do not add every individual lesson directly to `copilot-instructions.md`.

If a rule belongs to a specialized concern, put it in the appropriate skill.

---

# Adding a New Skill

Create:

```text
skills/<skill-name>/
└── SKILL.md
```

A new skill should have:

1. A clear purpose.
2. A defined scope.
3. Practical principles.
4. Workflows where appropriate.
5. Common failure modes.
6. Validation guidance.
7. A definition of done.

Before creating a new skill, ask:

> Is this genuinely a different engineering concern, or should it belong to an existing skill?

Avoid creating skills simply because a technology has a different name.

---

# Adding Technology Profiles

Technology-specific guidance can eventually live separately:

```text
profiles/
├── react/
│   └── PROFILE.md
├── vue/
│   └── PROFILE.md
├── svelte/
│   └── PROFILE.md
├── node/
│   └── PROFILE.md
└── typescript/
    └── PROFILE.md
```

A profile answers:

> How should these engineering principles be applied to this technology?

A skill answers:

> What engineering principles should be followed?

This distinction is important for portability.

---

# Relationship Between Skills and Profiles

Think of the system as layers:

```text
                    AI Assistant
                         │
                         ↓
                Project Instructions
                         │
              ┌──────────┴──────────┐
              ↓                     ↓
           Skills                Profiles
              │                     │
      ┌───────┼───────┐       ┌─────┼─────┐
      ↓       ↓       ↓       ↓     ↓     ↓
   Testing Security Frontend React  Vue  Node
```

Skills define engineering reasoning.

Profiles define technology-specific application.

Project instructions define the context of the current project.

---

# Repository Philosophy

The goal is not to make the AI generate more code.

The goal is to make the AI make better engineering decisions.

Prefer:

```text
Understand
Inspect
Reason
Implement
Validate
```

over:

```text
Generate
Hope
Fix
Repeat
```

The system should continuously evolve based on real engineering problems encountered in production and development.

---

# Roadmap

Potential future additions:

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
├── code-review/
├── observability/
├── api-design/
└── documentation/
```

Potential technology profiles:

```text
profiles/
├── react/
├── vue/
├── svelte/
├── angular/
├── node/
├── typescript/
├── python/
└── java/
```

Potential AI-specific skills:

```text
skills/
└── ai-engineering/
```

and eventually:

```text
skills/
└── ai-security/
```

These should only be added when there is enough accumulated knowledge to justify a dedicated skill.

---

# Contributing to the Skills

When adding or changing guidance:

1. Prefer general principles over project-specific rules.
2. Explain why a rule exists when the reason is not obvious.
3. Avoid absolute rules when context matters.
4. Prefer evidence over assumptions.
5. Avoid duplicating rules across skills.
6. Keep specialized knowledge in the appropriate skill.
7. Update the README when the repository structure changes.
8. Validate new guidance against real development scenarios.

---

# License

Add the repository's chosen license here.

````

## Quick Start

For a new project:

```text
1. Get this repository
        ↓
2. Select the skills relevant to the project
        ↓
3. Copy/adapt copilot-instructions.md
        ↓
4. Add project-specific technology/context
        ↓
5. Commit the AI configuration with the project
        ↓
6. Update skills when the central repository evolves
````

The central repository remains the source of truth for reusable engineering knowledge.
