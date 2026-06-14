# Repeated Mistakes

These are recurring agent mistakes. They are not preferences or design principles; they are failure modes to check explicitly before finishing work.

## Swift Explicit Returns In Multi-Statement Functions

```yaml
id: repeated.swift-explicit-returns
tier: repeated-mistake
review_passes: [repeated-check]
summary: In value-returning Swift functions, do not mix explicit return branches with expression-only branches in the same switch or conditional body.
applies_when:
  language: swift
  constructs: [switch, if, return, reducer, transition, factory, conversion]
mechanical_check:
  search_terms: ["return ", "switch", "case ", ".make", ".from"]
```

In Swift functions that return a value, do not mix explicit `return` branches with expression-only branches in the same `switch` or conditional body.

If any branch needs `return`, use `return` for every value-producing branch in that function body. Expression-only branches compile only when the surrounding function body remains a valid single-expression form.

This also applies inside a branch that has more than one statement. If a branch declares local values and then ends with a factory or conversion expression such as `.make(...)` or `.from(...)`, write `return .make(...)` or `return Type.from(...)` as appropriate. Omitting `return` there can make Swift lose the expected return context and produce misleading inference errors such as `cannot infer contextual base`.

## Misplaced Helper Methods On The Caller

```yaml
id: repeated.misplaced-helper-methods-on-caller
tier: repeated-mistake
review_passes: [repeated-check]
summary: Do not put helper methods on the current caller when the receiver type is not involved in the logic.
applies_when:
  constructs: [helper, method, extension, service, domain-model, factory, conversion]
mechanical_check:
  search_terms: ["private extension", "func _make", "func _create", "func _from", "Self.init", ".make("]
```

Do not place a helper method on a service, use case, view model, or other current caller merely because that type invokes the helper.

If the helper only constructs, copies, transforms, validates, or derives values for a specific target type using parameters, put the helper on the target type or inline the operation. The receiver should be involved in the logic through its own state, dependencies, invariants, or policy.

Before keeping a private helper on the caller, check whether the method actually uses `self` in a meaningful way. Dependency access, lifecycle policy, orchestration policy, or caller-owned state can justify caller placement. Pure target reconstruction usually cannot.

## Duplicating Existing Local Utility APIs

```yaml
id: repeated.duplicating-existing-local-utility-apis
tier: repeated-mistake
review_passes: [repeated-check]
summary: Search project and in-workspace dependencies before adding generic utility helpers.
applies_when:
  constructs: [helper, utility, extension, predicate, string, collection]
mechanical_check:
  search_terms: ["isBlank", "isNotBlank", "isEmpty", "trimmingCharacters", "private func _is", "private var _is"]
```

Before adding a local helper for generic behavior such as blank-string checks, collection checks, simple predicates, or common value transforms, search the project and imported in-workspace libraries for an existing utility.

Do not duplicate helpers that already exist in `Common`, `LazyKit`, or another dependency already imported by the file. Use the existing API unless there is a specific semantic difference worth naming locally.
