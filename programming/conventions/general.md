# General Programming Conventions

## Semantic Member Grouping

```yaml
id: convention.semantic-member-grouping
tier: convention
review_passes: [structural, substantive]
summary: Group members by semantic responsibility instead of placing cross-cutting APIs in incidental sections.
applies_when:
  constructs: [api-surface, interface, protocol, class, struct, extension, method, property, file-layout]
```

As a rule of thumb, group related methods and properties by the concept or responsibility they belong to. The exact grouping depends on context and is not a mechanical rule. Do not place a member in a section merely because it mentions one of that section's types when its behavior spans multiple concepts. Put cross-cutting APIs in a shared section or at the boundary where readers expect combined behavior.
