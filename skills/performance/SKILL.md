# Performance

## Purpose

Provide guidelines for identifying, measuring, diagnosing, and improving application performance.

Performance work should be evidence-driven.

Do not introduce optimizations based solely on assumptions, trends, or perceived performance problems.

The goal is to improve meaningful user and system performance while preserving correctness and maintainability.

---

## Core Performance Principles

### Measure Before Optimizing

Before making a performance optimization:

1. Identify the suspected bottleneck.
2. Measure the relevant behavior.
3. Establish a baseline when practical.
4. Make the smallest appropriate optimization.
5. Measure again.
6. Verify that the optimization produced a meaningful improvement.

Do not optimize code merely because it looks inefficient.

---

## Optimize Real Bottlenecks

Prioritize performance problems based on actual impact.

Consider:

* Frequency.
* User impact.
* Execution cost.
* Number of affected users.
* Device constraints.
* Network conditions.
* Memory usage.
* Rendering cost.
* Bundle impact.

Do not spend significant complexity solving a negligible performance problem.

---

## Performance vs Complexity

Every optimization has a cost.

Consider whether an optimization introduces:

* More code.
* More abstractions.
* More state.
* More maintenance.
* More cognitive overhead.
* More edge cases.
* More difficult debugging.

Prefer an optimization when its measurable benefit justifies the complexity introduced.

A faster system that is substantially harder to maintain is not automatically a better system.

---

## Establish a Baseline

When investigating a meaningful performance issue, record relevant baseline information.

Depending on the problem, this may include:

* Render count.
* Render duration.
* JavaScript execution time.
* Long tasks.
* Network latency.
* Resource size.
* Bundle size.
* Memory usage.
* CPU usage.
* Interaction latency.
* Core Web Vitals.

A baseline allows the result of an optimization to be evaluated objectively.

---

## Frontend Performance

Frontend performance can be considered across several layers:

```text id="m1j3ot"
Network
  ↓
Resource loading
  ↓
JavaScript execution
  ↓
Rendering
  ↓
Layout / Paint
  ↓
Interaction
```

When investigating a performance issue, identify which layer is actually responsible before optimizing.

Do not optimize rendering when the real bottleneck is network latency.

Do not optimize JavaScript when the real bottleneck is an expensive layout operation.

---

## React Rendering Performance

When a React application renders slowly:

1. Determine which component is rendering.
2. Determine why it is rendering.
3. Determine whether the render is actually expensive.
4. Identify expensive work performed during rendering.
5. Determine whether the work can be reduced or moved.
6. Measure the result.

Consider:

* Parent re-renders.
* State ownership.
* Prop identity.
* Context updates.
* Expensive calculations.
* Large component trees.
* Large lists.
* Unnecessary effects.
* Remounting caused by changing keys.

Do not assume every re-render is a performance problem.

Rendering is normal React behavior.

---

## Memoization

Do not add memoization automatically.

Use `useMemo`, `useCallback`, or `React.memo` only when there is a meaningful reason.

Potential reasons include:

* Expensive calculations.
* Expensive child renders.
* Referential stability required by a dependency.
* A measured rendering bottleneck.

Before adding memoization, consider whether the underlying problem can be solved more simply.

Examples of simpler alternatives:

* Move state closer to where it is used.
* Remove unnecessary derived state.
* Simplify component structure.
* Avoid unnecessary context updates.
* Reduce the amount of data being rendered.

Memoization is not inherently an optimization.

---

## Effects and Performance

Avoid using effects to perform work that can be calculated during rendering or handled directly by an event.

Excessive effects can create:

* Additional renders.
* Synchronization complexity.
* Cascading updates.
* Race conditions.
* Difficult-to-understand execution paths.

Before optimizing an effect, determine whether the effect should exist at all.

---

## State and Performance

State placement can have significant performance implications.

Prefer keeping state close to the components that need it.

Avoid unnecessarily placing frequently changing state in global stores or high-level providers.

When shared state causes widespread rendering:

* Identify which consumers actually need the state.
* Consider splitting state.
* Consider moving state closer to its consumers.
* Consider more targeted subscription mechanisms when appropriate.

Do not introduce a state-management library solely to solve a rendering problem without understanding the actual bottleneck.

---

## Context Performance

Context updates can cause many consumers to re-render.

When context becomes a performance concern:

* Identify which values change frequently.
* Identify which components consume them.
* Consider separating unrelated values into different contexts.
* Consider whether the state should be local.
* Consider whether a more targeted subscription mechanism is appropriate.

Do not split every context preemptively.

