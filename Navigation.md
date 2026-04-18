# Navigation

A native iPhone navigation model for hierarchical flows. On iOS 26+, start with one `NavigationStack` per top-level section, drive pushes from value state, use sheets for short modal tasks, and keep each tab's stack intact when people switch sections.

## When to Use

- **`NavigationStack`**: Drill into detail, child settings, related content, and multi-step hierarchies where Back should return to the previous screen
- **`sheet`**: Short, scoped tasks that are related to the current screen and should dismiss back to it
- **`fullScreenCover`**: Immersive or prolonged tasks like capture, onboarding, editing, playback, or authentication
- **One stack per tab**: Apps with top-level sections where people expect each tab to remember where they were

## Quick Start

Start with a typed route array. Use `NavigationPath` only when one stack needs multiple unrelated value types.

```swift
import SwiftUI

struct InboxScreen: View {
    struct Message: Identifiable, Hashable, Codable {
        let id: Int
        let subject: String
    }

    enum Route: Hashable, Codable {
        case message(Message)
    }

    @State private var path: [Route] = []

    private let messages = [
        Message(id: 1, subject: "Design Review"),
        Message(id: 2, subject: "Flight Details"),
        Message(id: 3, subject: "Invoice")
    ]

    var body: some View {
        NavigationStack(path: $path) {
            List(messages) { message in
                NavigationLink(value: Route.message(message)) {
                    Text(message.subject)
                }
            }
            .navigationTitle("Inbox")
            .navigationDestination(for: Route.self) { route in
                switch route {
                case .message(let message):
                    MessageDetailScreen(message: message)
                }
            }
        }
    }
}

private struct MessageDetailScreen: View {
    let message: InboxScreen.Message

    var body: some View {
        Text(message.subject)
            .navigationTitle("Message")
            .navigationBarTitleDisplayMode(.inline)
    }
}
```

## Usage Patterns

### Path-Driven Pushes

Use value-based links and destinations. Keep pushed state in data, not in stored view instances.

```swift
import SwiftUI

struct ProjectsScreen: View {
    struct Project: Identifiable, Hashable, Codable {
        let id: Int
        let name: String
    }

    enum Route: Hashable, Codable {
        case project(Project)
        case members(projectID: Int)
    }

    @State private var path: [Route] = []

    private let projects = [
        Project(id: 1, name: "Atlas"),
        Project(id: 2, name: "Beacon")
    ]

    var body: some View {
        NavigationStack(path: $path) {
            List(projects) { project in
                NavigationLink(value: Route.project(project)) {
                    Text(project.name)
                }
            }
            .navigationTitle("Projects")
            .navigationDestination(for: Route.self) { route in
                switch route {
                case .project(let project):
                    ProjectDetailScreen(
                        project: project,
                        showMembers: { path.append(.members(projectID: project.id)) }
                    )

                case .members(let projectID):
                    Text("Members for project \(projectID)")
                        .navigationTitle("Members")
                        .navigationBarTitleDisplayMode(.inline)
                }
            }
        }
    }
}

private struct ProjectDetailScreen: View {
    let project: ProjectsScreen.Project
    let showMembers: () -> Void

    var body: some View {
        List {
            Section {
                Button("View Members") {
                    showMembers()
                }
            }
        }
        .navigationTitle(project.name)
        .navigationBarTitleDisplayMode(.inline)
    }
}
```

### Push vs Present

Push when the next screen becomes part of the hierarchy. Present a sheet when the task is temporary. Use full-screen cover when the task should temporarily replace the app's normal chrome.

```swift
import SwiftUI

struct AccountScreen: View {
    @State private var showingAddCard = false
    @State private var showingCamera = false

    var body: some View {
        List {
            NavigationLink("Billing History") {
                Text("Billing History")
                    .navigationTitle("Billing History")
                    .navigationBarTitleDisplayMode(.inline)
            }

            Button("Add Payment Method") {
                showingAddCard = true
            }

            Button("Scan Card") {
                showingCamera = true
            }
        }
        .navigationTitle("Account")
        .sheet(isPresented: $showingAddCard) {
            AddCardScreen()
        }
        .fullScreenCover(isPresented: $showingCamera) {
            CardScannerScreen()
        }
    }
}

private struct AddCardScreen: View {
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        NavigationStack {
            Form {
                TextField("Card Number", text: .constant(""))
            }
            .navigationTitle("Add Card")
            .navigationBarTitleDisplayMode(.inline)
            .toolbar {
                ToolbarItem(placement: .topBarLeading) {
                    Button("Cancel") { dismiss() }
                }
                ToolbarItem(placement: .topBarTrailing) {
                    Button("Save") { dismiss() }
                }
            }
        }
    }
}

private struct CardScannerScreen: View {
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        ZStack(alignment: .topTrailing) {
            Color.black.ignoresSafeArea()

            Button("Done") {
                dismiss()
            }
            .padding()
        }
    }
}
```

### Navigation Titles

Use the title to describe the current place in the hierarchy.

```swift
NavigationStack {
    List(1..<10) { index in
        NavigationLink("Order \(index)") {
            Text("Order \(index)")
                .navigationTitle("Order \(index)")
                .navigationBarTitleDisplayMode(.inline)
        }
    }
    .navigationTitle("Orders")
}
```

Strong default:

- Root screens: large title when the screen is a primary destination or browse view
- Pushed detail/edit screens: inline title
- Title text: short noun phrase, not marketing copy

### Back Behavior

Prefer the system back button and edge-swipe gesture. On pushed screens, do not replace Back just to run custom logic.

