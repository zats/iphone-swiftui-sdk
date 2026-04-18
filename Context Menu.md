# Context Menu

A native iPhone secondary-action surface for item-specific commands. On iOS 26+, use a context menu for lightweight actions behind a long press when visible UI would add clutter.

## When to Use

- **Long-press item actions**: Save, favorite, share, rename, move, delete
- **Object previews**: Photos, files, links, messages, places, rich cards
- **Lightweight secondary commands**: Actions tied to one object, not the whole screen
- **Dense lists and grids**: Places where repeating visible buttons would overwhelm the layout

Use something else when:

- The action is **primary or frequent**: use a visible `Button`
- The action belongs to a **list row gesture**: use `swipeActions`
- The trigger should be **obvious and tappable**: use `Menu`
- The action set is **large or hierarchical**: redesign the flow

## Quick Start

Start with a short list of object-specific actions. No preview unless the object benefits from one.

```swift
struct MessageRow: View {
    let message: Message

    var body: some View {
        MessageCell(message: message)
            .contextMenu {
                Button {
                    reply(to: message)
                } label: {
                    Label("Reply", systemImage: "arrowshape.turn.up.left")
                }

                Button {
                    toggleStar(for: message)
                } label: {
                    Label(message.isStarred ? "Remove Star" : "Star",
                          systemImage: message.isStarred ? "star.slash" : "star")
                }

                Button {
                    share(message)
                } label: {
                    Label("Share", systemImage: "square.and.arrow.up")
                }

                Button("Delete", role: .destructive) {
                    delete(message)
                }
            }
    }
}
```

## Usage Patterns

### Row or Card Secondary Actions

This is the default use case on iPhone: long-press an object to reveal a few relevant actions.

```swift
FileRow(file: file)
    .contextMenu {
        Button {
            openInfo(for: file)
        } label: {
            Label("Get Info", systemImage: "info.circle")
        }

        Button {
            rename(file)
        } label: {
            Label("Rename", systemImage: "pencil")
        }

        Button {
            move(file)
        } label: {
            Label("Move", systemImage: "folder")
        }

        Button("Delete", role: .destructive) {
            delete(file)
        }
    }
```

Typical actions:

- Open supporting actions
- Share or export
- Save, favorite, pin, archive
- Rename, move, duplicate
- Delete after confirmation when needed

### Context Menu with Preview

Add a preview when the object has visual or spatial meaning. Keep the preview informational.

```swift
PhotoThumbnail(photo: photo)
    .contextMenu {
        Button {
            share(photo)
        } label: {
            Label("Share", systemImage: "square.and.arrow.up")
        }

        Button {
            toggleFavorite(photo)
        } label: {
            Label(photo.isFavorite ? "Remove Favorite" : "Favorite",
                  systemImage: photo.isFavorite ? "heart.slash" : "heart")
        }

        Button("Delete", role: .destructive) {
            delete(photo)
        }
    } preview: {
        Image(uiImage: photo.previewImage)
            .resizable()
            .scaledToFit()
            .frame(width: 240, height: 240)
            .clipShape(RoundedRectangle(cornerRadius: 24, style: .continuous))
    }
```

Use previews for:

- Photos and videos
- Files with meaningful thumbnails
- Rich links, locations, messages, or cards

Skip previews for:

- Simple text rows
- Utility actions with no meaningful object representation
- Menus that would open constantly during fast list work

### Prefer Swipe Actions in Lists

If the action is common and row-based, make it discoverable with swipe.

```swift
MessageRow(message: message)
    .swipeActions(edge: .leading, allowsFullSwipe: false) {
        Button {
            toggleUnread(message)
        } label: {
            Label("Unread", systemImage: "envelope.badge")
        }
        .tint(.blue)
    }
    .swipeActions {
        Button(role: .destructive) {
            delete(message)
        } label: {
            Label("Delete", systemImage: "trash")
        }
    }
    .contextMenu {
        Button {
            reply(to: message)
        } label: {
            Label("Reply", systemImage: "arrowshape.turn.up.left")
        }

        Button {
            archive(message)
        } label: {
            Label("Archive", systemImage: "archivebox")
        }
    }
```

