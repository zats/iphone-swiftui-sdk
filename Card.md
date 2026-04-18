# Card

A native iPhone grouped surface for previewing or summarizing a chunk of content. Use cards sparingly. Start with lists, sections, and system containers first; reach for cards when the content needs a self-contained preview.

## When to Use

- **Dashboard summaries**
- **Rich content previews**
- **Standalone featured items**

Use something else when:

- The screen is a normal list
- Every row would become a card for no reason

## Quick Start

```swift
import SwiftUI

struct SummaryCard: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("Weekly Summary")
                .font(.headline)

            Text("You completed 12 tasks this week.")
                .foregroundStyle(.secondary)
        }
        .padding()
        .background(.thinMaterial, in: RoundedRectangle(cornerRadius: 16, style: .continuous))
    }
}
```

## Design System Rules

### Do

- Use cards for summary or featured content
- Keep card density low
- Keep internal hierarchy simple

### Don't

- Turn every screen into stacked cards
- Add heavy borders and shadows everywhere

## Required Structure

```swift
VStack(alignment: .leading) {
    Text("Title")
    Text("Subtitle")
}
.padding()
.background(.thinMaterial, in: RoundedRectangle(cornerRadius: 16, style: .continuous))
```
