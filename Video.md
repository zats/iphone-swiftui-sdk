# Video

A native iPhone video surface for playback, preview, and capture review. Use video where motion is the content, not just decoration. Keep controls obvious and immersion intentional.

## When to Use

- **Playback**
- **Preview clips**
- **Tutorial or walkthrough video**
- **Recorded content review**

Use something else when:

- A still image tells the story
- The video is background decoration

## Quick Start

```swift
import SwiftUI
import AVKit

struct VideoPlayerView: View {
    let player = AVPlayer(url: videoURL)

    var body: some View {
        VideoPlayer(player: player)
    }
}
```

## Usage Patterns

### Inline Preview

```swift
VideoPlayer(player: player)
    .frame(height: 220)
    .clipShape(RoundedRectangle(cornerRadius: 16, style: .continuous))
```

### Full-Screen Playback

Use when video is the primary task.

## Design System Rules

### Do

- Let video own the surface when it is primary
- Keep overlays minimal
- Use playback chrome that stays readable

### Don't

- Autoplay noisy video in dense browsing surfaces
- Add heavy UI over the content without reason

## Required Structure

```swift
VideoPlayer(player: player)
```