Rule: swipe for common row actions, context menu for broader secondary actions.

### Prefer Menu When the Trigger Should Be Visible

Use `Menu` when people should know actions exist before they touch and hold.

```swift
Menu {
    Button("Sort by Name") { sortByName() }
    Button("Sort by Date") { sortByDate() }
    Button("Sort by Size") { sortBySize() }
} label: {
    Label("Sort", systemImage: "arrow.up.arrow.down")
}
```

Use `Menu` for:

- Explicit overflow controls
- Toolbar action groups
- Sort, filter, and view options
- Commands that affect the current screen instead of one object

## Design System Rules

### Do

- Use context menus for **3-6 relevant actions** tied to a specific object
- Put the **most likely actions first**
- Use `Label` with familiar SF Symbols when they improve scanning
- Use `role: .destructive` and `role: .cancel` semantics where appropriate
- Add a preview only when it helps identify the object faster
- Keep actions lightweight and immediate
- Make the same important action available somewhere more discoverable if people need it often
- Use the system long-press behavior; don’t invent a custom gesture

### Don't

- Hide a primary action in a context menu
- Put screen-level commands in an item’s context menu
- Stuff the menu with rare, admin, or debug actions
- Build deep submenu hierarchies for iPhone
- Rely on context menus as the only way to discover essential features
- Put interactive mini-interfaces in the preview
- Duplicate every swipe action, toolbar action, and visible button in the same menu

## Required Structure

Prefer this shape:

```swift
ContentView(item: item)
    .contextMenu {
        Button { primarySecondaryAction(item) } label: {
            Label("Action", systemImage: "star")
        }

        Button { supportingAction(item) } label: {
            Label("Another Action", systemImage: "square.and.arrow.up")
        }

        Button("Delete", role: .destructive) {
            delete(item)
        }
    }
```

If the object benefits from a richer glance, add a preview:

```swift
ContentView(item: item)
    .contextMenu {
        menuItems(for: item)
    } preview: {
        ItemPreview(item: item)
    }
```

## Component API

### Choose a Presentation

| API | Use Case |
| --- | --- |
| `.contextMenu { ... }` | Default object actions |
| `.contextMenu(menuItems:preview:)` | Object actions that benefit from a custom preview |

### What Belongs Inside

Prefer these menu item types:

- `Button` for actions
- `Button(role: .destructive)` for destructive actions
- `Toggle` for simple on/off object state
- `Picker` only for very small mutually exclusive choices

Avoid turning the menu into a form.

### Action Selection Rules

| Pattern | Best For |
| --- | --- |
| `contextMenu` | Hidden secondary object actions |
| `swipeActions` | Frequent list-row actions |
| `Menu` | Visible menu trigger and screen-level options |
| Visible `Button` | Primary or high-frequency action |

### Preview Rules

- Keep previews glanceable and static in feel
- Show the object, not a different workflow
- Size the preview modestly for iPhone
- Don’t depend on the preview for critical information

## Common Patterns

### Lightweight Object Actions

```swift
NoteCard(note: note)
    .contextMenu {
        Button {
            pin(note)
        } label: {
            Label("Pin", systemImage: "pin")
        }

        Button {
            duplicate(note)
        } label: {
            Label("Duplicate", systemImage: "plus.square.on.square")
        }

        Button {
            share(note)
        } label: {
            Label("Share", systemImage: "square.and.arrow.up")
        }
    }
```

### Conditional Availability

If an object has no useful secondary actions, don’t attach a context menu.

```swift
if item.supportsSecondaryActions {
    ItemRow(item: item)
        .contextMenu {
            Button("Share") { share(item) }
        }
} else {
    ItemRow(item: item)
}
```
