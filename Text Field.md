# Text Field

A native iPhone single-line text input. Use `TextField` for short values like names, email, tags, codes, and search-like entry. Keep the field purpose obvious and the keyboard correct.

## When to Use

- **Short text**: Names, titles, labels
- **Structured text**: Email, URL, code, phone
- **Search-like input** inside a form or custom entry flow

Use something else when:

- Input is secret: use `SecureField`
- Input is long-form: use `TextEditor`

## Quick Start

```swift
import SwiftUI

struct ProjectNameField: View {
    @State private var name = ""

    var body: some View {
        TextField("Project Name", text: $name)
            .textInputAutocapitalization(.words)
    }
}
```

## Usage Patterns

### Email Field

```swift
TextField("Email", text: $email)
    .keyboardType(.emailAddress)
    .textInputAutocapitalization(.never)
    .autocorrectionDisabled()
```

### Code or Token Entry

```swift
TextField("Invite Code", text: $code)
    .textInputAutocapitalization(.characters)
    .autocorrectionDisabled()
```

### Submit Behavior

```swift
TextField("Search", text: $query)
    .submitLabel(.search)
    .onSubmit {
        runSearch()
    }
```

## Design System Rules

### Do

- Match the keyboard to the content
- Disable autocorrection where it hurts
- Use a clear field title or placeholder
- Keep formatting and validation predictable

### Don't

- Use one generic keyboard for everything
- Auto-capitalize email or URLs
- Use a single-line field for paragraphs

## Required Structure

```swift
TextField("Label", text: $value)
```

## Component API

| Modifier | Use Case |
| --- | --- |
| `.keyboardType(...)` | Input optimization |
| `.textInputAutocapitalization(...)` | Capitalization rules |
| `.autocorrectionDisabled()` | Structured input |
| `.submitLabel(...)` | Keyboard action |

## Common Patterns

### Search-in-Form Field

```swift
TextField("Invite by email", text: $email)
    .keyboardType(.emailAddress)
    .submitLabel(.done)
```
