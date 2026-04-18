# Slider

A native iPhone control for adjusting a continuous value quickly. Use `Slider` for ranges where relative movement matters more than exact typing.

## When to Use

- **Volume and intensity**
- **Playback speed**
- **Image adjustments**
- **Any continuous value**

Use something else when:

- People need exact numeric entry
- The value is binary or very small set

## Quick Start

```swift
import SwiftUI

struct VolumeSliderView: View {
    @State private var volume = 0.5

    var body: some View {
        Slider(value: $volume, in: 0...1)
    }
}
```

## Usage Patterns

### Label + Value

```swift
VStack(alignment: .leading, spacing: 8) {
    Text("Brightness")
    Slider(value: $brightness, in: 0...100)
}
```

### Step-Based Slider

Use when the value still feels continuous but should snap.

```swift
Slider(value: $speed, in: 0.5...2.0, step: 0.25)
```

## Design System Rules

### Do

- Use a slider for continuous adjustment
- Show context for what the value changes
- Use step values when precision matters

### Don't

- Use a slider for exact code or quantity entry
- Hide the meaning of min and max

## Required Structure

```swift
Slider(value: $value, in: range)
```

## Component API

| API | Use Case |
| --- | --- |
| `Slider(value:in:)` | Continuous range |
| `Slider(value:in:step:)` | Snapped range |

## Common Patterns

### Playback Speed

```swift
Slider(value: $speed, in: 0.5...2.0, step: 0.25)
```
