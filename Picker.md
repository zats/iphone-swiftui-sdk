# Picker

A native iPhone control for choosing one option from a set. Use `Picker` when the choice is structured and the system presentation should do most of the work.

## When to Use

- **One-of-many choices**
- **Settings**
- **Short configuration screens**
- **Enum-backed form input**

Use something else when:

- The choice is binary: use `Toggle`
- The choices should stay visible side-by-side: use segmented control
- The options are mostly actions: use `Menu`

## Quick Start

```swift
import SwiftUI

struct ThemePickerView: View {
    enum Theme: String, CaseIterable, Identifiable {
        case system, light, dark
        var id: Self { self }
    }

    @State private var theme: Theme = .system

    var body: some View {
        Picker("Theme", selection: $theme) {
            ForEach(Theme.allCases) { theme in
                Text(theme.rawValue.capitalized).tag(theme)
            }
        }
    }
}
```

## Usage Patterns

### Inline Settings Choice

Use inline or navigation-linked picker behavior for settings screens.

### Form Picker

```swift
Picker("Category", selection: $category) {
    ForEach(categories) { category in
        Text(category.title).tag(category)
    }
}
```

### Wheel or Compact Styles

Use only when the content and context justify it. Default to system behavior first.

## Design System Rules

### Do

- Use `Picker` for structured choices
- Keep labels clear
- Keep option titles short
- Back the selection with stable enum or ID values

### Don't

- Use a picker for destructive actions
- Use it when people need to compare many rich rows at once
- Hide the current value

## Required Structure

```swift
Picker("Label", selection: $selection) {
    ForEach(options) { option in
        Text(option.title).tag(option.id)
    }
}
```

## Component API

| API | Use Case |
| --- | --- |
| `Picker` | Standard one-of-many choice |
| `.pickerStyle(...)` | Style override when needed |

## Common Patterns

### Filter Choice

```swift
Picker("Status", selection: $status) {
    Text("All").tag(Status.all)
    Text("Open").tag(Status.open)
    Text("Closed").tag(Status.closed)
}
```
