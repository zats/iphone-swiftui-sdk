# Stepper

A native iPhone control for increasing or decreasing a value in small discrete increments. Use `Stepper` when exact adjustment matters more than fast sweeping changes.

## When to Use

- **Quantity**
- **Small numeric ranges**
- **Count-based settings**

Use something else when:

- The range is large and exploratory: use `Slider`
- The value is freeform text: use `TextField`

## Quick Start

```swift
import SwiftUI

struct GuestsStepperView: View {
    @State private var guests = 2

    var body: some View {
        Stepper("Guests: \(guests)", value: $guests, in: 1...12)
    }
}
```

## Usage Patterns

### Quantity Row

```swift
Stepper("Copies: \(copies)", value: $copies, in: 1...20)
```

### Duration or Count

```swift
Stepper("Snooze: \(minutes) min", value: $minutes, in: 5...60, step: 5)
```

## Design System Rules

### Do

- Use a stepper for precise increment/decrement
- Keep the range bounded when possible
- Show the current value in the label

### Don't

- Use a stepper for large continuous ranges
- Hide the current count

## Required Structure

```swift
Stepper("Label: \(value)", value: $value, in: range)
```

## Component API

| API | Use Case |
| --- | --- |
| `Stepper(_:value:in:)` | Standard bounded stepper |
| `Stepper(_:value:in:step:)` | Larger increments |

## Common Patterns

### Reminder Minutes

```swift
Stepper("Reminder: \(minutes) min", value: $minutes, in: 5...60, step: 5)
```
