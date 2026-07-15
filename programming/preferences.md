# Preferences

These are preferred style and organization rules. They matter, but they are more flexible than core correctness rules.

Preference entries may include `preference_strength` when Josh has chosen a numeric priority:

- `1` is the weakest preference.
- `10` is the strongest preference.
- Omitted `preference_strength` means the preference has not been numerically ranked yet.

## Keep Type Definitions Minimal

```yaml
id: preference.type-definitions-minimal
tier: preference
review_passes: [structural]
summary: Keep original type declarations focused on core shape; put conformances and supporting members in extensions by default.
applies_when:
  constructs: [type-declaration, struct, enum, protocol-conformance, nested-type, initializer]
```

The original type declaration should show the core shape of the type:

- enum cases for enums,
- stored properties for structs,
- nested helper types only when they are local enough to belong there.

Put protocol conformances in separate extensions by default. This keeps the main type body easy to scan and groups custom conformance members with the conformance.

When a protocol conformance needs explicit supporting members, put those members in the same conformance extension. Do not split a conformance declaration from its associated type aliases, required methods, or required properties.

Preferred:

```swift
public extension Photoshoot {

    struct Availability {
        public let times: [LocalTime_HHmm]
        public let sunEvent: SunEvent?
    }

}

extension Photoshoot.Availability : Equatable { }
```

## Split Files For Navigation

```yaml
id: preference.split-files-for-navigation
tier: preference
review_passes: [structural]
summary: Split nested types into discoverable files when they become major concepts, are widely used, or make a file hard to scan.
applies_when:
  constructs: [file-layout, nested-type, major-type, directory, navigation]
```

Nested types may stay in the parent file when they are small and mainly used by that parent. Split them when they become major concepts, are used by many places, or make the file hard to scan.

Use full nesting paths in filenames:

- `Photoshoot.Option.swift`
- `Photoshoot.Option.Selection.swift`
- `Photoshoot.Availability.swift`

When a nested concept has many related files, create a directory named after that nested type:

- `Domain/Type/Photoshoot/QnA/`

Rationale:

- People often navigate by files, not only by Xcode type navigation.
- Xcode's open type menu can be unreliable.
- Major feature concepts should be discoverable by filename/path.

## Private Names

```yaml
id: preference.private-names
tier: preference
review_passes: [structural]
summary: Prefix private members and private helper types with an underscore.
applies_when:
  language: swift
  constructs: [private-member, private-type, helper, dependency-key]
```

Code convention: prefix private members and private helper types with `_`.

The underscore is not decoration. It makes the access boundary visible at call sites and helps readers reason about whether a member or type is part of the surrounding type's external surface.

Apply this to private functions, private stored properties, private computed properties, private nested/helper types, and private dependency keys. Do not apply it to public or internal APIs.

## Extension Locality

```yaml
id: preference.extension-locality
tier: preference
review_passes: [structural, substantive]
summary: Keep single-file extension members private and near their use; use wider extensions only for real API surface.
applies_when:
  constructs: [extension, helper, method, computed-property, api-surface]
```

Code convention: place extension members near their use when they are only used locally.

If an extension member is used only in one file, keep it in that file and make the extension private. Use public or internal extensions in separate files only when the member is genuinely part of the wider API surface or is used across multiple files.

## UI Construction

```yaml
id: preference.ui-construction
tier: preference
review_passes: [structural, substantive]
summary: Do not build UIKit UI programmatically when Storyboard, XIB, or SwiftUI is available; do not edit Storyboard/XIB XML unless instructed.
applies_when:
  constructs: [UIKit, Storyboard, XIB, SwiftUI, programmatic-ui, view-controller]
```

Code convention: do not build UIKit UI programmatically when Storyboard, XIB, or SwiftUI is available.

Default to visual UI construction because the resulting layout is easier to inspect directly, and because splitting a UIKit screen between Interface Builder and programmatic view setup makes the screen harder to understand. Prefer SwiftUI when the UI is self-contained enough to be hosted from UIKit.

Programmatic UIKit view construction should be reserved for cases with a clear local reason, such as genuinely dynamic structures, imperative setup that is clearer in code, or layouts the visual UI tool cannot express cleanly. The threshold is not whether the UI is simple or complex; the default is to avoid programmatic UIKit UI unless code is the better representation.

For Storyboard and XIB files, do not directly edit the generated XML by hand or through an agent unless explicitly instructed. Interface Builder assigns and maintains generated IDs and metadata that are easy to break with textual edits. When a Storyboard/XIB change is needed, describe the required Interface Builder changes for Josh to apply, then wire code to the resulting outlets or containers.

## Enum Associated Value Labels

```yaml
id: preference.enum-associated-value-labels
tier: preference
review_passes: [structural]
summary: Omit associated value labels when the value type makes the role clear; use labels only for disambiguation or call-site meaning.
applies_when:
  language: swift
  constructs: [enum, associated-value, case]
```

When an associated value's type already makes its role clear, omit the label. Prefer labels only when they disambiguate multiple values of similar types or materially improve call-site meaning.

## Type Conversion APIs

```yaml
id: preference.type-conversion-apis
tier: preference
review_passes: [substantive]
summary: Prefer static factory APIs on the target type over computed source properties when converting between types.
applies_when:
  constructs: [type-conversion, factory, computed-property, extension]
```

When creating a value of one type from another type, prefer a static factory on the target type, such as `Target.from(source)`, over a computed property on the source type.

Use a source member only when it reads semantically as behavior of the source. If conversion direction is unclear, put the API on the target type.
