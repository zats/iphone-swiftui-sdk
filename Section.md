# Section

Use `Section` to group related rows inside `List` and `Form`. On iPhone, it is the default way to add just enough structure for scanning without turning a screen into labeled noise.

## When to Use

Use `Section` when people benefit from a visible group label, short supporting context, or a separated destructive area.

Use it for:
- Settings and preferences
- Forms with related fields
- Search results grouped by result type
- Action groups with a different risk level

Skip it when:
- A list has one obvious group
- A section would contain one routine row with no header or footer
- Every row would need its own section

## Quick Start

```swift
import SwiftUI

struct NotificationsView: View {
    @State private var pushEnabled = true
    @State private var marketingEnabled = false

    var body: some View {
        Form {
            Section("Notifications") {
                Toggle("Push Notifications", isOn: $pushEnabled)
                Toggle("Marketing Emails", isOn: $marketingEnabled)
            } footer: {
                Text("Turn off marketing messages without affecting account alerts.")
            }
        }
    }
}
```

Default to:
- A short noun phrase header
- Rows that share one purpose
- A footer only when it prevents mistakes or answers a likely question

## Usage Patterns

### Settings Section

```swift
struct PrivacySettingsView: View {
    @State private var analyticsEnabled = false
    @State private var adPersonalizationEnabled = false

    var body: some View {
        Form {
            Section("Privacy") {
                Toggle("Share Analytics", isOn: $analyticsEnabled)
                Toggle("Personalized Ads", isOn: $adPersonalizationEnabled)
            } footer: {
                Text("You can change these choices at any time.")
            }
        }
    }
}
```

### List Grouping

```swift
struct LibraryView: View {
    let downloaded = ["Episode 1", "Episode 2"]
    let saved = ["Design Notes", "Release Plan"]

    var body: some View {
        List {
            Section("Downloaded") {
                ForEach(downloaded, id: \.self) { title in
                    Text(title)
                }
            }

            Section("Saved") {
                ForEach(saved, id: \.self) { title in
                    Text(title)
                }
            }
        }
    }
}
```

### Search Result Sections

```swift
struct SearchResultsView: View {
    let people = ["Maya Patel", "Noah Kim"]
    let documents = ["Q2 Plan", "Onboarding Checklist"]

    var body: some View {
        List {
            if !people.isEmpty {
                Section("People") {
                    ForEach(people, id: \.self) { name in
                        Text(name)
                    }
                }
            }

            if !documents.isEmpty {
                Section("Documents") {
                    ForEach(documents, id: \.self) { title in
                        Text(title)
                    }
                }
            }
        }
    }
}
```

Only add a result section when it has content. Empty categories add noise.

### Destructive Section

```swift
struct AccountDangerZoneView: View {
    var body: some View {
        Form {
            Section {
                Button("Sign Out", role: .destructive) {
                    signOut()
                }

                Button("Delete Account", role: .destructive) {
                    deleteAccount()
                }
            } footer: {
                Text("Deleting your account permanently removes your data.")
            }
        }
    }

    private func signOut() {}
    private func deleteAccount() {}
}
```

Separate destructive actions from routine settings. If the action needs explanation, put it in the footer.

## Design System Rules

- Use `Section` only inside section-aware containers such as `List` and `Form`.
- Keep headers short. Prefer 1 to 3 words.
- Omit the header when the group is already obvious from surrounding context.
- Use footers for consequences, recovery info, or compact helper text.
- Keep one intent per section. Do not mix account info, notifications, and billing in one block.
- Prefer 2 to 7 rows per section.
- If a section has one ordinary row and no meaningful label, remove the section.
- Separate destructive actions into their own section.
- Do not add decorative sections just to create spacing.

Do:
- Group related controls people may compare or scan together
- Use sentence-style footer copy that answers one likely question
- Hide empty search-result sections

Don't:
- Stack many tiny one-row sections
- Repeat the screen title as a section header
- Write long paragraph headers
- Use a footer under every section

## Required Structure

Each section should have:
- A section-aware container: `List` or `Form`
- Related rows with one shared purpose

Add when needed:
- `header` for label and scanning
- `footer` for help, warning, or consequence

Keep structure minimal:
- No nested `Section`
- No empty headers
- No empty sections

## Component API

Use the simplest initializer that fits:

```swift
Section("Title") {
    rows
}
```

```swift
Section {
    rows
} header: {
    Text("Title")
} footer: {
    Text("Helpful context")
}
```

Common header/footer content:
- `Text` for most cases
- Custom header content only when text is not enough

Common row content:
- `Text`
- `Toggle`
- `NavigationLink`
- `Button`
- `Picker`

## Common Patterns

### Enough Structure

Good:
- 3 account rows under `Account`
- 2 privacy toggles under `Privacy`
- Search results split into `People` and `Documents`

Too much:
- `Profile`, `Email`, `Password`, `Sign Out` as four separate one-row sections
- Headers that restate what each row already says

Rule of thumb: add a new section only when the label changes how people scan, decide, or avoid mistakes.