Measure before making architectural changes.

---

## Lists

Large lists can become expensive due to:

* Rendering many elements.
* Expensive child components.
* Frequent updates.
* Large DOM trees.
* Layout and paint work.

When a list is slow:

1. Measure the list rendering behavior.
2. Identify whether the bottleneck is data processing, React rendering, DOM size, or layout.
3. Reduce unnecessary work.
4. Consider virtualization when the number of rendered elements is genuinely large.

Do not introduce virtualization for small lists without evidence that it is needed.

---

## Virtualization

Virtualization can reduce the amount of DOM rendered for large collections.

Consider it when:

* Lists contain many items.
* Only a subset of items is visible.
* Rendering the complete collection creates measurable performance problems.

Before introducing virtualization, consider:

* Variable item heights.
* Keyboard navigation.
* Accessibility.
* Scroll behavior.
* Search and filtering.
* Dynamic content.
* Browser compatibility.

Do not use virtualization simply because a list contains more than an arbitrary number of items.

---

## JavaScript Performance

When JavaScript execution is expensive:

* Identify expensive functions.
* Measure execution time.
* Reduce unnecessary calculations.
* Avoid repeated work.
* Move expensive work out of critical interaction paths when appropriate.
* Consider caching when repeated computation is meaningful.

Do not optimize microbenchmarks when the real user experience is unaffected.

Prefer algorithmic improvements over low-level micro-optimizations when they provide meaningful gains.

---

## Algorithms and Data Processing

When processing large datasets:

* Consider algorithmic complexity.
* Avoid repeated searches through the same collection when a more appropriate data structure exists.
* Avoid unnecessary sorting.
* Avoid repeated transformations.
* Consider caching derived data when justified.

Choose data structures based on actual access patterns.

Do not optimize Big-O complexity when the dataset is small and the added complexity is not justified.

---

## Bundle Performance

When bundle size is a concern, inspect:

* Dependency sizes.
* Duplicate dependencies.
* Unused code.
* Code splitting.
* Dynamic imports.
* Tree shaking.
* Large assets.
* Transpilation output.

Use bundle analysis tools when available.

Do not remove a dependency solely because it appears large without understanding which functionality depends on it.

---

## Code Splitting

Use code splitting when it meaningfully reduces initial loading cost.

Good candidates may include:

* Large routes.
* Rarely used features.
* Administrative interfaces.
* Large optional libraries.
* Features that are not required for the initial experience.

Do not split every component.

Excessive code splitting can increase:

* Network requests.
* Loading complexity.
* Waterfalls.
* Runtime overhead.

Optimize the critical path first.

---

## Lazy Loading

Lazy load resources when deferring them improves the actual user experience.

Consider:

* Initial page requirements.
* User interaction patterns.
* Network conditions.
* Resource size.
* Loading latency.

Do not lazy load tiny resources simply because lazy loading is available.

Avoid creating a loading waterfall where resources that are likely to be needed are delayed unnecessarily.

---

## Network Performance

When investigating network performance, consider:

* Number of requests.
* Request size.
* Response size.
* Latency.
* Caching.
* Compression.
* Connection setup.
* Parallelism.
* Request waterfalls.
* API design.

Avoid optimizing frontend code when the primary bottleneck is server response time or network latency.

---

## Data Fetching

Avoid unnecessary requests.

Consider:

* Duplicate requests.
* Requests triggered by unnecessary renders.
* Requests triggered by effects with incorrect dependencies.
* Re-fetching unchanged data.
* Sequential requests that could be parallelized.
* Requests for data that is not currently needed.

Do not aggressively cache everything.

Caching introduces invalidation and consistency concerns.

---

## Images and Static Assets

For large visual assets, consider:

* Appropriate formats.
* Compression.
* Dimensions.
* Responsive sizing.
* Lazy loading when appropriate.
* Priority for above-the-fold assets.

Do not lazy load critical above-the-fold content merely to reduce initial requests.

Do not serve significantly larger assets than the rendered dimensions require without justification.

---

## CSS and Layout Performance

When investigating visual performance, consider:

* Layout thrashing.
* Forced synchronous layout.
* Excessive DOM size.
* Expensive selectors.
* Large style recalculations.
* Repeated style reads and writes.
* Animations that trigger layout.

Prefer animations that can be handled efficiently by the browser when appropriate.

Do not optimize CSS based solely on theoretical selector performance.

---

## Animation

Prefer animations that avoid unnecessary layout and paint work.

When animation performance is poor:

