# Text Editor

A native iPhone multiline text input for notes, descriptions, comments, and long-form entry. Use `TextEditor` when the user needs room to think, draft, or revise.

## When to Use

- **Notes**
- **Descriptions**
- **Comments**
- **Message drafts**

Use something else when:

- The input is short: use `TextField`
- The content is read-only text

## Quick Start

```swift
import SwiftUI

struct NotesEditorView: View {
    @State private var notes = ""

    var body: some View {
        TextEditor(text: $notes)
            .frame(minHeight: 180)
    }
}
```

## Usage Patterns

### Form Section Editor

```swift
Form {
    Section("Notes") {
        TextEditor(text: $notes)
            .frame(minHeight: 160)
    }
}
```

### Standalone Draft Surface

Use when writing is the whole task.

```swift
TextEditor(text: $draft)
    .navigationTitle("Draft")
```

## Design System Rules

### Do

- Give multiline entry room to breathe
- Use `TextField` for short values instead
- Keep the editor visually quiet
- Save drafts predictably

### Don't

- Force long content into a single-line field
- Put several large editors on one small screen
- Overdecorate the editor surface

## Required Structure

```swift
TextEditor(text: $text)
    .frame(minHeight: 160)
```

## Component API

| API | Use Case |
| --- | --- |
| `TextEditor` | Multiline entry |
| `.frame(minHeight:)` | Comfortable writing space |

## Common Patterns

### Comment Composer

```swift
TextEditor(text: $comment)
    .frame(minHeight: 120)
```
