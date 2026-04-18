# Search Results

A native iPhone search results pattern for iOS 26+. Start with a plain `List`, update results as query state changes, keep ranking legible through order and sectioning, and push into detail with value-based navigation.

## When to Use

- **In-stack search**: Search filters or finds content inside the current `NavigationStack`
- **Search destination**: Search has its own screen, empty state, recent history, or grouped result types
- **Live lookup**: Results should refresh as people type
- **Recent searches**: The empty-query state benefits from history before any live results exist

Use a Search tab only when search is a top-level destination. Otherwise attach `searchable` to the current screen and render results in-place.

## Quick Start

The default iPhone search results screen:

```swift
import SwiftUI

struct SearchResultsScreen: View {
    struct Result: Identifiable, Hashable {
        let id: UUID
        let title: String
        let subtitle: String?
        let snippet: String?
        let systemImage: String
        let kind: Kind

        enum Kind: String, CaseIterable {
            case top = "Top Results"
            case projects = "Projects"
            case people = "People"
        }
    }

    enum Route: Hashable {
        case result(Result)
    }

    @State private var query = ""
    @State private var recentSearches = ["Atlas", "Design System", "Quarterly Plan"]
    @State private var isLoading = false
    @State private var results: [Result] = []

    var sectionedResults: [(title: String, items: [Result])] {
        Dictionary(grouping: results, by: \.kind)
            .sorted { $0.key.sortOrder < $1.key.sortOrder }
            .map { (title: $0.key.rawValue, items: $0.value) }
    }

    var body: some View {
        NavigationStack {
            List {
                if query.isEmpty {
                    Section("Recent") {
                        ForEach(recentSearches, id: \.self) { recent in
                            Button {
                                query = recent
                            } label: {
                                Label(recent, systemImage: "clock.arrow.circlepath")
                            }
                            .buttonStyle(.plain)
                        }
                    }
                } else if results.isEmpty && !isLoading {
                    ContentUnavailableView.search(text: query)
                        .listRowBackground(Color.clear)
                } else {
                    ForEach(sectionedResults, id: \.title) { section in
                        Section(section.title) {
                            ForEach(section.items) { result in
                                NavigationLink(value: Route.result(result)) {
                                    SearchResultRow(result: result, query: query)
                                }
                            }
                        }
                    }

                    if isLoading {
                        HStack {
                            Spacer()
                            ProgressView()
                            Spacer()
                        }
                        .listRowSeparator(.hidden)
                    }
                }
            }
            .listStyle(.plain)
            .navigationTitle("Search")
            .searchable(text: $query, prompt: "Search")
            .task(id: query) {
                await loadResults(for: query)
            }
            .navigationDestination(for: Route.self) { route in
                switch route {
                case .result(let result):
                    ResultDetailScreen(result: result)
                }
            }
        }
    }

    private func loadResults(for query: String) async {
        guard !query.isEmpty else {
            results = []
            isLoading = false
            return
        }

        isLoading = true

        try? await Task.sleep(for: .milliseconds(250))
        guard !Task.isCancelled else { return }

        let allResults: [Result] = [
            Result(id: UUID(), title: "Atlas", subtitle: "Project", snippet: "Design system rollout and migration plan", systemImage: "square.stack.3d.up", kind: .top),
            Result(id: UUID(), title: "Atlas Team", subtitle: "People", snippet: "Engineering, design, and operations", systemImage: "person.3", kind: .people),
            Result(id: UUID(), title: "Quarterly Plan", subtitle: "Project", snippet: "Milestones, staffing, and launch checklist", systemImage: "calendar", kind: .projects)
        ]

        results = allResults.filter {
            $0.title.localizedCaseInsensitiveContains(query) ||
            ($0.snippet?.localizedCaseInsensitiveContains(query) ?? false)
        }

        isLoading = false
    }
}

private extension SearchResultsScreen.Result.Kind {
    var sortOrder: Int {
        switch self {
        case .top: 0
        case .projects: 1
        case .people: 2
        }
    }
}

private struct SearchResultRow: View {
    let result: SearchResultsScreen.Result
    let query: String

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            Image(systemName: result.systemImage)
                .frame(width: 24)
                .foregroundStyle(.secondary)

            VStack(alignment: .leading, spacing: 4) {
                HighlightedText(result.title, query: query)
                    .font(.body.weight(.medium))
                    .foregroundStyle(.primary)

                if let subtitle = result.subtitle {
                    Text(subtitle)
                        .font(.subheadline)
                        .foregroundStyle(.secondary)
                }

                if let snippet = result.snippet {
                    HighlightedText(snippet, query: query)
                        .font(.subheadline)
                        .foregroundStyle(.secondary)
                        .lineLimit(2)
                }
            }
        }
        .padding(.vertical, 4)
    }
}

private struct HighlightedText: View {
    let text: String
    let query: String

    init(_ text: String, query: String) {
        self.text = text
        self.query = query
    }

    var body: some View {
        if query.isEmpty {
            Text(text)
        } else if let range = text.range(of: query, options: .caseInsensitive) {
            let before = String(text[..<range.lowerBound])
            let match = String(text[range])
            let after = String(text[range.upperBound...])

            Text(before) +
            Text(match).fontWeight(.semibold) +
            Text(after)
        } else {
            Text(text)
        }
    }
}

private struct ResultDetailScreen: View {
    let result: SearchResultsScreen.Result

    var body: some View {
        Text(result.title)
            .navigationTitle(result.title)
            .navigationBarTitleDisplayMode(.inline)
    }
}
```

