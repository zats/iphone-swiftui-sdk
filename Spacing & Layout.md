# Spacing & Layout

A native iPhone spacing and layout system for clarity and rhythm. Start with simple stacks, system containers, safe areas, and consistent gaps. Layout should disappear behind the content.

## When to Use

- **Every screen**
- **Grouping**
- **Alignment**
- **Action hierarchy**
- **Adaptive layouts**

Use something else when:

- Complex geometry is replacing a simple vertical structure
- Decorative spacing is fighting clear hierarchy

## Quick Start

Start with a simple vertical stack and one padding value.

```swift
import SwiftUI

VStack(alignment: .leading, spacing: 16) {
    header
    content
    actions
}
.padding(20)
```

## Usage Patterns

### Vertical Rhythm

Group related elements tightly. Separate different blocks more clearly.

```swift
VStack(alignment: .leading, spacing: 8) {
    Text("Title")
    Text("Supporting copy")
        .foregroundStyle(.secondary)
}

VStack(alignment: .leading, spacing: 16) {
    sectionA
    sectionB
    sectionC
}
```

Default rule:

- 4-8 pt inside small text groups
- 12-16 pt between related controls
- 20-24 pt between major sections

### Safe Areas and Bottom Actions

Use safe areas as part of the layout, not as something to fight.

```swift
ScrollView {
    content
        .padding()
}
.safeAreaInset(edge: .bottom) {
    Button("Continue") {
        continueFlow()
    }
    .buttonStyle(.glassProminent)
    .buttonSizing(.flexible)
    .padding(.horizontal)
    .padding(.top, 8)
    .padding(.bottom, 12)
}
```

Use `ignoresSafeArea()` only for intentional full-bleed media or immersive moments.

### Stacks Before Geometry

Prefer `VStack`, `HStack`, `Spacer`, and `Grid` before reaching for custom measurement.

```swift
HStack(spacing: 12) {
    avatar

    VStack(alignment: .leading, spacing: 4) {
        title
        subtitle
    }

    Spacer()

    accessory
}
```

Good reasons for more advanced layout:

- complex dashboards
- adaptive grids
- overlay coordination

Bad reasons:

- centering something a stack already centers
- compensating for inconsistent spacing elsewhere

### Adaptive Layout at Large Type Sizes

When text grows, switch from inline to stacked layouts.

```swift
VStack(alignment: .leading, spacing: 4) {
    Text(title)

    HStack(spacing: 8) {
        Text(status)
        Text(timestamp)
    }
    .font(.subheadline)
    .foregroundStyle(.secondary)
}
```

If the horizontal version truncates at large sizes, stack secondary items underneath.

### Lists, Forms, and Sections

Let system containers own most of the layout.

```swift
Form {
    Section("Profile") {
        TextField("Name", text: $name)
        TextField("Email", text: $email)
    }
}
```

Do not rebuild list and form spacing manually unless the screen truly stops being a list or form.

## Design System Rules

### Do

- Use consistent spacing inside one screen
- Group related elements tightly
- Leave larger gaps between different blocks
- Prefer simple stacks before custom layout logic
- Use safe area insets for persistent bottom actions
- Let content align to a small number of clear edges
- Reflow layouts for large Dynamic Type sizes

### Don't

- Add padding everywhere without hierarchy
- Jam unrelated controls into one block
- Use geometry tricks when stacks already solve it
- Fight the safe area with overlays by default
- Put too many floating surfaces on one phone screen
- Center everything when the content wants a strong leading edge

## Required Structure

Prefer this screen shape:

```swift
ScrollView {
    VStack(alignment: .leading, spacing: 24) {
        header
        primaryContent
        secondaryContent
    }
    .padding(20)
}
```

Prefer this row shape:

```swift
HStack(spacing: 12) {
    leading

    VStack(alignment: .leading, spacing: 4) {
        title
        subtitle
    }

    Spacer()

    trailing
}
```

## Component API

### Core Layout Tools

| API | Use Case |
| --- | --- |
| `VStack` | Vertical flow |
| `HStack` | Horizontal grouping |
| `Spacer()` | Flexible separation |
| `Grid` / `LazyVGrid` | Structured grids |
| `.padding(...)` | Outer and inner spacing |
| `.safeAreaInset(...)` | Persistent edges |

### Escalation Tools

| API | Use Case |
| --- | --- |
| `GeometryReader` | Rare size-aware layout |
| `ViewThatFits` | Alternate layout shapes |
| Custom `Layout` | Reusable advanced layout logic |

## Common Patterns

### Header + Body + Actions

```swift
VStack(alignment: .leading, spacing: 20) {
    header
    body
    Button("Continue") {
        continueFlow()
    }
    .buttonStyle(.glassProminent)
}
.padding(20)
```

### Compact Metadata Row

```swift
HStack(spacing: 8) {
    Text(status)
    Text("•")
    Text(timestamp)
}
.font(.footnote)
.foregroundStyle(.secondary)
```

### Floating Bottom Action

```swift
.safeAreaInset(edge: .bottom) {
    actionBar
        .padding(.horizontal)
        .padding(.bottom, 12)
}
```
