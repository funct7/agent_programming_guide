# SwiftUI Programming Guide

## Button Hit Area

```yaml
id: swiftui.button-hit-area
tier: convention
review_passes: [structural]
summary: Make the intended whole button surface tappable by sizing the Button label to fill that surface.
tags: [swift-lang]
applies_when:
  language: swift
  constructs: [SwiftUI, Button, hit-area, layout]
```

For SwiftUI `Button`, make the full intended button surface tappable, not only the intrinsic text or icon label.

Default to sizing the button label to fill the intended width and height, then apply the visual styling around that filled label. This avoids the common SwiftUI trap where the button looks full-width but only the label's intrinsic content responds to taps.
