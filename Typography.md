# Typography

A native iPhone typography system for hierarchy and readability. Start with system text styles. Use weight and size to establish structure, not brand theater.

## When to Use

- **Every screen**
- **Hierarchy**
- **Long-form readability**
- **Status and summaries**

## Design System Rules

### Do

- Use semantic text styles
- Keep hierarchy shallow
- Let Dynamic Type expand naturally

### Don't

- Invent many custom font scales
- Use oversized titles everywhere
- Make secondary text too faint to read

## Common Patterns

```swift
Text("Title")
    .font(.title2.weight(.semibold))

Text("Supporting text")
    .font(.subheadline)
    .foregroundStyle(.secondary)
```
