# Toolbar

A native iPhone toolbar for navigation, search, and a small set of high-value actions. On iOS 26+, use SwiftUI’s standard toolbar APIs and let the system provide the Liquid Glass surface, grouping, and scroll-edge legibility.

## When to Use

- Screen-level actions in a `NavigationStack`
- Sheet or editor chrome with cancel/confirm actions
- Search that applies to the current screen or navigation container
- A compact action cluster for share, favorite, filter, or more

## Quick Start

The default list-screen toolbar:

```swift
struct InboxView: View {
    @State private var searchText = ""

    var body: some View {
        NavigationStack {
            MessageList()
                .navigationTitle("Inbox")
                .toolbarRole(.browser)
                .searchable(text: $searchText, prompt: "Search mail")
                .toolbar {
                    ToolbarItem(placement: .topBarTrailing) {
                        EditButton()
                    }

                    ToolbarItem(placement: .primaryAction) {
                        Button {
                            compose()
                        } label: {
                            Image(systemName: "square.and.pencil")
                        }
                        .accessibilityLabel("Compose")
                    }
                }
        }
    }
}
```

What this gets right:

- Title comes from the navigation bar
- Search uses the system toolbar placement
- Primary action sits on the trailing edge
- Secondary action stays lighter than the primary action

## Usage Patterns

### Browser Toolbar

Use this for list and collection screens. Keep the top bar to one primary action and up to two secondary actions.

```swift
struct NotesView: View {
    @State private var searchText = ""

    var body: some View {
        NavigationStack {
            NotesList()
                .navigationTitle("Notes")
                .toolbarRole(.browser)
                .searchable(text: $searchText, prompt: "Search notes")
                .toolbar {
                    ToolbarItem(placement: .topBarTrailing) {
                        Button {
                            showFilters()
                        } label: {
                            Image(systemName: "line.3.horizontal.decrease.circle")
                        }
                        .accessibilityLabel("Filter")
                    }

                    ToolbarItem(placement: .primaryAction) {
                        Button {
                            createNote()
                        } label: {
                            Image(systemName: "square.and.pencil")
                        }
                        .accessibilityLabel("New Note")
                    }
                }
        }
    }
}
```

### Sheet Toolbar

Use semantic placements for temporary flows. Text is usually better than icons here.

```swift
struct EditProfileView: View {
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        NavigationStack {
            Form {
                ProfileFields()
            }
            .navigationTitle("Edit Profile")
            .navigationBarTitleDisplayMode(.inline)
            .toolbarRole(.editor)
            .toolbar {
                ToolbarItem(placement: .cancellationAction) {
                    Button("Cancel") {
                        dismiss()
                    }
                }

                ToolbarItem(placement: .confirmationAction) {
                    Button("Done") {
                        save()
                    }
                }
            }
        }
    }
}
```

### Group Related Actions

Related actions should share a group. Unrelated actions should be separated into different toolbar items or an overflow menu.

```swift
struct PhotoDetailView: View {
    var body: some View {
        PhotoContent()
            .navigationTitle("Photo")
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    ShareLink(item: photoURL) {
                        Image(systemName: "square.and.arrow.up")
                    }
                    .accessibilityLabel("Share")
                }

                ToolbarItemGroup(placement: .topBarTrailing) {
                    Button {
                        toggleFavorite()
                    } label: {
                        Image(systemName: isFavorite ? "heart.fill" : "heart")
                    }
                    .accessibilityLabel("Favorite")

                    Button {
                        addToAlbum()
                    } label: {
                        Image(systemName: "plus.rectangle.on.folder")
                    }
                    .accessibilityLabel("Add to Album")
                }
            }
    }
}
```

### Search That Minimizes

Use minimized search when search matters, but not enough to occupy a full field all the time.

```swift
struct RecipesView: View {
    @State private var searchText = ""

    var body: some View {
        NavigationStack {
            RecipeList()
                .navigationTitle("Recipes")
                .searchable(text: $searchText, prompt: "Search recipes")
                .searchToolbarBehavior(.minimized)
        }
    }
}
```

If search is core to the screen, skip minimization and let the system show the full search field.

### Keep Actions Visible During Search

Use this when hiding toolbar actions during search would break the flow.

```swift
List(results) { result in
    ResultRow(result: result)
}
.searchable(text: $searchText, prompt: "Search")
.searchPresentationToolbarBehavior(.avoidHidingContent)
```

### Badged Toolbar Item

Badges work well for low-volume status or unread counts. Keep them short.

```swift
ToolbarItem(placement: .topBarTrailing) {
    Button {
        openActivity()
    } label: {
        Image(systemName: "bell")
    }
    .badge(unreadCount)
    .accessibilityLabel("Activity")
}
```

### Overflow for Secondary Actions

If you need more than a few actions, keep the primary action visible and push the rest into a menu.

```swift
.toolbar {
    ToolbarItem(placement: .primaryAction) {
        Button("Done") {
            finishEditing()
        }
    }

    ToolbarItem(placement: .topBarTrailing) {
        Menu {
            Button("Duplicate") { duplicateItem() }
            Button("Move") { moveItem() }
            Button("Delete", role: .destructive) { deleteItem() }
        } label: {
            Image(systemName: "ellipsis.circle")
        }
        .accessibilityLabel("More")
    }
}
```

## Design System Rules

### Do

- Use `NavigationStack`, `navigationTitle`, and `.toolbar {}` as the starting point
- Put the most important action in `.primaryAction`, `.confirmationAction`, or `.cancellationAction`
- Keep iPhone top bars tight: one primary action, one or two visible secondary actions
- Prefer monochrome SF Symbols for secondary toolbar actions
- Use text actions for `Cancel`, `Done`, `Edit`, and other actions that benefit from explicit wording
- Use icon-only actions for common secondary actions like search, filter, share, favorite, and more
- Add `accessibilityLabel(...)` to every icon-only toolbar item
- Attach `searchable(...)` to the container the search should cover
- Use `.searchToolbarBehavior(.minimized)` when search is useful but not central
- Group related actions with `ToolbarItemGroup`
- Move excess actions into a `Menu`
- Let the system handle the Liquid Glass surface and scroll-edge treatment

