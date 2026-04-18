# Map

A native iPhone map surface for location, context, and spatial exploration. Use a map when geography matters to the task. Keep overlays light and let the map stay legible.

## When to Use

- **Places**
- **Routes**
- **Nearby search**
- **Location selection**

Use something else when:

- A simple address is enough
- The spatial view adds no value

## Quick Start

```swift
import SwiftUI
import MapKit

struct LocationMapView: View {
    @State private var position: MapCameraPosition = .automatic

    var body: some View {
        Map(position: $position)
    }
}
```

## Usage Patterns

### Pin a Place

```swift
Map {
    Marker("Office", coordinate: officeCoordinate)
}
```

### Map with Bottom Summary

Use a bottom sheet or bottom call to action for supporting detail instead of crowding the map.

## Design System Rules

### Do

- Keep controls minimal
- Let the map carry the spatial meaning
- Use overlays only for high-value actions

### Don't

- Cover the map with several floating panels
- Use a map where a static address would be clearer

## Required Structure

```swift
Map {
    Marker("Place", coordinate: coordinate)
}
```
