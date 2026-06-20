# TCA-SM Programming Guide

## State Machine File Organization

```yaml
id: tca-sm.state-machine-file-organization
tier: convention
review_passes: [structural]
summary: For TCA-SM features, keep state machine declaration, reducer, and IO implementation in separate files unless the feature is very small.
tags: []
applies_when:
  language: swift
  constructs: [tca-sm, state-machine, reducer, io, effect, feature, file-layout]
```

For TCA-SM features, prefer separating the state machine declaration, reducer, and IO implementation into separate files.

The usual layout is:
- `Feature.swift` for the state machine type, state, input, routes, effects, result types, dependencies, and construction.
- `Feature.Reducer.swift` for reducer and transition logic.
- `Feature.IO.swift` for IO effect execution.

This keeps the feature declaration readable and makes transition logic and side-effect code easier to inspect independently.

Keeping everything in one file is acceptable when the feature is small enough that the declaration, reducer, and IO code remain easy to scan together. Use a single file only when there is a clear readability reason to do so.
