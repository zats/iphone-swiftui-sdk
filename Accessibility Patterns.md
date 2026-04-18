# Accessibility Patterns

A native iPhone accessibility baseline for all components and screens. Accessibility is not a polish pass. It is part of the default design, interaction model, and content hierarchy.

## When to Use

- **Every screen**
- **Every control**
- **Every state change**
- **Every custom component**

Use something else when:

- A custom gesture or visual treatment is replacing a standard accessible control

## Quick Start

Label icon-only actions, keep touch targets generous, and prefer semantic controls.

```swift
import SwiftUI

Button {
    compose()
} label: {
    Image(systemName: "square.and.pencil")
}
.accessibilityLabel("Compose")
```

## Usage Patterns

### Semantic Controls First

Use `Button`, `Toggle`, `NavigationLink`, `Picker`, and `TextField` instead of bare views with tap gestures.

```swift
Button("Save") {
    save()
}
```

Do this because the system already understands:

- focus
- activation
- labeling
- traits

### Icon-Only Controls

Always add an accessibility label when the visible text is missing.

```swift
Button {
    search()
} label: {
    Image(systemName: "magnifyingglass")
}
.accessibilityLabel("Search")
```

### Dynamic Type

Let text grow and layouts reflow.

```swift
VStack(alignment: .leading, spacing: 4) {
    Text(title)
        .font(.headline)

    Text(detail)
        .font(.subheadline)
        .foregroundStyle(.secondary)
}
```

At large sizes:

- stack content vertically when needed
- avoid truncating core information
- keep primary content near the top

### Touch Targets

Make custom rows fully tappable and comfortably large.

```swift
Button {
    openItem()
} label: {
    HStack {
        Text("Open")
        Spacer()
    }
    .padding(.vertical, 8)
    .contentShape(Rectangle())
}
```

### Status and Meaning

Do not rely on color alone.

```swift
Label("Sync Paused", systemImage: "exclamationmark.triangle.fill")
    .foregroundStyle(.orange)
```

Use:

- text
- symbol
- placement
- color as reinforcement

### Grouping and Reading Order

If several views form one logical element, combine them appropriately. If one visual block actually contains several actions, keep them separate.

Use accessibility grouping intentionally so VoiceOver reads the right thing in the right order.

## Design System Rules

### Do

- Label icon-only actions
- Preserve large tap targets
- Support Dynamic Type
- Use semantic controls instead of gestures on bare views
- Keep color from carrying meaning alone
- Make reading order match visual order
- Keep custom components simple enough to describe clearly
- Test with VoiceOver, larger text sizes, and increased contrast

### Don't

- Hide actions behind unlabeled icons
- Depend on tiny touch targets
- Use custom gestures where standard controls fit
- Truncate primary content at larger text sizes if the layout can reflow
- Make one tap target secretly perform multiple unrelated actions
- Treat accessibility like a QA-only checklist

## Required Structure

Prefer this pattern for icon-only actions:

```swift
Button {
    action()
} label: {
    Image(systemName: "ellipsis.circle")
}
.accessibilityLabel("More")
```

Prefer this pattern for rich rows:

```swift
Button {
    openItem()
} label: {
    HStack {
        VStack(alignment: .leading) {
            Text(title)
            Text(subtitle)
                .foregroundStyle(.secondary)
        }
        Spacer()
    }
    .contentShape(Rectangle())
}
```

## Component API

### Key APIs

| API | Use Case |
| --- | --- |
| `.accessibilityLabel(...)` | Name icon-only controls |
| `.accessibilityHint(...)` | Add light extra guidance |
| `.accessibilityValue(...)` | Expose current value |
| `.contentShape(...)` | Expand hit area for custom rows |
| `.dynamicTypeSize(...)` | Test and constrain only when necessary |

### Testing Targets

| Area | What to Check |
| --- | --- |
| VoiceOver | Labels, order, action clarity |
| Dynamic Type | Reflow, truncation, hierarchy |
| Contrast | Legibility and distinction |
| Touch targets | Comfort and accuracy |

## Common Patterns

### Labeled Toolbar Action

```swift
Button {
    share()
} label: {
    Image(systemName: "square.and.arrow.up")
}
.accessibilityLabel("Share")
```

### Accessible Toggle Row

```swift
Toggle(isOn: $notificationsEnabled) {
    VStack(alignment: .leading, spacing: 2) {
        Text("Notifications")
        Text("Get shared updates")
            .font(.subheadline)
            .foregroundStyle(.secondary)
    }
}
```

### Full-Width Custom Row

```swift
Button {
    openSettings()
} label: {
    HStack {
        Text("Settings")
        Spacer()
        Image(systemName: "chevron.right")
    }
    .padding(.vertical, 8)
    .contentShape(Rectangle())
}
```
