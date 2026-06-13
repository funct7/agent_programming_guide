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