What this gets right:

- Empty query shows recent searches, not fake results
- Non-empty query updates a result list in place
- Sections express ranking or type without extra chrome
- Rows push into detail with `NavigationLink(value:)`
- Loading stays lightweight and does not replace the whole screen

## Usage Patterns

### Recent Before Live Results

Use recent searches only when the query is empty. As soon as people type, switch to live results or suggestions.

```swift
if query.isEmpty {
    Section("Recent") {
        ForEach(recentSearches, id: \.self) { recent in
            Button {
                query = recent
            } label: {
                Label(recent, systemImage: "clock.arrow.circlepath")
            }
            .buttonStyle(.plain)
        }
    }
}
```

Default rule:

- Empty query: recent searches, pinned suggestions, or popular destinations
- Active query: live results
- Do not mix recent searches into the middle of ranked live results

### Section Results Only When It Helps Scanning

Use one flat list when ranking is global and obvious. Add sections when type, scope, or intent matters.

```swift
List {
    Section("Top Results") {
        ForEach(topResults) { result in
            NavigationLink(value: result) {
                SearchResultRow(result: result, query: query)
            }
        }
    }

    Section("People") {
        ForEach(peopleResults) { result in
            NavigationLink(value: result) {
                SearchResultRow(result: result, query: query)
            }
        }
    }
}
```

Good section choices:

- `Top Results`
- Content type like `People`, `Projects`, `Files`
- Search scope like `This App` and `Shared`

Avoid sectioning purely to decorate a short result list.

### Highlight Matches Sparingly

Highlight the query in the title and the most useful snippet when it helps scanning. One clear match is enough.

```swift
HighlightedText(result.title, query: query)
    .font(.body.weight(.medium))

if let snippet = result.snippet {
    HighlightedText(snippet, query: query)
        .font(.subheadline)
        .foregroundStyle(.secondary)
        .lineLimit(2)
}
```

Rules:

- Highlight exact or near-exact text matches
- Prefer title first, snippet second
- Keep highlight readable in all appearances
- Do not highlight every metadata field in the row
- Do not rely on color alone to convey the match

### Present Ranking Through Order

Ranking should be obvious from position. The best result goes first. A “Top Results” section is usually enough.

Useful supporting signals:

- A short subtitle like `Project` or `Person`
- A concise snippet showing why the result matched
- A stable source label when multiple backends feed one list

Avoid visible numeric ranks, scores, or “97% match” labels unless ranking itself is part of the product.

### Keep Loading Lightweight

