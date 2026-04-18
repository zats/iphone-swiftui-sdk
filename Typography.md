# Typography

A native iPhone typography system for hierarchy and readability. Start with system text styles, keep hierarchy shallow, and let Dynamic Type work. Typography should clarify the interface, not compete with it.

## When to Use

- **Every screen**
- **Hierarchy**
- **Long-form readability**
- **Status, metrics, and summaries**

Use something else when:

- Size and weight are trying to solve a layout problem that spacing should solve
- Decorative type is replacing clear structure

## Quick Start

Start with semantic text styles.

```swift
import SwiftUI

VStack(alignment: .leading, spacing: 6) {
    Text("Weekly Summary")
        .font(.title2.weight(.semibold))

    Text("You completed 12 tasks this week.")
        .font(.subheadline)
        .foregroundStyle(.secondary)
}
```

## Usage Patterns

### Primary Hierarchy

Use one clear headline and one clear supporting style.

```swift
Text("Project Atlas")
    .font(.title3.weight(.semibold))

Text("Updated 2 hours ago")
    .font(.subheadline)
    .foregroundStyle(.secondary)
```

Good default pairings:

- `.title2` + `.body`
- `.headline` + `.subheadline`
- `.body` + `.footnote`

### Long-Form Reading

Use readable sizes and comfortable line lengths.

```swift
Text(articleBody)
    .font(.body)
    .lineSpacing(4)
```

For longer passages:

- prefer `.body`
- use regular, medium, or semibold weights
- avoid very light weights

### Dense UI Text

Use smaller styles only when the content is genuinely secondary.

```swift
Text("Synced just now")
    .font(.footnote)
    .foregroundStyle(.secondary)
```

Do not shrink core information just to make a crowded layout fit.

### Dynamic Type

Design the layout to adapt before forcing text to shrink.

```swift
VStack(alignment: .leading, spacing: 4) {
    Text(title)
        .font(.headline)

    Text(detail)
        .font(.subheadline)
        .foregroundStyle(.secondary)
}
```

Use `minimumScaleFactor` rarely and only for tight, single-line cases like compact labels.

```swift
Text(accountName)
    .lineLimit(1)
    .minimumScaleFactor(0.8)
```

### Matching Symbols and Text

Use SF Symbols alongside system text styles so weight and scale stay aligned.

```swift
Label("Downloads", systemImage: "arrow.down.circle")
    .font(.headline)
```

## Design System Rules

### Do

- Start with system text styles
- Use size, weight, and color to build hierarchy
- Keep the number of text styles on one screen low
- Prefer regular, medium, semibold, or bold weights over thin weights
- Support Dynamic Type by letting layouts reflow
- Use secondary color before shrinking type
- Match SF Symbols to adjacent text styles

### Don't

- Invent a custom font scale for every screen
- Use ultralight or thin text for important content
- Make every heading huge
- Use footnote-sized text for core actions or content
- Depend on `minimumScaleFactor` to rescue bad layouts
- Mix several typefaces without a strong reason

## Required Structure

Prefer this hierarchy:

```swift
VStack(alignment: .leading, spacing: 4) {
    Text("Title")
        .font(.headline)

    Text("Supporting text")
        .font(.subheadline)
        .foregroundStyle(.secondary)
}
```

For larger screen headers:

```swift
VStack(alignment: .leading, spacing: 8) {
    Text("Overview")
        .font(.title2.weight(.semibold))

    Text("Short explanatory text.")
        .font(.body)
        .foregroundStyle(.secondary)
}
```

## Component API

### System Styles

| Style | Use Case |
| --- | --- |
| `.largeTitle` | Rare top-level hero headers |
| `.title2` / `.title3` | Screen and section headers |
| `.headline` | Important row titles |
| `.body` | Main reading text |
| `.subheadline` | Secondary content |
| `.footnote` / `.caption` | Metadata and low-priority detail |

### Useful Modifiers

| API | Use Case |
| --- | --- |
| `.font(...)` | Semantic style |
| `.fontWeight(...)` | Extra emphasis |
| `.lineSpacing(...)` | Reading comfort |
| `.lineLimit(...)` | Bound long text |
| `.minimumScaleFactor(...)` | Rare compact labels |

## Common Patterns

### Settings Row

```swift
VStack(alignment: .leading, spacing: 2) {
    Text("Notifications")
        .font(.body)

    Text("Push, sounds, badges")
        .font(.subheadline)
        .foregroundStyle(.secondary)
}
```

### Metric + Label

```swift
VStack(alignment: .leading, spacing: 2) {
    Text("12")
        .font(.title2.weight(.semibold))

    Text("Open Tasks")
        .font(.footnote)
        .foregroundStyle(.secondary)
}
```

### Long-Form Body

```swift
Text(bodyText)
    .font(.body)
    .lineSpacing(4)
    .foregroundStyle(.primary)
```