```swift
import SwiftUI

struct EditProfileScreen: View {
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        Form {
            TextField("Name", text: .constant(""))
        }
        .navigationTitle("Edit Profile")
        .navigationBarTitleDisplayMode(.inline)
        .toolbar {
            ToolbarItem(placement: .topBarTrailing) {
                Button("Done") {
                    dismiss()
                }
            }
        }
    }
}
```

Use `dismiss()` for explicit completion actions like Done, Close, or Save. Let the system own Back.

## Design System Rules

### Do

- Use **one `NavigationStack` per top-level tab or root section**
- Prefer **typed `[Route]` state** over `NavigationPath`
- Use **`NavigationLink(value:)`** and **`.navigationDestination(for:)`**
- Make route values **`Hashable`**; make them **`Codable`** when deep links or restoration matter
- Use **push** for hierarchy and **sheet** for temporary tasks
- Keep **sheet flows short** and show only one main sheet at a time
- Use **full-screen cover** for immersive or multi-step tasks
- Give every pushed screen a **navigation title**
- Use **large titles at the root** and **inline titles deeper in the stack**
- Keep **one navigation history per tab**
- Deep-link by selecting the right tab first, then mutating that tab's path

### Don't

- Use a sheet to browse normal app hierarchy
- Share one global path across unrelated tabs
- Reset a tab's navigation path every time the user switches tabs
- Store views in navigation state
- Hide or replace the system back button without a real product requirement
- Put critical completion controls only in swipe-to-dismiss behavior
- Stack multiple sheets from the main interface
- Duplicate the same title in both the navigation bar and the screen body
- Push temporary forms that should simply dismiss back to the current screen

## Required Structure

Prefer this shape:

```swift
import SwiftUI

enum AppTab: Hashable {
    case home
    case search
    case settings
}

enum HomeRoute: Hashable, Codable {
    case detail(Int)
}

struct AppRoot: View {
    @State private var selectedTab: AppTab = .home
    @State private var homePath: [HomeRoute] = []
    @State private var searchPath: [String] = []
    @State private var settingsPath: [String] = []

    var body: some View {
        TabView(selection: $selectedTab) {
            NavigationStack(path: $homePath) {
                Text("Home")
                    .navigationTitle("Home")
                    .navigationDestination(for: HomeRoute.self) { route in
                        switch route {
                        case .detail(let id):
                            Text("Detail \(id)")
                                .navigationTitle("Detail")
                                .navigationBarTitleDisplayMode(.inline)
                        }
                    }
            }
            .tabItem { Label("Home", systemImage: "house") }
            .tag(AppTab.home)

            NavigationStack(path: $searchPath) {
                Text("Search")
                    .navigationTitle("Search")
            }
            .tabItem { Label("Search", systemImage: "magnifyingglass") }
            .tag(AppTab.search)

            NavigationStack(path: $settingsPath) {
                Text("Settings")
                    .navigationTitle("Settings")
            }
            .tabItem { Label("Settings", systemImage: "gearshape") }
            .tag(AppTab.settings)
        }
    }
}
```

Minimum rules:

- Each top-level tab owns its own path
- Each stack registers destinations near the root of that stack
- Each route is data, not UI
- Modal flows can have their own internal `NavigationStack` when needed

## Component API

### Default Stack APIs

| API | Use Case |
| --- | --- |
| `NavigationStack(path:)` | Root container for a single hierarchical flow |
| `NavigationLink(value:)` | User-initiated push |
| `.navigationDestination(for:)` | Map route values to screens |
| `@Environment(\\.dismiss)` | Programmatic exit for Done, Close, Save, Cancel |

### Presentation APIs

| API | Use Case |
| --- | --- |
| `.sheet(isPresented:)` / `.sheet(item:)` | Short modal task tied to current context |
| `.fullScreenCover(isPresented:)` / `.fullScreenCover(item:)` | Immersive or prolonged modal flow |

### Title APIs

| API | Use Case |
| --- | --- |
| `.navigationTitle(_:)` | Title for the current place in the hierarchy |
| `.navigationBarTitleDisplayMode(.automatic)` | Let the system resolve title style |
| `.navigationBarTitleDisplayMode(.inline)` | Detail, edit, and utility screens |

### Escape Hatch

Use `NavigationPath` when one stack truly needs heterogeneous route types. If a single route enum can model the flow, keep the enum.

## Common Patterns

### Deep Links

Select the destination tab first, then replace or append that tab's path.

```swift
import SwiftUI

struct RootView: View {
    enum Tab: Hashable {
        case inbox
        case settings
    }

    enum InboxRoute: Hashable, Codable {
        case message(Int)
    }

    @State private var selectedTab: Tab = .inbox
    @State private var inboxPath: [InboxRoute] = []

    var body: some View {
        TabView(selection: $selectedTab) {
            NavigationStack(path: $inboxPath) {
                Text("Inbox")
                    .navigationTitle("Inbox")
                    .navigationDestination(for: InboxRoute.self) { route in
                        switch route {
                        case .message(let id):
                            Text("Message \(id)")
                                .navigationTitle("Message")
                                .navigationBarTitleDisplayMode(.inline)
                        }
                    }
            }
            .tabItem { Label("Inbox", systemImage: "tray") }
            .tag(Tab.inbox)

            NavigationStack {
                Text("Settings")
                    .navigationTitle("Settings")
            }
            .tabItem { Label("Settings", systemImage: "gearshape") }
            .tag(Tab.settings)
        }
        .onOpenURL { url in
            guard url.host == "message",
                  let id = Int(url.lastPathComponent) else { return }

            selectedTab = .inbox
            inboxPath = [.message(id)]
        }
    }
}
```

### Preserving Context Across Tabs

Do not clear a tab's path on tab switch. People expect to return to the same place they left.

For state restoration across launches, keep routes `Codable` and persist each tab's path representation. If you need mixed types, persist `NavigationPath.CodableRepresentation`.
