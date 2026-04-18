# Sheet

A native iPhone temporary presentation for scoped work. Use `sheet` for creation, editing, picking, and short supporting flows that should return to the current context when dismissed.

## When to Use

- **Create or edit flows**
- **Pickers**
- **Filters**
- **Supporting tasks tied to the current screen**

Use something else when:

- The flow replaces the whole app experience: use full-screen cover
- The next screen is part of the hierarchy: push it

## Quick Start

```swift
import SwiftUI

struct ProjectsView: View {
    @State private var showingSheet = false

    var body: some View {
        Button("New Project") {
            showingSheet = true
        }
        .sheet(isPresented: $showingSheet) {
            NavigationStack {
                ProjectFormView()
                    .navigationTitle("New Project")
                    .navigationBarTitleDisplayMode(.inline)
            }
        }
    }
}
```

## Design System Rules

### Do

- Use sheets for scoped work
- Keep toolbar cancel/confirm actions clear
- Let the sheet dismiss back to the current context

### Don't

- Turn sheets into deep browse flows
- Replace primary app navigation with stacked sheets

## Required Structure

```swift
.sheet(isPresented: $showingSheet) {
    NavigationStack {
        Content()
    }
}
```
