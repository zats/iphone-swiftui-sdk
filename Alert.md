# Alert

A native iPhone interruptive message for critical information, blocking errors, and high-signal confirmations. Use `alert` when the message matters more than the surrounding screen.

## When to Use

- **Errors**
- **Blocking warnings**
- **Simple confirmations**

Use something else when:

- The action set is larger: use `confirmationDialog`

## Quick Start

```swift
import SwiftUI

struct SaveAlertView: View {
    @State private var showingError = false

    var body: some View {
        Button("Save") {
            showingError = true
        }
        .alert("Unable to Save", isPresented: $showingError) {
            Button("OK") {}
        } message: {
            Text("Check your connection and try again.")
        }
    }
}
```

## Design System Rules

### Do

- Keep titles short
- Explain the issue in one sentence
- Limit actions

### Don't

- Stack alerts for minor issues
- Put long troubleshooting guides in alerts

## Required Structure

```swift
.alert("Title", isPresented: $showingAlert) {
    Button("OK") {}
} message: {
    Text("Short message.")
}
```
