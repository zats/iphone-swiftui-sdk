# Accessibility Patterns

A native iPhone accessibility baseline for all components and screens. Accessibility is not a polish pass. It is part of the default design.

## When to Use

- **Every screen**
- **Every control**
- **Every state change**

## Design System Rules

### Do

- Label icon-only actions
- Preserve large tap targets
- Support Dynamic Type
- Use semantic controls instead of gestures on bare views
- Keep color from carrying meaning alone

### Don't

- Hide actions behind unlabeled icons
- Depend on tiny touch targets
- Use custom gestures where standard controls fit

## Common Patterns

```swift
Button {
    compose()
} label: {
    Image(systemName: "square.and.pencil")
}
.accessibilityLabel("Compose")
```
