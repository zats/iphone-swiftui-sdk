# Date Picker

A native iPhone control for choosing a date, time, or both. Use `DatePicker` when scheduling, filtering by date, or setting reminders. Keep the displayed granularity aligned with the actual task.

## When to Use

- **Scheduling**
- **Deadlines and reminders**
- **Date-based filters**
- **Birthdays and profile details**

Use something else when:

- The user is choosing from a few named presets
- Time is irrelevant and should stay hidden

## Quick Start

```swift
import SwiftUI

struct ReminderDateView: View {
    @State private var dueDate = Date()

    var body: some View {
        DatePicker("Due Date", selection: $dueDate, displayedComponents: [.date, .hourAndMinute])
    }
}
```

## Usage Patterns

### Date Only

```swift
DatePicker("Birthday", selection: $birthday, displayedComponents: .date)
```

### Time Only

```swift
DatePicker("Reminder Time", selection: $time, displayedComponents: .hourAndMinute)
```

### Form Scheduling

Use inside forms for straightforward entry.

## Design System Rules

### Do

- Match displayed components to the task
- Use the system date picker first
- Keep labels specific

### Don't

- Ask for time when only date matters
- Hide what timezone or day context means when it matters

## Required Structure

```swift
DatePicker("Label", selection: $date, displayedComponents: .date)
```

## Component API

| API | Use Case |
| --- | --- |
| `DatePicker` | Date and time selection |
| `displayedComponents` | Choose date, time, or both |

## Common Patterns

### Deadline Field

```swift
DatePicker("Deadline", selection: $deadline, displayedComponents: [.date, .hourAndMinute])
```
