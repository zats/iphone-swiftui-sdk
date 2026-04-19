# Localization

A native iPhone localization baseline for SwiftUI apps. Localization is more than translated strings. It includes language, region, plural rules, grammar, data formatting, layout direction, assets, sorting, search, and testing.

## When to Use

- **Every app**
- **Every user-facing string**
- **Dates, times, numbers, currency, and units**
- **Search and sorting**
- **Right-to-left languages**
- **Localized assets**

Use something else when:

- The text is debug-only or internal
- You are translating user-generated content at runtime instead of localizing your app UI

## Quick Start

Start with localizable text, locale-aware formatting, and string catalogs.

```swift
import SwiftUI

struct OrderSummaryView: View {
    let itemCount: Int
    let total: Decimal
    let deliveryDate: Date

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("Order Summary", comment: "Title above checkout totals")
                .font(.headline)

            Text("\(itemCount) items")

            Text(deliveryDate, format: .dateTime.month().day().hour().minute())
                .foregroundStyle(.secondary)

            Text(total, format: .currency(code: "USD"))
        }
    }
}
```

What this gets right:

- SwiftUI text is extractable into a string catalog
- The count can vary by plural in the string catalog
- The date uses locale-aware formatting
- The currency uses a currency format style instead of string building

## Usage Patterns

### Localize Text With Localizable Types

Use `Text` with string literals in SwiftUI views. Use `LocalizedStringResource` or `String(localized:)` when the string moves through app code.

```swift
struct EmptyProjectsView: View {
    let title: LocalizedStringResource = "No Projects"
    let message: LocalizedStringResource = "Create your first project to get started."

    var body: some View {
        ContentUnavailableView {
            Label(title, systemImage: "folder")
        } description: {
            Text(message)
        }
    }
}
```

For non-view code:

```swift
let title = String(localized: "Downloads")
```

Use `LocalizedStringResource` when:

- a view model passes text into a view
- you want comments, tables, or a custom default value
- you want one type that stays localizable until display

### Keep String Catalogs as the Source of Truth

Use a string catalog for translations, plurals, and device-specific text. Add comments wherever wording could be ambiguous.

```swift
Text("Edit", comment: "Button title that switches the screen into editing mode")
```

Use separate tables only when the catalog is large enough to need structure.

```swift
Text("Explore", tableName: "Navigation")
String(localized: "Clear", table: "Buttons")
```

If the string lives outside the main app target, point lookup at the correct bundle.

```swift
Text("Collections", bundle: #bundle, comment: "Section title above saved collections")
```

### Separate Language From Locale

Do not treat app language, region, calendar, numbering system, and measurement system as one setting.

- language chooses translated text
- locale drives formatting
- region can change currency, units, and first day of week
- numbering system can change visible digits

Test combinations like:

- English text with `en_GB` formatting
- Arabic text with Arabic or Western digits
- metric and US measurement output for the same feature

In previews, drive formatting with an explicit locale.

```swift
#Preview("English UK") {
    ContentView()
        .environment(\.locale, Locale(identifier: "en_GB"))
}
```

### Format Data Instead of Building Strings

Never hand-build user-facing dates, times, currency, numbers, percentages, units, or lists.

```swift
VStack(alignment: .leading, spacing: 8) {
    Text(Date.now, format: .dateTime.year().month().day())
    Text(0.15, format: .percent)
    Text(Decimal(1234.56), format: .currency(code: "USD"))
    Text(Measurement(value: 8, unit: UnitLength.feet),
         format: .measurement(width: .abbreviated))
    Text(["Small", "Medium", "Large"].formatted(.list(type: .and)))
}
```

Use this for:

- locale-specific separators
- 12/24-hour time
- calendar conventions
- measurement conversion
- conjunctions like `and` and `or`

For money, use `Decimal`.

```swift
let subtotal: Decimal = 19.99
```

### Prefer Relative and Duration Styles for Time-Based Copy

Use the built-in relative and duration styles instead of English phrases.

```swift
Text(deadline, format: .relative(presentation: .named))
Text(Duration.seconds(2000), format: .time(pattern: .minuteSecond))
```

Treat relative date output like standalone text. Do not embed strings like `yesterday` or `in 2 weeks` inside bigger manually written sentences.

### Handle Plurals in the Catalog

Start with interpolation in code, then vary the string by plural in the string catalog.

```swift
Text("\(photoCount) photos")
```

Use this for:

- item counts
- duration units
- progress summaries
- any noun that changes with quantity

Do not hardcode English-style `1 item` / `n items` logic in Swift.

### Use Grammar Agreement When the Language Needs It

Some languages need more than plural handling. Use localized attributed strings and inflection when nouns, adjectives, or pronouns must agree.

```swift
let order = AttributedString(
    localized: "Add ^[\(quantity) \(sizeName) \(foodName)](inflect: true) to your order"
)
```

Use term-of-address APIs when the wording depends on grammatical gender or pronoun preference.

This is for:

- pronoun substitution
- adjective agreement
- noun agreement in inflected languages

### Keep Layout Direction-Aware

Let SwiftUI mirror the interface. Use `leading` and `trailing`, not `left` and `right`.

```swift
HStack(spacing: 12) {
    VStack(alignment: .leading, spacing: 4) {
        Text(title)
        Text(subtitle)
            .foregroundStyle(.secondary)
    }

    Spacer()

    Image(systemName: "chevron.forward")
}
```

For right-to-left languages:

- directional controls should flip
- progress direction should flip
- back and forward affordances should flip
- photos, logos, and general artwork usually should not flip
- numbers inside one number stay in their normal digit order

Use paragraphs carefully. A paragraph should align to its own language, not blindly to the surrounding interface direction.

### Localize Search and Sort Behavior

User-visible search and sort should follow locale-aware comparison rules.

