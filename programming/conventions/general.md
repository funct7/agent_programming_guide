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

## Data Holder Mutability by Type Semantics

```yaml
id: convention.data-holder-mutability
tier: convention
review_passes: [structural, substantive]
summary: Default reference-type data holders to immutable state, while permitting mutable value-type fields when the domain allows them to change.
tags: [type]
applies_when:
  constructs: [data-holder, reference-type, value-type, class, struct, stored-property, identity, invariant]
```

For plain data holders, choose mutability using both the domain meaning of the data and the language's reference or value semantics.

Stored state on a reference-type data holder should be immutable by default. Aliasing allows mutation through one reference to become visible through every other reference, bypassing the mutability of enclosing containers and making changes harder to control. Permit mutable stored state only when the domain requires it and the design establishes clear ownership or a behavior-enforcing mutation boundary. This default does not apply indiscriminately to behavior-bearing reference types whose responsibility includes managing state changes.

A value-type data holder may expose mutable fields when the domain permits those fields to change freely. Mutating a nested value still requires mutable access to its enclosing value, so do not make such fields immutable merely to imitate the defensive immutability needed for aliased reference types.

Identity, fixed configuration, completed snapshots, and other invariants remain immutable regardless of the type's reference or value semantics.

Property-binding immutability is not necessarily transitive immutability. An immutable property that refers to a mutable object can still expose changes inside that object. Apply only the parts of this convention supported by the language's type system; languages without user-defined value types still apply the reference-type guidance.

## Symmetric Multiline Delimiters

```yaml
id: convention.symmetric-multiline-delimiters
tier: convention
review_passes: [structural]
summary: Keep multiline delimiter placement symmetric between opening and closing delimiters.
applies_when:
  constructs: [function-call, initializer-call, array-literal, dictionary-literal, object-literal, closure, block, multiline-expression, delimiter]
```

When parentheses, brackets, braces, or similar delimiters open a multiline expression, mirror that structure at the closing delimiter.

If an opening delimiter introduces a multiline block, place the matching closing delimiter on its own line at the corresponding indentation level. Do not attach that closing delimiter to the final argument, element, or statement.

When multiple opening delimiters appear on the same line, keep their matching closing delimiters together on the same line unless splitting them materially improves readability.

Inline delimiters are fine when the whole expression remains inline and readable. Domain-specific formats may follow their established conventions when readability or ecosystem norms clearly favor them.

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
