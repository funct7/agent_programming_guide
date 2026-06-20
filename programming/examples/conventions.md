# Convention Examples

Examples clarify accepted conventions but do not replace them. Extract the common convention; do not treat the example list as exhaustive.

## Helper Method On Target Type

```yaml
id: example.helper-method-on-target-type
summary: Shows that helper methods that only reconstruct a target type should not be placed on the current caller.
tags: []
illustrates: [swift.helper-method-placement]
```

Avoid placing a pure target-reconstruction helper on the type that happens to call it:

```swift
private extension BookUseCaseImpl {

    func _makePassage(_ passage: Book.Passage, id: Book.Passage.ID, images: [URI]) -> Book.Passage {
        .make(
            id: id,
            association: passage.association,
            text: passage.text,
            images: images,
            position: passage.position,
            tags: passage.tags,
            addedAt: passage.addedAt,
            metadata: passage.metadata
        )
    }

}
```

`BookUseCaseImpl` is only the caller. The operation copies a `Book.Passage` with overrides, so either inline the construction or put the helper on `Book.Passage`:

```swift
public extension Book.Passage {

    static func from(_ passage: Self, id: ID, images: [URI]) -> Self {
        .make(
            id: id,
            association: passage.association,
            text: passage.text,
            images: images,
            position: passage.position,
            tags: passage.tags,
            addedAt: passage.addedAt,
            metadata: passage.metadata
        )
    }

}
```

Keeping the helper on the caller is justified only when the caller's state, dependencies, invariants, lifecycle, or orchestration policy are meaningfully involved.

## Early Exit Without Double Negatives

```yaml
id: example.early-exit-without-double-negatives
summary: Shows using if for direct early exits instead of guard with negated Boolean flow.
tags: []
illustrates: [swift.early-exit-boolean-conditions]
```

Avoid forcing a direct no-op or failure condition into `guard` when doing so creates double-negative flow:

```swift
guard !referralCode.isEmpty else { return }
guard referralCode.isNotEmpty else { return }
```

Both forms require the reader to evaluate a condition and then invert it again through `else`.

Prefer naming the exit condition directly:

```swift
if referralCode.isEmpty { return }
```

Use `guard` when the code below requires a positive continuation condition, especially for required binding:

```swift
guard let user else { return }
```
