# Progress

A native iPhone progress indicator for ongoing work. Use `ProgressView` for loading, syncing, uploading, and any visible task that needs reassurance without drama.

## When to Use

- **Loading**
- **Syncing**
- **Uploading**
- **Deterministic progress**

## Quick Start

```swift
import SwiftUI

struct UploadProgressView: View {
    @State private var progress = 0.4

    var body: some View {
        ProgressView(value: progress)
    }
}
```

## Design System Rules

### Do

- Use indeterminate progress when exact progress is unknown
- Use labeled progress when people benefit from context

### Don't

- Show long explanatory text around a spinner
- Fake exact progress when you do not have it

## Common Patterns

```swift
ProgressView("Uploading...")
```
