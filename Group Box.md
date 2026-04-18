# Group Box

A native grouped surface for related controls or read-only information inside a larger screen. Use `GroupBox` when a small cluster needs a title and a shared boundary, but not full card treatment.

## When to Use

- **Small settings clusters**
- **Inline summaries**
- **Read-only grouped metadata**

Use something else when:

- The whole screen is already a form or list section
- The content needs a richer card

## Quick Start

```swift
import SwiftUI

struct StorageGroupView: View {
    var body: some View {
        GroupBox("Storage") {
            VStack(alignment: .leading, spacing: 8) {
                Text("Used: 12 GB")
                Text("Available: 38 GB")
                    .foregroundStyle(.secondary)
            }
        }
    }
}
```