### Don't

- Build a fake toolbar with `safeAreaInset`, overlays, or pinned stacks
- Duplicate the system back button with your own leading navigation button
- Fill the top bar with four or five unrelated icons
- Mix icon-only and text-heavy actions randomly in the same cluster
- Tint every toolbar icon
- Put destructive actions in the most prominent visible position unless the whole screen is about destruction
- Add custom capsules, blur plates, or colored backgrounds behind toolbar buttons
- Keep old `toolbarBackground`, gradient scrims, or dark overlays that sit behind the bar unless you have a specific legibility problem to solve
- Force search into a custom row when `searchable(...)` already fits the job
- Leave badges on high-count or noisy actions that fire constantly

## Required Structure

Great iPhone toolbars usually follow this shape:

```swift
NavigationStack {
    ScreenContent()
        .navigationTitle("Title")
        .toolbarRole(.browser) // or .editor when editing
        .searchable(text: $searchText, prompt: "Search") // when the screen is searchable
        .toolbar {
            ToolbarItem(placement: .cancellationAction) {
                Button("Cancel") { cancel() }
            }

            ToolbarItemGroup(placement: .topBarTrailing) {
                Button {
                    showSecondaryAction()
                } label: {
                    Image(systemName: "slider.horizontal.3")
                }
                .accessibilityLabel("Filters")
            }

            ToolbarItem(placement: .primaryAction) {
                Button("Done") {
                    confirm()
                }
            }
        }
}
```

Use this structure:

- Title owned by the navigation bar
- Primary action on the trailing side
- Secondary actions grouped and minimized
- Search attached to the screen container, not manually embedded
- Overflow menu for everything that does not deserve permanent visibility

## Component API

### Placements

| Placement | Use Case |
| --- | --- |
| `.primaryAction` | Main screen-level action on iPhone |
| `.topBarTrailing` | Secondary actions like share, filter, favorite, more |
| `.topBarLeading` | Rare custom leading actions when there is no system back action |
| `.cancellationAction` | Dismiss or cancel in sheets and temporary flows |
| `.confirmationAction` | Save, done, apply, confirm |
| `.destructiveAction` | Destructive action in modal/editor contexts |
| `.bottomBar` | Bottom utility actions when needed; avoid competing with toolbar search |

### Roles

| Role | Use Case |
| --- | --- |
| `.automatic` | Default |
| `.browser` | Browse or inspect content collections |
| `.editor` | Editing flows and temporary changes |

### Search

| API | Use Case |
| --- | --- |
| `.searchable(text:prompt:)` | Default toolbar search |
| `.searchToolbarBehavior(.minimized)` | Collapse inactive search into a button-like control on iPhone |
| `.searchPresentationToolbarBehavior(.avoidHidingContent)` | Keep toolbar content visible while search is active |

### Supporting APIs

| API | Use Case |
| --- | --- |
| `ToolbarItemGroup` | Group related actions on one shared surface |
| `.badge(_:)` | Unread count or small status indicator |
| `Menu` | Overflow for secondary actions |

## Common Patterns

### Great iPhone List Toolbar

```swift
NavigationStack {
    MailList()
        .navigationTitle("Inbox")
        .toolbarRole(.browser)
        .searchable(text: $searchText, prompt: "Search mail")
        .toolbar {
            ToolbarItem(placement: .topBarTrailing) {
                EditButton()
            }

            ToolbarItem(placement: .primaryAction) {
                Button {
                    compose()
                } label: {
                    Image(systemName: "square.and.pencil")
                }
                .accessibilityLabel("Compose")
            }
        }
}
```

### Great iPhone Editor Toolbar

```swift
NavigationStack {
    Form {
        EditorFields()
    }
    .navigationTitle("New Reminder")
    .navigationBarTitleDisplayMode(.inline)
    .toolbarRole(.editor)
    .toolbar {
        ToolbarItem(placement: .cancellationAction) {
            Button("Cancel") { dismiss() }
        }

        ToolbarItem(placement: .confirmationAction) {
            Button("Add") { addReminder() }
        }
    }
}
```

### Great iPhone Detail Toolbar

```swift
PhotoDetail()
    .navigationTitle("Photo")
    .toolbar {
        ToolbarItem(placement: .topBarTrailing) {
            ShareLink(item: photoURL) {
                Image(systemName: "square.and.arrow.up")
            }
            .accessibilityLabel("Share")
        }

        ToolbarItemGroup(placement: .topBarTrailing) {
            Button {
                toggleFavorite()
            } label: {
                Image(systemName: isFavorite ? "heart.fill" : "heart")
            }
            .accessibilityLabel("Favorite")

            Menu {
                Button("Info") { showInfo() }
                Button("Delete", role: .destructive) { deletePhoto() }
            } label: {
                Image(systemName: "ellipsis.circle")
            }
            .accessibilityLabel("More")
        }
    }
```

## Remove These Old Patterns

When adopting iOS 26 toolbar behavior, remove:

- Custom top scrims and dark gradients behind the navigation bar
- Opaque `toolbarBackground` styling carried over from older designs
- Extra material capsules behind toolbar buttons
- Manual search bars pinned above lists
- Large action clusters that should live in a menu

A great iPhone toolbar feels sparse, stable, and obvious: one clear next action, a few quiet supporting actions, search within thumb reach, and no extra chrome fighting the system surface.
