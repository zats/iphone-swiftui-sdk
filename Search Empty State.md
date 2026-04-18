# Search Empty State

A native iPhone search empty state for iOS 26+. Start with the system search field, use `ContentUnavailableView` for no-results states, and keep the pre-search state lightweight: recent searches, a few suggestions, and one clear next step.

## When to Use

- **No query**: The search field is active, but the person has not typed anything yet
- **No results**: A real query returned zero matches
- **Suggestions**: You can help people start with likely queries, categories, or completions
- **Recent searches**: Repeating prior searches is faster than typing again
- **Corrective guidance**: Filters, spelling, or scope are the likely cause of zero results
- **Fallback action**: There is one obvious recovery path like clear, browse all, or create

Use a different empty state for loading and network failure. Search empty states should assume the interface is working.

## Quick Start

The default pattern:

```swift
import SwiftUI

struct LibrarySearchScreen: View {
    @State private var query = ""
    @State private var recentSearches = ["Design Systems", "Buttons", "Navigation"]

    private let allItems = [
        "App Shell",
        "Button",
        "Empty State",
        "List",
        "Search"
    ]

    private var results: [String] {
        guard !query.isEmpty else { return [] }
        return allItems.filter { $0.localizedStandardContains(query) }
    }

    var body: some View {
        NavigationStack {
            Group {
                if query.isEmpty {
                    SearchStartState(
                        recentSearches: recentSearches,
                        suggestedSearches: ["Buttons", "Lists", "Navigation"],
                        onPick: applySearch
                    )
                } else if results.isEmpty {
                    ContentUnavailableView.search(text: query)
                } else {
                    List(results, id: \.self) { item in
                        Text(item)
                    }
                }
            }
            .navigationTitle("Library")
            .searchable(text: $query, prompt: "Search components") {
                ForEach(["Buttons", "Lists", "Navigation"], id: \.self) { suggestion in
                    Text(suggestion)
                        .searchCompletion(suggestion)
                }
            }
        }
    }

    private func applySearch(_ value: String) {
        query = value
    }
}

private struct SearchStartState: View {
    let recentSearches: [String]
    let suggestedSearches: [String]
    let onPick: (String) -> Void

    var body: some View {
        List {
            if !recentSearches.isEmpty {
                Section("Recent") {
                    ForEach(recentSearches, id: \.self) { term in
                        Button {
                            onPick(term)
                        } label: {
                            Label(term, systemImage: "clock.arrow.circlepath")
                        }
                        .buttonStyle(.plain)
                    }
                }
            }

            Section("Suggested") {
                ForEach(suggestedSearches, id: \.self) { term in
                    Button {
                        onPick(term)
                    } label: {
                        Label(term, systemImage: "magnifyingglass")
                    }
                    .buttonStyle(.plain)
                }
            }
        }
    }
}
```

## Usage Patterns

### No Query

Before a person types, do not show a generic empty message. Show useful starting points instead.

```swift
List {
    if !recentSearches.isEmpty {
        Section("Recent") {
            ForEach(recentSearches, id: \.self) { term in
                Button(term) {
                    query = term
                }
                .buttonStyle(.plain)
            }
        }
    }

    Section("Suggested") {
        ForEach(suggestedSearches, id: \.self) { term in
            Button(term) {
                query = term
            }
            .buttonStyle(.plain)
        }
    }
}
```

Default:

- Show up to 5 recent searches
- Show up to 6 suggestions
- Use short noun phrases, not marketing copy

### No Results

Use the system search empty state first.

```swift
ContentUnavailableView.search(text: query)
```

If people can recover immediately, add one supporting action.

```swift
ContentUnavailableView {
    Label("No Results", systemImage: "magnifyingglass")
} description: {
    Text("Try a broader term or clear filters.")
} actions: {
    Button("Clear Filters") {
        selectedFilter = nil
    }
    .buttonStyle(.glass)
}
```

Default:

- One title
- One short line of guidance
- Zero or one secondary action

### Suggestion States

