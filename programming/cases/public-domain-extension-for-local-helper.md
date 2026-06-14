# Public Domain Extension For Local Helper

```yaml
id: case.public-domain-extension-for-local-helper
status: reported
summary: A local use-case helper was added as a public domain type extension even though it was not domain-level API.
tags: []
related_rules: [preference.extension-locality]
```

## Summary

A local use-case helper was added as a public domain type extension even though it was not domain-level API. The authoritative rule already exists under `preference.extension-locality`; this case records a failure to apply that rule when moving helper logic.

## Status

Reported case. Not authoritative.

## Related Guide Areas

- Preferences: extension locality
- Preferences: helper placement
- Swift API surface control

## Current Interpretation

When fixing misplaced helper methods, do not overcorrect by making helper APIs public or placing them in the domain type's declaration file. If the helper is only used by one implementation file and is not meaningful as domain API, keep it as a private same-file extension near the use site.

## Reported Incidents

### 2026-06-14: Notes BookUseCase Copy Helper

#### Context

`BookUseCaseImpl` needed small copy helpers to create modified `Book.Passage` and `Note` values while assigning generated IDs and persisted image URIs. The first implementation placed `_makePassage` and `_makeNote` on `BookUseCaseImpl`, which was wrong because the helpers only reconstructed target values. The attempted fix moved them into `public extension Book.Passage` and `public extension Note` as `replacing(...)`.

#### Feedback

Josh pointed out that these `replacing(...)` helpers are not meaningful domain-level constructors. They only support `BookUseCaseImpl`, so they should not live in the public `Book.Passage` or `Note` declaration files. This violated `preference.extension-locality`.

#### Nuance

The correct placement is not always the target type's public file. Helper ownership and extension locality both apply: target-owned logic can still be local implementation support. If the helper is not part of the wider API surface, place it in a private same-file extension in the implementation file that uses it.

#### Possible Promotion

No promotion needed yet. The existing `preference.extension-locality` rule is clear; this case exists to make the failure mode easier to remember during future review and feedback application.
