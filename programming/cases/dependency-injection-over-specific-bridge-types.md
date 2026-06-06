# Dependency Injection With Over-Specific Bridge Types

```yaml
kind: guide-case
id: case.dependency-injection-over-specific-bridge-types
status: reported
summary: A feature depended on a concrete bridge type even though it only needed to observe boolean close events.
tags: []
related_rules: []
```

## Summary

A feature depended on a concrete bridge type even though it only needed to observe boolean close events. The likely issue is injecting more capability than the consumer needs, not necessarily a broad rule requiring a new interface for every dependency.

## Status

Reported case. Not authoritative.

## Related Guide Areas

- Principles: dependency boundaries
- Principles: interface segregation
- Application wiring

## Current Interpretation

Dependency injection should usually expose the smallest meaningful capability required by the consumer. When a consumer only needs events, a publisher or stream value may be enough; a separate protocol is not always necessary.

This case should not be overgeneralized into "always create an interface." In many layers, concrete DataAccess implementations are already hidden behind service interfaces. This incident is more specific: a bridge implementation leaked into feature logic even though the feature only needed an event source.

## Reported Incidents

### 2026-06-01: Q&A Compose Sheet Bridge Dependency

#### Context

`PhotoshootQnAFeature` depended on `PhotoshootQnAComposeSheetBridge` through a dependency key. The bridge also implemented the compose router command side, but Q&A only needed to listen for `Bool` close events.

#### Feedback

Josh pointed out that injecting a concrete bridge type does not make sense here. If the feature only needs close events, it should depend on that event stream, such as `AnyPublisher<Bool, Never>`, rather than the bridge object that also has unrelated capabilities.

#### Nuance

This may relate to the Interface Segregation Principle, but it is not yet promoted as a general guide rule. A publisher can itself be a sufficient abstraction, so creating a new protocol just for this event stream may be unnecessary.

#### Possible Promotion

If similar cases repeat, consider adding a principle that dependency-injected values should expose only the capability the consumer needs, while avoiding unnecessary interface proliferation.
