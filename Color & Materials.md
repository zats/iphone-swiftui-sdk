# Color & Materials

A native iPhone color and material system for hierarchy, meaning, and depth. On iOS 26+, start with semantic system colors and Liquid Glass-compatible materials. Add brand color sparingly and with intent.

## When to Use

- **Hierarchy**: Primary text, secondary text, grouped surfaces, backgrounds
- **Meaning**: Success, warning, destructive, selection, emphasis
- **Depth**: Toolbars, tab bars, overlays, cards, bottom actions
- **Brand**: One restrained accent, not a full-screen paint job

Use something else when:

- You are using color only to decorate empty space
- Color is doing the job that spacing, type, or structure should handle

## Quick Start

Start with semantic colors and materials.

```swift
import SwiftUI

struct SettingsCard: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("Storage")
                .foregroundStyle(.primary)

            Text("12 GB used")
                .foregroundStyle(.secondary)
        }
        .padding()
        .background(.thinMaterial, in: RoundedRectangle(cornerRadius: 16, style: .continuous))
    }
}
```

For primary emphasis:

```swift
Button("Continue") {
    continueFlow()
}
.buttonStyle(.glassProminent)
```

## Usage Patterns

### Semantic Text and Backgrounds

Default to semantic foreground styles before inventing a palette.

```swift
VStack(alignment: .leading, spacing: 4) {
    Text("Title")
        .foregroundStyle(.primary)

    Text("Supporting text")
        .foregroundStyle(.secondary)
}
.background(Color(uiColor: .systemBackground))
```

Use this for:

- text hierarchy
- list and form backgrounds
- adaptive light/dark behavior

### Brand Color as Accent

Use one accent where the product benefits from a recognizable voice.

```swift
Button("Done") {
    save()
}
.tint(.blue)
.buttonStyle(.glassProminent)
```

Good places:

- one primary action
- selected tab
- meaningful highlight

Bad places:

- every toolbar item
- every card background
- all text that could simply be primary or secondary

### Materials for Depth

Use materials when a surface should float above content.

```swift
ZStack(alignment: .bottom) {
    MapPreview()

    VStack(alignment: .leading, spacing: 4) {
        Text("Cafe")
        Text("5 min away")
            .foregroundStyle(.secondary)
    }
    .padding(.horizontal)
    .padding(.vertical, 12)
    .background(.regularMaterial, in: RoundedRectangle(cornerRadius: 16))
    .padding()
}
```

Good fits:

- floating summaries
- overlays above imagery
- small supporting surfaces that need separation from busy content

If a bottom call to action sits over especially busy media, a restrained material container can help. It should not be the default shell treatment.

Do not add material everywhere. If everything floats, nothing does.

### Meaningful Status Color

Use color consistently when it encodes meaning.

```swift
Label("Saved", systemImage: "checkmark.circle.fill")
    .foregroundStyle(.green)

Label("Sync Paused", systemImage: "exclamationmark.triangle.fill")
    .foregroundStyle(.orange)
```

Rule:

- one color meaning per app
- do not reuse the destructive color for neutral emphasis

### Color on Liquid Glass

On iOS 26+, be especially restrained with colored glass controls.

Good:

- one prominent action
- selected control state

Bad:

- several tinted glass buttons in one toolbar
- colorful controls on already colorful content

## Design System Rules

### Do

- Start with semantic system colors
- Use `.primary`, `.secondary`, and system backgrounds before custom colors
- Keep one accent color strategy across the app
- Use color to reinforce meaning, not replace labels
- Let materials create depth where a surface should float
- Make sure custom colors work in light, dark, and increased contrast modes
- Prefer monochrome controls when the content layer is already visually rich

### Don't

- Paint every surface with custom color
- Use the same color to mean different things
- Tint every icon, button, and label because the brand color exists
- Put several colored glass controls side by side
- Rely on color alone for state, status, or errors
- Fight system materials with heavy custom blur, borders, and chrome

## Required Structure

Prefer this stack:

```swift
VStack(alignment: .leading, spacing: 8) {
    Text("Primary")
        .foregroundStyle(.primary)

    Text("Secondary")
        .foregroundStyle(.secondary)
}
.padding()
.background(.thinMaterial, in: RoundedRectangle(cornerRadius: 16, style: .continuous))
```

Use this action pattern:

```swift
Button("Continue") {
    continueFlow()
}
.buttonStyle(.glassProminent)
```

## Component API

### Semantic Color

| API | Use Case |
| --- | --- |
| `.foregroundStyle(.primary)` | Main text |
| `.foregroundStyle(.secondary)` | Supporting text |
| `Color(uiColor: .systemBackground)` | Base screen background |
| `Color(uiColor: .secondarySystemBackground)` | Grouped surfaces |

### Materials

| API | Use Case |
| --- | --- |
| `.thinMaterial` | Light grouped surfaces |
| `.regularMaterial` | Stronger floating surfaces |
| `.ultraThinMaterial` | Very light overlays |

### Accent and Meaning

| API | Use Case |
| --- | --- |
| `.tint(...)` | Selection and primary emphasis |
| `.foregroundStyle(.green)` | Success |
| `.foregroundStyle(.orange)` | Warning |
| `.foregroundStyle(.red)` | Destructive |

## Common Patterns

### Monochrome Toolbar Over Rich Content

```swift
ToolbarItem(placement: .topBarTrailing) {
    Button {
        share()
    } label: {
        Image(systemName: "square.and.arrow.up")
    }
    .foregroundStyle(.primary)
}
```

### Colored Primary Action Only

```swift
HStack {
    Button("Cancel") {
        dismiss()
    }
    .buttonStyle(.glass)

    Button("Save") {
        save()
    }
    .buttonStyle(.glassProminent)
}
```

### Quiet Grouped Surface

```swift
VStack(alignment: .leading, spacing: 8) {
    Text("Plan")
    Text("Pro")
        .foregroundStyle(.secondary)
}
.padding()
.background(Color(uiColor: .secondarySystemBackground), in: RoundedRectangle(cornerRadius: 16, style: .continuous))
```
