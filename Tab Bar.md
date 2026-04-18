# Tab Bar

A native iPhone top-level navigation container. On iOS 26+, use `TabView` with system tabs, let the bar float above content, and keep each tab focused on a distinct top-level section.

## When to Use

- **Top-level app sections**: Home, Library, Search, Profile, Settings
- **Frequently revisited destinations**: Areas people switch between often
- **Parallel navigation stacks**: Sections that should keep their own history and scroll position
- **Dedicated search destination**: Apps where search is a primary destination, not just a filter

Use a tab bar only for peer sections of the app.

Do not use a tab bar for:

- Actions like create, compose, filter, or sort
- Steps in a flow
- Deep destinations that belong inside a tab's navigation stack
- Temporary modes or toggles

## Quick Start

Start with 3-5 tabs. Give each tab its own root view and its own `NavigationStack`.

```swift
import SwiftUI

enum AppTab: Hashable {
    case home
    case library
    case settings
}

struct TabBarDemo: View {
    @State private var selection: AppTab = .home

    var body: some View {
        TabView(selection: $selection) {
            Tab("Home", systemImage: "house", value: .home) {
                HomeTab()
            }

            Tab("Library", systemImage: "books.vertical", value: .library) {
                LibraryTab()
            }

            Tab("Settings", systemImage: "gearshape", value: .settings) {
                SettingsTab()
            }
        }
    }
}

private struct HomeTab: View {
    @State private var path: [Int] = []

    var body: some View {
        NavigationStack(path: $path) {
            List(0..<20, id: \.self) { item in
                NavigationLink(value: item) {
                    Text("Home item \(item)")
                }
            }
            .navigationTitle("Home")
            .navigationDestination(for: Int.self) { item in
                Text("Home detail \(item)")
                    .navigationTitle("Item \(item)")
            }
        }
    }
}

private struct LibraryTab: View {
    @State private var path: [String] = []

    var body: some View {
        NavigationStack(path: $path) {
            List(["Albums", "Artists", "Playlists"], id: \.self) { section in
                NavigationLink(value: section) {
                    Text(section)
                }
            }
            .navigationTitle("Library")
            .navigationDestination(for: String.self) { section in
                Text(section)
                    .navigationTitle(section)
            }
        }
    }
}

private struct SettingsTab: View {
    var body: some View {
        NavigationStack {
            List {
                Text("Account")
                Text("Notifications")
                Text("About")
            }
            .navigationTitle("Settings")
        }
    }
}
```

## Usage Patterns

### Preserve State Per Tab

Each tab should own its own navigation state. This is the default people expect: switching tabs should not reset where they were.

- Put a separate `NavigationStack` inside each tab root
- Keep each tab's path and screen state inside that tab
- Bind `TabView(selection:)` to an enum when you need programmatic tab changes

Do not share one `NavigationStack` across all tabs.

### Search Tab

Use a search tab only when search is a top-level destination across the app. Mark exactly one tab with the search role, then put `.searchable(...)` on the `TabView`.

```swift
import SwiftUI

enum SearchAppTab: Hashable {
    case browse
    case search
    case profile
}

struct SearchTabDemo: View {
    @State private var selection: SearchAppTab = .browse
    @State private var query = ""

    private let items = [
        "Albums", "Artists", "Downloads", "Favorites", "Podcasts", "Stations"
    ]

    private var results: [String] {
        guard !query.isEmpty else { return items }
        return items.filter { $0.localizedCaseInsensitiveContains(query) }
    }

    var body: some View {
        TabView(selection: $selection) {
            Tab("Browse", systemImage: "square.grid.2x2", value: .browse) {
                NavigationStack {
                    List(items, id: \.self) { item in
                        Text(item)
                    }
                    .navigationTitle("Browse")
                }
            }

            Tab("Search", systemImage: "magnifyingglass", value: .search, role: .search) {
                NavigationStack {
                    List(results, id: \.self) { result in
                        Text(result)
                    }
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
        .searchable(text: $query, prompt: "Search everything")
    }
}
```

Use this pattern when search should span the app. If search only filters one screen, keep it inside that screen instead of adding a search tab.

### Minimize with a Bottom Accessory

Use minimization for scroll-heavy tabs and persistent accessory content like a mini player. Keep the accessory compact and glanceable.

