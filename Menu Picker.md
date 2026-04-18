# Menu Picker

A native iPhone menu-based choice control for one-of-many options that do not deserve permanent space. Use a menu picker when the current value matters, but the full option list should stay tucked away.

## When to Use

- **Sort order**
- **Secondary filters**
- **Compact settings**

Use something else when:

- The options should stay visible: use segmented control
- The choice is core to the screen: use inline picker or selection list

## Quick Start

```swift
import SwiftUI

struct SortMenuPickerView: View {
    enum Sort: String, CaseIterable, Identifiable {
        case newest = "Newest"
        case oldest = "Oldest"
        case name = "Name"
        var id: Self { self }
    }

    @State private var sort: Sort = .newest

    var body: some View {
        Menu {
            Picker("Sort", selection: $sort) {
                ForEach(Sort.allCases) { sort in
                    Text(sort.rawValue).tag(sort)
                }
            }
        } label: {
            Label(sort.rawValue, systemImage: "arrow.up.arrow.down")
        }
    }
}
```

## Usage Patterns

### Toolbar Sort Control

```swift
Menu {
    Picker("Sort", selection: $sort) {
        Text("Newest").tag(Sort.newest)
        Text("Oldest").tag(Sort.oldest)
    }
} label: {
    Image(systemName: "arrow.up.arrow.down")
}
```

### Secondary Filter Choice

Use when the choice is useful, but not important enough to stay visible.

## Design System Rules

### Do

- Show the current choice in the label when helpful
- Keep the option list short
- Use it for secondary controls

### Don't

- Hide the main screen mode behind a menu picker
- Use it when people need to compare options side by side

## Required Structure

```swift
Menu {
    Picker("Label", selection: $selection) {
        ForEach(options) { option in
            Text(option.title).tag(option.id)
        }
    }
} label: {
    Label(currentTitle, systemImage: "arrow.up.arrow.down")
}
```

## Component API

| API | Use Case |
| --- | --- |
| `Menu` + `Picker` | Compact one-of-many choice |

## Common Patterns

### Compact Sort Picker

```swift
Menu {
    Picker("Sort", selection: $sort) {
        Text("Newest").tag(Sort.newest)
        Text("Oldest").tag(Sort.oldest)
        Text("Name").tag(Sort.name)
    }
} label: {
    Label("Sort", systemImage: "arrow.up.arrow.down")
}
```
