# List

A native iPhone container for browsable, row-based content. On iOS 26+, start with SwiftUI `List`, use system row affordances, keep styling light, and let the screen feel like a place to scan, tap, swipe, and edit without friction.

## When to Use

- **Browseable collections**: inboxes, notes, files, orders, playlists, search results
- **Grouped settings**: preferences, account screens, feature toggles, detail metadata
- **Sectioned content**: recent items, pinned items, grouped history, categorized results
- **Editable collections**: multi-select, delete, move, reorder

Use `List` when rows are the primary interaction model. If the layout is more freeform or card-based, start elsewhere.

## Quick Start

The default browse list on iPhone:

```swift
import SwiftUI

struct Message: Identifiable, Hashable {
    let id: UUID
    let sender: String
    let subject: String
    let preview: String
    let isUnread: Bool
}

struct InboxView: View {
    let messages: [Message]

    var body: some View {
        NavigationStack {
            List(messages) { message in
                NavigationLink(value: message) {
                    InboxRow(message: message)
                }
            }
            .listStyle(.plain)
            .navigationTitle("Inbox")
            .navigationDestination(for: Message.self) { message in
                MessageDetailView(message: message)
            }
        }
    }
}

private struct InboxRow: View {
    let message: Message

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            HStack {
                Text(message.sender)
                    .font(.headline)

                Spacer()

                if message.isUnread {
                    Circle()
                        .fill(.tint)
                        .frame(width: 8, height: 8)
                }
            }

            Text(message.subject)
                .font(.subheadline)
                .foregroundStyle(.primary)
                .lineLimit(1)

            Text(message.preview)
                .font(.subheadline)
                .foregroundStyle(.secondary)
                .lineLimit(2)
        }
        .padding(.vertical, 4)
    }
}
```

The default grouped settings list:

```swift
struct SettingsView: View {
    @State private var notificationsEnabled = true

    var body: some View {
        NavigationStack {
            List {
                Section("Playback") {
                    NavigationLink("Downloads") {
                        DownloadsView()
                    }

                    Toggle("Notifications", isOn: $notificationsEnabled)
                }

                Section("Support") {
                    NavigationLink("Help") {
                        HelpView()
                    }
                }
            }
            .listStyle(.insetGrouped)
            .navigationTitle("Settings")
        }
    }
}
```

## Usage Patterns

### Browseable Collections

Use `NavigationLink` when tapping a row should drill in. This gives you the native highlight, chevron, back behavior, and edge-swipe navigation for free.

```swift
List(orders) { order in
    NavigationLink(value: order) {
        OrderRow(order: order)
    }
}
.listStyle(.plain)
```

Strong default:

- `.plain` for inboxes, feeds, search results, and dense browse views
- Large title on the root screen
- Inline title on pushed detail screens

### Grouped and Inset Styles

Use grouped styles when the list is about settings, metadata, or small sets of related controls.

```swift
List {
    Section("Account") {
        NavigationLink("Profile") { ProfileView() }
        NavigationLink("Security") { SecurityView() }
    }

    Section("Billing") {
        NavigationLink("Payment Methods") { PaymentMethodsView() }
    }
}
.listStyle(.insetGrouped)
```

Choose deliberately:

- `.insetGrouped`: default for settings and preference screens on iPhone
- `.grouped`: broader grouped blocks when you want less side inset and a more container-like feel
- `.plain`: long-content browsing

### List Backgrounds

Keep the system background unless the whole screen has a strong custom surface.

```swift
List {
    Section {
        ForEach(items) { item in
            NavigationLink(item.title) {}
        }
    }
}
.scrollContentBackground(.hidden)
.background(Color(uiColor: .systemGroupedBackground))
```

Use this sparingly:

- Hide the scroll background only when the parent view supplies the real background
- Prefer system background colors and materials
- Do not add card chrome behind every row just to make the list feel designed

### Selection and Edit Mode

On iPhone, selection is usually an editing behavior, not a browsing behavior. If people tap a row, they usually expect navigation or direct action.

```swift
struct DownloadsView: View {
    struct File: Identifiable, Hashable {
        let id: UUID
        let name: String
    }

    @State private var files: [File] = []
    @State private var selection = Set<File.ID>()

    var body: some View {
        List(files, selection: $selection) { file in
            Text(file.name)
        }
        .toolbar {
            ToolbarItem(placement: .topBarTrailing) {
                EditButton()
            }
        }
    }
}
```

Default rule:

- Tap row to open or act
- Enter edit mode to multi-select, reorder, or batch delete
- Use `EditButton()` unless the flow needs a custom edit trigger

### Row Affordances

Rows should advertise one primary action clearly.

```swift
List {
    NavigationLink {
        ProjectDetailView()
    } label: {
        Label("Atlas", systemImage: "folder")
    }

    Toggle(isOn: $isEnabled) {
        Label("Offline Mode", systemImage: "arrow.down.circle")
    }
}
```

Good row affordances:

- `NavigationLink` for drill-in
- `Toggle` for immediate on/off state
- `Button` for explicit row actions that do not navigate
- Small trailing metadata for counts, dates, status

Avoid making one row do multiple unrelated things.

### Swipe Actions

Swipe actions are for common, contextual row actions. Keep them short and obvious.

```swift
List(messages) { message in
    NavigationLink(value: message) {
        InboxRow(message: message)
    }
    .swipeActions {
        Button {
            archive(message)
        } label: {
            Label("Archive", systemImage: "archivebox")
        }

        Button(role: .destructive) {
            delete(message)
        } label: {
            Label("Delete", systemImage: "trash")
        }
    }
}
```

