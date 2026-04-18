# Full-Screen Cover

A native iPhone immersive presentation for tasks that should temporarily replace normal app chrome. Use `fullScreenCover` for onboarding, capture, playback, or auth flows that need full focus.

## When to Use

- **Onboarding**
- **Auth**
- **Camera or scanner**
- **Immersive playback**

Use something else when:

- The task is short and scoped: use a sheet
- The next screen belongs in navigation: push it

## Quick Start

```swift
import SwiftUI

struct RootView: View {
    @State private var showingOnboarding = true

    var body: some View {
        MainAppView()
            .fullScreenCover(isPresented: $showingOnboarding) {
                OnboardingView()
            }
    }
}
```

## Design System Rules

### Do

- Use full-screen cover for immersive or blocking flows
- Provide an obvious exit when appropriate
- Keep the flow self-contained

### Don't

- Use it for every form
- Hide normal tasks behind a full-screen cover

## Required Structure

```swift
.fullScreenCover(isPresented: $showingCover) {
    FlowView()
}
```
