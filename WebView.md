# WebView

A native iPhone web content surface for embedded HTML, in-app articles, account flows, and controlled browsing experiences. On iOS 26+, start with SwiftUI’s new `WebView` and `WebPage` APIs. Use them when web content is part of your product. For generic external browsing, prefer Safari or `SFSafariViewController`.

## When to Use

- **Embedded product content**: Articles, docs, help, account pages, dashboards
- **Controlled web experiences**: In-app browsing where navigation, progress, or JavaScript integration matters
- **Local bundled web content**: HTML/CSS/JS shipped with the app
- **Hybrid surfaces**: Native chrome around web content

Use something else when:

- The user is just visiting an external website: use `Link` or `SFSafariViewController`
- The content should be fully native: build it in SwiftUI
- The web surface is only a one-off legal or support link: don’t embed a browser for that

## Quick Start

Use `WebView(url:)` for the simple case.

```swift
import SwiftUI
import WebKit

struct HelpArticleView: View {
    let url = URL(string: "https://www.webkit.org")!

    var body: some View {
        WebView(url: url)
            .navigationTitle("Help")
            .navigationBarTitleDisplayMode(.inline)
    }
}
```

Use `WebPage` when the app needs to observe or control the page.

```swift
import SwiftUI
import WebKit

@Observable
final class ArticleModel {
    let page = WebPage()

    func load(_ url: URL) {
        page.load(URLRequest(url: url))
    }
}

struct ArticleView: View {
    @State private var model = ArticleModel()
    let url = URL(string: "https://www.webkit.org")!

    var body: some View {
        WebView(model.page)
            .navigationTitle(model.page.title ?? "Article")
            .navigationBarTitleDisplayMode(.inline)
            .task {
                model.load(url)
            }
    }
}
```

## Usage Patterns

### Simple Embedded Content

Use `WebView(url:)` when the app only needs to show a page and let it behave like a browser.

```swift
WebView(url: articleURL)
    .webViewBackForwardNavigationGestures(.disabled)
```

Strong default:

- inline title
- no extra browser chrome unless it helps
- disable gestures only when the embedded experience should stay tightly scoped

### Controlled Web Experience

Use `WebPage` when you need app logic around navigation, title, URL, loading progress, or JavaScript.

```swift
@Observable
final class BillingModel {
    let page = WebPage()

    var isLoading: Bool {
        page.currentNavigationEvent != .finished
    }

    func load() {
        page.load(URLRequest(url: billingURL))
    }
}
```

Good uses:

- show progress while navigating
- update native titles from the page
- coordinate native controls with web content
- react when a checkout or sign-in flow finishes

### External Links vs Embedded Web

Do not default to `WebView` for ordinary web links.

Use:

- `Link` for simple external destinations
- `SFSafariViewController` for a standard in-app browser
- `WebView` only when the web content is meaningfully part of the app experience

Rule: if the user expects “open website,” give them Safari behavior. If the user expects “use this feature inside the app,” embed it.

### Navigation Policy

Use `WebPage.NavigationDeciding` to restrict or reroute navigation.

```swift
import WebKit

struct NavigationDecider: WebPage.NavigationDeciding {
    func decidePolicy(
        for action: WebPage.NavigationAction,
        preferences: inout WebPage.NavigationPreferences
    ) async -> WKNavigationActionPolicy {
        guard let url = action.request.url else { return .cancel }

        if url.host == "example.com" {
            return .allow
        }

        return .cancel
    }
}
```

Use this for:

- keeping the user inside an allowed domain
- kicking external links out to Safari
- blocking unexpected redirects

### JavaScript Integration

Use `callJavaScript` when the app must talk to the page.

```swift
let title = try? await page.callJavaScript(
    "document.title"
)
```

Good uses:

- read the page title or state
- trigger an in-page function
- scroll to a known anchor

Bad uses:

- rebuilding large chunks of app logic in JavaScript just because the bridge exists

### Local Bundled Content

Use `WebPage` with local HTML or custom scheme handling when the app ships web resources.

```swift
page.load(
    htmlString,
    baseURL: Bundle.main.bundleURL
)
```

This is a good fit for:

- offline docs
- rich articles
- help content that is easier to author on the web stack

### Find in Page and Scroll Control

`WebView` supports standard SwiftUI find and scroll integrations.

```swift
WebView(page)
    .findNavigator(isPresented: $showingFind)
```

Use this when:

- the web content is long-form
- people need in-page search
- native controls should scroll the page to a specific section

## Design System Rules

### Do

- Start with `WebView(url:)` for simple embedded pages
- Switch to `WebPage` when the app needs control or observation
- Keep native chrome minimal and purposeful
- Use inline titles for embedded web surfaces
- Treat external websites differently from in-app web content
- Restrict navigation if the surface is supposed to stay inside a product flow
- Show loading state when the page is part of a task, not just passive reading
- Use local web content when the web stack is the right authoring medium

### Don't

- Build a full custom browser when Safari behavior is what the user actually wants
- Embed every support or legal link in a web view
- Mix heavy native chrome and full browser chrome on the same screen
- Let embedded checkout, auth, or billing flows navigate anywhere without policy control
- Use JavaScript bridging for simple tasks the native app should own directly
- Wrap a generic external site in `WebView` just to keep the user trapped in the app

## Required Structure

Simple embedded page:

```swift
WebView(url: url)
```

Controlled page:

```swift
@Observable
final class PageModel {
    let page = WebPage()
}

WebView(model.page)
```

With navigation policy:

```swift
var configuration = WebPage.Configuration()
configuration.navigationDecider = NavigationDecider()
let page = WebPage(configuration: configuration)
```

## Component API

### Core Types

| API | Use Case |
| --- | --- |
| `WebView(url:)` | Simple embedded web content |
| `WebView(_:)` | Controlled web content backed by `WebPage` |
| `WebPage` | Loading, observation, JavaScript, navigation coordination |
| `WebPage.Configuration` | Behavior and environment customization |
| `WebPage.NavigationDeciding` | Navigation policy |

### Useful Behaviors

| API | Use Case |
| --- | --- |
| `page.load(URLRequest(...))` | Remote content |
| `page.load(_:baseURL:)` | Direct HTML |
| `page.callJavaScript(...)` | App/page bridge |
| `.findNavigator(...)` | Find in page |
| `.webViewBackForwardNavigationGestures(...)` | Gesture control |
| `.webViewScrollPosition(...)` | Scroll coordination |

## Common Patterns

### Help Center Inside the App

```swift
WebView(url: helpURL)
    .navigationTitle("Help")
    .navigationBarTitleDisplayMode(.inline)
```

### Billing Flow With Progress

```swift
ZStack {
    WebView(model.page)

    if model.page.currentNavigationEvent != .finished {
        ProgressView()
    }
}
```

### External Link Escape Hatch

```swift
struct Decider: WebPage.NavigationDeciding {
    func decidePolicy(
        for action: WebPage.NavigationAction,
        preferences: inout WebPage.NavigationPreferences
    ) async -> WKNavigationActionPolicy {
        guard let url = action.request.url else { return .cancel }
        return url.host == "example.com" ? .allow : .cancel
    }
}
```
