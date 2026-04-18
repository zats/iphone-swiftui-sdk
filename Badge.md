# Badge

A native iPhone micro-indicator for count or status. Use badges sparingly for unread counts, new activity, or compact state signals.

## When to Use

- **Unread count**
- **New activity**
- **Small status marker**

Use something else when:

- The value needs explanation
- The status is central to the screen

## Quick Start

```swift
import SwiftUI

Text("Inbox")
    .badge(3)
```

## Design System Rules

### Do

- Keep counts short
- Use badges for small signals
- Remove stale badges quickly

### Don't

- Put badges on everything
- Use high-noise counts everywhere

## Common Patterns

```swift
ToolbarItem(placement: .topBarTrailing) {
    Button {
        openActivity()
    } label: {
        Image(systemName: "bell")
    }
    .badge(unreadCount)
}
```