```swift
let results = items.filter { item in
    item.title.localizedStandardContains(query)
}

let sorted = items.sorted(using: KeyPathComparator(\.title, comparator: .localizedStandard))
```

Use locale-aware comparison for:

- file names
- titles
- search suggestions
- list sorting

Do not use plain binary or ASCII-style comparison for UI ordering.

### Localize Assets When They Carry Language

Localize images, symbols, or colors only when the asset itself changes by language or region.

Good fits:

- images containing text
- region-specific symbols or maps
- artwork whose meaning depends on reading direction

Bad fits:

- decorative photos
- logos that should stay stable
- ordinary icons that the system already mirrors

### Test Early With Real Locales and Pseudolanguages

Test localization while building the screen, not after translation lands.

In SwiftUI previews:

```swift
#Preview("French") {
    ContentView()
        .environment(\.locale, Locale(identifier: "fr_FR"))
}

#Preview("Arabic RTL") {
    ContentView()
        .environment(\.locale, Locale(identifier: "ar"))
        .environment(\.layoutDirection, .rightToLeft)
}
```

Also test in Xcode with:

- Double-Length Pseudolanguage
- Right-to-Left Pseudolanguage
- Accented Pseudolanguage
- Bounded String Pseudolanguage
- Show non-localized strings

These catch:

- truncation
- hardcoded English
- broken mirroring
- text baked into layout too tightly

## Design System Rules

### Do

- Localize every user-facing string
- Use string catalogs as the default workflow
- Use `LocalizedStringResource` when passing localizable text through Swift code
- Add translator comments when wording is ambiguous
- Use format styles for dates, times, numbers, currency, units, durations, and lists
- Use `Decimal` for currency values
- Use interpolation plus plural variants instead of English plural logic
- Use `leading` and `trailing` layout semantics
- Test with real locales and pseudolanguages
- Localize assets only when the asset meaning actually changes
- Use locale-aware search and sorting

### Don't

- Concatenate translated sentence fragments
- Hardcode separators, date order, or unit abbreviations
- Treat localization as strings only
- Assume left-to-right layout
- Bake user-facing text into images when live text would work
- Sort or search visible content with plain string comparison
- Use `Float` or `Double` for money
- Wait for translators before testing layout
- Infer language from region or region from language
- Translate App Store metadata and app UI as if they are the same workflow

## Required Structure

Prefer this baseline:

```swift
struct SummaryView: View {
    let title: LocalizedStringResource
    let itemCount: Int
    let total: Decimal
    let dueDate: Date

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text(title)
                .font(.headline)

            Text("\(itemCount) items")

            Text(dueDate, format: .dateTime.month().day().year())
                .foregroundStyle(.secondary)

            Text(total, format: .currency(code: "USD"))
        }
    }
}
```

Minimum rules:

- text comes from localizable APIs
- counts are ready for plural variants
- formatted data uses format styles
- layout uses direction-aware alignment
- previews cover at least one long-text locale and one RTL locale

## Component API

### Text and Strings

| API | Use Case |
| --- | --- |
| `Text("...", comment: ...)` | Localizable SwiftUI text with translator context |
| `LocalizedStringResource` | Passing localizable text through Swift code |
| `String(localized:)` | Localizable non-view strings |
| `AttributedString(localized:)` | Localized rich text, links, and grammar agreement |

### Formatting

| API | Use Case |
| --- | --- |
| `.dateTime` | Locale-aware date and time |
| `.relative(...)` | Relative dates like `yesterday` or `in 2 weeks` |
| `.currency(code:)` | Currency formatting |
| `.percent` | Percent formatting |
| `.measurement(...)` | Units and unit conversion |
| `.list(type:width:)` | Locale-aware lists |
| `PersonNameComponents.FormatStyle` | Person names |

### Environment and Testing

| API | Use Case |
| --- | --- |
| `@Environment(\\.locale)` | Use the current locale in a view |
| `.environment(\\.locale, ...)` | Preview or test another locale |
| `@Environment(\\.layoutDirection)` | Read current layout direction |
| `.environment(\\.layoutDirection, .rightToLeft)` | Preview RTL |

### Search and Sorting

| API | Use Case |
| --- | --- |
| `localizedStandardContains(...)` | User-visible search matching |
| `localizedStandardCompare(...)` | Finder-like string ordering |
| `SortComparator.localized` | Locale-aware comparison |
| `SortComparator.localizedStandard` | Locale-aware, numeric-friendly comparison |

### Resources

| API | Use Case |
| --- | --- |
| `bundle: #bundle` | String lookup outside the main target |
| `Bundle.module` | Swift package resources |
| Localized asset catalogs | Region- or language-specific assets |

## Common Patterns

### Localized Screen Copy

```swift
struct WelcomeView: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("Welcome", comment: "Home screen title for a signed-in user")
                .font(.title2.weight(.semibold))

            Text("Your recent activity appears here.")
                .foregroundStyle(.secondary)
        }
    }
}
```

### Localized Price and Delivery Time

```swift
VStack(alignment: .leading, spacing: 4) {
    Text(total, format: .currency(code: "USD"))
    Text(arrivalDate, format: .relative(presentation: .named))
        .foregroundStyle(.secondary)
}
```

### Locale-Aware Measurement

```swift
Text(distance, format: .measurement(width: .abbreviated))
```

### RTL Preview

```swift
#Preview("Arabic") {
    DetailView()
        .environment(\.locale, Locale(identifier: "ar"))
        .environment(\.layoutDirection, .rightToLeft)
}
```

### Locale-Aware Search Results

```swift
let filtered = items.filter { item in
    item.title.localizedStandardContains(query)
}

let ordered = filtered.sorted(using: KeyPathComparator(\.title, comparator: .localizedStandard))
```
