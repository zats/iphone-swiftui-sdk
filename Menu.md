# Menu

A native iPhone overflow control for secondary actions and compact option sets. On iOS 26+, use `Menu` to keep the screen focused while still exposing related commands. Keep the most frequent action visible; put the rest in the menu.

## When to Use

- **Overflow actions**: Extra actions in navigation bars, toolbars, cards, and rows
- **Related secondary commands**: Rename, duplicate, move, share, export
- **Compact settings**: Secondary toggles or one-of-many options that don't need constant visibility
- **Contextual actions**: Commands that only matter for the current item or current selection

Prefer something else when:

- The action is **primary or frequent**: use a visible `Button`
- The user must choose among **consequences of an intentional action**: use `confirmationDialog`
- The setting changes often and benefits from always being visible: use a visible `Toggle`, `Picker`, or segmented control

## Quick Start

Use an ellipsis menu for overflow actions. Keep destructive work separate and confirm it when it can't be trivially undone.

```swift
struct NoteActionsMenu: View {
    @State private var showingDeleteDialog = false

    var body: some View {
        Menu {
            Section {
                Button("Rename", systemImage: "pencil") {
                    renameNote()
                }

                Button("Duplicate", systemImage: "doc.on.doc") {
                    duplicateNote()
                }

                Button("Share", systemImage: "square.and.arrow.up") {
                    shareNote()
                }
            }

            Section {
                Button("Delete", systemImage: "trash", role: .destructive) {
                    showingDeleteDialog = true
                }
            }
        } label: {
            Image(systemName: "ellipsis.circle")
        }
        .menuStyle(.button)
        .buttonStyle(.glass)
        .accessibilityLabel("More")
        .confirmationDialog("Delete this note?", isPresented: $showingDeleteDialog) {
            Button("Delete", role: .destructive) {
                deleteNote()
            }
        }
    }
}
```

## Usage Patterns

### Toolbar Overflow

Keep the top one or two actions visible. Put the rest behind a single overflow menu.

```swift
struct DocumentScreen: View {
    var body: some View {
        Text("Document")
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    Menu {
                        Button("Share", systemImage: "square.and.arrow.up") {
                            shareDocument()
                        }

                        Button("Rename", systemImage: "pencil") {
                            renameDocument()
                        }

                        Button("Export PDF", systemImage: "doc") {
                            exportPDF()
                        }
                    } label: {
                        Image(systemName: "ellipsis.circle")
                    }
                    .menuStyle(.button)
                    .buttonStyle(.glass)
                    .accessibilityLabel("More actions")
                }
            }
    }
}
```

### Group Related Actions

Use `Section` to group related commands. On iPhone, prefer sections over nested submenus.

```swift
Menu("Project", systemImage: "folder") {
    Section {
        Button("Rename", systemImage: "pencil") {
            renameProject()
        }

        Button("Duplicate", systemImage: "doc.on.doc") {
            duplicateProject()
        }
    }

    Section {
        Button("Archive", systemImage: "archivebox") {
            archiveProject()
        }

        Button("Share", systemImage: "square.and.arrow.up") {
            shareProject()
        }
    }
}
.menuStyle(.button)
.buttonStyle(.glass)
```

### Toggles and Pickers in Menus

Use a `Toggle` for a secondary on/off option. Use a `Picker` in a menu for one-of-many choices that don't deserve persistent UI, especially when the option list is longer.

```swift
enum SortOrder: String, CaseIterable {
    case newest
    case oldest
    case name
    case size
}

struct FileOptionsMenu: View {
    @State private var showHiddenFiles = false
    @State private var sortOrder: SortOrder = .newest

    var body: some View {
        Menu("View", systemImage: "slider.horizontal.3") {
            Toggle("Show Hidden Files", systemImage: "eye.slash", isOn: $showHiddenFiles)

            Picker("Sort By", selection: $sortOrder) {
                Label("Newest First", systemImage: "clock").tag(SortOrder.newest)
                Label("Oldest First", systemImage: "clock.arrow.trianglehead.counterclockwise.rotate.90").tag(SortOrder.oldest)
                Label("Name", systemImage: "textformat").tag(SortOrder.name)
                Label("Size", systemImage: "arrow.up.left.and.arrow.down.right").tag(SortOrder.size)
            }
        }
        .menuStyle(.button)
        .buttonStyle(.glass)
    }
}
```

### Menu vs Visible Buttons vs Confirmation Dialog

- Use a **visible button** for the top action on the screen and any action people need to discover quickly
- Use a **menu** for secondary actions people reveal on purpose
- Use **`confirmationDialog`** after a deliberate action when people must confirm, discard, or choose among outcomes

Typical flow:

```swift
Button("Delete") {
    showingDeleteChoices = true
}
.buttonStyle(.glass)
.confirmationDialog("Delete this note?", isPresented: $showingDeleteChoices) {
    Button("Delete", role: .destructive) {
        deleteNote()
    }

    Button("Move to Trash") {
        moveToTrash()
    }
}
```

Don't replace that dialog with a menu. The user already initiated a risky action and now needs a clear decision surface.

## Design System Rules

### Do

