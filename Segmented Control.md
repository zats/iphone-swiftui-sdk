# Segmented Control

A native iPhone control for switching between a small number of peer views or filters. Use a segmented control when people need to compare 2-4 options quickly and keep all choices visible.

## When to Use

- **Short mutually exclusive filters**
- **View mode switches**
- **Tight content scopes**

Use something else when:

- There are many options: use `Picker`
- The options are not peers
- The labels are too long

## Quick Start

```swift
import SwiftUI

struct MailScopeView: View {
    enum Scope: String, CaseIterable, Identifiable {
        case all = "All"
        case unread = "Unread"
        case flagged = "Flagged"
        var id: Self { self }
    }

    @State private var scope: Scope = .all

    var body: some View {
        Picker("Scope", selection: $scope) {
            ForEach(Scope.allCases) { scope in
                Text(scope.rawValue).tag(scope)
            }
        }
        .pickerStyle(.segmented)
    }
}
```

## Usage Patterns

### Filter Bar

Keep it close to the content it changes.

### View Mode Toggle

Use for tight mode changes like `List` / `Grid` when both are equally important.

## Design System Rules

### Do

- Keep to 2-4 options
- Use short labels
- Make the current segment meaningful immediately

### Don't

- Put six or seven segments in a row
- Use a segmented control for navigation between major app areas
- Put long sentences in segment labels

## Required Structure

```swift
Picker("Mode", selection: $mode) {
    Text("All").tag(Mode.all)
    Text("Unread").tag(Mode.unread)
}
.pickerStyle(.segmented)
```

## Component API

| API | Use Case |
| --- | --- |
| `Picker` + `.segmented` | Standard segmented control |

## Common Patterns

### Results Filter

```swift
Picker("Results", selection: $scope) {
    Text("All").tag(Scope.all)
    Text("Files").tag(Scope.files)
    Text("People").tag(Scope.people)
}
.pickerStyle(.segmented)
```
