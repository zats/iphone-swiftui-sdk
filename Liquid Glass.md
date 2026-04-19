# Liquid Glass

A native iPhone guide for adopting Liquid Glass in SwiftUI on iOS 26+. Start with standard app structure, bars, sheets, and controls. Let the system own the glass layer. Add custom Liquid Glass effects only when the interface truly needs a custom functional element.

## When to Use

- **Navigation bars**
- **Tab bars**
- **Toolbars**
- **Sheets and popovers**
- **Primary and secondary buttons**
- **Small custom controls that float above content**

Use something else when:

- The view is part of the **content layer**
- You are styling **cards, rows, panels, or screen backgrounds**
- A standard SwiftUI control already provides the effect automatically

## Quick Start

On iPhone, the fastest way to adopt Liquid Glass is to stop fighting the system.

```swift
import SwiftUI

struct LibraryScreen: View {
    @State private var showingAddSheet = false

    var body: some View {
        NavigationStack {
            List(items) { item in
                NavigationLink(item.title) {
                    DetailScreen(item: item)
                }
            }
            .navigationTitle("Library")
            .toolbar {
                ToolbarItem(placement: .primaryAction) {
                    Button {
                        showingAddSheet = true
                    } label: {
                        Image(systemName: "plus")
                    }
                    .buttonStyle(.glass)
                    .accessibilityLabel("Add Item")
                }
            }
            .sheet(isPresented: $showingAddSheet) {
                NavigationStack {
                    AddItemView()
                        .navigationTitle("New Item")
                        .navigationBarTitleDisplayMode(.inline)
                }
            }
        }
    }
}
```

What this gets right:

- navigation and toolbar pick up the system glass treatment automatically
- the toolbar action uses a glass button style instead of custom chrome
- the sheet keeps the system-provided presentation background

## Usage Patterns

### Let Standard Containers Adopt the Design

Build with the latest SDK first. If the app already uses `TabView`, `NavigationStack`, `sheet`, `Menu`, `Toolbar`, `Button`, `Toggle`, `Slider`, and `Picker`, a lot of the new look arrives automatically.

Use standard containers before custom effects.

Good fits:

- `TabView` for top-level sections
- `NavigationStack` for hierarchy
- `sheet` for scoped work
- `toolbar` for screen actions
- system controls for interaction

### Keep Liquid Glass in the Functional Layer

Liquid Glass belongs above content. It should help people navigate and act, not turn the content itself into chrome.

Use Liquid Glass for:

- bars
- controls
- floating action elements
- compact custom overlays with a clear job

Use standard materials or system backgrounds for:

- content surfaces
- app backgrounds
- grouped sections
- cards
- list and form structure

Do not spread Liquid Glass across the content layer just because the bars use it.

### Remove Old Custom Backgrounds

Custom blur plates, dark scrims, tinted capsules, and manual toolbar backgrounds often fight the new system behavior.

Audit and remove:

- custom `toolbarBackground` styling that only existed to fake chrome
- custom `presentationBackground` on ordinary sheets
- overlays behind tab bars and navigation bars
- extra darkening layers under toolbar items

Let the system provide:

- bar surfaces
- scroll-edge legibility
- sheet background behavior
- morphing between controls and presentations

### Use Glass Button Styles Before Custom Glass

