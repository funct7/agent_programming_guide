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
summary: Preserve synthesized memberwise initialization for structs, and use static factories only when they add meaningful construction behavior or semantics.
tags: [swift-lang, constructor, type, access-level]
applies_when:
  language: swift
  constructs: [public-type, public-struct, public-class, initializer, public-api, factory]
mechanical_check:
  search_terms: ["public struct", "public final class", "public class", "public init", "static func make"]
```

For public structs, prefer preserving synthesized memberwise initialization instead of exposing a custom `public init(...)` by default. When a public construction API is needed, a `public static` factory method can preserve the synthesized memberwise initializer.

This synthesis rationale does not apply to classes. Do not prefer a static factory over an initializer merely because the type is public or concrete.

For any type, use a static factory when it provides meaningful construction behavior or semantics. Do not add a factory that only forwards ordinary construction without adding such meaning.

Preferred pattern:
- keep the struct definition as simple as possible
- use synthesized code whenever possible
- use a `public static` factory when a struct needs a public construction API without suppressing synthesized memberwise initialization
- choose class construction APIs based on their actual contract rather than a general factory preference
- require meaningful construction behavior or semantics for static factories on any type
- only add a custom initializer when there is a specific need that cannot be expressed cleanly otherwise

## Initializer Calls

```yaml
id: swift.initializer-calls
tier: convention
review_passes: [structural]
summary: Prefer Self.init(...) over Self(...) when invoking an initializer explicitly.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [initializer-call, factory]
```

Prefer `Self.init(...)` over `Self(...)` when invoking an initializer explicitly. This keeps initializer calls recognizable to Xcode/source tooling and preserves syntax highlighting. This convention does not apply to named types: continue to write `SomeType(...)`, not `SomeType.init(...)`.

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

## Early Exit Boolean Conditions

```yaml
id: swift.early-exit-boolean-conditions
tier: convention
review_passes: [structural, substantive]
summary: Avoid double-negative early-exit flow; use guard for positive continuation requirements and if for direct exit conditions.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [guard, if, boolean, negation, early-exit, control-flow]
```

For early exits, prefer expressing the condition that exits directly.

Use `guard` when the condition describes a positive requirement for the code path that continues.

When a `guard` condition would need a negative expression and then invert it again through `else`, prefer an `if` statement that directly names the exit condition. This avoids double-negative control flow and keeps the early exit easier to read.

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

## Extension Body Cohesion

```yaml
id: swift.extension-body-cohesion
tier: convention
review_passes: [structural]
summary: Keep each Swift extension body cohesive; split extensions by conformance, visibility, lifecycle, or member purpose when they differ.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [extension, conformance, method, computed-property, factory, initializer, access-level, api-surface]
```

Keep each Swift extension body cohesive. Members in the same extension should have the same reason to be grouped together.

Prefer separate extension bodies when members differ by:
- protocol conformance or interface being implemented
- intended visibility or API surface
- lifecycle or usage boundary
- member purpose, such as construction, conversion, conformance requirements, computed projections, or internal helpers

For example, do not lump unrelated `Equatable`, `Hashable`, and `Identifiable` implementations into one generic extension body. Likewise, avoid mixing public construction APIs with internal convenience helpers when their callers and visibility are different.

This is a readability convention, not a strict one-extension-per-method rule. Keep related small members together when they form one coherent API surface or one conformance implementation.

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

## Weak Versus Unowned References

```yaml
id: swift.weak-versus-unowned
tier: convention
review_passes: [structural, substantive]
summary: Use unowned when the referenced object is guaranteed to outlive the owner; reserve weak for uncertain lifecycles.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [reference, ownership, weak, unowned, lifecycle, UIKit, IBOutlet]
```

Use `unowned` when the referenced object is guaranteed to outlive the owner under the local lifecycle contract.

Reserve `weak` for references whose lifecycle is genuinely uncertain, where the referenced object may disappear independently and `nil` is a meaningful runtime state.

UIKit outlet references owned by a view controller's loaded view hierarchy should generally be `unowned` implicitly unwrapped optionals, because the view hierarchy owns the views and the view controller should not treat connected outlets as independently disappearing.

## Implicitly Unwrapped Optionals

```yaml
id: swift.implicitly-unwrapped-optionals
tier: convention
review_passes: [structural, substantive]
summary: Use implicitly unwrapped optionals for late-initialized values that are required after setup and should fail loudly if missing.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [optional, implicitly-unwrapped-optional, late-initialization, IBOutlet, lifecycle, UIKit]
```

Use implicitly unwrapped optionals for values that are assigned after initialization but are required for normal operation once setup has completed.

Treat `T!` as a late-initialized required value, not as a nullable value. If absence is a valid state that callers should handle, use `T?` instead.

UIKit outlets that must be connected for the screen to function are a normal use case for implicitly unwrapped optionals.

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

## Local Constants

```yaml
id: swift.local-constants
tier: convention
review_passes: [substantive]
summary: Avoid single-use local constants when the inline expression remains easy to read.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [local-constant, let-binding, control-flow, reducer]
```

Avoid introducing a local constant when the value is used only once and the inline expression remains easy to read.

Local constants are useful when they name a meaningful intermediate concept, avoid duplicated work, clarify a complicated expression, or help separate distinct steps. Do not add them mechanically for short single-use expressions.

## Empty Collection Transforms

```yaml
id: swift.empty-collection-transforms
tier: convention
review_passes: [substantive]
summary: Do not branch around collection map/flatMap only to handle the empty case.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [collection, map, flatMap, isEmpty, control-flow]
```

Do not add an `isEmpty` branch before applying `map`, `flatMap`, or equivalent collection transforms only to handle the empty case. Collection transforms already preserve emptiness, so the branch adds noise unless the empty path has meaningfully different behavior.

## Prefer Key Paths And Function Composition In Transforms

```yaml
id: swift.prefer-keypath-function-composition
tier: convention
review_passes: [structural, substantive]
summary: Prefer key-path expressions and function composition over inline anonymous transform closures when they express the operation clearly.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [closure, map, flatMap, compactMap, keypath, function-composition]
```

Prefer key-path expressions and function composition over inline anonymous closures in `map`, `flatMap`, `compactMap`, and similar transform positions when they express the operation clearly.

Inline transform closures make it easy to add hidden side effects inside a transformation. If the operation is pure projection or function composition, express that directly. If the operation needs side effects, do not hide it in a transform closure.
