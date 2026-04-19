# App Shell

A native iPhone app shell for iOS 26+. Start with system containers, let navigation and bars inherit the current Liquid Glass design, and keep custom chrome out of the way. An award-quality shell feels calm, obvious, and depth-aware: content leads, bars float, navigation is predictable, and modal work stays scoped.

## When to Use

- **`TabView`**: Apps with 2-5 top-level sections that people switch between regularly
- **Single `NavigationStack`**: Apps with one primary flow, or utility screens without top-level sections
- **One `NavigationStack` per tab**: Tab-based apps where each tab needs its own history
- **Sheets**: Create, pick, filter, or edit flows that are closely related to the current screen
- **`searchable`**: Filtering or finding content inside the current tab or stack

Use standard containers before building custom shells.

## Quick Start

The default iPhone app shell:

```swift
import SwiftUI

@main
struct SampleApp: App {
    var body: some Scene {
        WindowGroup {
            RootView()
        }
    }
}

struct RootView: View {
    @State private var selectedTab: AppTab = .home

    var body: some View {
        TabView(selection: $selectedTab) {
            Tab("Home", systemImage: "house", value: .home) {
                HomeTab()
            }

            Tab("Library", systemImage: "books.vertical", value: .library) {
                LibraryTab()
            }

            Tab("Profile", systemImage: "person.crop.circle", value: .profile) {
                ProfileTab()
            }
        }
    }
}

enum AppTab: Hashable {
    case home
    case library
    case profile
}

struct HomeTab: View {
    var body: some View {
        NavigationStack {
            HomeScreen()
                .navigationTitle("Home")
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
}
```

## Usage Patterns

### Tab App Shell

Use `TabView` when each tab is a destination, not an action. Keep each tab’s navigation state separate.

```swift
enum AppTab: Hashable {
    case inbox
    case projects
    case search
}

struct RootView: View {
    @State private var selectedTab: AppTab = .inbox
    @State private var query = ""

    var body: some View {
        TabView(selection: $selectedTab) {
            Tab("Inbox", systemImage: "tray", value: .inbox) {
                NavigationStack {
                    InboxScreen()
                }
            }

            Tab("Projects", systemImage: "folder", value: .projects) {
                NavigationStack {
                    ProjectsScreen()
                }
            }

            Tab("Search", systemImage: "magnifyingglass", value: .search, role: .search) {
                NavigationStack {
                    SearchScreen(query: query)
                }
            }
        }
        .searchable(text: $query, prompt: "Search")
    }
}
```

Use a Search tab only when search is a top-level destination. In that pattern, mark exactly one tab with `role: .search` and put `.searchable(...)` on the `TabView`. If search only filters the current tab’s content, keep it inside that tab with `searchable`.

### Single-Flow App Shell

Use one stack when the app has one primary hierarchy.

```swift
struct RootView: View {
    var body: some View {
        NavigationStack {
            NotesScreen()
                .navigationTitle("Notes")
        }
    }
}
```

### Search in the Right Place

Attach `searchable` to the view inside the active `NavigationStack`. Let the system place the search field in the bar.

```swift
struct LibraryTab: View {
    @State private var query = ""

    var body: some View {
        NavigationStack {
            LibraryScreen(query: query)
                .navigationTitle("Library")
                .searchable(text: $query, prompt: "Search library")
        }
    }
}
```

Default rule:

- Search that filters the current screen: `searchable`
- Search that is a destination with its own empty state, history, or suggestions: Search tab
- If search is a tab-level destination, put `.searchable(...)` on the `TabView` and mark one tab with `role: .search`

### Toolbar Structure

Use semantic placements. Keep the top bar light.

```swift
struct ProjectScreen: View {
    @State private var showingAddSheet = false

    var body: some View {
        List(projects) { project in
            NavigationLink(project.name, value: project)
        }
        .navigationTitle("Projects")
        .toolbar {
            ToolbarItem(placement: .topBarLeading) {
                Button("Edit") {
                    // Edit mode
                }
            }

            ToolbarItem(placement: .primaryAction) {
                Button {
                    showingAddSheet = true
                } label: {
                    Image(systemName: "plus")
                }
                .accessibilityLabel("New Project")
            }
        }
        .sheet(isPresented: $showingAddSheet) {
            NewProjectSheet()
        }
    }
}
```

Default placement:

- Title: navigation bar title
- Most-important action: `.primaryAction`
- Secondary actions: `.topBarTrailing`
- Contextual edit/cancel/back-supporting actions: `.topBarLeading`

### Sheets for Scoped Work

Use sheets for related tasks, not for primary navigation.

```swift
struct InboxScreen: View {
    @State private var showingComposer = false
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        List(messages) { message in
            Text(message.subject)
        }
        .navigationTitle("Inbox")
        .toolbar {
            ToolbarItem(placement: .primaryAction) {
                Button("Compose") {
                    showingComposer = true
                }
            }
        }
        .sheet(isPresented: $showingComposer) {
            NavigationStack {
                ComposeScreen()
                    .navigationTitle("New Message")
                    .navigationBarTitleDisplayMode(.inline)
                    .toolbar {
                        ToolbarItem(placement: .cancellationAction) {
                            Button("Cancel") {
                                dismiss()
                            }
                        }

                        ToolbarItem(placement: .confirmationAction) {
                            Button("Send") {
                                dismiss()
                            }
                        }
                    }
            }
        }
    }
}
```

Good sheet jobs:

- Create something
- Pick something
- Apply filters or sorting
- Edit metadata

Bad sheet jobs:

