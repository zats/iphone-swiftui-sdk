# Drag and Drop

A native iPhone direct-manipulation pattern for moving or importing content with touch. Use drag and drop when spatial movement is the point and the destination is obvious.

## When to Use

- **Reordering collections**
- **Dropping media or files**
- **Moving items between surfaces**

Use something else when:

- A button or menu is clearer
- The target is not visually obvious

## Quick Start

```swift
import SwiftUI
import UniformTypeIdentifiers

Text(item.title)
    .draggable(item.title)
```

## Design System Rules

### Do

- Make drop targets obvious
- Use it when direct manipulation saves time
- Keep fallback actions available

### Don't

- Rely on drag and drop as the only path
- Hide where dropped content will land

## Common Patterns

```swift
DropDestination(for: String.self) { items, location in
    droppedItems.append(contentsOf: items)
    return true
}
```