Start with built-in button styles.

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
```

Use:

- `.glass` for secondary visible actions
- `.glassProminent` for the one action that should lead the screen

Do not place several prominent glass buttons side by side.

### Use Color Sparingly

Liquid Glass already picks up color from the content underneath. Extra tint should mean something.

Good uses:

- one primary action
- a meaningful status or call to action
- one emphasized custom control

Bad uses:

- every toolbar item
- several tinted glass controls in one cluster
- decorative color on already colorful content

### Use Clear Glass Only Over Rich Backgrounds

For custom effects, the regular variant is the default. Use clear glass only when the element sits over visually rich content and should preserve more of that detail.

This usually means:

- hero imagery
- maps
- photo or video surfaces

If the background is plain or text-heavy, regular glass is usually the better choice.

### Keep Toolbars Sparse and Grouped

On iPhone, toolbar items sit on a shared Liquid Glass surface. Too many actions make the bar feel noisy and crowded.

Prefer:

- one primary action
- one or two visible secondary actions
- an overflow `Menu` for the rest

Use toolbar grouping intentionally. Related actions should sit together. Unrelated actions should separate cleanly.

### Let Search Use the System Placement

Search now lives comfortably in the system chrome. On iPhone, toolbar search is designed to be within thumb reach and may minimize depending on context.

Use:

```swift
NavigationStack {
    ResultsView(query: query)
        .navigationTitle("Search")
        .searchable(text: $query, prompt: "Search")
        .searchToolbarBehavior(.minimized)
}
```

Do not build a fake search bar with overlays or safe-area hacks.

### Let the Tab Bar Float

On iPhone, the tab bar floats above content. If the app benefits from more content focus while scrolling, opt into minimization.

```swift
TabView {
    Tab("Home", systemImage: "house") {
        HomeTab()
    }

    Tab("Library", systemImage: "books.vertical") {
        LibraryTab()
    }
}
.tabBarMinimizeBehavior(.onScrollDown)
```

Use this when:

- content is scroll-heavy
- hiding the bar during downward scroll improves focus

If the app needs persistent lightweight controls above the tab bar, use a bottom accessory instead of building your own floating panel.

### Respect the New Sheet Behavior

On iPhone, partial-height sheets are inset by default and use a Liquid Glass background. As they expand, the presentation becomes more opaque and more anchored.

That means:

- check content near the corners
- check the content peeking around the inset edges
- remove custom sheet backgrounds unless the product truly needs one

### Use `glassEffect` Only for Truly Custom Elements

When a small custom control or overlay needs to feel like part of the system glass layer, use `glassEffect`.

```swift
Label("Live", systemImage: "dot.radiowaves.left.and.right")
    .font(.subheadline.weight(.semibold))
    .padding(.horizontal, 14)
    .padding(.vertical, 10)
    .glassEffect(in: .rect(cornerRadius: 14))
```

Strong defaults:

- keep the element compact
- choose a consistent shape
- apply the modifier after modifiers that affect the view’s appearance

If the element is interactive on iPhone, use the interactive variant.

```swift
Image(systemName: "play.fill")
    .frame(width: 56, height: 56)
    .glassEffect(.regular.interactive(), in: .circle)
```

### Group Multiple Custom Effects in a Container

If several custom glass elements appear together, wrap them in `GlassEffectContainer`.

```swift
GlassEffectContainer(spacing: 24) {
    HStack(spacing: 24) {
        Image(systemName: "heart.fill")
            .frame(width: 56, height: 56)
            .glassEffect(in: .circle)

        Image(systemName: "bookmark.fill")
            .frame(width: 56, height: 56)
            .glassEffect(in: .circle)
    }
}
```

Use a container because:

- rendering is better
- nearby effects blend and separate more naturally
- morphing transitions work correctly

Do not scatter many glass effects across unrelated containers.

### Morph Effects on Purpose

If custom glass elements appear, disappear, expand, or collapse, use `glassEffectID` inside a shared namespace.

```swift
@Namespace private var glassNamespace
@State private var expanded = false

GlassEffectContainer(spacing: 24) {
    HStack(spacing: 24) {
        Image(systemName: "sparkles")
            .frame(width: 56, height: 56)
            .glassEffect()
            .glassEffectID("sparkles", in: glassNamespace)

        if expanded {
            Image(systemName: "star.fill")
                .frame(width: 56, height: 56)
                .glassEffect()
                .glassEffectID("star", in: glassNamespace)
        }
    }
}
```

Use this for:

- expanding tool clusters
- badges that fan out from one control
- compact-to-expanded custom overlays

Do not use morphing as decoration. It should clarify state change.

### Watch Density and Performance

Liquid Glass loses value when everything floats. It also costs rendering work.

Keep the number of simultaneous custom effects low. Prefer one meaningful cluster over many isolated effects.

If performance starts to slip:

- reduce the number of custom glass elements onscreen
- group them in fewer containers
- avoid effects on large scrolling content cells

### Test With Real Content and Accessibility Settings

Glass changes with the content underneath and with system settings. Test with:

- light and dark content behind bars
- colorful imagery
- long localized text
- Increase Contrast
- Reduce Transparency
- Reduce Motion

If you built with standard components, most adaptation comes for free. Custom glass needs explicit checking.

## Design System Rules

### Do

- Let standard SwiftUI structure adopt Liquid Glass automatically
- Keep Liquid Glass in the navigation and control layer
- Use `.glass` and `.glassProminent` before custom `glassEffect`
- Keep toolbars sparse and grouped
- Remove old custom backgrounds that interfere with system chrome
- Use tint only to convey meaning
- Use custom glass sparingly
- Wrap related custom effects in `GlassEffectContainer`
- Test with rich and plain backgrounds
- Test accessibility settings that affect transparency and motion

### Don't

- Use Liquid Glass as a default background material for content
- Turn every card, row, or section into glass
- Stack many tinted glass controls together
- Add fake search bars, fake tab bars, or fake toolbar chrome
- Keep old scrims and blur capsules behind system bars
- Build custom glass buttons when a built-in button style fits
- Scatter many custom glass effects across the screen
- Use morphing only because it looks flashy

## Required Structure

Prefer this hierarchy:

```swift
TabView {
    Tab("Home", systemImage: "house") {
        NavigationStack {
            ScreenContent()
                .navigationTitle("Title")
                .toolbar {
                    ToolbarItem(placement: .primaryAction) {
                        Button("Add") {
                            addItem()
                        }
                        .buttonStyle(.glassProminent)
                    }
                }
        }
    }
}
```

If you add custom glass, keep it to a small, clearly functional element:

```swift
CompactControl()
    .padding(12)
    .glassEffect(in: .rect(cornerRadius: 14))