* Identify what properties are being animated.
* Determine whether layout or paint is being triggered.
* Check whether JavaScript is driving the animation unnecessarily.
* Consider browser-native animation mechanisms.

Do not remove animation solely because it exists.

Evaluate whether it affects the actual user experience.

---

## Browser Rendering

When diagnosing rendering performance, consider:

```text id="8q3w6k"
JavaScript
    ↓
Style calculation
    ↓
Layout
    ↓
Paint
    ↓
Composite
```

Determine which stage is expensive before choosing an optimization.

Do not assume that every visual performance issue is caused by JavaScript.

---

## Main Thread

The main thread is responsible for important browser work, including JavaScript execution, style calculation, layout, and other tasks.

When interaction becomes sluggish, investigate:

* Long JavaScript tasks.
* Expensive rendering.
* Large synchronous operations.
* Excessive event handlers.
* Heavy computation during interactions.

Avoid blocking the main thread with unnecessary synchronous work.

For genuinely expensive work, consider appropriate alternatives such as:

* Web Workers.
* Incremental processing.
* Scheduling.
* Server-side processing.

Do not introduce these mechanisms without evidence that the workload justifies them.

---

## Event Handlers

High-frequency events such as:

* `scroll`
* `resize`
* `mousemove`
* `pointermove`
* `input`

can trigger significant work.

When performance problems are associated with high-frequency events:

* Measure handler frequency.
* Minimize work performed per event.
* Consider throttling or debouncing when appropriate.
* Consider `requestAnimationFrame` for visual updates when appropriate.
* Ensure listeners are properly cleaned up.

Do not debounce or throttle every event automatically.

Choose the technique based on the desired behavior.

---

## Memory

When investigating memory problems:

* Identify objects that remain referenced unexpectedly.
* Check event listeners.
* Check subscriptions.
* Check timers.
* Check caches.
* Check retained DOM nodes.
* Check long-lived closures.

Do not assume that increasing memory usage always represents a leak.

Applications legitimately retain memory for caching and active state.

---

## Performance and Accessibility

Performance optimizations must not reduce accessibility.

Before accepting an optimization, verify that it does not negatively affect:

* Keyboard navigation.
* Focus management.
* Screen reader behavior.
* Content availability.
* Loading feedback.
* Reduced-motion behavior.

Do not remove semantic content merely because it appears to add DOM or rendering cost without measuring the actual impact.

---

## Performance and Correctness

Never sacrifice correctness for an unmeasured performance improvement.

Do not:

* Remove necessary validation.
* Skip required state updates.
* Hide errors.
* Introduce stale data.
* Break synchronization.
* Remove important accessibility behavior.

Performance is one dimension of quality, not a replacement for correctness.

---

## Profiling Tools

Use appropriate tools when available.

Examples include:

* Browser Performance panel.
* React DevTools Profiler.
* Network panel.
* Lighthouse.
* Bundle analyzers.
* Memory profiler.
* Performance APIs.
* Production monitoring.

Use profiling data to guide decisions.

Do not treat a single synthetic benchmark as proof of real-world performance.

---

## Production Performance

Development performance may differ significantly from production performance.

When relevant, consider:

* Production builds.
* Minification.
* Compression.
* Caching.
* Real network conditions.
* Device capabilities.
* Real user monitoring.
* Production traffic patterns.

Do not assume development profiling perfectly represents production behavior.

---

## Core Web Vitals

When applicable, consider the user's actual experience through metrics such as:

* Largest Contentful Paint (LCP).
* Interaction to Next Paint (INP).
* Cumulative Layout Shift (CLS).

Use these metrics as signals for user experience, not as isolated targets.

When a metric is poor:

1. Identify what contributes to it.
2. Measure the relevant behavior.
3. Address the underlying bottleneck.
4. Measure again.

Do not optimize a metric without understanding the user-facing problem behind it.

---

## Performance Regression Prevention

When a significant performance problem has been fixed:

* Add automated coverage where appropriate.
* Preserve useful performance measurements.
* Document important constraints.
* Consider whether a regression could be detected automatically.

Do not add complex performance infrastructure for insignificant optimizations.

---

## Performance Definition of Done

A performance task is complete when:

* The performance problem has been identified.
* Relevant measurements or evidence support the diagnosis.
* A baseline exists when practical.
* The optimization addresses the actual bottleneck.
* The change does not introduce unnecessary complexity.
* Correctness is preserved.
* Accessibility is preserved.
* The result has been measured or otherwise meaningfully validated.
* Relevant performance regressions have been considered.
* Any remaining limitations are communicated.
