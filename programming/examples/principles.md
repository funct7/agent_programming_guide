# Principle Examples

Examples clarify accepted principles but do not replace them. Extract the common principle; do not treat the example list as exhaustive.

## Q&A Modeling Case Study

```yaml
kind: guide-example
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
kind: guide-example
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
