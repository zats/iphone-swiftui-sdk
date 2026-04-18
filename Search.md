# Search

A native iPhone search pattern for finding or filtering content with SwiftUI. On iOS 26+, start with `searchable`, let the system own the search field and its toolbar behavior, and use a dedicated Search tab only when search is a top-level destination.

## When to Use

- **`searchable` on a screen**: Filter or find content in the current list, grid, or collection
- **Search tab with `role: .search`**: App-wide discovery, recent searches, suggestions, or a dedicated search destination
- **`.searchToolbarBehavior(.minimized)`**: Search matters, but it should not sit open all the time
- **`.searchScopes`**: Large result sets with a few clear categories
- **`.searchSuggestions`**: Recent searches, common queries, completions, or token-like shortcuts

If search only affects the current screen, keep it in that screen. Do not create a Search tab for local filtering.

## Quick Start

The default iPhone list search:

```swift
import SwiftUI

struct LibrarySearchView: View {
    @State private var query = ""

    private let items = [
        "Arrival", "Blade Runner 2049", "Dune", "Ex Machina", "Interstellar"
    ]

    private var results: [String] {
        guard !query.isEmpty else { return items }
        return items.filter { $0.localizedCaseInsensitiveContains(query) }
    }

    var body: some View {
        NavigationStack {
            List(results, id: \.self) { item in
                Text(item)
            }
            .navigationTitle("Library")
            .toolbarRole(.browser)
            .searchable(text: $query, prompt: "Titles")
            .searchToolbarBehavior(.minimized)
        }
    }
}
```

What this gets right:

- Search stays attached to the screen it filters
- The prompt describes the content, not the action
- Minimized behavior keeps search easy to reach without taking over the bar

## Usage Patterns

### Local Search

Use `searchable` when search narrows the content already on screen. This is the default pattern for lists, grids, and browse views.

```swift
struct NotesView: View {
    @State private var query = ""
    private let notes = [
        "Design review", "Flight details", "Invoice", "Launch checklist"
    ]

    private var filteredNotes: [String] {
        guard !query.isEmpty else { return notes }
        return notes.filter { $0.localizedCaseInsensitiveContains(query) }
    }

    var body: some View {
        NavigationStack {
            List(filteredNotes, id: \.self) { note in
                Text(note)
            }
            .navigationTitle("Notes")
            .searchable(text: $query, prompt: "Titles and content")
            .searchToolbarBehavior(.minimized)
        }
    }
}
```

Strong default:

- Update local results as the person types
- Keep search in the same screen instead of pushing to a separate results screen
- Use minimized toolbar behavior when browse content matters more than a persistent field

### Search Tab

Use a Search tab when search is a destination with its own empty state, recents, suggestions, or app-wide results. Mark exactly one tab with `role: .search`.

```swift
import SwiftUI

enum AppTab: Hashable {
    case home
    case search
    case profile
}

struct RootView: View {
    @State private var selection: AppTab = .home
    @State private var query = ""

    var body: some View {
        TabView(selection: $selection) {
            Tab("Home", systemImage: "house", value: .home) {
                NavigationStack {
                    Text("Home")
                        .navigationTitle("Home")
                }
            }

            Tab("Search", systemImage: "magnifyingglass", value: .search, role: .search) {
                NavigationStack {
                    SearchScreen(query: query)
                        .navigationTitle("Search")
                }
            }

            Tab("Profile", systemImage: "person.crop.circle", value: .profile) {
                NavigationStack {
                    Text("Profile")
                        .navigationTitle("Profile")
                }
            }
        }
        .searchable(text: $query, prompt: "People, posts, places")
    }
}

private struct SearchScreen: View {
    let query: String

    var body: some View {
        Group {
            if query.isEmpty {
                ContentUnavailableView(
                    "Search",
                    systemImage: "magnifyingglass",
                    description: Text("Recent searches and suggestions belong here.")
                )
            } else {
                List {
                    Text("Result for \(query)")
                }
            }
        }
    }
}
```

Use this pattern when:

- Search spans multiple top-level sections
- Search has meaningful zero-query content
- People should be able to return to search as a place, not just a control

If you put `.searchable(...)` on `TabView`, mark one tab with `role: .search`. Otherwise SwiftUI can apply search across tabs and reset search state as selection changes.

Do not use this pattern for a single screen’s filter bar.

### Search Scopes

Use scopes only when the categories are obvious and materially reduce work. Default to the broadest scope.

```swift
import SwiftUI

struct MailSearchView: View {
    enum Scope: String, CaseIterable {
        case all = "All"
        case inbox = "Inbox"
        case sent = "Sent"
    }

    @State private var query = ""
    @State private var scope: Scope = .all

    var body: some View {
        NavigationStack {
            MessageResultsList(query: query, scope: scope)
                .navigationTitle("Mail")
                .searchable(text: $query, prompt: "People, subject, text")
                .searchScopes($scope) {
                    ForEach(Scope.allCases, id: \.self) { scope in
                        Text(scope.rawValue).tag(scope)
                    }
                }
        }
    }
}
```

Use scopes for:

