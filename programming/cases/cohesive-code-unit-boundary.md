# Cohesive Code Unit Boundary

```yaml
id: case.cohesive-code-unit-boundary
status: reported
summary: A code unit mixed distinct responsibilities instead of representing one coherent operation or concept.
tags: []
related_rules: [principle.physical-behavior-boundaries]
```

## Summary

A code unit mixed distinct responsibilities instead of representing one coherent operation or concept. This may point to a future principle about one code unit doing one thing, but for now it is recorded as a reported case related to `principle.physical-behavior-boundaries`.

## Status

Reported case. Not authoritative.

## Related Guide Areas

- Principles: physical behavior boundaries
- Principles: modeling by change boundaries
- Principles: helper API justification
- Swift conventions: extension body cohesion
- Swift conventions: RxSwift pipeline boundaries

## Current Interpretation

A code unit should represent one coherent responsibility. This applies beyond function names: a type, extension body, function, method, helper, closure, or Rx operator should have a clear single purpose at its abstraction level. When validation, filtering, derivation, creation, mutation, IO, and compensation are distinct responsibilities, they should usually be separated into explicit boundaries instead of being hidden inside one unit.

This overlaps with `principle.physical-behavior-boundaries`, but may deserve a separate principle later after reviewing overlap with the rest of the guide.

## Reported Incidents

### 2026-06-27: Notes Tag Definition Helper And Extension

#### Context

`BookUseCaseImpl` had a private tag-coordination extension containing `_ensureNewTagDefinitions`, `_ensureTagDefinitions`, and `_makeMissingTagDefinitions`. The block mixed repository orchestration, requested-tag screening, retired-tag validation, missing-definition derivation, ID assignment, and creation of `TagDefinition` values.

#### Feedback

Josh pointed out that the problem is broader than a misleading function name. The concern is that one unit of code should do one thing, whether that unit is a type, extension body, function, method, closure, or pipeline operator. In this case, the extension and helper methods mixed distinct responsibilities that should be separated.

#### Nuance

The issue is not only whether a helper deserves to exist or whether its name is honest. The larger concern is cohesive responsibility boundaries: distinct operations should not be bundled into one unit just because they are part of the same caller flow.

#### Possible Promotion

If similar cases repeat, consider adding a principle that each code unit should represent one coherent responsibility at its abstraction level. The principle should be reviewed against existing physical-boundary, change-boundary, helper-justification, and extension-cohesion guidance before promotion.

### 2026-07-14: Annotation Assignment Fetch And Transformation Pipeline

#### Context

GRDB annotation assignment helpers inlined `fetchAll` into deterministic grouping and combined projecting records to tags with converting those tags into a set inside one `mapValues` operation.

#### Feedback

Josh distinguished two boundaries: non-deterministic database reads should be materialized before deterministic processing, while deterministic transformations may remain chained as long as each stage performs one semantic transformation.

#### Nuance

The concern is not chaining itself or a mechanical preference for local constants. Pure grouping, projection, and representation conversion can form one readable chain; the database read needs a separate boundary, and distinct deterministic transformations need distinct stages.

#### Possible Promotion

This incident supports clarifying `principle.physical-behavior-boundaries` rather than introducing an overlapping principle.
