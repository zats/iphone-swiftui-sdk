# Secure Field

A native iPhone secure text input for passwords, passphrases, and verification secrets. Use `SecureField` when typed content should stay hidden by default.

## When to Use

- **Passwords**
- **Passphrases**
- **Sensitive tokens**

Use something else when:

- The input is not secret: use `TextField`
- The value needs multiline editing

## Quick Start

```swift
import SwiftUI

struct PasswordField: View {
    @State private var password = ""

    var body: some View {
        SecureField("Password", text: $password)
    }
}
```

## Usage Patterns

### Sign In

```swift
SecureField("Password", text: $password)
    .textContentType(.password)
```

### New Password

```swift
SecureField("New Password", text: $newPassword)
    .textContentType(.newPassword)
```

### Reveal Toggle

If you add show/hide, keep the layout stable.

```swift
HStack {
    if isRevealed {
        TextField("Password", text: $password)
    } else {
        SecureField("Password", text: $password)
    }

    Button(isRevealed ? "Hide" : "Show") {
        isRevealed.toggle()
    }
}
```

## Design System Rules

### Do

- Hide secret content by default
- Use the correct `textContentType`
- Offer reveal only when it helps completion
- Keep auth flows simple

### Don't

- Use secure entry for non-secret values
- Make reveal state confusing
- Over-style password rows

## Required Structure

```swift
SecureField("Password", text: $password)
```

## Component API

| API | Use Case |
| --- | --- |
| `SecureField` | Hidden text entry |
| `.textContentType(.password)` | Sign in |
| `.textContentType(.newPassword)` | Account creation |

## Common Patterns

### Password Confirmation

```swift
SecureField("Password", text: $password)
SecureField("Confirm Password", text: $confirmPassword)
```