- `All` vs a current container
- A few mutually exclusive content types
- Clear search domains people already understand

Do not use scopes as a general-purpose filter bar or sort control.

### Search Suggestions

Show suggestions when they help people start faster. Good suggestions are recent, common, or context-aware.

```swift
import SwiftUI

struct RecipeSearchView: View {
    @State private var query = ""
    private let suggestions = ["Pasta", "Salad", "Soup", "Dessert"]

    var body: some View {
        NavigationStack {
            RecipeResults(query: query)
                .navigationTitle("Recipes")
                .searchable(text: $query, prompt: "Recipes or ingredients")
                .searchSuggestions {
                    ForEach(suggestions, id: \.self) { suggestion in
                        Text(suggestion)
                            .searchCompletion(suggestion)
                    }
                }
        }
    }
}
```

Rule of thumb:

- Suggestions should reduce typing
- Keep the list short
- If a suggestion should fill the field when tapped, give it `searchCompletion`

### Remote Search

For remote or expensive search, do not hammer the network on every keystroke. Submit or debounce.

```swift
import SwiftUI

struct StoreSearchView: View {
    @State private var query = ""

    var body: some View {
        NavigationStack {
            SearchResultsView(query: query)
                .navigationTitle("Store")
                .searchable(text: $query, prompt: "Apps and games")
                .onSubmit(of: .search) {
                    runSearch(query)
                }
        }
    }
}
```

## Design System Rules

### Do

- Start with `searchable(text:prompt:)`
- Attach `searchable` to the container that owns the results
- Prefer the system toolbar search placement on iPhone
- Prefer bottom-toolbar search behavior when search is important and should stay reachable
- Use `.searchToolbarBehavior(.minimized)` when search should collapse to a compact entry point
- Use a Search tab only for app-wide or destination-level search
- Mark the dedicated search tab with `role: .search`
- Use a prompt that names the content being searched
- Update local results immediately when the operation is cheap
- Show recent searches or suggestions when zero-query search is useful
- Default scopes to the broadest useful option
- Keep search results stable and easy to scan

### Don't

- Build a fake search bar with `safeAreaInset`, overlays, or custom toolbar chrome
- Add both a local search field and a Search tab for the same job
- Create a Search tab just to filter one list
- Use placeholder text like `Search`
- Add many scopes for minor filter differences
- Treat scopes as sorting
- Fire expensive network searches on every keystroke without throttling or submit
- Hide essential toolbar actions during search unless that focus shift clearly improves the flow

## Required Structure

Prefer one of these three patterns:

### Screen-Level Search

```swift
NavigationStack {
    ResultsList(query: query)
        .navigationTitle("Library")
        .searchable(text: $query, prompt: "Titles")
        .searchToolbarBehavior(.minimized)
}
```

### Dedicated Search Tab

```swift
Tab("Search", systemImage: "magnifyingglass", value: .search, role: .search) {
    NavigationStack {
        SearchScreen(query: query)
    }
}
```

### Search with Scopes

```swift
ResultsList(query: query, scope: scope)
    .searchable(text: $query, prompt: "Mail")
    .searchScopes($scope) {
        Text("All").tag(Scope.all)
        Text("Inbox").tag(Scope.inbox)
    }
```

## Component API

### Core API

| API | Use Case |
| --- | --- |
| `.searchable(text:prompt:)` | Default local search |
| `.searchable(text:placement:prompt:)` | Ask for a specific placement when structure alone is not enough |
| `.searchToolbarBehavior(.minimized)` | Compact inactive search control in the toolbar |
| `.searchPresentationToolbarBehavior(.avoidHidingContent)` | Keep key toolbar content visible while search is active |

### Refinements

| API | Use Case |
| --- | --- |
| `.searchSuggestions { ... }` | Recent queries, completions, common searches |
| `.searchCompletion(_:)` | Fill the field when a suggestion is chosen |
| `.searchScopes($scope) { ... }` | Narrow search with a small set of clear categories |
| `.onSubmit(of: .search)` | Start expensive or remote searches on submit |
| `@Environment(\\.isSearching)` | React to active search presentation |
| `@Environment(\\.dismissSearch)` | End search from another interaction |
| `.searchable(text:isPresented:prompt:)` | Programmatically present search when the flow needs it |

### Search Tab API

| API | Use Case |
| --- | --- |
| `Tab(..., role: .search)` | Marks a dedicated search tab |
| `.searchable(...)` on `TabView` | Shares app-wide search UI across tabs |
| `.tabViewSearchActivation(...)` | Controls whether selecting the search tab activates search |

## Common Patterns

### What Good iPhone Search Feels Like

- Easy to reach with one hand
- Immediate for local results
- Quiet when inactive
- Specific about what it searches
- Helpful before typing, not empty and dead
- Narrowed by scopes only when that saves real effort

### Choosing the Right Pattern

- Current screen only: `searchable`
- App-wide destination: Search tab with `role: .search`
- Useful but secondary: `.searchToolbarBehavior(.minimized)`
- Large result space with a few obvious buckets: `.searchScopes`
- High-frequency or repeated queries: `.searchSuggestions`