- Replacing tab navigation
- Hiding your main app structure
- Long multi-level browsing flows that should be in a stack

### Backgrounds and Safe Areas

Let bars own the glass layer. Keep content backgrounds simple.

```swift
struct DetailScreen: View {
    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 20) {
                // Content
            }
            .padding()
        }
        .background(Color(.systemBackground))
        .navigationTitle("Detail")
    }
}
```

Use `ignoresSafeArea()` only for intentional full-bleed media or brand moments.

For bottom actions that should stay above the home indicator, prefer `safeAreaInset(edge: .bottom)` over a manual overlay.

```swift
struct CheckoutScreen: View {
    var body: some View {
        ScrollView {
            CheckoutContent()
                .padding()
        }
        .safeAreaInset(edge: .bottom) {
            Button("Place Order") {
                placeOrder()
            }
            .buttonStyle(.glassProminent)
            .buttonSizing(.flexible)
            .padding(.horizontal)
            .padding(.top, 8)
            .padding(.bottom, 12)
        }
    }
}
```

Start with the button alone. Add a material-backed container only when the content behind it is busy enough to hurt legibility.

```swift
struct PhotoScreen: View {
    var body: some View {
        ZStack(alignment: .bottom) {
            Image("hero")
                .resizable()
                .scaledToFill()
                .ignoresSafeArea()

            LinearGradient(
                colors: [.clear, .black.opacity(0.45)],
                startPoint: .center,
                endPoint: .bottom
            )
            .ignoresSafeArea()
        }
    }
}
```

## Design System Rules

### Do

- Use **`TabView` for navigation**, not actions
- Use **one `NavigationStack` per tab**
- Keep **tab labels short** and always provide a symbol
- Let **tab bars, navigation bars, toolbars, and sheets** use the system’s Liquid Glass treatment
- Put **screen search** in `searchable`
- Put **primary actions** in `.primaryAction`
- Use **sheets for focused, temporary work**
- Keep **content scrolling under system bars**
- Use **system backgrounds and materials** before custom ones
- Make the shell feel **stable across tabs, pushes, sheets, and search**

### Don't

- Nest a single global `NavigationStack` around the entire `TabView`
- Put **more than 5 top-level tabs** on iPhone
- Turn tabs into action buttons
- Rebuild top or bottom bars with custom overlays
- Put **persistent search fields** in page content when `searchable` fits
- Use sheets as a substitute for normal drill-in navigation
- Fill content cards and panels with Liquid Glass just because bars use it
- Ignore safe areas on ordinary text and list screens
- Hide the tab bar unpredictably during normal navigation

## Required Structure

Start here:

```swift
struct RootView: View {
    @State private var query = ""

    var body: some View {
        TabView {
            Tab("Section", systemImage: "house") {
                NavigationStack {
                    Screen()
                        .navigationTitle("Section")
                        .searchable(text: $query)
                        .toolbar {
                            ToolbarItem(placement: .primaryAction) {
                                Button("Add") {}
                            }
                        }
                }
            }
        }
    }
}
```

If the app does not need tabs:

```swift
NavigationStack {
    Screen()
}
```

## Component API

### Pick a Top-Level Container

| Container | Use Case |
| --- | --- |
| `TabView` | 2-5 top-level destinations on iPhone |
| `NavigationStack` | One primary hierarchy |
| `sheet(...)` | Focused modal task related to current context |

### Place Search Intentionally

| Pattern | Use Case |
| --- | --- |
| `.searchable(text:prompt:)` | Search/filter within the current screen or tab |
| `Tab(..., role: .search)` | Search is its own destination |

### Place Toolbar Items Semantically

| Placement | Use Case |
| --- | --- |
| `.primaryAction` | Most important action for the screen |
| `.topBarTrailing` | Secondary actions |
| `.topBarLeading` | Edit, close, or supporting context actions |
| `.confirmationAction` | Commit inside sheets |
| `.cancellationAction` | Dismiss inside sheets |

### Background and Bar Controls

- `toolbarBackground(_:for:)` is a last resort when the system default still fails a real product requirement
- `toolbar(_:for:)` controls visibility of system-managed bars
- Prefer `Color(.systemBackground)` or standard grouped backgrounds for content
- Do not clear or fake bars just to chase a full-bleed look
- Reserve custom materials for rare branded moments, not the default shell

## Common Patterns

### Tab + Search + Compose

```swift
struct InboxTab: View {
    @State private var query = ""
    @State private var showingCompose = false

    var body: some View {
        NavigationStack {
            InboxList(query: query)
                .navigationTitle("Inbox")
                .searchable(text: $query, prompt: "Search inbox")
                .toolbar {
                    ToolbarItem(placement: .primaryAction) {
                        Button {
                            showingCompose = true
                        } label: {
                            Image(systemName: "square.and.pencil")
                        }
                        .accessibilityLabel("Compose")
                    }
                }
        }
        .sheet(isPresented: $showingCompose) {
            ComposeSheet()
        }
    }
}
```

### List Detail Shell

```swift
struct LibraryTab: View {
    var body: some View {
        NavigationStack {
            List(books) { book in
                NavigationLink(book.title, value: book)
            }
            .navigationTitle("Library")
            .navigationDestination(for: Book.self) { book in
                BookDetailScreen(book: book)
            }
        }
    }
}
```

### Full-Bleed Hero with Standard Chrome

```swift
struct StoryScreen: View {
    var body: some View {
        ScrollView {
            HeroHeader()
            StoryBody()
        }
        .ignoresSafeArea(edges: .top)
        .navigationBarTitleDisplayMode(.inline)
    }
}
```
