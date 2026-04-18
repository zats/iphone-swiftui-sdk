# Empty State

A native iPhone empty state for first-run, zero-content, and no-results screens. Keep it minimal, useful, and action-oriented. Start with `ContentUnavailableView` when it fits, then add one clear next step.

## When to Use

- **First run**: The user has not created or connected anything yet
- **Post-action empty**: Filters, deletes, or cleanup leave a list empty
- **No-results**: Search or filter returned nothing
- **Permission-dependent content**: Data exists only after setup or access is granted

## Quick Start

Use `ContentUnavailableView` for the default case.

```swift
import SwiftUI

struct ProjectsView: View {
    var projects: [Project]
    @State private var showingCreateSheet = false

    var body: some View {
        NavigationStack {
            Group {
                if projects.isEmpty {
                    ContentUnavailableView {
                        Label("No Projects", systemImage: "folder")
                    } description: {
                        Text("Create your first project to get started.")
                    } actions: {
                        Button("New Project") {
                            showingCreateSheet = true
                        }
                        .buttonStyle(.glassProminent)
                    }
                } else {
                    ProjectsList(projects: projects)
                }
            }
            .navigationTitle("Projects")
        }
    }
}
```

## Usage Patterns

### First-Run Empty State

Explain what is missing and give one primary action.

```swift
ContentUnavailableView {
    Label("No Downloads", systemImage: "arrow.down.circle")
} description: {
    Text("Saved files will appear here.")
} actions: {
    Button("Browse Files") {
        browseFiles()
    }
    .buttonStyle(.glassProminent)
}
```

### Post-Filter or Post-Search Empty State

Use a lighter tone. The system search variant is often enough.

```swift
ContentUnavailableView.search(text: query)
```

### Destructive-Empty State

After delete or archive flows, use calm confirmation rather than celebration.

```swift
ContentUnavailableView {
    Label("Nothing Left to Review", systemImage: "checkmark")
} description: {
    Text("Archived items will stay available in Archive.")
}
```

### Permission-Dependent Empty State

Say what the user gets and why permission matters.

```swift
ContentUnavailableView {
    Label("Photos Access Needed", systemImage: "photo")
} description: {
    Text("Allow access to show your library here.")
} actions: {
    Button("Allow Access") {
        requestPhotosAccess()
    }
    .buttonStyle(.glassProminent)
}
```

## Design System Rules

### Do

- Lead with what is missing
- Add one sentence about what happens next
- Show one primary action
- Use `ContentUnavailableView` first
- Keep illustrations and chrome minimal
- Match the action strength to the situation

### Don't

- Fill the screen with explanatory paragraphs
- Show multiple competing primary actions
- Use an empty state before content has finished loading
- Celebrate destructive emptiness
- Add decorative cards and panels around the empty state

## Required Structure

Prefer this shape:

```swift
ContentUnavailableView {
    Label("Title", systemImage: "symbol")
} description: {
    Text("Short explanation.")
} actions: {
    Button("Action") {
        performAction()
    }
    .buttonStyle(.glassProminent)
}
```

## Component API

### Core APIs

| API | Use Case |
| --- | --- |
| `ContentUnavailableView` | General empty states |
| `ContentUnavailableView.search(text:)` | Search no-results |
| Custom `Group` / `VStack` | Rare escape hatch for richer product-specific states |

## Common Patterns

### First Project

```swift
ContentUnavailableView {
    Label("No Projects", systemImage: "folder")
} description: {
    Text("Create your first project to get started.")
} actions: {
    Button("New Project") {
        createProject()
    }
    .buttonStyle(.glassProminent)
}
```
