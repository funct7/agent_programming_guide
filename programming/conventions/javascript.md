# JavaScript Programming Guide

## Prefer Arrow Functions

```yaml
id: javascript.prefer-arrow-functions
tier: convention
review_passes: [structural]
summary: Prefer named arrow functions in JavaScript and TypeScript whenever possible; use function declarations only when their specific language behavior is needed.
tags: []
applies_when:
  constructs: [function, arrow-function, component, hook, callback, helper, module-api]
```

Prefer arrow functions in JavaScript and TypeScript whenever possible.

Use named `const` bindings for exported functions, React components, hooks, callbacks, and local helpers. Avoid anonymous default-export arrow functions because they weaken searchability and debugging names.

Use traditional `function` declarations only when their specific language behavior is useful, such as overload signatures in TypeScript, generators, declaration-style APIs, or an external API shape that expects a function declaration.
