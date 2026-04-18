# Swipe Actions

Use swipe actions for fast, row-scoped commands in lists. On iPhone, they work best for a small set of high-frequency actions people already expect on a horizontal swipe.

## When to Use

Use swipe actions when:

- The action applies to one list row.
- The action is common enough to deserve faster access than a context menu.
- The action can be understood from a short verb and symbol.
- You want Mail-style behavior like delete, flag, pin, unread, archive, or favorite.

Use something else when:

- The action is rare, destructive, or needs explanation. Use a confirmation flow or context menu.
- The action affects multiple rows. Use edit mode or a toolbar action.
- You need more than a couple of actions. Swipe actions get cluttered fast.

## Quick Start

Default to one primary trailing action. Add a leading action only when it is common and clearly different.

```swift
import SwiftUI

struct InboxView: View {
    @State private var messages = SampleMessage.inbox

    var body: some View {
        List {
            ForEach(messages) { message in
                MessageRow(message: message)
                    .swipeActions {
                        Button(role: .destructive) {
                            delete(message)
                        } label: {
                            Label("Delete", systemImage: "trash")
                        }
                    }
            }
        }
    }

    private func delete(_ message: SampleMessage) {
        messages.removeAll { $0.id == message.id }
    }
}
```

Add leading and trailing edges only when each side has a clear job.

```swift
import SwiftUI

struct MailboxView: View {
    @State private var messages = SampleMessage.inbox

    var body: some View {
        List {
            ForEach(messages) { message in
                MessageRow(message: message)
                    .swipeActions(edge: .leading, allowsFullSwipe: false) {
                        Button {
                            toggleRead(message)
                        } label: {
                            Label(
                                message.isUnread ? "Read" : "Unread",
                                systemImage: message.isUnread ? "envelope.open" : "envelope.badge"
                            )
                        }
                        .tint(.blue)
                    }
                    .swipeActions(edge: .trailing) {
                        Button {
                            flag(message)
                        } label: {
                            Label("Flag", systemImage: "flag")
                        }
                        .tint(.orange)

                        Button(role: .destructive) {
                            delete(message)
                        } label: {
                            Label("Delete", systemImage: "trash")
                        }
                    }
            }
        }
    }

    private func toggleRead(_ message: SampleMessage) {}
    private func flag(_ message: SampleMessage) {}
    private func delete(_ message: SampleMessage) {}
}
```

## Usage Patterns

### Trailing edge

Use trailing actions for destructive or secondary management actions.

- Good defaults: `Delete`, `Archive`, `Flag`.
- Put the most important action first in code if you allow full swipe.
- Keep it to one or two actions in most rows.

### Leading edge

Use leading actions for state toggles or lightweight classification.

- Good defaults: `Read/Unread`, `Pin`, `Favorite`.
- Prefer `allowsFullSwipe: false` unless the first action is very safe and very common.
- Avoid putting destructive actions on the leading edge.

### Swipe vs Context Menu

Choose swipe actions when speed matters and the action set is small.

- Swipe actions win for frequent row actions people should discover through normal list use.
- Context menus win for larger action sets, low-frequency actions, or actions that need labels visible before reveal.
- If an action already lives in a context menu, only promote it to swipe when usage justifies the extra surface area.

## Design System Rules

- Default to trailing-only.
- Default to one action. Two is usually the practical max.
- Avoid more than two actions per edge.
- Never rely on more than four total swipe actions across both edges.
- Use full swipe only for the safest primary action on that edge.
- Use `.destructive` only for real destructive work. Let the system color it red.
- Use short labels. One verb. No punctuation.
- Use familiar SF Symbols. Swipe actions are too small for novelty.
- Keep the row itself tappable. Swipe actions should supplement the primary tap target, not replace it.

Do:

- Put frequent actions on swipe.
- Make the first action on an edge the one you want closest to that edge.
- Confirm destructive actions when recovery is hard or impossible.
- Use tint sparingly for non-destructive actions.

Don't:

- Add swipe actions to every list just because the API exists.
- Mix too many unrelated actions into one row.
- Hide critical navigation behind a swipe.
- Enable full swipe for risky actions like permanent delete unless the pattern is already deeply expected.

