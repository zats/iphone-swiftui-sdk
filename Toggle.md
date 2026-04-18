# Toggle

A native iPhone binary control for on/off state. Use `Toggle` when the primary task is changing a boolean value immediately. Keep the label clear and the result obvious.

## When to Use

- **Settings**
- **Feature flags**
- **Permissions inside setup**
- **Immediate state changes**

Use something else when:

- The choice has more than two options
- The change needs confirmation before applying

## Quick Start

```swift
import SwiftUI

struct NotificationsToggleView: View {
    @State private var notificationsEnabled = true

    var body: some View {
        Toggle("Notifications", isOn: $notificationsEnabled)
    }
}
```

## Usage Patterns

### Settings Row

```swift
Toggle("Offline Mode", isOn: $offlineMode)
```

### Rich Label

```swift
Toggle(isOn: $faceIDEnabled) {
    VStack(alignment: .leading, spacing: 2) {
        Text("Face ID")
        Text("Unlock the app quickly")
            .font(.subheadline)
            .foregroundStyle(.secondary)
    }
}
```

### Setup Flow

```swift
Toggle("Email me product updates", isOn: $marketingOptIn)
```

## Design System Rules

### Do

- Use a toggle for immediate on/off state
- Write labels as the state being controlled
- Keep subtitles short
- Let the value change instantly when possible

### Don't

- Use a toggle for multi-choice decisions
- Put a toggle inside a row that also navigates
- Make people confirm every simple toggle change

## Required Structure

```swift
Toggle("Label", isOn: $isEnabled)
```

## Component API

| API | Use Case |
| --- | --- |
| `Toggle(_:isOn:)` | Standard binary control |
| `Toggle(isOn:label:)` | Rich labels |

## Common Patterns

### Settings Group

```swift
Section("Preferences") {
    Toggle("Notifications", isOn: $notifications)
    Toggle("Offline Mode", isOn: $offlineMode)
}
```
