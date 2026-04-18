# Spacing & Layout

A native iPhone spacing and layout system for clarity and rhythm. Start with simple vertical stacks, system containers, and consistent gaps. Layout should disappear behind the content.

## When to Use

- **Every screen**
- **Grouping**
- **Alignment**
- **Action hierarchy**

## Design System Rules

### Do

- Use consistent spacing inside one screen
- Group related elements tightly
- Leave larger gaps between different blocks
- Prefer simple stacks before custom geometry

### Don't

- Add padding everywhere without hierarchy
- Jam unrelated controls into the same block
- Use layout tricks when stacks already solve it

## Common Patterns

```swift
VStack(alignment: .leading, spacing: 16) {
    header
    content
    actions
}
.padding(20)
```