## Required Structure

Swipe actions should feel crisp because the row stays simple.

- One clear primary tap action for the row.
- One dominant swipe edge.
- One obvious first action on that edge.
- Consistent verbs and symbols across the whole list.

What makes swipe actions feel crisp:

- The row content is stable and uncluttered.
- The revealed actions match user intent immediately.
- Full swipe performs a predictable result.
- The same row type always swipes the same way.

What makes them feel cluttered:

- Too many actions.
- Different rows exposing different action sets without a strong reason.
- Two edges full of equally important actions.
- Bright tints everywhere competing with row content.

## Component API

```swift
func swipeActions<T>(
    edge: HorizontalEdge = .trailing,
    allowsFullSwipe: Bool = true,
    @ViewBuilder content: () -> T
) -> some View where T: View
```

### Parameters

- `edge`: `.trailing` by default. Use `.leading` only for clearly useful row-state actions.
- `allowsFullSwipe`: When `true`, a full swipe triggers the first action on that edge automatically.
- `content`: Usually `Button` views with `Label`.

### Behavior Notes

- Swipe actions are designed for views acting as rows in a `List`.
- Actions appear in the order you declare them, starting from the swipe’s originating edge.
- SwiftUI automatically uses the filled SF Symbol variant inside swipe actions.
- If you add swipe actions, SwiftUI stops synthesizing the delete affordance from `onDelete`. Add your own delete action if you still need it.
- Multiple `.swipeActions` modifiers on the same row and edge accumulate.

## Common Patterns

### Safe full swipe

Use full swipe for a reversible, high-frequency action.

```swift
import SwiftUI

struct RemindersView: View {
    @State private var reminders = SampleReminder.items

    var body: some View {
        List {
            ForEach(reminders) { reminder in
                ReminderRow(reminder: reminder)
                    .swipeActions {
                        Button {
                            complete(reminder)
                        } label: {
                            Label("Complete", systemImage: "checkmark")
                        }
                        .tint(.green)
                    }
            }
        }
    }

    private func complete(_ reminder: SampleReminder) {}
}
```

### Destructive action with confirmation

Use this when the consequence is heavier than the swipe gesture suggests.

```swift
import SwiftUI

struct ProjectsView: View {
    @State private var projects = SampleProject.items
    @State private var pendingDelete: SampleProject?

    var body: some View {
        List {
            ForEach(projects) { project in
                ProjectRow(project: project)
                    .swipeActions(edge: .trailing, allowsFullSwipe: false) {
                        Button(role: .destructive) {
                            pendingDelete = project
                        } label: {
                            Label("Delete", systemImage: "trash")
                        }
                    }
            }
        }
        .confirmationDialog(
            "Delete project?",
            isPresented: Binding(
                get: { pendingDelete != nil },
                set: { if !$0 { pendingDelete = nil } }
            )
        ) {
            Button("Delete", role: .destructive) {
                guard let pendingDelete else { return }
                delete(pendingDelete)
                self.pendingDelete = nil
            }
            Button("Cancel", role: .cancel) {}
        }
    }

    private func delete(_ project: SampleProject) {}
}
```

### State toggle on leading, management on trailing

Use this pattern when both edges have obvious, repeatable meaning.

```swift
import SwiftUI

struct NotesView: View {
    @State private var notes = SampleNote.items

    var body: some View {
        List {
            ForEach(notes) { note in
                NoteRow(note: note)
                    .swipeActions(edge: .leading, allowsFullSwipe: false) {
                        Button {
                            togglePinned(note)
                        } label: {
                            Label("Pin", systemImage: "pin")
                        }
                        .tint(.yellow)
                    }
                    .swipeActions(edge: .trailing, allowsFullSwipe: true) {
                        Button(role: .destructive) {
                            delete(note)
                        } label: {
                            Label("Delete", systemImage: "trash")
                        }
                    }
            }
        }
    }

    private func togglePinned(_ note: SampleNote) {}
    private func delete(_ note: SampleNote) {}
}
```