Suggestions should reduce typing or help people pick the right vocabulary.

```swift
.searchable(text: $query, prompt: "Search library") {
    ForEach(suggestedSearches, id: \.self) { suggestion in
        Label(suggestion, systemImage: "magnifyingglass")
            .searchCompletion(suggestion)
    }
}
```

Use suggestions for:

- Popular queries
- Recent searches
- Known categories or tags
- Correct spellings or canonical names

### Corrective Guidance

If zero results are probably caused by filters, scope, or spelling, say that directly.

```swift
ContentUnavailableView {
    Label("No Matches", systemImage: "line.3.horizontal.decrease.circle")
} description: {
    Text("Nothing matches “\(query)” in Favorites. Try All Items.")
} actions: {
    Button("Show All Items") {
        scope = .all
    }
    .buttonStyle(.glass)
}
```

Good guidance names the likely problem and one fix. Do not stack multiple troubleshooting paragraphs.

### Fallback Actions

Use the fallback that best matches intent:

- **Clear search** when the query is probably wrong
- **Clear filters** when the filter state is the problem
- **Browse all** when search is optional
- **Create** when zero results often means the item does not exist yet

If you offer a create action, keep the search recovery action visually lighter.

## Design System Rules

### Do

- Prefer `ContentUnavailableView.search(text:)` for the default no-results treatment
- Keep no-query UI inside the main content area, usually as a `List`
- Use section headers like `Recent` and `Suggested`
- Make recent and suggested items directly tappable
- Write copy that explains the next move in one sentence
- Show one fallback action before adding two
- Keep suggestion labels short enough to scan in one line
- Remove stale or low-value recent searches

### Don't

- Show a blank screen when the query is empty
- Use a no-results empty state before someone has searched
- Add illustrations, large hero graphics, or decorative chrome
- Explain every possible reason a search failed
- Show more than one prominent action in the empty state
- Keep old recent searches forever
- Turn suggestions into a dense menu of every category in the app

## Required Structure

Every search screen should resolve to one of these states:

1. **Empty query**: recent searches, suggestions, or both
2. **Active results**: matching content
3. **No results**: `ContentUnavailableView` with optional corrective action

Default structure:

```swift
Group {
    if query.isEmpty {
        SearchStartState(...)
    } else if results.isEmpty {
        ContentUnavailableView.search(text: query)
    } else {
        SearchResultsList(results: results)
    }
}
```

## Component API

If you wrap this into a reusable component, keep the API small and state-driven.

```swift
struct SearchEmptyStateView: View {
    let query: String
    let recentSearches: [String]
    let suggestedSearches: [String]
    let message: Text?
    let actionTitle: LocalizedStringKey?
    let onSelectSearch: (String) -> Void
    let onAction: (() -> Void)?

    var body: some View { ... }
}
```

Recommended behavior:

- `query.isEmpty`: render recent and suggested searches
- `!query.isEmpty`: render `ContentUnavailableView.search(text: query)`
- `message`: only for corrective guidance beyond the system default
- `actionTitle` + `onAction`: one supporting recovery action

## Common Patterns

### Recent Searches With Clear-All

Keep clear-all secondary.

```swift
Section {
    ForEach(recentSearches, id: \.self) { term in
        Button(term) {
            query = term
        }
        .buttonStyle(.plain)
    }
} header: {
    HStack {
        Text("Recent")
        Spacer()
        Button("Clear") {
            recentSearches.removeAll()
        }
        .buttonStyle(.plain)
    }
}
```

### Search With Create Fallback

Use this when search often ends in creating something new.

```swift
ContentUnavailableView {
    Label("No Projects", systemImage: "folder")
} description: {
    Text("No project matches “\(query)”.")
} actions: {
    Button("New Project") {
        showingCreateSheet = true
    }
    .buttonStyle(.glassProminent)

    Button("Clear Search") {
        query = ""
    }
    .buttonStyle(.glass)
}
```

Use this only when creating from empty search is a common, valid next step.
