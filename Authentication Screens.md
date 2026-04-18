# Authentication Screens

A native iPhone auth flow for sign in, sign up, and recovery. Keep auth calm, direct, and lightweight. Lead with the shortest path into the product, then reveal secondary paths only when needed.

## When to Use

- **Required identity**: Sync, collaboration, purchases, personal data
- **Returning user sign in**: Email, password, passkey, or social sign in
- **New user setup**: Create account, continue with Apple, verify email
- **Recovery**: Forgot password, magic link, re-authentication

## Quick Start

```swift
import SwiftUI

struct SignInView: View {
    @State private var email = ""
    @State private var password = ""

    var body: some View {
        NavigationStack {
            Form {
                Section {
                    TextField("Email", text: $email)
                        .textInputAutocapitalization(.never)
                        .keyboardType(.emailAddress)
                        .autocorrectionDisabled()

                    SecureField("Password", text: $password)
                }

                Section {
                    Button("Sign In") {
                        signIn()
                    }
                    .buttonStyle(.glassProminent)
                }

                Section {
                    Button("Forgot Password?") {
                        recover()
                    }
                    .buttonStyle(.plain)
                }
            }
            .navigationTitle("Sign In")
        }
    }

    private func signIn() {}
    private func recover() {}
}
```

## Usage Patterns

### Primary Auth Path

Show the main path first. For most apps:

- Continue with Apple
- Email + password or magic link
- Secondary auth options below

### Sign In vs Sign Up

Do not mix both forms on one dense screen. Make one primary, one secondary.

```swift
VStack(spacing: 16) {
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

### Recovery

Recovery should be low-friction and low-noise.

```swift
Button("Forgot Password?") {
    showRecovery = true
}
.buttonStyle(.plain)
```

### Social and Passkey Auth

Use them when they meaningfully reduce friction. Do not show six providers by default.

## Design System Rules

### Do

- Show one primary auth path
- Keep fields minimal
- Use the keyboard type that matches the field
- Keep recovery obvious
- Put legal links in footnote-scale copy
- Move directly into the app after success

### Don't

- Put sign in and sign up forms on one crowded screen
- Ask for profile setup before account creation succeeds
- Hide recovery behind menus
- Overdecorate the auth screen

## Required Structure

```swift
Form {
    Section {
        TextField("Email", text: $email)
        SecureField("Password", text: $password)
    }

    Section {
        Button("Sign In") {
            signIn()
        }
    }
}
```

## Component API

| API | Use Case |
| --- | --- |
| `TextField` | Email, code, username |
| `SecureField` | Passwords |
| `Form` | Standard auth layout |
| `sheet` | Recovery or verification |

## Common Patterns

### Magic Link Entry

```swift
TextField("Email", text: $email)
    .keyboardType(.emailAddress)
    .textInputAutocapitalization(.never)

Button("Email Me a Link") {
    sendMagicLink()
}
.buttonStyle(.glassProminent)
```
