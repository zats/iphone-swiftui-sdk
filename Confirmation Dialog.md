# Confirmation Dialog

A native iPhone decision surface for confirming a deliberate action or choosing among a small set of consequences. Use `confirmationDialog` after the user has already expressed intent.

## When to Use

- **Destructive confirmation**
- **Choose outcome after one initiating action**
- **Compact action sets tied to one object**

Use something else when:

- The action should stay visible: use `Menu`
- The message is purely informational: use `Alert`

## Quick Start

```swift
import SwiftUI

struct DeleteButtonView: View {
    @State private var showingDialog = false

    var body: some View {
        Button("Delete") {
            showingDialog = true
        }
        .buttonStyle(.glass)
        .confirmationDialog("Delete this note?", isPresented: $showingDialog) {
            Button("Delete", role: .destructive) {
                deleteNote()
            }
            Button("Cancel", role: .cancel) {}
        }
    }

    private func deleteNote() {}
}
```

## Design System Rules

### Do

- Show it only after a user action
- Keep copy short
- Put destructive actions behind `role: .destructive`

### Don't

- Use it as a general overflow menu
- Ask for confirmation on every harmless action

## Required Structure

```swift
.confirmationDialog("Title", isPresented: $showingDialog) {
    Button("Action") { performAction() }
}
```
