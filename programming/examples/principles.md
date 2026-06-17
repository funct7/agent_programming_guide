# Principle Examples

Examples clarify accepted principles but do not replace them. Extract the common principle; do not treat the example list as exhaustive.

## Q&A Modeling Case Study

```yaml
id: example.qna-modeling-case-study
summary: Shows how Q&A server list/thread shapes should map into one domain inquiry concept instead of transport-shaped domain types.
tags: []
illustrates: [principle.domain-modeling, core.domain-boundaries]
```

The Q&A API has list and thread endpoints, but that does not mean Domain needs separate `Summary`, `Thread`, and `Page` concepts.

The domain concept is one inquiry/Q&A resource:

- it has identity,
- it is written by a user,
- it belongs to a category,
- it has private or public content,
- public content can contain one or more question/answer posts.

Pagination, endpoint summaries, status strings, and answer counts are transport/application details unless they are truly part of the business concept.

## Derived Authentication State

```yaml
id: example.derived-authentication-state
summary: Shows how currentUserID can be the source of truth instead of storing isSignedIn beside it.
tags: []
illustrates: [principle.derived-values, core.state-shape]
```

Do not store `isSignedIn` beside `currentUserID` when the user ID already determines the sign-in state.

Prefer one source of truth:

```swift
let currentUserID: User.ID?
```

Then derive the boolean when needed:

```swift
let isSignedIn = currentUserID != nil
```

If a feature only operates for signed-in users, make that requirement explicit by storing a non-optional `User.ID` in its context instead of carrying both a boolean and an optional ID.

## Control Flow Behavior Boundaries

```yaml
id: example.control-flow-behavior-boundaries
summary: Shows how separate control-flow statements reduce cognitive load by separating required state extraction from valid no-op behavior.
tags: []
illustrates: [principle.physical-behavior-boundaries]
```

Control flow readability is one application of physical behavior boundaries.

Use one `guard` to extract required state, then another statement for a separate validity check. Do not combine state extraction, validity checks, authorization checks, and no-op checks just to make the code shorter.

Prefer this:

```swift
guard let user else { return .error(.missingUser) }

if user.isSuspended {
    return .empty()
}

return service.update(user)
```

Over this:

```swift
guard let user, !user.isSuspended else {
    return .empty()
}

return service.update(user)
```

The first version separates "required state exists" from "valid no-op condition," so the reader does not have to untangle two different behavioral meanings from one guard.

## Rx Pipeline Behavior Boundaries

```yaml
id: example.rx-pipeline-behavior-boundaries
summary: Shows how Rx operators can mark behavioral boundaries between lookup absence handling and update orchestration.
tags: []
illustrates: [principle.physical-behavior-boundaries]
```

Prefer separating "lookup and convert missing value into error" from "update the passage":

```swift
return _annotationStore.getPassage(id: passage.id)
    .compactMap(\.self)
    .ifEmpty(switchTo: .error(BookUseCaseError.notFound))
    .flatMapCompletable { [self] current in
        _imageStore.persistImages(passage.images)
            .flatMapCompletable { [self] images in
                let updated = withVar(passage, \.images, images)
                try _validatePassageBody(updated)

                let imagesToDeleteOnFailure = Array(Set(images).subtracting(current.images))
                return _annotationStore.updatePassage(updated)
                    .catch { [self] error in
                        _imageStore._deleteImagesBestEffort(imagesToDeleteOnFailure)
                            .andThen(Completable.error(error))
                    }
                    .andThen(_imageStore._deleteRemovedImagesBestEffort(old: current.images, new: images))
            }
    }
```

Instead of this:

```swift
return _annotationStore.getPassage(id: passage.id)
    .flatMapCompletable { [self] current in
        guard let current else { return .error(BookUseCaseError.notFound) }

        return _imageStore.persistImages(passage.images)
            .flatMapCompletable { [self] images in
                ...
            }
    }
```

The first version makes the behavior boundary explicit: after `ifEmpty`, the reader knows the missing-passage case is resolved. The next closure starts a new unit of behavior: finalizing images and updating the passage.
