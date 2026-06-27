# Feedback Cases Index

Feedback cases are reported incidents, not authoritative guide rules. Use them to detect repeated feedback patterns. Do not apply a case as a rule unless Josh confirms promotion into `core-rules.md`, `principles.md`, or `preferences.md`.

## How To Use

- Skim this index before creating a new case file.
- Read an individual case file only when its title or summary appears relevant.
- Add a new reported incident to an existing case when the feedback has the same reusable concern.
- Add nuance to an existing case when the feedback is related but meaningfully different.
- Create a new case only when no existing case captures the concern.
- Propose promotion into an authoritative tier only after repeated cases reveal a clear reusable rule, principle, or preference.

## Cases

| Case | Summary | Related Guide Area | Status |
| --- | --- | --- | --- |
| [Dependency Injection With Over-Specific Bridge Types](./dependency-injection-over-specific-bridge-types.md) | A feature depended on a concrete bridge even though it only needed a stream of close events. | Dependency boundaries, interface segregation | Reported case |
| [Public Domain Extension For Local Helper](./public-domain-extension-for-local-helper.md) | A local use-case helper was added as a public domain type extension even though it was not domain-level API. | Extension locality, helper placement | Reported case |
| [Cohesive Code Unit Boundary](./cohesive-code-unit-boundary.md) | A code unit mixed distinct responsibilities instead of representing one coherent operation or concept. | Physical behavior boundaries, cohesive responsibilities | Reported case |

## Case File Structure

Use this structure for new case files:

````markdown
# Case Title

```yaml
id: case.short-case-id
status: reported
summary: One sentence optimized for skimming from the index.
tags: []
related_rules: []
```

## Summary

One or two sentences optimized for skimming from the index.

## Status

Reported case. Not authoritative.

## Related Guide Areas

- Potential tier or file

## Current Interpretation

What the feedback might suggest, without overstating it as a rule.

## Reported Incidents

### YYYY-MM-DD: Short Incident Title

#### Context

Where the feedback came from.

#### Feedback

Short quote or paraphrase of Josh's feedback.

#### Nuance

What makes this incident specific, uncertain, or not yet general.

#### Possible Promotion

What tier this could move to if similar feedback repeats.
````
