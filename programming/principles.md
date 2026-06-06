# Principles

These are default reasoning guidelines. Follow them unless local context provides a clear reason not to.

## Domain Modeling

```yaml
kind: guide-rule
id: principle.domain-modeling
tier: principle
summary: Model Domain from the business/resource concept, not one server response, screen shape, pagination, or duplicated metadata.
applies_when:
  layers: [Domain, DataAccess, Application]
  constructs: [domain-model, entity, value-object, api-response, dto, pagination]
```

A domain type captures a business/resource concept. For product/application businesses, the line between Domain and Application can be blurry, but start from the resource perspective.

When modeling Domain, ask:

- What is the entity or value?
- What identity does it have, if any?
- What resource is it tied to?
- What facts are intrinsic to the concept?
- What states can actually exist?

Do not start from:

- the shape of one server response,
- the shape of one screen,
- pagination or endpoint mechanics,
- duplicated or derived metadata.

## Practical Compromise

```yaml
kind: guide-rule
id: principle.practical-compromise
tier: principle
summary: Make compromises explicit; omit unavailable facts instead of filling idealized fields from brittle sources.
applies_when:
  constructs: [domain-model, api-gap, data-mapping, server-response, compromise]
```

Ideally, business, server, client, and developers share one language and one coherent resource shape. In practice, server resources may be incomplete, awkward, or shaped by application constraints.

Compromises are fine when they are explicit:

- Do not pretend the model is purer than the data allows.
- Do not fill idealized fields from brittle sources.
- Omit unavailable facts when that is the honest current model.
- Ask for API/resource changes when the missing fact matters.

## Derived Values

```yaml
kind: guide-rule
id: principle.derived-values
tier: principle
summary: Avoid storing fields that merely summarize richer model data already present when contradictions could result.
applies_when:
  constructs: [state, domain-model, status, count, flag, derived-value]
```

Avoid storing fields that merely summarize richer model data already present.

Examples: status, count, or availability fields may be better derived from the model structure when doing so is reliable. This is not a ban; it is a prompt to check whether storing both would create contradictory state.

## Sentinel Values

```yaml
kind: guide-rule
id: principle.sentinel-values
tier: principle
summary: Sentinel values are acceptable only when they have one clear meaning, are impossible as real data, and are named in one place.
applies_when:
  constructs: [sentinel, placeholder, missing-data, domain-model, feature-state]
```

Sentinel values are acceptable when they simplify the model and are tightly constrained.

Use a sentinel only when:

- the sentinel has one clear meaning,
- the value cannot realistically occur as real data,
- the sentinel is named and constructed in one place,
- checking for the sentinel is straightforward,
- the sentinel avoids a more confusing shape, such as extra flags that can contradict the model.

Do not use sentinel values when they hide missing domain facts in a broad or ambiguous way, or when many consumers would have to independently remember the sentinel convention.

## Modeling By Change Boundaries

```yaml
kind: guide-rule
id: principle.modeling-by-change-boundaries
tier: principle
summary: Group values that change together and separate values with independent lifecycles.
applies_when:
  constructs: [state, context, model, struct, enum, child-feature, lifecycle]
```

Model data by grouping values that change together and separating values that change independently.

Before choosing a shape, ask:

- Which values are stable context?
- Which values change as one unit?
- Which values have their own lifecycle?
- Which values can change independently without invalidating the others?

Use those change boundaries to decide where structs, enums, child state, and child features belong.

Do not flatten unrelated values into one type just because one screen or workflow currently needs all of them. A flat property list makes it easier to create invalid combinations and harder to see what actually changes together.

## Feature State Modeling

```yaml
kind: guide-rule
id: principle.feature-state-modeling
tier: principle
summary: Organize feature state around lifecycle and user-visible modes, not flat optional property bags.
applies_when:
  layers: [Application, Presentation]
  constructs: [feature-state, state-machine, child-feature, sheet, modal, optional-state]
```

Feature state should be organized around the feature's backbone lifecycle and user-visible modes, not as a flat bag of properties.