```swift
import SwiftUI

enum PlayerTab: Hashable {
    case listen
    case library
    case settings
}

struct Track: Equatable {
    let title: String
    let artist: String

    static let sample = Track(title: "Night Drive", artist: "Comet")
}

struct MiniPlayerTabDemo: View {
    @State private var selection: PlayerTab = .listen
    @State private var nowPlaying: Track? = .sample

    var body: some View {
        TabView(selection: $selection) {
            Tab("Listen", systemImage: "play.circle", value: .listen) {
                NavigationStack {
                    List(0..<50, id: \.self) { item in
                        Text("Track \(item)")
                    }
                    .navigationTitle("Listen")
                }
            }

            Tab("Library", systemImage: "books.vertical", value: .library) {
                NavigationStack {
                    Text("Library")
                        .navigationTitle("Library")
                }
            }

            Tab("Settings", systemImage: "gearshape", value: .settings) {
                NavigationStack {
                    Text("Settings")
                        .navigationTitle("Settings")
                }
            }
        }
        .tabBarMinimizeBehavior(.onScrollDown)
        .tabViewBottomAccessory(isEnabled: nowPlaying != nil) {
            if let nowPlaying {
                MiniPlayerAccessory(track: nowPlaying)
            }
        }
    }
}

private struct MiniPlayerAccessory: View {
    let track: Track
    @Environment(\.tabViewBottomAccessoryPlacement) private var placement

    var body: some View {
        HStack(spacing: 12) {
            VStack(alignment: .leading, spacing: 2) {
                Text(track.title)
                    .font(.subheadline.weight(.semibold))
                Text(track.artist)
                    .font(.caption)
                    .foregroundStyle(.secondary)
            }

            Spacer()

            Button {
            } label: {
                Image(systemName: "play.fill")
            }
            .buttonStyle(.glass)
            .accessibilityLabel("Play")
        }
        .padding(.horizontal, placement == .inline ? 12 : 16)
        .padding(.vertical, 10)
    }
}
```

Use an accessory for:

- Mini player
- Call or recording status
- Lightweight now-playing or live status

Do not use it for banners, required forms, or primary actions.

## Design System Rules

### Do

- Use the tab bar for **top-level navigation only**
- Start with **3-5 tabs**
- Keep tab labels short, usually one word
- Use a clear SF Symbol plus a text label for every tab
- Give each tab its **own root view** and **own navigation stack**
- Use **one** search tab with `role: .search` when search is a destination
- Put `.searchable(...)` on the **`TabView`** when using a search tab
- Use `.tabBarMinimizeBehavior(.onScrollDown)` for long, scrollable content
- Use `tabViewBottomAccessory` for compact persistent content tied to the whole tab experience

### Don't

- Don't put create, compose, add, filter, sort, or overflow actions in the tab bar
- Don't use more than **5 default tabs** on iPhone
- Don't rely on the More tab as a normal navigation path
- Don't hide or disable tabs based on temporary state
- Don't share one `NavigationStack` or `NavigationPath` across all tabs
- Don't add more than one search tab
- Don't use a search tab for local, single-screen filtering
- Don't put heavy controls, forms, or alerts in the bottom accessory

## Required Structure

Prefer this shape:

```swift
TabView(selection: $selection) {
    Tab("Home", systemImage: "house", value: .home) {
        NavigationStack {
            HomeView()
        }
    }

    Tab("Library", systemImage: "books.vertical", value: .library) {
        NavigationStack {
            LibraryView()
        }
    }

    Tab("Settings", systemImage: "gearshape", value: .settings) {
        NavigationStack {
            SettingsView()
        }
    }
}
```

Required decisions:

- A stable tab enum for selection
- One tab per top-level destination
- One root container per tab
- One label and one symbol per tab
- Optional: one search tab
- Optional: one bottom accessory

## Component API

### Core APIs

| API | Use Case |
| --- | --- |
| `TabView(selection:)` | Top-level tab container with programmatic selection |
| `Tab(_:systemImage:value:content:)` | Standard iPhone tab definition |
| `Tab(_:systemImage:value:role:content:)` | Standard tab with a semantic role, usually search |
| `NavigationStack` inside each tab | Preserve history independently per tab |

### Search

| API | Use Case |
| --- | --- |
| `role: .search` | Marks the dedicated search tab |
| `.searchable(text:prompt:)` on `TabView` | Makes the search tab morph into a search field on iPhone |

If no tab has the search role, SwiftUI can apply search across tabs and reset search state as selection changes. Prefer an explicit search tab when search is a destination.

### Minimize and Accessory

| API | Use Case |
| --- | --- |
| `.tabBarMinimizeBehavior(.onScrollDown)` | Minimize the floating tab bar while scrolling down |
| `.tabViewBottomAccessory(isEnabled:content:)` | Add persistent accessory content above or inline with the tab bar |
| `@Environment(\\.tabViewBottomAccessoryPlacement)` | Adjust accessory layout for expanded vs inline placement |

### Optional

| API | Use Case |
| --- | --- |
| `.badge(_:)` | Unread counts or lightweight status on a tab |

Use badges sparingly. They should signal new or changed information, not permanent clutter.

## Common Patterns

### Standard App Shell

- `Home`
- `Library`
- `Settings`

Good default for simple iPhone apps.

### Search-Centric App

- `Browse`
- `Search`
- `Profile`

Use when search is a first-class destination.

### Media App

- `Listen`
- `Library`
- `Settings`
- Mini player in `tabViewBottomAccessory`
- `.tabBarMinimizeBehavior(.onScrollDown)`

Use when playback should stay visible across tabs without becoming a separate tab.
