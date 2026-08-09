# Accessibility

## Purpose

Provide guidelines for building and evaluating accessible user interfaces.

Accessibility should be treated as part of the product's functionality, not as a final compliance step.

The goal is to ensure that users with different abilities, input methods, browsers, and assistive technologies can understand, navigate, and operate the interface.

---

## Core Accessibility Principles

Accessibility work should consider four fundamental requirements:

* Perceivable.
* Operable.
* Understandable.
* Robust.

When evaluating an interface, consider both the visual experience and the underlying semantic and interaction model.

Do not assume that visually correct UI is accessible.

---

## Semantic HTML

Prefer native HTML elements whenever they provide the required behavior.

Use semantic elements such as:

* `button`
* `a`
* `input`
* `select`
* `textarea`
* `label`
* `nav`
* `main`
* `header`
* `footer`
* `section`
* `article`

before recreating their behavior with generic elements.

Avoid using:

```text
div
span
```

as interactive controls when an appropriate native element exists.

Native HTML provides behavior, keyboard interaction, focus management, and accessibility semantics that are difficult to reproduce correctly.

---

## Buttons vs Links

Use a button for an action.

Use a link for navigation.

Do not use one as a substitute for the other merely because CSS makes them look similar.

Examples:

```text
Button:
Open menu
Submit form
Delete item
Toggle panel

Link:
Navigate to profile
Open documentation
Go to another page
```

Do not create fake links or buttons using generic elements unless there is a compelling technical reason.

---

## Accessible Names

Every interactive control should have an accessible name that communicates its purpose.

Consider the accessible name of:

* Buttons.
* Links.
* Inputs.
* Form controls.
* Interactive icons.
* Custom widgets.

Do not assume that visible content automatically produces the correct accessible name.

For icon-only controls, provide an appropriate accessible name.

Avoid adding redundant ARIA when native semantics already provide the correct name.

---

## Labels and Descriptions

Distinguish between:

### Accessible Name

Identifies what a control is.

Examples:

```text
"Search"
"Close dialog"
"Email address"
```

### Accessible Description

Provides additional information about the control.

Examples:

```text
"Password must contain at least eight characters."
"Changes are saved automatically."
```

Use `aria-label` when an accessible name is needed and no suitable visible label exists.

Prefer visible labels when possible.

Use `aria-describedby` when additional descriptive information is associated with a control.

Do not use `aria-label` as a replacement for a visible label when a visible label is required or beneficial.

---

## ARIA

### First Rule of ARIA

Prefer native HTML semantics whenever possible.

ARIA should enhance semantics when necessary, not replace correct HTML unnecessarily.

Do not add ARIA attributes merely because they appear to improve accessibility.

Every ARIA attribute should have a clear semantic purpose.

---

## ARIA State and Properties

When implementing custom interactive components, ensure that relevant state is communicated to assistive technologies.

Examples include:

* `aria-expanded`
* `aria-selected`
* `aria-checked`
* `aria-pressed`
* `aria-disabled`
* `aria-current`
* `aria-invalid`
* `aria-busy`

State should reflect the actual UI state.

Do not leave ARIA state permanently set to a value that does not represent the current interface.

---

## Focus Management

Keyboard focus should be predictable and intentional.

Consider focus when:

* Opening dialogs.
* Closing dialogs.
* Opening menus.
* Changing routes.
* Removing focused elements.
* Displaying validation errors.
* Updating dynamic content.
* Switching between interactive states.

When an element is removed while focused, determine where focus should move.

Do not move focus unnecessarily.

Unexpected focus movement can be as problematic as missing focus movement.

---

## Focus Order

The focus order should follow a logical sequence that matches the intended interaction flow.

When evaluating focus order:

* Consider DOM order.
* Consider visual order.
* Consider keyboard navigation.
* Consider dynamically inserted content.
* Consider overlays and dialogs.

Avoid using positive `tabindex` values to manually construct a focus order.

Prefer a logical DOM structure that naturally produces the correct sequence.

A visually correct layout can still produce an incorrect keyboard experience.

---

