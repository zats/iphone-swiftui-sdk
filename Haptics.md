# Haptics

A native iPhone tactile feedback layer for confirming actions and reinforcing state changes. Use haptics sparingly and only when touch feedback improves confidence, clarity, or delight.

## When to Use

- **Success confirmation**
- **Selection changes**
- **Important state toggles**
- **Gesture completion**
- **Threshold crossing**

Use something else when:

- The event is too minor to deserve feedback
- Visual change already makes the outcome obvious

## Quick Start

On modern SwiftUI, start with `sensoryFeedback`.

```swift
import SwiftUI

struct FavoriteButton: View {
    @State private var isFavorite = false

    var body: some View {
        Button {
            isFavorite.toggle()
        } label: {
            Image(systemName: isFavorite ? "heart.fill" : "heart")
        }
        .sensoryFeedback(.selection, trigger: isFavorite)
    }
}
```

## Usage Patterns

### Selection and State Changes

Use light feedback for discrete state changes.

```swift
.sensoryFeedback(.selection, trigger: selectedFilter)
```

Good fits:

- segmented control changes
- selection changes
- snapping to a new level

### Success and Failure

Use stronger notification-style feedback for meaningful outcomes.

```swift
.sensoryFeedback(.success, trigger: didSave)
```

Or with UIKit when needed:

```swift
let generator = UINotificationFeedbackGenerator()
generator.notificationOccurred(.success)
```

Good fits:

- save completed
- payment approved
- destructive failure

### Threshold Crossing

Use feedback when a value crosses a meaningful boundary, not on every pixel of movement.

```swift
.sensoryFeedback(.increase, trigger: progressLevel) { oldValue, newValue in
    newValue > oldValue
}
```

Good fits:

- progress level changes
- snapping between discrete zoom levels
- reaching a goal threshold

### Press and Release

Use press/release feedback sparingly for controls or interactions where touch state matters.

```swift
.sensoryFeedback(.press(.soft), trigger: isPressed)
```

This is useful when:

- the interaction is tactile by nature
- the pressed state carries meaning

Do not use it on every ordinary button in the app.

### UIKit and Core Haptics Escape Hatches

Use UIKit generators for classic interaction feedback. Use Core Haptics only when the product truly needs a custom tactile language.

UIKit:

- `UISelectionFeedbackGenerator`
- `UIImpactFeedbackGenerator`
- `UINotificationFeedbackGenerator`

Core Haptics:

- custom continuous or transient patterns
- richer, product-specific tactile behavior

Most apps do not need Core Haptics for ordinary UI.

## Design System Rules

### Do

- Match haptic strength to event importance
- Use haptics for meaningful confirmation
- Prefer `sensoryFeedback` in SwiftUI
- Keep haptics rare enough to stay valuable
- Use selection feedback for discrete changes, not noisy continuous motion
- Remember the system may decide not to play feedback

### Don't

- Fire haptics on every tap
- Use strong feedback for minor events
- Stack visual, audio, and haptic emphasis on every interaction
- Build a custom haptic language when system feedback already fits
- Depend on haptics as the only sign that something happened

## Required Structure

Prefer this pattern:

```swift
Button("Save") {
    save()
}
.sensoryFeedback(.success, trigger: didSave)
```

For discrete changes:

```swift
Picker("Filter", selection: $filter) {
    ...
}
.sensoryFeedback(.selection, trigger: filter)
```

## Component API

### SwiftUI

| API | Use Case |
| --- | --- |
| `.sensoryFeedback(.selection, trigger: ...)` | Discrete selection changes |
| `.sensoryFeedback(.success, trigger: ...)` | Successful completion |
| `.sensoryFeedback(.warning, trigger: ...)` | Warning-level outcome |
| `.sensoryFeedback(.error, trigger: ...)` | Failed outcome |
| `.sensoryFeedback(.increase / .decrease, trigger: ...)` | Crossing meaningful thresholds |

### UIKit

| API | Use Case |
| --- | --- |
| `UISelectionFeedbackGenerator` | Selection changes |
| `UIImpactFeedbackGenerator` | Impact and weight |
| `UINotificationFeedbackGenerator` | Success, warning, error |

## Common Patterns

### Save Confirmation

```swift
Button("Save") {
    save()
    didSave.toggle()
}
.sensoryFeedback(.success, trigger: didSave)
```

### Filter Change

```swift
Picker("Filter", selection: $filter) {
    Text("All").tag(Filter.all)
    Text("Open").tag(Filter.open)
}
.pickerStyle(.segmented)
.sensoryFeedback(.selection, trigger: filter)
```

### Important Toggle

```swift
Toggle("Face ID", isOn: $faceIDEnabled)
    .sensoryFeedback(.selection, trigger: faceIDEnabled)
```