- Keep the **primary action visible**
- Use a menu for **secondary, contextual, or overflow** actions
- Use `Section` to group related items
- Put **destructive actions last** and isolate them in their own section
- Use `role: .destructive` for destructive items
- Follow destructive menu items with a **`confirmationDialog`** when the action is irreversible or hard to undo
- Use SF Symbols when they make scanning faster
- Add an accessibility label to **icon-only menu triggers**
- Keep labels short and verb-led: `Rename`, `Move`, `Share`
- Prefer one overflow menu over several competing icon buttons

### Don't

- Hide the screen's main action in a menu
- Put unrelated commands in the same menu just because space is tight
- Nest menus deeply on iPhone
- Use a menu for a binary choice that should be a visible `Toggle`
- Use a menu for a short, always-visible segmented choice
- Put destructive items in the middle of safe actions
- Rely on color alone; use `role` and clear labels
- Add icons to every item if the symbols don't improve recognition
- Use `primaryAction` on iPhone unless the split behavior is obvious and intentional

## Required Structure

Start with this shape:

```swift
Menu {
    Section {
        Button("Rename", systemImage: "pencil") {
            rename()
        }

        Button("Share", systemImage: "square.and.arrow.up") {
            share()
        }
    }

    Section {
        Button("Delete", systemImage: "trash", role: .destructive) {
            showingDeleteDialog = true
        }
    }
} label: {
    Image(systemName: "ellipsis.circle")
}
.menuStyle(.button)
.buttonStyle(.glass)
.accessibilityLabel("More")
.confirmationDialog("Delete this item?", isPresented: $showingDeleteDialog) {
    Button("Delete", role: .destructive) {
        delete()
    }
}
```

This gives you:

- A clear trigger
- Related actions grouped together
- A safe destructive path
- System-native menu presentation on iPhone

## Component API

### Choose an Initializer

| API | Use Case |
| --- | --- |
| `Menu("Actions") { ... }` | Simple text label |
| `Menu("Actions", systemImage: "ellipsis.circle") { ... }` | Text + symbol trigger |
| `Menu { ... } label: { ... }` | Custom trigger view |
| `Menu(..., primaryAction: { ... })` | Rare escape hatch when tap should run an action and secondary interaction should open the menu |

For iPhone, start with the normal menu behavior. `primaryAction` is harder to discover than a plain overflow menu.

### Menu Content

Use these inside the menu:

- `Button` for actions
- `Toggle` for secondary on/off state
- `Picker` for one-of-many choices
- `Section` for grouping
- `Menu` for a submenu only when one extra level is truly worth it

### Ordering and Dismissal

- `menuOrder(.fixed)` keeps the order you wrote
- Default ordering can prioritize items near the touch point on iPhone
- Use `menuOrder(.fixed)` when item order communicates importance
- `menuActionDismissBehavior(.disabled)` is an escape hatch for repeatable adjustments

```swift
Menu("Font Size") {
    Button("Increase", systemImage: "plus.magnifyingglass") {
        increaseSize()
    }
    .menuActionDismissBehavior(.disabled)

    Button("Decrease", systemImage: "minus.magnifyingglass") {
        decreaseSize()
    }
    .menuActionDismissBehavior(.disabled)

    Button("Reset") {
        resetSize()
    }
}
```

### Trigger Styling

- To make the trigger behave like a button, start with `.menuStyle(.button)`
- In toolbars and overflow affordances, pair that with `.buttonStyle(.glass)`
- For inline text-like triggers inside dense content, `.buttonStyle(.plain)` can be appropriate
- Match the trigger style to the surrounding button system; don't invent special menu chrome

## Common Patterns

### Row Accessory Menu

```swift
struct FileRow: View {
    let file: File

    var body: some View {
        HStack {
            VStack(alignment: .leading) {
                Text(file.name)
                Text(file.detail)
                    .foregroundStyle(.secondary)
            }

            Spacer()

            Menu {
                Button("Rename", systemImage: "pencil") {
                    rename(file)
                }

                Button("Move", systemImage: "folder") {
                    move(file)
                }

                Button("Share", systemImage: "square.and.arrow.up") {
                    share(file)
                }
            } label: {
                Image(systemName: "ellipsis.circle")
            }
            .menuStyle(.button)
            .buttonStyle(.plain)
            .accessibilityLabel("More actions")
        }
    }
}
```

### Sort and Filter Menu

```swift
Menu("Sort", systemImage: "arrow.up.arrow.down.circle") {
    Picker("Sort By", selection: $sortOrder) {
        Text("Date").tag(SortOrder.date)
        Text("Name").tag(SortOrder.name)
        Text("Size").tag(SortOrder.size)
    }

    Toggle("Show Favorites Only", systemImage: "star", isOn: $favoritesOnly)
}
.menuStyle(.button)
.buttonStyle(.glass)
```

### Safe Destructive Overflow

```swift
Menu {
    Button("Export", systemImage: "square.and.arrow.up") {
        exportItem()
    }

    Section {
        Button("Delete", systemImage: "trash", role: .destructive) {
            showingDeleteDialog = true
        }
    }
} label: {
    Image(systemName: "ellipsis.circle")
}
.menuStyle(.button)
.buttonStyle(.glass)
.accessibilityLabel("More")
.confirmationDialog("Delete this item?", isPresented: $showingDeleteDialog) {
    Button("Delete", role: .destructive) {
        deleteItem()
    }
}
```
