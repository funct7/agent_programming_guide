# Transport Decoder Performed Domain Validation

```yaml
id: case.transport-decoder-domain-validation
status: reported
summary: A transport decoder mixed wire-shape decoding with validation of cross-field domain invariants.
tags: []
related_rules: [core.domain-boundaries, principle.physical-behavior-boundaries]
```

## Summary

A transport decoder constructed a DTO and also validated whether the decoded values formed a valid application-domain association or target.

## Status

Reported case. Not authoritative.

## Related Guide Areas

- Core domain boundaries
- Physical behavior boundaries

## Current Interpretation

Transport decoding should establish that input can be represented by the transport DTO. Required fields, compatible wire types, recognized discriminators, and representable scalar values belong at this boundary. Cross-field rules that determine whether the DTO represents a valid domain concept should be enforced when the transport value is adapted into the application's local or domain representation.

## Reported Incidents

### 2026-07-19: CloudKit Annotation Decoding Validated Domain Relationships

#### Context

CloudKit decoders for passage and note synchronization values checked required CKRecord fields and also invoked `Book.Association.from` and note-target validation while constructing transport DTOs.

#### Feedback

Keep failures for missing required CKRecord fields or incompatible field shapes in the decoder, but move validation of passage associations and note targets to the code that adapts decoded synchronization values into application records.

#### Nuance

This does not make transport decoding permissive about malformed input. The distinction is between representational validity at the wire boundary and application-domain validity at the adaptation boundary.

#### Possible Promotion

If this recurs, promote it to an example clarifying `core.domain-boundaries` rather than adding a separate rule.
