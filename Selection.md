# Selection

A native iPhone selection pattern for choosing one or many items. Keep selection explicit, predictable, and separate from navigation. On iPhone, single-tap usually means open or act; persistent multi-selection usually belongs in edit mode.

## When to Use

- **Single selection**: Pick one item, account, filter, or destination
- **Multi-selection**: Batch actions inside edit mode
- **Current choice display**: Settings and pickers where one option is active

Use something else when:

- A tap should navigate: use `NavigationLink`
- A tap should perform work: use `Button`
- The choice is binary: use `Toggle`

## Quick Start

Single selection should feel obvious and stable.

```swift
import SwiftUI

struct AccountPickerView: View {
    struct Account: Identifiable, Hashable {
        let id: UUID
        let name: String
    }

    @State private var selectedAccountID: UUID?
    let accounts: [Account]

    var body: some View {
        List(accounts) { account in
            Button {
                selectedAccountID = account.id
            } label: {
                HStack {
                    Text(account.name)
                    Spacer()

                    if selectedAccountID == account.id {
                        Image(systemName: "checkmark")
                            .foregroundStyle(.tint)
                    }
                }
            }
            .buttonStyle(.plain)
        }
        .navigationTitle("Account")
    }
}
```

## Usage Patterns

### Single Selection

Use a visible selected state when one option is active.

```swift
ForEach(sorts, id: \.self) { sort in
    Button {
        selectedSort = sort
    } label: {
        HStack {
            Text(sort.title)
            Spacer()

            if selectedSort == sort {
                Image(systemName: "checkmark")
                    .foregroundStyle(.tint)
            }
        }
    }
    .buttonStyle(.plain)
}
```

### Multi-Selection

On iPhone, multi-select usually belongs in edit mode.

```swift
List(items, selection: $selection) { item in
    Row(item: item)
}
```

### Selection vs Navigation

Do not make one tap both select and navigate.

```swift
NavigationLink {
    DetailView(item: item)
} label: {
    Row(item: item)
}
```

If the row opens detail, it should not also act like a selectable checkbox row outside edit mode.

### Selection in Pickers

When the screen exists to choose one value, selection should be the whole point.

```swift
Picker("Theme", selection: $theme) {
    Text("System").tag(Theme.system)
    Text("Light").tag(Theme.light)
    Text("Dark").tag(Theme.dark)
}
.pickerStyle(.inline)
```

## Design System Rules

### Do

- Make selection visually explicit
- Use one selection model per screen
- Keep selection separate from navigation
- Use edit mode for multi-select in lists
- Use a checkmark or clear state marker for single selection
- Keep selected state stable as content refreshes

### Don't

- Make people guess whether a tap selects or opens
- Mix multi-select and drill-in in the same browsing state
- Use subtle color alone for selected state
- Keep more than one item selected in a single-select screen
- Leave stale selections after the backing items disappear

## Required Structure

Single selection:

```swift
Button {
    selectedID = item.id
} label: {
    HStack {
        Text(item.title)
        Spacer()
        if selectedID == item.id {
            Image(systemName: "checkmark")
        }
    }
}
.buttonStyle(.plain)
```

Multi-selection:

```swift
List(items, selection: $selection) { item in
    Row(item: item)
}
```

## Component API

### Common State

| State | Use Case |
| --- | --- |
| `selectedID: Item.ID?` | Single selection |
| `selection: Set<Item.ID>` | Multi-selection |

### Common Containers

| API | Use Case |
| --- | --- |
| `List(_:selection:)` | Multi-select lists |
| `Picker(selection:)` | Standard single-choice controls |
| Plain `Button` rows | Rich single-selection lists |

## Common Patterns

### Settings Choice Screen

```swift
ForEach(options) { option in
    Button {
        selectedOption = option.id
    } label: {
        HStack {
            Text(option.title)
            Spacer()
            if selectedOption == option.id {
                Image(systemName: "checkmark")
            }
        }
    }
    .buttonStyle(.plain)
}
```
