# Core Rules

These are high-priority rules. They should almost never be broken. If a tradeoff seems to require breaking one, stop and make the compromise explicit.

## Domain Facts

```yaml
id: core.domain-facts
tier: core-rule
review_passes: [substantive]
summary: Do not invent missing domain facts or parse incidental/display strings to recover structured values.
applies_when:
  constructs: [domain-model, data-mapping, parser, dto, api-response]
```

- Do not invent domain facts that the server/resource does not provide.
- Do not parse display strings or incidental text to recover structured domain values.
- If a missing fact is truly needed, ask for the API/resource to provide it explicitly.

## Domain Boundaries

```yaml
id: core.domain-boundaries
tier: core-rule
review_passes: [substantive]
summary: Do not turn DTOs, endpoint payloads, or pagination wrappers into Domain; DataAccess adapts transport into Domain.
applies_when:
  constructs: [domain-model, dto, endpoint-payload, pagination, service-mapping]
```

- Do not turn DTOs, endpoint payloads, or pagination wrappers into Domain types just because the server returns them.
- DataAccess adapts server responses into Domain; Domain should model the business/resource concept.

## State Shape

```yaml
id: core.state-shape
tier: core-rule
review_passes: [substantive]
summary: Prefer sum types for invalid product states and avoid duplicated boolean flags plus values for mutually exclusive states.
applies_when:
  constructs: [state, enum, optional, boolean-flag, product-type, domain-model, feature-state, derived-value]
```

- Prefer sum types when product types would allow impossible, invalid, or ambiguous states.
- Do not preserve boolean flags plus values when the real states are mutually exclusive.

## Behavior-Enforcing Design

```yaml
id: core.behavior-enforcing-design
tier: core-rule
review_passes: [substantive]
summary: Design types and interfaces so required behavior is enforced by construction and incorrect use is not available.
applies_when:
  constructs: [api-surface, interface, state, invariant, lifecycle, validation, side-effect, encapsulation, type-shape]
```

Design types and interfaces so the caller can only perform the correct behavior.

When a type has required invariants, lifecycle rules, validation, state synchronization, side effects, or presentation updates, encode those constraints into the type or API boundary. Prefer designs where misuse is impossible or unavailable, rather than relying on callers to remember which sequence, helper, or convention is required.

Use type shape, access control, property observers, focused methods, state transitions, and sum types to make the valid path the natural and enforceable path. Do not expose raw state or unconstrained operations when using them directly can bypass behavior the type is responsible for preserving.

## Test Scope And Boundaries

```yaml
id: core.test-scope-boundaries
tier: core-rule
review_passes: [substantive]
summary: "Prefer London/mockist-style unit tests: isolate the behavior under test and verify one meaningful boundary at a time."
applies_when:
  constructs: [test, unit-test, integration-test, adapter, collaborator, external-system, mapper, mock, fake, stub]
```

Prefer London/mockist-style unit tests. A unit test should isolate the behavior under test from collaborators with meaningful behavior, and it should make the verified boundary explicit.

- Test one meaningful behavior boundary at a time.
- Do not exercise a production call path just because it eventually reaches the behavior you care about.
- Do not mix adapter behavior, transport behavior, decoding, mapping, persistence, or external-system behavior unless the test is explicitly an integration test.
- Do not use mocks, fakes, or stubs to make an integration-shaped test look deterministic while leaving the real integration unproven.
- If the relevant behavior is a pure transformation, state transition, value construction, or mapper, test that behavior directly.
- If the relevant behavior depends on an external system, either isolate that dependency behind a test double for unit coverage or run a real integration test against a controlled environment.
- A mocked collaborator proves only the local contract assumed by the test; it does not prove that the real collaborator, server, database, or network behaves that way.

Examples:

- Mapper: if the behavior is server payload mapping into an application value, use a representative sample payload and test the mapper directly. Do not route that assertion through transport, request construction, response handling, decoding, and then mapping unless that full adapter boundary is the real subject.
- Adapter: if the behavior is request construction, test request construction directly only when that logic is non-trivial enough to justify coverage. Do not fake a server just to observe a simple request path.
- Integration: if the behavior is client/server compatibility, use a controlled real server and known data state. A mocked transport response does not prove routing, auth, deployed schema compatibility, server behavior, or database-backed behavior.