Use leading-edge swipe only when it is frequent and easy to understand, like flagging or marking unread.

```swift
.swipeActions(edge: .leading, allowsFullSwipe: true) {
    Button {
        toggleRead(message)
    } label: {
        Label("Read", systemImage: "envelope.open")
    }
    .tint(.blue)
}
```

### Performance and Identity Basics

Lists stay smooth when identity is stable and rows stay cheap.

```swift
struct Note: Identifiable, Hashable {
    let id: UUID
    let title: String
}

List(notes) { note in
    NavigationLink(value: note) {
        Text(note.title)
    }
}
```

Start here:

- Make row data `Identifiable` with a stable `id`
- Do not use `id: \.self` for mutable models unless the value itself is truly stable
- Keep expensive formatting, sorting, and filtering out of row bodies
- Avoid nested vertical scroll views inside list rows
- Prefer a consistent row height range so scanning and scrolling feel predictable

## Design System Rules

### Do

- Use `List` for vertical collections people scan row by row
- Default to `.plain` for content browsing and `.insetGrouped` for settings
- Wrap drill-in rows in `NavigationLink`
- Use `Section` to group meaningfully related rows
- Let separators, highlights, edit controls, and swipe affordances stay system-native
- Keep row content short, aligned, and easy to scan
- Put secondary text in `.secondary`
- Use trailing accessories for metadata, not decoration
- Add swipe actions only for actions people actually use
- Keep full-swipe reserved for obvious, safe, high-frequency actions

### Don't

- Build a fake list out of `ScrollView` and `LazyVStack` unless you intentionally need custom behavior `List` cannot provide
- Use selection as the main browse interaction on iPhone
- Hide the list background and then replace it with loud custom chrome
- Put multiple competing buttons inside one tappable row
- Add chevrons manually to rows that already use `NavigationLink`
- Mix unrelated row layouts in the same list without a strong reason
- Use swipe actions as the only way to access a critical action
- Depend on unstable identity for row updates, animations, or selection

## Required Structure

Prefer one of these row patterns:

### Browse Row

```swift
NavigationLink(value: item) {
    ItemRow(item: item)
}
```

### Settings Row

```swift
Section("Playback") {
    Toggle("Offline Mode", isOn: $isOffline)
    NavigationLink("Downloads") {
        DownloadsView()
    }
}
```

### Editable Row

```swift
List(items, selection: $selection) { item in
    Text(item.title)
}
```

If a row navigates, let `NavigationLink` own the affordance. If a row toggles state, use `Toggle`. If a row performs an action, use `Button`.

## Component API

### Choose a Style

| Style | Use Case |
| --- | --- |
| `.automatic` | Acceptable when the container already implies the right appearance |
| `.plain` | Browsing long collections, search results, inboxes, feeds |
| `.insetGrouped` | Settings, account screens, grouped controls |
| `.grouped` | Sectioned settings or metadata with stronger group containers |

### Common List Modifiers

| Modifier | Use Case |
| --- | --- |
| `.listStyle(...)` | Pick the overall list presentation |
| `.scrollContentBackground(...)` | Hide the built-in scroll background when the parent supplies one |
| `.refreshable { ... }` | Pull to refresh browseable content |
| `.searchable(text:prompt:)` | Filter or find within the current list |
| `.listRowSeparator(...)` | Adjust row separators when the design truly needs it |
| `.listRowBackground(...)` | Set row background for specific rows or sections |
| `.listSectionSpacing(...)` | Tune section spacing when the default feels off |
| `.environment(\\.editMode, ...)` | Programmatically control edit mode |

### Selection and Editing

- `List(data, selection: $selection)` enables single or multi-selection depending on the binding type
- `EditButton()` is the default iPhone affordance for entering edit mode
- Use `.onDelete`, `.onMove`, and list bindings for native editing flows

### Row Actions

- `.swipeActions(edge:allowsFullSwipe:content:)` adds row-level contextual actions
- Put the most common actions closest to the resting row edge
- Keep labels short; SwiftUI will adapt symbols for the swipe presentation

## Common Patterns

### Search Results List

```swift
struct SearchView: View {
    @State private var query = ""

    var body: some View {
        NavigationStack {
            List(results) { result in
                NavigationLink(value: result) {
                    SearchResultRow(result: result)
                }
            }
            .listStyle(.plain)
            .navigationTitle("Search")
            .searchable(text: $query, prompt: "Search")
        }
    }
}
```

### Settings Screen

```swift
List {
    Section("Account") {
        NavigationLink("Profile") { ProfileView() }
        NavigationLink("Privacy") { PrivacyView() }
    }

    Section("Notifications") {
        Toggle("Push Notifications", isOn: $pushEnabled)
    }
}
.listStyle(.insetGrouped)
```

### Custom Background, Native Rows

```swift
ZStack {
    LinearGradient(
        colors: [.black, Color(white: 0.12)],
        startPoint: .top,
        endPoint: .bottom
    )
    .ignoresSafeArea()

    List {
        Section {
            ForEach(items) { item in
                NavigationLink(item.title) {}
            }
        }
    }
    .scrollContentBackground(.hidden)
}
```

### Edit and Batch Delete

```swift
List(files, selection: $selection) { file in
    Text(file.name)
}
.toolbar {
    ToolbarItem(placement: .topBarLeading) {
        if !selection.isEmpty {
            Button("Delete", role: .destructive) {
                deleteSelectedFiles()
            }
        }
    }

    ToolbarItem(placement: .topBarTrailing) {
        EditButton()
    }
}
```