## Keyboard Accessibility

All interactive functionality should be usable without a mouse.

Verify:

* Elements can receive focus when appropriate.
* Keyboard users can activate controls.
* Menus can be navigated appropriately.
* Dialogs can be operated.
* Focus is not trapped unexpectedly.
* Escape behavior is appropriate where relevant.
* Keyboard interaction does not depend on pointer-specific events.

Do not implement keyboard behavior only after mouse interaction has been completed.

Keyboard interaction should be considered during component design.

---

## Custom Interactive Components

When creating a custom widget, first determine whether a native HTML element or established accessible pattern can provide the behavior.

If a custom component is necessary:

1. Define its semantic role.
2. Define its accessible name.
3. Define its keyboard interactions.
4. Define its focus behavior.
5. Define its state.
6. Define how state is exposed to assistive technologies.
7. Test the complete interaction model.

Do not add a `role` without implementing the behavior associated with that role.

A custom component is not accessible simply because it has ARIA attributes.

---

## Menus

Distinguish between:

* Navigation menus.
* Application menus.
* Context menus.
* Select-like controls.
* Disclosure components.

Do not automatically apply the ARIA `menu` role to every collection of links or actions.

Choose semantics based on the actual interaction pattern.

For menu-like widgets, consider:

* Keyboard navigation.
* Arrow-key behavior where appropriate.
* Escape behavior.
* Focus management.
* Disabled items.
* Selection state.
* Opening and closing behavior.

---

## Dialogs and Modals

Dialogs should:

* Have an accessible name.
* Communicate their purpose.
* Receive appropriate focus when opened.
* Keep keyboard interaction within the dialog when modal behavior requires it.
* Return focus appropriately when closed.
* Provide a reliable mechanism for dismissal when appropriate.

Do not assume that adding `role="dialog"` automatically creates an accessible modal.

---

## Forms

Form controls should have:

* A clear accessible name.
* Appropriate type or role.
* Clear instructions when needed.
* Validation feedback.
* Error identification.
* Appropriate required state.
* Programmatically associated descriptions where necessary.

When validation fails:

* Identify the invalid field.
* Provide understandable feedback.
* Associate the error with the relevant control.
* Consider appropriate focus management for the interaction.

Do not rely only on color to communicate validation state.

---

## Error Messages

Error messages should:

* Explain what went wrong.
* Help the user understand how to recover.
* Be associated with the relevant control when applicable.
* Be perceivable by assistive technologies.

Avoid messages such as:

```text
"Invalid input"
"Error"
"Something went wrong"
```

when more useful information can be provided.

---

## Dynamic Content

When content changes without a page navigation, consider whether users need to be informed of the change.

Examples:

* Toast notifications.
* Validation messages.
* Search results.
* Loading state changes.
* Status updates.
* Background operations.

Use appropriate semantics or live-region mechanisms when necessary.

Do not make every dynamic update a live announcement.

Excessive announcements can make interfaces difficult to use with screen readers.

---

## Screen Readers

When validating with a screen reader, consider what the user actually perceives.

Evaluate:

* Accessible names.
* Roles.
* States.
* Descriptions.
* Reading order.
* Focus order.
* Announcements.
* Hidden content.
* Dynamic updates.
* Form errors.

Do not assume that the DOM source code accurately predicts the complete screen reader experience.

---

## NVDA

When NVDA is available, use it as a practical validation tool for Windows accessibility behavior.

Consider testing with:

* NVDA + Firefox.
* NVDA + Chrome.

When behavior differs between browsers:

1. Verify the difference.
2. Determine whether the difference is caused by browser behavior, accessibility tree exposure, or application code.
3. Avoid browser-specific workarounds unless necessary.
4. Prefer standards-based behavior.

Do not assume that behavior observed in one browser represents all screen reader environments.

---

## Browser Accessibility Tree

When accessibility behavior is unclear, inspect the browser's accessibility tree when available.

Use it to investigate:

* Role.
* Accessible name.
* Accessible description.
* State.
* Relationships.
* Visibility.
* Focus.

