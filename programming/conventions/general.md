# General Programming Conventions

## Semantic Member Grouping

```yaml
id: convention.semantic-member-grouping
tier: convention
review_passes: [structural, substantive]
summary: Group members by semantic responsibility instead of placing cross-cutting APIs in incidental sections.
applies_when:
  constructs: [api-surface, interface, protocol, class, struct, extension, method, property, file-layout]
```

As a rule of thumb, group related methods and properties by the concept or responsibility they belong to. The exact grouping depends on context and is not a mechanical rule. Do not place a member in a section merely because it mentions one of that section's types when its behavior spans multiple concepts. Put cross-cutting APIs in a shared section or at the boundary where readers expect combined behavior.

## Observable Time-Varying Values

```yaml
id: convention.observable-time-varying-values
tier: convention
review_passes: [substantive]
summary: Make observable/reactive time-varying values explicit instead of hiding them as plain mutable properties.
applies_when:
  constructs: [state, observable, stream, publisher, property, state-machine, ui-state, lifecycle]
```

Not every mutable value is observable state. A plain mutable property is fine when mutation is local and no surrounding code is expected to react to it.

When a value is expected to change over time and those changes should drive behavior or UI updates, represent it with the observable/state mechanism used by that context. The code should make it obvious whether a value is just mutable implementation detail or reactive feature state.
