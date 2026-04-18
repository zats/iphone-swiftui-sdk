# Bottom Call To Action

A native iPhone persistent action bar above the home indicator. Use a bottom call to action when the main next step should stay visible while content scrolls.

## When to Use

- **Checkout**
- **Booking**
- **Apply / Continue**
- **Large forms with one final next step**

Use something else when:

- The action is local to a small section
- The flow already has an obvious toolbar confirm action

## Quick Start

```swift
import SwiftUI

struct CheckoutView: View {
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
            .background(.regularMaterial)
        }
    }

    private func placeOrder() {}
}
```

## Design System Rules

### Do

- Keep one primary action
- Use `safeAreaInset(edge: .bottom)`
- Let content scroll behind or above it cleanly

### Don't

- Stack several buttons in the bottom bar
- Use it when the action is not clearly primary

## Required Structure

```swift
.safeAreaInset(edge: .bottom) {
    Button("Continue") { continueFlow() }
        .buttonStyle(.glassProminent)
}
```
