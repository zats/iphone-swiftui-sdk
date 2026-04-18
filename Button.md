# Button

A native iPhone action control for primary, secondary, inline, and toolbar actions. On iOS 26+, prefer Liquid Glass button styles for visible actions that should feel current and system-native.

## When to Use

- **`.glassProminent`**: The main action in a screen, sheet, or focused action group
- **`.glass`**: Secondary actions, floating actions, toolbar actions, contextual actions
- **`.plain`**: Inline tertiary actions where surrounding UI already provides enough structure
- **`.bordered` / `.borderedProminent`**: Dense form layouts, compatibility with existing grouped controls, places where glass adds too much visual weight
- **`role: .destructive` / `role: .cancel`**: Confirmation flows, alerts, destructive actions

## Quick Start

The default primary + secondary pair:

```swift
HStack {
    Button("Cancel") {
        dismiss()
    }
    .buttonStyle(.glass)

    Button("Save") {
        save()
    }
    .buttonStyle(.glassProminent)
}
.controlSize(.large)
```

Button with icon when the symbol improves clarity:

```swift
Button {
    addItem()
} label: {
    Label("Add Item", systemImage: "plus")
}
.buttonStyle(.glassProminent)
```

## Usage Patterns

### Primary Actions

Use one prominent button for the action people are most likely to take.

```swift
Button("Continue") {
    continueFlow()
}
.buttonStyle(.glassProminent)
.controlSize(.large)
```

### Secondary Actions

Use glass for supporting actions near a primary action.

```swift
HStack {
    Button("Later") {
        skipForNow()
    }
    .buttonStyle(.glass)

    Button("Continue") {
        continueFlow()
    }
    .buttonStyle(.glassProminent)
}
```

### Inline Actions

Use plain buttons when the surrounding layout already makes the action readable.

```swift
Button("Show More") {
    expandSection()
}
.buttonStyle(.plain)
.foregroundStyle(.tint)
```

Typical places:

- Section footers
- Inline “See All” actions
- Trailing actions inside rich content rows

### Toolbar Buttons

Toolbar actions should usually feel light, direct, and monochrome. Start with symbol-only buttons and semantic toolbar placement.

```swift
.toolbar {
    ToolbarItem(placement: .topBarTrailing) {
        Button {
            compose()
        } label: {
            Image(systemName: "square.and.pencil")
        }
        .buttonStyle(.glass)
        .accessibilityLabel("Compose")
    }
}
```

For the single toolbar action that should lead the flow, use `glassProminent` sparingly.

```swift
.toolbar {
    ToolbarItem(placement: .primaryAction) {
        Button("Done") {
            finishEditing()
        }
        .buttonStyle(.glassProminent)
    }
}
```

### Destructive Actions

Use `role` for destructive intent. Let the system present the action with the correct emphasis.

```swift
Button("Delete", role: .destructive) {
    deleteItem()
}
.buttonStyle(.glass)
```

In a confirmation dialog:

```swift
.confirmationDialog("Delete this file?", isPresented: $showingDeleteDialog) {
    Button("Delete", role: .destructive) {
        deleteItem()
    }

    Button("Cancel", role: .cancel) {}
}
```

### Full-Width Actions

Use flexible sizing for a single call to action that should fill available width.

```swift
Button("Continue") {
    continueFlow()
}
.buttonStyle(.glassProminent)
.buttonSizing(.flexible)
.controlSize(.large)
```

### Dense Form Actions

Use bordered styles when the screen is already visually dense and floating glass would compete with nearby controls.

```swift
HStack {
    Button("Reset") {
        resetForm()
    }
    .buttonStyle(.bordered)

    Button("Apply") {
        applyChanges()
    }
    .buttonStyle(.borderedProminent)
}
.controlSize(.small)
```

## Design System Rules

### Do

