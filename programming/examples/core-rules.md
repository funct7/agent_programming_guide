# Core Rule Examples

Examples clarify accepted core rules but do not replace them. Extract the common rule; do not treat the example list as exhaustive.

## Product Type vs Sum Type

```yaml
kind: guide-example
id: example.product-type-vs-sum-type
summary: Shows how a product type can represent invalid states and how a sum type removes the invalid combinations.
tags: []
illustrates: [core.state-shape]
```

A product type can accidentally represent invalid states:

```swift
struct Content {
    let text: String
    let isPrivate: Bool
}
```

If private content means the text is not knowable, use a sum type:

```swift
enum Content {
    case `private`
    case `public`(PublicContent)
}
```

The rule is broader than privacy: when fields create invalid combinations, model the real alternatives directly.
