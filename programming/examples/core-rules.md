# Core Rule Examples

Examples clarify accepted core rules but do not replace them. Extract the common rule; do not treat the example list as exhaustive.

## Product Type vs Sum Type

```yaml
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

## Property Change With Required Synchronization

```yaml
id: example.property-change-required-synchronization
summary: Shows constraining state changes so required synchronization cannot be bypassed.
tags: []
illustrates: [core.behavior-enforcing-design]
```

If changing a stored value must also update other state or UI, avoid exposing a separate helper that callers must remember to use:

```swift
private var referralCode = ""

private func setReferralCode(_ referralCode: String) {
    self.referralCode = referralCode
    referralCodeButton.isVisible = referralCode.isNotEmpty
    referralCodeButton.textLabel.text = referralCode
}
```

This shape leaves two possible mutation paths: direct assignment and the helper. Direct assignment can bypass the required synchronization.

Constrain the mutation boundary so the required behavior is attached to the value change:

```swift
private var referralCode = "" {
    didSet {
        referralCodeButton.isVisible = referralCode.isNotEmpty
        referralCodeButton.textLabel.text = referralCode
    }
}
```

The point is not that every property needs `didSet`. The point is that when behavior must always happen with a change, the interface should make bypassing that behavior unavailable.