- Use **one prominent button** per screen, sheet, or tightly related action group
- Prefer **`.glassProminent`** for the primary action people should notice first
- Prefer **`.glass`** for secondary visible actions, especially in toolbars and floating contexts
- Start labels with a **verb** when possible: `Save`, `Continue`, `Add Payment Method`
- Use **`Label`** when the symbol adds meaning; use text alone when it is already clear
- Use **`role`** for destructive and cancel semantics instead of styling by hand
- Use **toolbar placements** like `.primaryAction` and `.topBarTrailing`
- Add an **accessibility label** to icon-only buttons
- Keep adjacent buttons at the **same control size**
- Use **tint** to convey meaning, not decoration
- Use **`.buttonSizing(.flexible)`** for full-width calls to action
- Fall back to **bordered styles** when the layout is dense enough that glass feels heavy

### Don't

- Put multiple **`.glassProminent`** or **`.borderedProminent`** buttons next to each other
- Make a destructive action the most visually dominant button in a view
- Use **`.plain`** for a primary call to action
- Create tappable `Text`, `Image`, or `HStack` with `onTapGesture` when it should be a button
- Tint every button in the interface
- Mix text-heavy buttons and icon-only buttons randomly in the same action cluster
- Use icon-only buttons in content areas without strong surrounding context
- Rebuild toolbar chrome with custom stacks just to position actions
- Add extra backgrounds behind toolbar or sheet actions when the system material already does the work
- Keep older custom button chrome that fights the Liquid Glass system appearance

## Required Structure

Prefer one of these three patterns:

### Text-only action

```swift
Button("Save") {
    save()
}
.buttonStyle(.glassProminent)
```

### Icon + text action

```swift
Button {
    share()
} label: {
    Label("Share", systemImage: "square.and.arrow.up")
}
.buttonStyle(.glass)
```

### Icon-only toolbar action

```swift
Button {
    search()
} label: {
    Image(systemName: "magnifyingglass")
}
.buttonStyle(.glass)
.accessibilityLabel("Search")
```

## Component API

### Choose a Style

| Style | Use Case |
| --- | --- |
| `.automatic` | Acceptable when the surrounding container already resolves to the right treatment |
| `.glassProminent` | Primary action |
| `.glass` | Secondary, contextual, floating, and toolbar actions |
| `.borderedProminent` | Dense layouts that still need a strong primary action |
| `.bordered` | Dense secondary actions |
| `.plain` | Inline tertiary action |

### Choose a Role

| Role | Use Case |
| --- | --- |
| default | Normal action |
| `.cancel` | Dismiss or back out of a temporary decision |
| `.destructive` | Delete, remove, discard, reset |

### Size and Layout

- `controlSize(...)` adjusts visual density and tap target sizing
- `buttonSizing(.flexible)` lets a button expand along its main axis
- Keep grouped actions visually balanced by using the same sizing on siblings
- Extra-large buttons are appropriate for especially important actions

### Shape and Emphasis

- `buttonBorderShape(...)` adjusts the border shape for bordered and glass-backed button styles
- `tint(...)` emphasizes the most important action or communicates meaning
- Prefer the system-resolved shape unless the surrounding design clearly calls for a different one
- At the bottom of sheets and other rounded containers, align button shape with the container’s corners

## Common Patterns

### Form Footer Actions

```swift
VStack(spacing: 12) {
    Button("Create Account") {
        createAccount()
    }
    .buttonStyle(.glassProminent)
    .buttonSizing(.flexible)

    Button("Maybe Later") {
        dismiss()
    }
    .buttonStyle(.plain)
}
.controlSize(.large)
```

### Row Accessory Action

```swift
HStack {
    VStack(alignment: .leading) {
        Text(file.name)
        Text(file.detail)
            .foregroundStyle(.secondary)
    }

    Spacer()

    Button {
        showMenu(for: file)
    } label: {
        Image(systemName: "ellipsis.circle")
    }
    .buttonStyle(.plain)
    .accessibilityLabel("More")
}
```

### Bottom Sheet Call to Action

```swift
Button("Book Trip") {
    bookTrip()
}
.buttonStyle(.glassProminent)
.buttonSizing(.flexible)
.controlSize(.large)
```
