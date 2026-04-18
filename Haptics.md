# Haptics

A native iPhone tactile feedback layer for confirming actions and reinforcing state changes. Use haptics sparingly and only when touch feedback improves confidence.

## When to Use

- **Success confirmation**
- **Selection changes**
- **Important state toggles**
- **Gesture completion**

## Design System Rules

### Do

- Match haptic strength to event importance
- Use them for meaningful confirmation
- Keep them rare enough to stay valuable

### Don't

- Fire haptics on every tap
- Use strong feedback for minor events

## Common Patterns

```swift
Button("Save") {
    save()
    let generator = UINotificationFeedbackGenerator()
    generator.notificationOccurred(.success)
}
```