```

Minimum rules:

- bars and sheets use the system treatment
- content stays in the content layer
- one prominent action per scope
- custom glass is compact and purposeful
- related custom effects share a container

## Component API

### System Adoption

| API | Use Case |
| --- | --- |
| `TabView` | Floating iPhone tab bar |
| `NavigationStack` | System navigation chrome |
| `toolbar` | Liquid Glass toolbar surface |
| `sheet` | Inset iPhone sheet with system background |
| `searchable` | System search field placement |

### Controls

| API | Use Case |
| --- | --- |
| `.buttonStyle(.glass)` | Secondary visible action |
| `.buttonStyle(.glassProminent)` | Primary action |
| `ToolbarSpacer` | Separate related toolbar groups |
| `.tabBarMinimizeBehavior(...)` | Minimize floating tab bar on scroll |
| `.tabViewBottomAccessory(...)` | Lightweight persistent control above the tab bar |

### Custom Glass

| API | Use Case |
| --- | --- |
| `.glassEffect(...)` | Apply Liquid Glass to a custom view |
| `Glass.regular` | Default custom glass |
| `Glass.clear` | Clearer variant for rich backgrounds |
| `.interactive()` | Interactive custom glass on iPhone |
| `GlassEffectContainer` | Group multiple custom effects |
| `.glassEffectUnion(...)` | Merge multiple views into one effect shape |
| `.glassEffectID(...)` | Coordinate morphing across transitions |

### Supporting APIs

| API | Use Case |
| --- | --- |
| `.scrollEdgeEffectStyle(...)` | Tune legibility where content scrolls beneath controls |
| `.buttonBorderShape(...)` | Align button shape with the new design |
| `.safeAreaInset(...)` | Place bottom actions without building fake chrome |

## Common Patterns

### Toolbar Action

```swift
ToolbarItem(placement: .primaryAction) {
    Button {
        compose()
    } label: {
        Image(systemName: "square.and.pencil")
    }
    .buttonStyle(.glass)
    .accessibilityLabel("Compose")
}
```

### Primary Call to Action

```swift
Button("Continue") {
    continueFlow()
}
.buttonStyle(.glassProminent)
.buttonSizing(.flexible)
```

### Floating iPhone Tab Bar

```swift
TabView {
    Tab("Home", systemImage: "house") {
        HomeTab()
    }

    Tab("Search", systemImage: "magnifyingglass") {
        SearchTab()
    }
}
.tabBarMinimizeBehavior(.onScrollDown)
```

### Custom Glass Badge

```swift
Label("Pro", systemImage: "star.fill")
    .font(.subheadline.weight(.semibold))
    .padding(.horizontal, 12)
    .padding(.vertical, 8)
    .glassEffect(.regular.tint(.yellow), in: .capsule)
```

### Grouped Custom Effects

```swift
GlassEffectContainer(spacing: 20) {
    HStack(spacing: 20) {
        Image(systemName: "bolt.fill")
            .frame(width: 52, height: 52)
            .glassEffect(in: .circle)

        Image(systemName: "moon.fill")
            .frame(width: 52, height: 52)
            .glassEffect(in: .circle)
    }
}
```