The accessibility tree can reveal problems that are not obvious from the DOM.

Do not rely exclusively on visual inspection.

---

## Hidden Content

Understand the difference between:

* Visually hidden content.
* Content hidden from assistive technologies.
* Inert content.
* `display: none`.
* `visibility: hidden`.
* `aria-hidden`.

Do not use `aria-hidden` merely to hide visually irrelevant content without understanding its accessibility consequences.

Never hide an element from assistive technologies if it contains the only accessible name or information necessary to operate an interactive control.

---

## Color and Visual Information

Do not rely solely on:

* Color.
* Shape.
* Position.
* Animation.

to communicate important information.

When color communicates meaning, provide another indication.

Consider:

* Text.
* Icons.
* Labels.
* Patterns.
* State indicators.

Visual design should remain understandable without relying on color alone.

---

## Motion and Animation

Consider users who may be sensitive to motion.

Avoid unnecessary animation.

Respect reduced-motion preferences when appropriate.

Do not make essential functionality dependent on animation.

Ensure that animated interfaces remain usable when motion is reduced or disabled.

---

## Responsive Accessibility

Accessibility must be preserved across viewport sizes.

Check:

* Keyboard navigation.
* Focus visibility.
* Text resizing.
* Content reflow.
* Touch targets.
* Zoom behavior.
* Responsive menus.
* Dialog behavior.
* Horizontal scrolling.

Do not assume that accessibility on desktop automatically means accessibility on mobile or narrow viewports.

---

## Testing Accessibility

Accessibility should be evaluated at multiple levels.

### Automated Testing

Use automated tools to identify issues such as:

* Missing accessible names.
* Invalid ARIA.
* Some color contrast problems.
* Structural issues.

Automated testing cannot verify all accessibility requirements.

### Keyboard Testing

Perform manual keyboard testing for interactive flows.

### Screen Reader Testing

Use screen readers when behavior depends on semantics, focus, announcements, or complex interaction patterns.

### Visual Testing

Verify:

* Focus visibility.
* Zoom behavior.
* Text readability.
* Contrast.
* Reflow.
* Responsive behavior.

Use multiple forms of testing rather than relying on a single accessibility checker.

---

## WCAG

When evaluating compliance, identify the relevant WCAG success criterion rather than making vague claims that something is "not accessible."

For example:

```text
WCAG 2.4.3 — Focus Order
```

Explain:

* What the requirement means.
* What behavior violates it.
* How the current implementation behaves.
* What change would address the issue.

Do not claim WCAG compliance based solely on automated tooling.

---

## Accessibility and Component Libraries

When using an existing component library:

* Understand the accessibility behavior provided by the library.
* Do not assume every component is accessible in every usage pattern.
* Verify accessible names, focus behavior, keyboard interaction, and state.
* Follow the library's established APIs before overriding behavior.

When replacing a custom implementation with a library component, verify that the new component preserves existing accessibility behavior.

---

## Accessibility Regressions

When fixing an accessibility issue:

1. Reproduce the accessibility problem.
2. Identify the semantic or interaction failure.
3. Implement the smallest appropriate fix.
4. Add automated coverage when practical.
5. Perform manual keyboard validation.
6. Use a screen reader when relevant.
7. Verify that the fix does not break existing interaction behavior.

Accessibility fixes should not introduce new keyboard, focus, or semantic regressions.

---

## Accessibility Definition of Done

Before considering an accessibility-related task complete:

* Semantic HTML is used where appropriate.
* Interactive elements have meaningful accessible names.
* Keyboard interaction works.
* Focus behavior is predictable.
* Focus order is logical.
* Relevant states are exposed correctly.
* Forms provide accessible labels and errors.
* Dynamic content is communicated appropriately.
* Color is not the sole means of conveying information.
* Responsive behavior remains usable.
* Automated accessibility checks have been performed when applicable.
* Manual keyboard testing has been performed for relevant interactions.
* Screen reader testing has been performed when the interaction requires it.
* Relevant WCAG criteria have been considered when applicable.
* No unnecessary ARIA has been introduced.