Start with the states the screen or feature can actually be in, such as idle, loading, loaded, failed, editing, or presenting a child flow. Then place supporting data inside the state where that data is valid.

Prefer building feature-owned state around existing domain models when the domain model already represents the relevant structure.

When a sub-flow has its own lifecycle, inputs, validation, submission, or presentation rules, model it as a child feature instead of folding it into the parent state as optional fields.

## State Machine Reducers

```yaml
kind: guide-rule
id: principle.state-machine-reducers
tier: principle
summary: Prefer switching on the state/input or state/event tuple so legal transitions and invalid inputs are visible.
applies_when:
  layers: [Application]
  constructs: [state-machine, reducer, input, event, transition]
```

For state-machine reducers, prefer switching on `(state, input)` or `(state, event)` so valid and invalid state/input combinations are visible at the transition table level.

Avoid switching only on input and then checking state inside each case when the reducer is primarily describing a state machine. Pattern matching the tuple makes legal transitions, illegal inputs, and no-op cases easier to scan.

## Default Values

```yaml
kind: guide-rule
id: principle.default-values
tier: principle
summary: Default arguments and values communicate meaning; use them only when the omitted value is normal, dominant, or safe.
applies_when:
  constructs: [default-argument, default-value, initializer, factory, form, state]
```

Default arguments and default values communicate meaning. Use them only when the omitted value is genuinely the normal, dominant, or semantically safe choice.

Do not use defaults to hide incomplete data, temporary API gaps, or context-specific mapping decisions. If a value is uncertain or supplied by a specific layer, make that layer pass it explicitly.

## Optional Values

```yaml
kind: guide-rule
id: principle.optional-values
tier: principle
summary: Use optionals for legitimate absence, not invalid input, illegal state, failed construction, or hidden errors.
applies_when:
  constructs: [optional, initializer, factory, parser, validation, construction, error]
```

Use optionals to model the legitimate absence of a value.

Do not use optional return values to hide invalid input, illegal state, failed construction, or other error cases. If construction cannot succeed because the argument is invalid, throw an error such as `GeneralError.illegalArgument` instead of returning `nil`.

Optional is appropriate when `nil` is a valid domain or state meaning. It is not a substitute for error handling.

## Control Flow Readability

```yaml
kind: guide-rule
id: principle.control-flow-readability
tier: principle
summary: Prefer statements that express one coherent idea; do not compress unrelated extraction, validity, authorization, and no-op checks.
applies_when:
  constructs: [guard, if, switch, boolean-expression, reducer, control-flow]
```

Prefer clarity over compactness. A statement should express one coherent idea, and different ideas should usually be separated into different statements.

Do not combine state extraction, validity checks, authorization checks, and no-op checks just to make the code shorter. The code should make each condition's meaning clear.

Examples:

- Use one `guard` to extract required state, then another statement for a separate validity check.
- Prefer an explicit `if` for a valid no-op case, especially when the return value communicates meaning, such as `identity` instead of `undefined`.
- Avoid negative boolean logic in `guard` when naming the invalid case directly is easier to read.

## Helper API Justification

```yaml
kind: guide-rule
id: principle.helper-api-justification
tier: principle
summary: Introduce helper APIs only when they carry semantic weight, remove meaningful duplication, or clarify the caller.
applies_when:
  constructs: [helper, extension, computed-property, method, factory, api-surface]
```

Only introduce a helper method, computed property, or extension member when it carries enough semantic weight to justify becoming an API.

A helper is justified when it names a meaningful operation, removes meaningful duplication, isolates non-trivial transformation, or improves the caller. If it is used only once and does not clarify the caller, prefer inlining it.

## Examples Are Not Rules

```yaml
kind: guide-rule
id: principle.examples-are-not-rules
tier: principle
summary: Examples clarify guidance but are not exhaustive checklists and should not be applied blindly.
applies_when:
  constructs: [example, case-study, guide-update, code-feedback]
```

Examples under `examples/` illustrate accepted guidance. They are not exhaustive checklists, and they should not be applied blindly.
