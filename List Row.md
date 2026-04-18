# List Row

A native iPhone list row for settings, navigation, pickers, and item summaries. On iOS 26+, build rows that read clearly at a glance, keep one primary interaction per row, and let the system provide selection, highlight, and accessory behavior.

## When to Use

- **Navigation row**: Opens a detail screen or child settings screen
- **Action row**: Performs an immediate action such as refresh, retry, or sign out
- **State row**: Shows current value or status, with an accessory such as a toggle, badge, or trailing value
- **Static row**: Displays information inside a list without being tappable

Choose the row type by intent:

- Use **`NavigationLink`** when tapping moves deeper in the hierarchy
- Use **`Button`** when tapping performs work in place or presents a sheet/dialog
- Use **plain row content** when the row only informs

## Quick Start

Start with a title, optional subtitle, optional leading visual, and one trailing accessory.

```swift
import SwiftUI

struct SettingsScreen: View {
    var body: some View {
        NavigationStack {
            List {
                NavigationLink {
                    NotificationsScreen()
                } label: {
                    ListRow(
                        title: "Notifications",
                        subtitle: "Push, sounds, badges",
                        leadingSystemImage: "bell.badge",
                        trailingText: "On"
                    )
                }
            }
            .navigationTitle("Settings")
        }
    }
}

private struct NotificationsScreen: View {
    var body: some View {
        Text("Notifications")
            .navigationTitle("Notifications")
            .navigationBarTitleDisplayMode(.inline)
    }
}

private struct ListRow: View {
    let title: String
    let subtitle: String?
    let leadingSystemImage: String?
    let trailingText: String?

    var body: some View {
        HStack(spacing: 12) {
            if let leadingSystemImage {
                Image(systemName: leadingSystemImage)
                    .foregroundStyle(.tint)
                    .frame(width: 28, height: 28)
            }

            VStack(alignment: .leading, spacing: 2) {
                Text(title)

                if let subtitle {
                    Text(subtitle)
                        .font(.subheadline)
                        .foregroundStyle(.secondary)
                }
            }

            Spacer(minLength: 12)

            if let trailingText {
                Text(trailingText)
                    .foregroundStyle(.secondary)
            }
        }
        .padding(.vertical, 4)
    }
}
```

## Usage Patterns

### Navigation Rows

Use `NavigationLink` for drill-in rows. If the whole row navigates, let the system show the chevron.

```swift
List {
    NavigationLink {
        PrivacyScreen()
    } label: {
        ListRow(
            title: "Privacy",
            subtitle: "Permissions and access",
            leadingSystemImage: "hand.raised",
            trailingText: nil
        )
    }
}
```

Strong default:

- Title on the left
- Optional subtitle below
- Optional trailing value for current state
- System chevron from `NavigationLink`

Do not add your own chevron next to a `NavigationLink` label.

### Action Rows

Use `Button` when the row performs an action instead of opening a destination.

```swift
List {
    Button {
        syncNow()
    } label: {
        ListRow(
            title: "Sync Now",
            subtitle: "Last synced 2 minutes ago",
            leadingSystemImage: "arrow.clockwise",
            trailingText: nil
        )
    }
    .buttonStyle(.plain)
}
```

Use this for:

- Refresh
- Retry
- Start scan
- Present sheet or confirmation dialog

### Static Rows

Use static content when the row only communicates information.

```swift
List {
    ListRow(
        title: "Version",
        subtitle: nil,
        leadingSystemImage: nil,
        trailingText: "4.2.1"
    )
}
```

If it is not tappable, do not style it like an action.

### Rows With Toggles

Use a row-owned `Toggle` when the primary task is changing a boolean setting. Do not make the same row also navigate.

```swift
List {
    Toggle(isOn: $faceIDEnabled) {
        ListRowText(
            title: "Face ID",
            subtitle: "Unlock the app quickly"
        )
    }
}
```

```swift
private struct ListRowText: View {
    let title: String
    let subtitle: String?

    var body: some View {
        VStack(alignment: .leading, spacing: 2) {
            Text(title)

            if let subtitle {
                Text(subtitle)
                    .font(.subheadline)
                    .foregroundStyle(.secondary)
            }
        }
        .padding(.vertical, 4)
    }
}
```

Do not put a `Toggle` inside a tappable `Button` or `NavigationLink` row.

### Leading Icons and Avatars

Use a leading visual only when it speeds recognition.

```swift
HStack(spacing: 12) {
    Image(systemName: "creditcard")
        .foregroundStyle(.blue)
        .frame(width: 28, height: 28)

    ListRowText(
        title: "Payment Methods",
        subtitle: "Visa ending in 4242"
    )
}
```

Avatar example:

```swift
HStack(spacing: 12) {
    AsyncImage(url: profile.photoURL) { image in
        image
            .resizable()
            .scaledToFill()
    } placeholder: {
        Color.secondary.opacity(0.15)
    }
    .frame(width: 32, height: 32)
    .clipShape(Circle())

    ListRowText(
        title: profile.name,
        subtitle: profile.email
    )
}
```

