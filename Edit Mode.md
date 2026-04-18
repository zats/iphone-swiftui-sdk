# Edit Mode

A native iPhone editing state for batch actions on list content. Use edit mode when people need to select, move, reorder, or delete multiple items. Keep normal browsing fast, then switch into a clearly different mode for batch work.

## When to Use

- **Multi-select**: Delete, move, archive, mark, or export several items at once
- **Reordering**: Prioritized lists, playlists, favorites, custom collections
- **Batch actions**: Screens where acting on one item at a time would be slow
- **Collection management**: Saved items, downloads, reading lists, folders

Use something else when:

- The main task is opening a row
- The action is frequent and row-specific: use `swipeActions`
- The manipulation is obvious inline: use direct drag handles or a toggle instead

## Quick Start

Start with `List(selection:)` and `EditButton()`.

```swift
import SwiftUI

struct DownloadsView: View {
    struct File: Identifiable, Hashable {
        let id: UUID
        let name: String
    }

    @Environment(\.editMode) private var editMode
    @State private var files: [File] = [
        .init(id: UUID(), name: "Invoice.pdf"),
        .init(id: UUID(), name: "Slides.key"),
        .init(id: UUID(), name: "Notes.txt")
    ]
    @State private var selection = Set<File.ID>()

    var isEditing: Bool {
        editMode?.wrappedValue.isEditing == true
    }

    var body: some View {
        NavigationStack {
            List(files, selection: $selection) { file in
                Text(file.name)
            }
            .navigationTitle("Downloads")
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    EditButton()
                }

                if isEditing {
                    ToolbarItemGroup(placement: .bottomBar) {
                        Button("Move") {
                            moveSelection()
                        }
                        .disabled(selection.isEmpty)

                        Button("Delete", role: .destructive) {
                            deleteSelection()
                        }
                        .disabled(selection.isEmpty)
                    }
                }
            }
        }
    }

    private func moveSelection() {}

    private func deleteSelection() {
        files.removeAll { selection.contains($0.id) }
        selection.removeAll()
    }
}
```

## Usage Patterns

### Batch Selection

Use edit mode when people need to choose rows first, then act once.

```swift
List(messages, selection: $selection) { message in
    MessageRow(message: message)
}
```

Default:

- top-right `Edit`
- checkmarks while editing
- batch actions in the bottom bar

### Delete and Move

Batch destructive actions should only appear during editing.

```swift
.toolbar {
    if isEditing {
        ToolbarItemGroup(placement: .bottomBar) {
            Button("Move") {
                moveSelection()
            }
            .disabled(selection.isEmpty)

            Button("Delete", role: .destructive) {
                showingDeleteDialog = true
            }
            .disabled(selection.isEmpty)
        }
    }
}
.confirmationDialog("Delete selected items?", isPresented: $showingDeleteDialog) {
    Button("Delete", role: .destructive) {
        deleteSelection()
    }
}
```

Use a confirmation dialog when the action is destructive or hard to undo.

### Reordering

Use `onMove` when order belongs to the user.

```swift
List {
    ForEach(favorites) { favorite in
        Text(favorite.title)
    }
    .onMove { source, destination in
        favorites.move(fromOffsets: source, toOffset: destination)
    }
}
.toolbar {
    ToolbarItem(placement: .topBarTrailing) {
        EditButton()
    }
}
```

Good reorder targets:

- favorites
- pinned items
- custom lists

Bad reorder targets:

- search results
- server-ranked feeds
- chronological history

### Editing Toolbar State

The toolbar should change with mode. Browsing actions should usually disappear while editing.

```swift
.toolbar {
    if isEditing {
        ToolbarItem(placement: .confirmationAction) {
            Button("Done") {
                editMode?.wrappedValue = .inactive
            }
        }
    } else {
        ToolbarItem(placement: .topBarTrailing) {
            EditButton()
        }

        ToolbarItem(placement: .primaryAction) {
            Button {
                createItem()
            } label: {
                Image(systemName: "plus")
            }
        }
    }
}
```

### Edit Mode vs Direct Manipulation

Prefer direct manipulation when one obvious row action solves the problem.

- Use swipe to archive one message
- Use a toggle for one setting
- Use edit mode for repeated or multi-item work

If people enter edit mode just to perform one common row action, the design is too heavy.

## Design System Rules

### Do

- Use `EditButton()` as the default entry point
- Reserve edit mode for batch actions and reorder
- Keep normal browsing unchanged outside edit mode
- Show batch actions only while editing
- Disable batch actions until selection exists
- Use the bottom bar for batch controls
- Clear selection when the action completes or edit mode ends
- Confirm destructive batch actions when they are hard to undo

### Don't

- Start in edit mode by default
- Use edit mode for one frequent row action
- Leave `Edit`, `Add`, search, and batch actions visible at once
- Use reorder when order is not user-controlled
- Keep stale selections after leaving edit mode
- Mix navigation and multi-select taps in an ambiguous way

## Required Structure

Prefer this shape:

```swift
@Environment(\.editMode) private var editMode
@State private var selection = Set<Item.ID>()

List(items, selection: $selection) { item in
    Row(item: item)
}
.toolbar {
    ToolbarItem(placement: .topBarTrailing) {
        EditButton()
    }

    if editMode?.wrappedValue.isEditing == true {
        ToolbarItemGroup(placement: .bottomBar) {
            Button("Action") { performBatchAction() }
                .disabled(selection.isEmpty)
        }
    }
}
```

## Component API

### Core Pieces

| API | Use Case |
| --- | --- |
| `@Environment(\\.editMode)` | Read or control edit state |
| `EditButton()` | Default edit-mode toggle |
| `List(_:selection:)` | Multi-select content |
| `.onMove(...)` | Reordering |
| `.onDelete(...)` | Inline delete when appropriate |

### Placements

| Placement | Use Case |
| --- | --- |
| `.topBarTrailing` | `EditButton()` |
| `.confirmationAction` | `Done` while editing |
| `.bottomBar` | Batch actions |

## Common Patterns

### Mail-Style Batch Actions

```swift
.toolbar {
    ToolbarItem(placement: .topBarTrailing) {
        EditButton()
    }

    if isEditing {
        ToolbarItemGroup(placement: .bottomBar) {
            Button("Mark Read") { markRead() }
            Button("Archive") { archive() }
            Button("Delete", role: .destructive) { showingDeleteDialog = true }
        }
    }
}
```
