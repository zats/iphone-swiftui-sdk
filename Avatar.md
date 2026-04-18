# Avatar

A native iPhone identity image for people, teams, and accounts. Use avatars to signal ownership and identity quickly without making every row feel social.

## When to Use

- **People**
- **Teams**
- **Accounts**
- **Collaborators**

Use something else when:

- The content is not identity-based
- A symbol already communicates the role well enough

## Quick Start

```swift
import SwiftUI

AsyncImage(url: user.photoURL) { image in
    image
        .resizable()
        .scaledToFill()
} placeholder: {
    Color.secondary.opacity(0.12)
}
.frame(width: 40, height: 40)
.clipShape(Circle())
```

## Usage Patterns

### List Row Avatar

```swift
HStack(spacing: 12) {
    AvatarView(url: user.photoURL, initials: user.initials)
    VStack(alignment: .leading) {
        Text(user.name)
        Text(user.email)
            .foregroundStyle(.secondary)
    }
}
```

### Fallback Initials

```swift
Text(user.initials)
    .font(.subheadline.weight(.semibold))
    .frame(width: 40, height: 40)
    .background(.thinMaterial, in: Circle())
```

## Design System Rules

### Do

- Use circular avatars by default
- Provide a graceful initials fallback
- Keep sizes consistent inside one screen

### Don't

- Use avatars where identity is irrelevant
- Mix many avatar sizes in one tight list

## Required Structure

```swift
AvatarView(url: user.photoURL, initials: user.initials)
    .frame(width: 40, height: 40)
```

## Common Patterns

### Account Row

```swift
HStack(spacing: 12) {
    AvatarView(url: user.photoURL, initials: user.initials)
    Text(user.name)
}
```
