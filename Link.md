# Link

A native iPhone control for opening a URL. On iOS 26+, use `Link` for real destinations like websites, universal links, `mailto:`, `tel:`, and app deep links. If the tap stays inside your SwiftUI navigation hierarchy, use `NavigationLink` instead.

## When to Use

- **External URLs**: Help center, privacy policy, account portal, docs
- **Universal links and app deep links**: Open another app or a specific destination from a URL
- **Inline legal/support copy**: Terms, privacy, contact, attribution
- **Read-only destination rows**: A row whose only job is to open a URL

Prefer something else when:

- The tap **pushes an in-app screen**: use `NavigationLink`
- The tap must **save, validate, confirm, log, or branch** before opening: use `Button` and call `openURL`
- The user is **sharing** content: use `ShareLink`

## Quick Start

Start with a plain text link for a real URL:

```swift
import SwiftUI

struct LegalFooter: View {
    var body: some View {
        Link("Privacy Policy", destination: URL(string: "https://example.com/privacy")!)
    }
}
```

For inline prose, use markdown in `Text`:

```swift
Text("By continuing, you agree to the [Terms of Service](https://example.com/terms).")
```

## Usage Patterns

### External URLs

Use `Link` directly when tapping should hand the URL to the system.

```swift
struct HelpLinks: View {
    var body: some View {
        List {
            Link("Help Center", destination: URL(string: "https://support.example.com")!)
            Link("Privacy Policy", destination: URL(string: "https://example.com/privacy")!)
        }
        .navigationTitle("Help")
    }
}
```

Strong default:

- Use the page or destination name as the label
- Prefer `https` URLs
- Let the system decide whether to open Safari or an associated app

### Deep Links

Use `Link` when the destination is a real URL, including universal links and app-specific schemes.

```swift
Link("Open Order", destination: URL(string: "myapp://orders/1234")!)
```

Use this for URL-based handoff. If the destination is already part of your app's current navigation stack, prefer `NavigationLink`.

### In-App Links vs Buttons

Choose the primitive by what the tap means:

```swift
import SwiftUI

struct AccountView: View {
    @Environment(\.openURL) private var openURL

    var body: some View {
        List {
            NavigationLink("Billing Details") {
                BillingDetailsView()
            }

            Link("Open Status Page", destination: URL(string: "https://status.example.com")!)

            Button("Open Help Center") {
                trackHelpTap()
                openURL(URL(string: "https://support.example.com")!)
            }
            .buttonStyle(.glass)
        }
    }
}
```

Rule of thumb:

- **`NavigationLink`**: moves deeper into your app
- **`Link`**: opens a URL with no extra work
- **`Button` + `openURL`**: you need button styling or app logic first

### Inline Links in Text

Inline links should read like text, not like buttons.

```swift
VStack(alignment: .leading, spacing: 8) {
    Text("Need help? Visit the [Help Center](https://support.example.com).")

    Text("By continuing, you agree to the [Terms](https://example.com/terms) and [Privacy Policy](https://example.com/privacy).")
        .font(.footnote)
        .foregroundStyle(.secondary)
}
```

Use inline links for:

- Legal copy
- Support copy
- Short references inside paragraphs

Don't use inline links for the main call to action on a screen.

### Custom `openURL` Behavior

`Link` and markdown links in `Text` both use the `openURL` environment value. Override it when URL taps should route through app logic first.

```swift
import SwiftUI

struct SupportScreen: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 16) {
            Link("Help Center", destination: URL(string: "https://support.example.com")!)

            Text("Read our [status page](https://status.example.com).")
        }
        .environment(\.openURL, OpenURLAction { url in
            if url.scheme == "myapp" {
                AppRouter.shared.open(url)
                return .handled
            }

            return .systemAction(url)
        })
    }
}
```

Use this for:

- Routing app deep links into your own coordinator
- Logging or gating URL opens
- Swapping certain links to in-app presentation while leaving the rest to the system

`onOpenURL` is different. Use `onOpenURL` for URLs your app receives from outside, not for handling taps on a `Link`.

## Design System Rules

### Do

- Use **`Link` for real URLs**
- Use **`NavigationLink`** for in-app hierarchy
- Use **inline text styling** for links inside sentences and footers
- Use a **descriptive destination label**: `Privacy Policy`, `Help Center`, `Open Order`
- Keep standalone links **plain by default**
- Use **`Button` + `openURL`** when the link needs button chrome or pre-open work
- Prefer **one clear standalone link** over several low-value links in a cluster
- Scope custom **`openURL`** handlers narrowly to the subtree that needs them
- Prefer **universal links** over custom schemes when both are available

### Don't

- Use `Link` to push a SwiftUI destination
- Use `Button` for a plain legal or support URL that needs no app logic
- Turn the screen's primary action into a tiny inline link
- Write vague labels like `Tap here`, `Learn more`, or `Read this` without context
- Wrap a `Link` in another tap target or gesture
- Intercept every URL globally if only one screen needs custom handling
- Style footer links like prominent buttons
- Fake links with `Text` plus `onTapGesture`

## Required Structure

Prefer one of these three patterns:

### Standalone text link

```swift
Link("Privacy Policy", destination: URL(string: "https://example.com/privacy")!)
```

### Inline markdown link

```swift
Text("Read the [Terms of Service](https://example.com/terms).")
```

### Button-like external action

```swift
struct HelpCTA: View {
    @Environment(\.openURL) private var openURL

    var body: some View {
        Button("Open Help Center") {
            openURL(URL(string: "https://support.example.com")!)
        }
        .buttonStyle(.glass)
    }
}
```

## Component API

### Choose the Right Primitive

| Primitive | Use Case |
| --- | --- |
| `Link` | Open a real URL directly |
| `Text` with markdown link | Inline links inside prose |
| `NavigationLink` | Push to another screen in your app |
| `Button` + `openURL` | URL opens that need logic first or should look like a button |

### Choose an Initializer

| API | Use Case |
| --- | --- |
| `Link(_:destination:)` | Most links with a text label |
| `Link(destination:label:)` | Custom label with icon or richer layout |

### URL Handling

| API | Use Case |
| --- | --- |
| `@Environment(\.openURL)` | Open a URL from your own action |
| `.environment(\.openURL, OpenURLAction { ... })` | Override link behavior for a subtree |
| `.onOpenURL { url in ... }` | Handle incoming URLs delivered to your app |

## Common Patterns

### Legal Footer

```swift
VStack(alignment: .leading, spacing: 4) {
    Text("By continuing, you agree to the [Terms](https://example.com/terms).")
    Text("See the [Privacy Policy](https://example.com/privacy).")
}
.font(.footnote)
.foregroundStyle(.secondary)
```

### Support Row

```swift
Link(destination: URL(string: "https://support.example.com")!) {
    Label("Help Center", systemImage: "questionmark.circle")
}
```

### Phone or Email Link

```swift
VStack(alignment: .leading, spacing: 12) {
    Link("Call Support", destination: URL(string: "tel:18005551212")!)
    Link("Email Support", destination: URL(string: "mailto:support@example.com")!)
}
```

### Route App Links Internally

```swift
VStack(alignment: .leading, spacing: 12) {
    Link("Open Order", destination: URL(string: "myapp://orders/1234")!)
    Link("Track Shipment", destination: URL(string: "myapp://shipments/1234")!)
}
.environment(\.openURL, OpenURLAction { url in
    AppRouter.shared.open(url)
    return .handled
})
```
