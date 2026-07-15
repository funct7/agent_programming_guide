# UIKit Programming Guide

## Interface Builder Configuration

```yaml
id: uikit.interface-builder-configuration
tier: convention
review_passes: [structural, substantive]
summary: Configure native Storyboard and XIB properties in Interface Builder rather than duplicating them in code.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [UIKit, Interface Builder, Storyboard, XIB, view-configuration]
```

When a UIKit view is defined in a Storyboard or XIB, configure properties exposed natively by Interface Builder there rather than setting them programmatically.

Use code for dynamic behavior or properties Interface Builder cannot represent natively. Do not use User Defined Runtime Attributes as a substitute for unsupported controls; configure those properties in code instead. Local exceptions are allowed when programmatic configuration is materially clearer.

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
