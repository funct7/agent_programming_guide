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