Prefer:

- SF Symbols for settings and utility rows
- Avatars for people, teams, and accounts
- No leading visual when it adds noise

### Trailing Values and Accessories

Keep trailing content short and secondary.

```swift
NavigationLink {
    Text("Appearance")
} label: {
    ListRow(
        title: "Appearance",
        subtitle: nil,
        leadingSystemImage: "circle.lefthalf.filled",
        trailingText: "Dark"
    )
}
```

Good trailing accessories:

- Current value: `Off`, `Dark`, `Automatic`
- Status: badge, count, short timestamp
- Toggle, when the row is a toggle row

Avoid long trailing text that fights the title.

## Design System Rules

### Do

- Make the entire row the tap target for navigation and action rows
- Keep row height comfortable; target at least a standard iPhone hit area
- Use one primary interaction per row
- Put primary text first and keep it stable across sibling rows
- Keep subtitles short and genuinely secondary
- Use the system chevron through `NavigationLink`
- Keep trailing values muted with `foregroundStyle(.secondary)`
- Use leading icons or avatars only when they improve scanning
- Use `contentShape(Rectangle())` if custom row content needs a full-width tap target

### Don't

- Put a `Button`, `Menu`, or `Toggle` inside a row that is already fully tappable unless that accessory is the row's only interaction
- Add a custom chevron to rows that already navigate
- Use `onTapGesture` for rows that should be buttons or navigation links
- Mix several different row layouts in one tight group without reason
- Make the subtitle longer or louder than the title
- Use decorative icons for every row
- Hide destructive actions in rows that look identical to safe navigation without clear labeling

## Required Structure

Start with this shape:

```swift
HStack(spacing: 12) {
    leading

    VStack(alignment: .leading, spacing: 2) {
        title
        subtitle
    }

    Spacer(minLength: 12)

    trailing
}
.padding(.vertical, 4)
```

Where:

- `leading` is optional and usually an SF Symbol or avatar
- `title` is required
- `subtitle` is optional and usually one line
- `trailing` is optional and should be compact

Strong defaults:

- One leading visual at most
- One title
- One short subtitle
- One trailing accessory

## Component API

### Row Types

| Type | Use Case |
| --- | --- |
| `NavigationLink` | Opens a child screen |
| `Button` | Performs an action or presents temporary UI |
| `Toggle` | Changes a boolean setting in place |
| Static content | Displays information only |

### Leading Content

- Prefer a single SF Symbol for settings, tools, categories, and system features
- Prefer a circular avatar for people or accounts
- Typical size: about `28...32` points square
- Tint only when the color conveys meaning or improves recognition

### Text Content

- `title`: required, plain-language noun or action
- `subtitle`: optional, short explanation or status
- Keep text readable at larger Dynamic Type sizes
- Expect title to wrap before trailing accessories become dominant

### Trailing Content

- Use short text for current value or status
- Use the system chevron by choosing `NavigationLink`
- Use a `Toggle` when the row changes a boolean in place
- Keep custom trailing controls rare on iPhone

### Tap Targets

- Treat `44x44` points as the floor for touch targets
- For navigation and action rows, the full row should be tappable
- If you build a custom row container, add `.contentShape(Rectangle())`

## Common Patterns

### Settings Row

```swift
NavigationLink {
    Text("Cellular")
        .navigationTitle("Cellular")
        .navigationBarTitleDisplayMode(.inline)
} label: {
    ListRow(
        title: "Cellular",
        subtitle: nil,
        leadingSystemImage: "antenna.radiowaves.left.and.right",
        trailingText: "Off"
    )
}
```

### Account Row

```swift
NavigationLink {
    Text("Account")
        .navigationTitle("Account")
        .navigationBarTitleDisplayMode(.inline)
} label: {
    HStack(spacing: 12) {
        AsyncImage(url: profile.photoURL) { image in
            image.resizable().scaledToFill()
        } placeholder: {
            Color.secondary.opacity(0.15)
        }
        .frame(width: 32, height: 32)
        .clipShape(Circle())

        ListRowText(
            title: profile.name,
            subtitle: profile.email
        )
    }
    .padding(.vertical, 4)
}
```

### Destructive Action Row

```swift
Button(role: .destructive) {
    showingSignOutDialog = true
} label: {
    ListRow(
        title: "Sign Out",
        subtitle: nil,
        leadingSystemImage: "rectangle.portrait.and.arrow.right",
        trailingText: nil
    )
}
.buttonStyle(.plain)
.confirmationDialog("Sign out?", isPresented: $showingSignOutDialog) {
    Button("Sign Out", role: .destructive) {
        signOut()
    }
}
```

### Toggle Row

```swift
Toggle(isOn: $backgroundRefreshEnabled) {
    ListRowText(
        title: "Background App Refresh",
        subtitle: "Keep content up to date"
    )
}
```
