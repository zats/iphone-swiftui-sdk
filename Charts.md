# Charts

A native iPhone data visualization surface for trends, comparisons, and distributions. Use charts when shape and pattern matter more than raw tables. Keep them readable at phone scale.

## When to Use

- **Trends over time**
- **Comparisons**
- **Progress toward goals**
- **Simple distributions**

Use something else when:

- A single number or summary row is enough
- Exact values matter more than pattern

## Quick Start

```swift
import SwiftUI
import Charts

struct ActivityChartView: View {
    let points: [ActivityPoint]

    var body: some View {
        Chart(points) { point in
            LineMark(
                x: .value("Day", point.day),
                y: .value("Value", point.value)
            )
        }
        .frame(height: 220)
    }
}
```
