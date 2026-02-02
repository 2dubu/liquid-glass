# Glass Struct API Reference

## Overview

The `Glass` struct defines the configuration of the Liquid Glass material.

```swift
struct Glass {
    static var regular: Glass
    static var clear: Glass
    static var identity: Glass

    func tint(_ color: Color?) -> Glass
    func interactive(_ isEnabled: Bool = true) -> Glass
}
```

## Static Properties

### `.regular`
The default glass effect with standard translucency. Use for most UI controls.

```swift
Button("Action") { }
    .glassEffect(.regular)

// Or simply (regular is default)
Button("Action") { }
    .glassEffect()
```

### `.clear`
A more transparent variant designed for media-heavy backgrounds.

```swift
// Use over photos, videos, maps
Button("Play") { }
    .glassEffect(.clear)
```

**Important**: When using `.clear`, add a dimming layer or other treatment beneath the glass to ensure content legibility.

```swift
// Recommended: Add dimming for better readability
ZStack {
    Image("background")
    Color.black.opacity(0.3) // Dimming layer
    Button("Play") { }
        .glassEffect(.clear)
}
```

### `.identity`
Disables the glass effect. Used for accessibility when user has "Reduce Transparency" enabled.

```swift
@Environment(\.accessibilityReduceTransparency) var reduceTransparency

Button("Action") { }
    .glassEffect(reduceTransparency ? .identity : .regular)
```

## Instance Methods

### `.tint(_:)`
Applies a color tint to the glass effect.

```swift
// Basic tint
.glassEffect(.regular.tint(.blue))

// With opacity
.glassEffect(.regular.tint(.purple.opacity(0.6)))
```

**Best Practice**: Use tinting for semantic meaning (primary action, state), NOT decoration. Apply selectively for call-to-action only.

```swift
// Secondary action - no tint
Button("Cancel") { }
    .glassEffect()

// Primary action - tinted
Button("Save") { }
    .glassEffect(.regular.tint(.blue))
```

### `.interactive(_:)`
Makes glass respond to touch and pointer interactions with scaling, bouncing, and shimmering effects.

```swift
func interactive(_ isEnabled: Bool = true) -> Glass
```

```swift
Button("Tap Me") { }
    .glassEffect(.regular.interactive())

// Conditionally disable interactivity
Button("Tap Me") { }
    .glassEffect(.regular.interactive(isInteractive))
```

**Availability**: Supported on iOS 26.0+, iPadOS 26.0+, Mac Catalyst 26.0+, macOS 26.0+, tvOS 26.0+, and watchOS 26.0+.

**Performance Warning**: Interactive effects are GPU-intensive. Use only for important interactive elements.

## Chaining Modifiers

Modifiers can be chained in any order:

```swift
// These are equivalent
.glassEffect(.regular.tint(.orange).interactive())
.glassEffect(.regular.interactive().tint(.orange))
```

## Common Patterns

### Floating Action Button
```swift
Button(action: performAction) {
    Image(systemName: "plus")
        .font(.title2)
        .padding(16)
}
.glassEffect(.regular.tint(.blue).interactive())
```

### Media Overlay Controls
```swift
HStack {
    Button("Previous") { }
    Button("Play") { }
    Button("Next") { }
}
.glassEffect(.clear.interactive())
```

### Contextual Tinting
```swift
Button(isDestructive ? "Delete" : "Save") { }
    .glassEffect(.regular.tint(isDestructive ? .red : .blue))
```
