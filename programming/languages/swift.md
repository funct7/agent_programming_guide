# Swift Language Programming Guide

## Synthesize First

```yaml
id: swift.synthesize-first
tier: convention
review_passes: [structural]
summary: Use compiler-synthesized code whenever possible; write custom implementations only for specific behavior.
tags: []
applies_when:
  language: swift
  constructs: [initializer, protocol-conformance, Equatable, synthesized-code]
```

Rule of thumb: use compiler-synthesized code whenever possible.

Apply this preference to:
- memberwise/synthesized initializers
- protocol conformances such as `Equatable` when synthesis is available
- other compiler-synthesized implementations

Only write a custom implementation when there is a specific need for custom behavior.

### Practical Interpretation

Examples of when custom code is justified:
- validation or invariants must be enforced during construction
- construction requires non-trivial transformation
- synthesized protocol behavior is semantically wrong for the type
- API clarity materially improves with a custom implementation

If none of those apply, prefer the synthesized form.

## Public Concrete Type Construction

```yaml
id: swift.public-concrete-type-construction
tier: convention
review_passes: [structural]
summary: For public concrete Swift types, preserve synthesized construction when possible and avoid public init unless specifically justified.
tags: [swift-lang, constructor, type, access-level]
applies_when:
  language: swift
  constructs: [public-type, public-struct, public-class, initializer, public-api, factory]
mechanical_check:
  search_terms: ["public struct", "public final class", "public class", "public init", "static func make"]
```

For public concrete Swift types, prefer preserving synthesized construction behavior instead of exposing a custom `public init(...)` by default.

When a public construction API is needed, prefer a `public static` factory method over a `public init(...)`.

This is especially important for `public struct` because adding a custom initializer prevents the compiler from providing the synthesized memberwise initializer. For public concrete classes, the concern is API surface: callers should not get direct construction unless that is deliberately part of the type's contract.

Preferred pattern:
- keep the struct definition as simple as possible
- use synthesized code whenever possible
- expose `public static` factory methods for public construction APIs
- only add a custom initializer when there is a specific need that cannot be expressed cleanly otherwise

## Data Type Property Mutability

```yaml
id: swift.data-type-property-mutability
tier: convention
review_passes: [structural, substantive]
summary: For plain Swift data types, choose let or var based on whether the property is conceptually stable or normally changes.
tags: [swift-lang, type]
applies_when:
  language: swift
  constructs: [struct, data-type, stored-property, request, state, value-object]
```

For plain data types with no meaningful behavior, stored property mutability should reflect the meaning of the value.

Use `let` when the property is conceptually stable after construction, such as identity, fixed configuration, or values that should not change as part of the type's normal lifecycle.

Use `var` when changing the property is a normal operation for the type, such as request parameters, paging cursors, form fields, mutable feature state, or values that are commonly copied and modified.

## Factory Naming

```yaml
id: swift.factory-naming
tier: convention
review_passes: [structural]
summary: Use make for regular construction, from for conversion, and get for retrieval where reuse may be meaningful.
tags: []
applies_when:
  language: swift
  constructs: [factory, construction, conversion, retrieval]
```

Use these names consistently:
- `make`: regular construction
- `from`: construction from another type or representation
- `get`: retrieval where a long-lived or reused instance may be returned, and a new instance is not implied

### Note On `get`

`get` should communicate retrieval rather than guaranteed allocation. Use it only when returning an existing, cached, shared, or otherwise non-new instance is a meaningful part of the API semantics.

## Positive Boolean Naming

```yaml
id: swift.positive-boolean-naming
tier: convention
review_passes: [structural, substantive]
summary: Prefer positive Boolean property and method names unless the negative state is the expected default being queried.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [boolean, property, method, computed-property, predicate]
mechanical_check:
  search_terms: ["isInvalid", "isNot", "hasNo", "shouldNot", "cannot"]
```

Prefer positive Boolean property and method names.

Avoid negative names such as `isInvalid`, `isNotReady`, or `hasNoItems` unless the negative state is the expected or default domain state being queried. Positive names keep call sites easier to read, especially when a condition must be negated.

## Helper Method Placement

```yaml
id: swift.helper-method-placement
tier: convention
review_passes: [structural, substantive]
summary: Put helper methods on the type that owns the logic, not merely on the current caller.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [helper, method, extension, service, domain-model, factory, conversion]
mechanical_check:
  search_terms: ["private extension", "func _make", "func _create", "func _from", "Self.init", ".make("]
```

Put helper methods on the type that owns the logic, not merely on the type that currently calls the helper.

If a helper only constructs, copies, transforms, validates, or derives values for a specific target type from parameters, prefer placing it on that target type or inlining it. Keep a helper on a service, use case, view model, or other caller type only when that caller's own state, dependencies, invariants, lifecycle, or orchestration policy are meaningfully involved.

## Explicit Existentials

```yaml
id: swift.explicit-existentials
tier: convention
review_passes: [structural]
summary: Use any when a value stores or accepts an interface existential rather than a concrete type.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [protocol, existential, property, parameter, dependency-injection]
mechanical_check:
  search_terms: ["private let _", "public init", "protocol", ": AnyObject"]
```

When a value stores, accepts, or returns an interface/protocol existential rather than a concrete type or generic constraint, write the type with `any`.

Use `any SomeProtocol` for stored dependencies and initializer parameters. Do not add `any` to protocol conformances, protocol inheritance clauses, or generic constraints.

## Optional Presence Binding

```yaml
id: swift.optional-presence-binding
tier: preference
review_passes: [structural]
summary: Prefer optional binding over direct nil comparison when checking optional presence.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [optional, condition, binding]
```

Medium preference: when checking whether an optional value exists, prefer optional binding over direct nil comparison when in doubt.

Prefer:

```swift
if let _ = store.state.qna.currentUserID {
    ...
}
```

Over:

```swift
if store.state.qna.currentUserID != nil {
    ...
}
```

This is a readability preference, not a correctness rule. Use judgment when a direct nil comparison is materially clearer in local context.
