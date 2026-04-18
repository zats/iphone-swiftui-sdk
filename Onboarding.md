# Onboarding

A native iPhone first-launch flow for helping people understand the app, create an account, and grant access when needed. Keep onboarding short, progressive, and obviously skippable unless the app truly cannot work without setup.

## When to Use

- **First launch**: The app needs initial orientation or setup
- **Account creation**: Identity is required before the product works
- **Permission gating**: Photos, notifications, location, camera
- **Progressive setup**: The app becomes more useful as the user adds data or grants access

Use something else when:

- The product is self-explanatory and can teach inline
- Permissions can be asked later at the moment of need
- The only purpose is marketing copy

## Quick Start

Start with one clear value statement, then move quickly into setup.

```swift
import SwiftUI

struct OnboardingView: View {
    @State private var selection = 0

    var body: some View {
        TabView(selection: $selection) {
            OnboardingPage(
                title: "Capture ideas quickly",
                subtitle: "Notes, links, and files stay organized automatically.",
                systemImage: "sparkles",
                primaryTitle: "Continue",
                primaryAction: { selection = 1 }
            )
            .tag(0)

            OnboardingPage(
                title: "Stay in sync",
                subtitle: "Create an account to back up your workspace.",
                systemImage: "icloud",
                primaryTitle: "Create Account",
                primaryAction: createAccount
            )
            .tag(1)
        }
        .tabViewStyle(.page)
    }

    private func createAccount() {}
}
```

## Usage Patterns

### Intro Pages

Use 1-3 pages when the product has one or two concepts worth teaching before setup.

Good content:

- what the app does
- why it is useful
- what happens next

Bad content:

- feature laundry lists
- brand slogans
- dense diagrams

### Immediate Setup

If the app needs account creation or import before it works, move to that quickly.

```swift
VStack(spacing: 16) {
    Text("Create your workspace")
        .font(.title2.weight(.semibold))

    Button("Create Account") {
        createAccount()
    }
    .buttonStyle(.glassProminent)

    Button("I Already Have an Account") {
        signIn()
    }
    .buttonStyle(.glass)
}
```

### Progressive Permissions

Ask permissions close to the moment they matter. Explain the benefit first, then trigger the system prompt.

```swift
ContentUnavailableView {
    Label("Allow Notifications", systemImage: "bell")
} description: {
    Text("Get alerts when shared items change.")
} actions: {
    Button("Enable Notifications") {
        requestNotifications()
    }
    .buttonStyle(.glassProminent)
}
```

Do not ask every permission on first launch.

### Skip Behavior

If the app can work without onboarding, make skip obvious.

```swift
.toolbar {
    ToolbarItem(placement: .topBarTrailing) {
        Button("Skip") {
            finishOnboarding()
        }
    }
}
```

If setup is required, replace skip with a real alternative like `Sign In` or `Not Now`.

## Design System Rules

### Do

- Keep onboarding short
- Move to setup quickly
- Show one primary action per page
- Make skip behavior clear when the app can support it
- Ask permissions progressively
- Use calm, product-focused copy
- Let onboarding hand off directly into the real app

### Don't

- Turn onboarding into a feature tour
- Ask multiple permissions in a row on first launch
- Hide the account path behind several pages
- Use autoplaying animations and noisy decoration
- Make every page look structurally different
- Keep users trapped if the product can be explored without setup

## Required Structure

Prefer one of these shapes:

### Short Intro + Setup

```swift
TabView {
    IntroPage(...)
    SetupPage(...)
}
.tabViewStyle(.page)
```

### Immediate Setup

```swift
VStack {
    Text("Create your workspace")
    Button("Create Account") { createAccount() }
}
```

### Moment-of-Need Permission

```swift
ContentUnavailableView {
    Label("Allow Photos", systemImage: "photo")
} description: {
    Text("Choose images from your library.")
} actions: {
    Button("Allow Access") {
        requestAccess()
    }
}
```

## Component API

### Common Building Blocks

| API | Use Case |
| --- | --- |
| `TabView` + `.page` | Short multi-page intro |
| `NavigationStack` | Setup or auth subflows |
| `sheet` / `fullScreenCover` | Focused auth or permission flows |
| `ContentUnavailableView` | Permission or blocked state |

## Common Patterns

### Auth Handoff

```swift
VStack(spacing: 16) {
    Button("Create Account") {
        createAccount()
    }
    .buttonStyle(.glassProminent)

    Button("Sign In") {
        signIn()
    }
    .buttonStyle(.glass)
}
```