For live search, keep the list stable while new results load. Show lightweight progress near the bottom or in place of an empty first load.

```swift
List {
    ForEach(results) { result in
        NavigationLink(value: result) {
            SearchResultRow(result: result, query: query)
        }
    }

    if isLoading {
        HStack {
            Spacer()
            ProgressView()
            Spacer()
        }
        .listRowSeparator(.hidden)
    }
}
```

Prefer:

- Debounced live updates for remote search
- Existing results staying visible during refresh
- `ContentUnavailableView.search(text:)` for no-results states

Avoid:

- Full-screen spinners for ordinary query changes
- Flashing between empty and loading on each keystroke
- Skeleton rows after results are already visible unless the screen truly needs them

### Push Search Results Into Detail

Search results usually belong in the current hierarchy. Use `NavigationLink(value:)` and typed destinations.

```swift
NavigationLink(value: Route.result(result)) {
    SearchResultRow(result: result, query: query)
}
```

Use a sheet only when selecting a result completes a temporary task like picking, attaching, or inserting.

## Design System Rules

### Do

- Use a plain `List` for iPhone search results
- Make the entire row the tap target
- Keep row height compact and consistent
- Put the strongest match signal in the title
- Use sections only when they improve scanning
- Show recent searches only before live results begin
- Preserve visible results during incremental loading
- Push into detail with `NavigationLink`
- Use `ContentUnavailableView.search(text:)` when nothing matches

### Don't

- Interleave recent searches with ranked live results
- Add custom cards, heavy borders, or nested containers around every result
- Show long metadata blocks under each row
- Use multiple competing highlights in one row
- Put trailing buttons inside a result row
- Present detail in a sheet by default
- Reset scroll position on every keystroke
- Hide why a result matched when the title alone is ambiguous

## Required Structure

Every live result row should follow this pattern:

```swift
NavigationLink(value: result) {
    HStack(alignment: .top, spacing: 12) {
        Image(systemName: result.systemImage)
            .frame(width: 24)

        VStack(alignment: .leading, spacing: 4) {
            Text(result.title)
            Text(result.subtitle)
            Text(result.snippet)
        }
    }
    .padding(.vertical, 4)
}
```

Minimum row anatomy:

- Leading icon or thumbnail when it helps identify type
- Primary title with the clearest match
- One supporting line for type, location, or snippet
- Full-row navigation affordance through `NavigationLink`

Empty-query anatomy:

- `Recent` section first
- Optional pinned suggestions below it
- No placeholder results pretending to be ranked search output

## Component API

If you build a reusable search results component, start with this shape:

```swift
struct SearchResultSection<Result: Identifiable & Hashable>: Identifiable {
    let id: String
    let title: String
    let results: [Result]
}

struct SearchResultItem: Identifiable, Hashable {
    let id: UUID
    let title: String
    let subtitle: String?
    let snippet: String?
    let systemImage: String?
    let matchReason: String?
}
```

Recommended inputs:

| Input | Use |
| --- | --- |
| `query: String` | Current search text |
| `recentSearches: [String]` | Empty-query history |
| `sections: [SearchResultSection]` | Ranked results, optionally grouped |
| `isLoading: Bool` | Incremental or initial loading state |
| `onSelectRecent: (String) -> Void` | Re-run a recent search |
| `destination` | Typed navigation destination for a selected result |

Recommended defaults:

- Flat list first
- At most one supporting text block per row
- `Top Results` as the first section when grouping helps
- Query highlighting in title and one snippet
- Push navigation inside the current stack

Escape hatches:

- Multiple sections when type truly matters
- Thumbnail rows for image-heavy results
- Scope-specific headers when search scopes are active

## Common Patterns

### In-Tab Search

Use `searchable` on the current list screen and replace the list content with results as the query becomes active.

### Search Destination

Use a dedicated Search screen or Search tab when the screen has its own recent history, suggestions, scopes, and no-results state.

### No Results

Use the system empty state:

```swift
ContentUnavailableView.search(text: query)
```

Keep the message short. Offer filters or correction actions only when they are actually useful.
