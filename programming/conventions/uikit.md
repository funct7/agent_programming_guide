# UIKit Programming Guide

## Visibility State

```yaml
id: uikit.visibility-state
tier: convention
review_passes: [structural, substantive]
summary: Prefer the positive isVisible wrapper over UIView.isHidden when changing UIKit visibility.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [UIKit, UIView, visibility, isHidden, isVisible]
```

Prefer `isVisible` over `UIView.isHidden` when showing or hiding UIKit views.

`isVisible` expresses the desired state directly and avoids the inverted boolean logic of `isHidden`. Use `isHidden` only when interacting with APIs that require it or when implementing the `isVisible` wrapper itself.
