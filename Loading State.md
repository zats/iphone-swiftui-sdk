# Loading State

A native iPhone loading state for content that has not arrived yet. Keep loading calm, proportional, and structurally close to the final UI.

## When to Use

- **Initial fetch**
- **Refresh**
- **Remote search**
- **Blocking setup steps**

## Quick Start

```swift
import SwiftUI

struct LoadingProjectsView: View {
    var isLoading: Bool

    var body: some View {
        Group {
            if isLoading {
                ProgressView("Loading Projects...")
            } else {
                ProjectsList()
            }
        }
    }
}
```

## Design System Rules

### Do

- Match loading weight to task weight
- Keep the current screen structure visible when possible
- Transition cleanly into the loaded state

### Don't

- Replace every refresh with a blank spinner screen
- Keep people waiting without any context

## Common Patterns

```swift
if isLoading {
    ProgressView()
} else {
    Content()
}
```
