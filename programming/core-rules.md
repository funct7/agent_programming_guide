# Core Rules

These are high-priority rules. They should almost never be broken. If a tradeoff seems to require breaking one, stop and make the compromise explicit.

## Domain Facts

```yaml
kind: guide-rule
id: core.domain-facts
tier: core-rule
summary: Do not invent missing domain facts or parse incidental/display strings to recover structured values.
applies_when:
  layers: [Domain, DataAccess, Application]
  constructs: [domain-model, data-mapping, parser, dto, api-response]
```

- Do not invent domain facts that the server/resource does not provide.
- Do not parse display strings or incidental text to recover structured domain values.
- If a missing fact is truly needed, ask for the API/resource to provide it explicitly.

## Domain Boundaries

```yaml
kind: guide-rule
id: core.domain-boundaries
tier: core-rule
summary: Do not turn DTOs, endpoint payloads, or pagination wrappers into Domain; DataAccess adapts transport into Domain.
applies_when:
  layers: [Domain, DataAccess, Application]
  constructs: [domain-model, dto, endpoint-payload, pagination, service-mapping]
```

- Do not turn DTOs, endpoint payloads, or pagination wrappers into Domain types just because the server returns them.
- DataAccess adapts server responses into Domain; Domain should model the business/resource concept.

## State Shape

```yaml
kind: guide-rule
id: core.state-shape
tier: core-rule
summary: Prefer sum types for invalid product states and avoid duplicated boolean flags plus values for mutually exclusive states.
applies_when:
  constructs: [state, enum, optional, boolean-flag, product-type, domain-model, feature-state, derived-value]
```

- Prefer sum types when product types would allow impossible, invalid, or ambiguous states.
- Do not preserve boolean flags plus values when the real states are mutually exclusive.
