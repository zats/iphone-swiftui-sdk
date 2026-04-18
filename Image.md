# Image & Icon

A native iPhone image and icon pattern for content, illustration, identity, and supporting visuals. Use images to carry real information or brand value. Use SF Symbols to clarify actions and improve scanning without competing with text.

## When to Use

- **Content imagery**
- **Hero visuals**
- **Thumbnails**
- **Illustrations with clear purpose**
- **Toolbar actions and status cues**
- **List row leading visuals**

Use something else when:

- A symbol already explains the idea
- The image is purely decorative noise

## Quick Start

```swift
import SwiftUI

Image("hero")
    .resizable()
    .scaledToFit()
```

For symbols:

```swift
Image(systemName: "bell")
    .foregroundStyle(.secondary)
```

## Usage Patterns

### Content Thumbnail

```swift
Image("photo")
    .resizable()
    .scaledToFill()
    .frame(width: 64, height: 64)
    .clipShape(RoundedRectangle(cornerRadius: 12, style: .continuous))
```

### Full-Width Image

```swift
Image("cover")
    .resizable()
    .scaledToFill()
    .frame(height: 220)
    .clipped()
```

### Async Remote Image

```swift
AsyncImage(url: imageURL) { image in
    image
        .resizable()
        .scaledToFill()
} placeholder: {
    Color.secondary.opacity(0.12)
}
```

### Toolbar Icons

Use monochrome by default.

```swift
Button {
    compose()
} label: {
    Image(systemName: "square.and.pencil")
}
```

### List Row Icons

```swift
Image(systemName: "folder")
    .frame(width: 28, height: 28)
    .foregroundStyle(.tint)
```

### Status Icons

```swift
Image(systemName: "checkmark.circle.fill")
    .foregroundStyle(.green)
```

## Design System Rules

### Do

- Use images with a clear job
- Prefer SF Symbols first for actions, status, and navigation cues
- Crop intentionally
- Keep placeholder states calm
- Match corner treatment to the surrounding UI
- Use monochrome icons in toolbars and dense UI
- Tint icons only when meaning matters

### Don't

- Stretch images arbitrarily
- Use decorative stock imagery to fill empty space
- Mix many unrelated aspect ratios in tight grids without structure
- Mix several icon styles in one cluster
- Tint every icon for decoration
- Use icons without labels when the meaning is unclear

## Required Structure

```swift
Image("name")
    .resizable()
    .scaledToFill()
```

For icons:

```swift
Image(systemName: "symbol.name")
```

## Component API

| API | Use Case |
| --- | --- |
| `Image` | Local assets |
| `Image(systemName:)` | SF Symbols |
| `AsyncImage` | Remote images |
| `.scaledToFit()` | Preserve full image |
| `.scaledToFill()` | Fill frame |
| `.symbolRenderingMode(...)` | Symbol treatment |
| `.foregroundStyle(...)` | Meaningful emphasis |

## Common Patterns

### Rounded Thumbnail

```swift
Image("cover")
    .resizable()
    .scaledToFill()
    .frame(width: 72, height: 72)
    .clipShape(RoundedRectangle(cornerRadius: 12, style: .continuous))
```

### Navigation Row Icon

```swift
Image(systemName: "gearshape")
    .frame(width: 28, height: 28)
    .foregroundStyle(.tint)
```
