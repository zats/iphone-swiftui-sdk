# Human Interface Guidelines

A top-level design baseline for iPhone apps in SwiftUI on iOS 26+. Start with system structure, keep hierarchy obvious, let content lead, and use custom UI only when the system patterns are genuinely not enough.

This is the umbrella guide. Use it to decide the shape of a screen before reaching for a component-level doc.

## When to Use

- **Every screen**
- **Every new feature**
- **App structure and navigation**
- **Action hierarchy**
- **Search, sheets, and modal decisions**
- **Visual hierarchy and chrome**
- **Accessibility, localization, and adaptation**

Use something else when:

- You already know the pattern and need detailed component guidance like `Button`, `Sheet`, `Search`, or `Tab Bar`
- You are designing for iPad, Mac, watchOS, tvOS, or visionOS

## Quick Start

Start with a standard shell, a small number of top-level sections, one clear primary action, and system search.

```swift
import SwiftUI

enum AppTab: Hashable {
    case home
    case library
    case search
}

struct RootView: View {
    @State private var selectedTab: AppTab = .home
    @State private var query = ""

    var body: some View {
        TabView(selection: $selectedTab) {
            Tab("Home", systemImage: "house", value: .home) {
                NavigationStack {
                    HomeScreen()
                        .navigationTitle("Home")
                }
            }

            Tab("Library", systemImage: "books.vertical", value: .library) {
                NavigationStack {
                    LibraryScreen()
                        .navigationTitle("Library")
                        .toolbar {
                            ToolbarItem(placement: .primaryAction) {
                                Button {
                                    // Create or add
                                } label: {
                                    Image(systemName: "plus")
                                }
                                .accessibilityLabel("Add Item")
                            }
                        }
                }
            }

            Tab("Search", systemImage: "magnifyingglass", value: .search, role: .search) {
                NavigationStack {
                    SearchScreen(query: query)
                        .navigationTitle("Search")
                }
            }
        }
        .searchable(text: $query, prompt: "Search")
    }
}
```

What this gets right:

- top-level sections use `TabView`
- each section keeps its own `NavigationStack`
- search uses the system pattern
- the interface has one visible primary action
- bars and controls pick up system appearance automatically

## Usage Patterns

### Start With the System Shape

On iPhone, a great default is:

- `TabView` for 2-5 top-level sections
- one `NavigationStack` per tab
- `List` or `Form` for structured content
- `toolbar` for actions
- `sheet` for short related tasks
- `fullScreenCover` for immersive or prolonged tasks

Do not invent a custom shell before trying the platform shell.

### Make the Hierarchy Obvious

People should always know:

- where they are
- what they can do here
- how to go back
- what the primary next step is

Strong defaults:

- root screens use short noun titles
- pushed screens use inline titles
- tabs represent peer sections
- pushed screens represent depth inside a section
- sheets represent temporary work, not navigation

### Keep Navigation Stable

Navigation on iPhone should feel predictable and reversible.

- preserve state inside each tab
- keep the tab bar visible across top-level sections
- prefer the system back button and edge-swipe gesture
- avoid hiding core navigation during ordinary browsing
- do not turn tabs into actions like compose or filter

If a screen changes where people are in the app, push it. If it asks them to do a temporary task, present it.

### Show One Primary Action

Most screens need one action that leads.

Good fits:

- create
- add
- send
- save
- continue

Keep secondary actions nearby but quieter. If there are many actions, put the rest in `Menu` or behind edit mode instead of turning the screen into a toolbar graveyard.

### Let Content Lead

The content layer should be easy to scan. The functional layer should stay out of the way.

- let bars, sheets, and controls own the glass layer
- keep content backgrounds simple
- avoid decorative chrome around cards, rows, and sections
- use spacing and typography for hierarchy before adding containers
- keep copy short and direct

On iPhone, too much chrome costs too much space.

### Put Controls Where They Are Reachable

People often use iPhone one-handed or on the move. Reach matters.

- keep common actions in the middle or bottom area when possible
- use swipe actions for row-level operations
- avoid tiny tap targets at the far corners when the same action could live lower
- make frequently used actions easy to repeat

Do not fight the ergonomics of the device.

### Use Search in the Right Place

Use search based on scope.

- current screen filter: `searchable`
- app-wide destination with suggestions, history, or discovery: search tab
- inline search above content: only when it clearly belongs to that content

Search should help people narrow content quickly.

- use a specific prompt
- show relevant results first
- consider suggestions and scope controls when the dataset is broad

### Use Sheets for Scoped Work

Sheets are for closely related work that should dismiss back to the current screen.

Good sheet jobs:

- create something
- edit metadata
- pick a value
- confirm or refine a choice
- apply filters

Strong defaults on iPhone:

- `Cancel` on the leading side
- `Done` or the confirming action on the trailing side
- support swipe to dismiss
- support medium height only when progressive disclosure helps

Do not stack sheets from the main interface or use a sheet as the main navigation model.

### Prefer Standard Components

Use built-in controls and containers first.

- `Button`
- `Toggle`
- `Picker`
- `Slider`
- `Menu`
- `ConfirmationDialog`
- `List`
- `Form`
- `TabView`
- `NavigationStack`

This buys you:

- current platform visuals
- current platform motion
- accessibility behavior
- localization support
- RTL support
- less code to maintain

### Adapt Automatically

iPhone UI should adapt cleanly to:

- portrait and landscape
- Dynamic Type
- Dark Mode
- increased contrast
- reduced transparency
- reduced motion
- right-to-left languages
- different locales and measurement systems

Use leading and trailing alignment, not left and right. Let SwiftUI mirror the interface. Keep paragraphs aligned to their language when needed.

### Use Color and Motion With Restraint

Color should communicate emphasis, status, or meaning. Motion should clarify state change or spatial relationship.

- one tinted primary action is often enough
- avoid multiple competing accent colors in one screen
- keep motion attached to interface meaning
- remove decorative effects that do not help understanding

If the content is already visually rich, the chrome should get quieter.

## Default Rules

- Start with system containers and system bars.
- Keep 2-5 tabs at most.
- Keep one `NavigationStack` per tab.
- Keep one clear primary action per screen.
- Use `searchable`, `sheet`, `Menu`, `swipeActions`, and `EditButton` before custom patterns.
- Use `List` and `Form` before custom scroll layouts for structured data entry or browsing.
- Keep top-level titles short and literal.
- Use SF Symbols for common actions.
- Keep custom glass effects rare and functional.
- Test with Dynamic Type, VoiceOver, RTL, reduced motion, and reduced transparency.

## Avoid

- custom tab bars and fake navigation bars
- actions inside the tab bar
- multiple prominent actions competing for attention
- overusing Liquid Glass in the content layer
- deep modal flows that should be push navigation
- decorative backgrounds behind every section and row
- hard-coded layout values that fight system sizing
- custom search bars built from overlays

## See Also

- [App Shell](./App%20Shell.md)
- [Navigation](./Navigation.md)
- [Tab Bar](./Tab%20Bar.md)
- [Toolbar](./Toolbar.md)
- [Search](./Search.md)
- [Sheet](./Sheet.md)
- [Color & Materials](./Color%20%26%20Materials.md)
- [Liquid Glass](./Liquid%20Glass.md)
- [Localization](./Localization.md)
- [Accessibility Patterns](./Accessibility%20Patterns.md)
