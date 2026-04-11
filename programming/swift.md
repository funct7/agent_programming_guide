# Swift Language Programming Guide

## Synthesize First

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

## Public Struct Construction

For `public struct`, prefer preserving synthesized construction behavior instead of exposing a custom `public init(...)` by default.

When a public construction API is needed, prefer a `public static` factory method over a `public init(...)`.

This is especially useful because adding a custom initializer prevents the compiler from providing the synthesized memberwise initializer.

Preferred pattern:
- keep the struct definition as simple as possible
- use synthesized code whenever possible
- expose `public static` factory methods for public construction APIs
- only add a custom initializer when there is a specific need that cannot be expressed cleanly otherwise

## Factory Naming

Use these names consistently:
- `make`: regular construction
- `from`: construction from another type or representation
- `get`: retrieval where a long-lived or reused instance may be returned, and a new instance is not implied

### Note On `get`

`get` should communicate retrieval rather than guaranteed allocation. Use it only when returning an existing, cached, shared, or otherwise non-new instance is a meaningful part of the API semantics.
