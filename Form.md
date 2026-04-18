# Form

A native iPhone container for structured data entry and settings. Use `Form` when the screen is about editing values, not browsing content. Keep forms short, grouped, and obvious.

## When to Use

- **Settings**
- **Profile editing**
- **Checkout and account flows**
- **Create or edit sheets**

Use something else when:

- The screen is mostly browsing: use `List`
- The layout is custom and spatial: use `ScrollView` + stacks

## Quick Start

```swift
import SwiftUI

struct ProfileFormView: View {
    @State private var name = ""
    @State private var email = ""
    @State private var notifications = true

    var body: some View {
        NavigationStack {
            Form {
                Section("Profile") {
                    TextField("Name", text: $name)
                    TextField("Email", text: $email)
                        .keyboardType(.emailAddress)
                }

                Section("Preferences") {
                    Toggle("Notifications", isOn: $notifications)
                }
            }
            .navigationTitle("Profile")
        }
    }
}
```

## Usage Patterns

### Create or Edit Flow

Use an inline title and confirm/cancel actions in the toolbar.

### Group Related Fields

Use `Section` for meaningful groups, not every two rows.

### Validation

Keep validation close to the field and concise.

## Design System Rules

### Do

- Group related fields
- Use clear labels and predictable keyboards
- Keep forms short
- Use standard controls first
- Keep one primary confirm action

### Don't

- Mix browsing content into the form
- Add decorative cards around each field
- Make every row a custom layout without reason
- Use placeholder text as the only label when clarity matters

## Required Structure

```swift
Form {
    Section("Group") {
        TextField("Field", text: $value)
    }
}
```

## Component API

| API | Use Case |
| --- | --- |
| `Form` | Structured entry screens |
| `Section` | Grouping |
| Toolbar confirm/cancel | Commit or dismiss |

## Common Patterns

### Sheet Form

```swift
Form {
    TextField("Project Name", text: $name)
}
.navigationTitle("New Project")
.navigationBarTitleDisplayMode(.inline)
```
