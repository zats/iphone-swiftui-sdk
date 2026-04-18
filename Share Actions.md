# Share Actions

A native iPhone share pattern for exporting or sending content out of the app. Use `ShareLink` first for straightforward sharing. Keep share affordances easy to find and secondary to the main task.

## When to Use

- **Export**
- **Send a link**
- **Share a document or image**
- **Invite collaborators**

Use something else when:

- The user is copying a short value only
- The action is internal, not actually sharing

## Quick Start

```swift
import SwiftUI

ShareLink(item: url) {
    Label("Share", systemImage: "square.and.arrow.up")
}
```

## Design System Rules

### Do

- Use `ShareLink` first
- Put share in the toolbar or overflow when it is secondary
- Share meaningful representations of content

### Don't

- Hide essential collaboration behind several menus
- Build a custom share sheet without a strong reason

## Common Patterns

```swift
ToolbarItem(placement: .topBarTrailing) {
    ShareLink(item: documentURL) {
        Image(systemName: "square.and.arrow.up")
    }
}
```
