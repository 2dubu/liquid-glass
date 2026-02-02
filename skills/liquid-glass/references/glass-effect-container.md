# GlassEffectContainer Reference

## Overview

`GlassEffectContainer` is a SwiftUI container view that combines multiple Liquid Glass shapes into a single unified shape. It enables smooth morphing transitions between elements.

## Why Use GlassEffectContainer?

When multiple `.glassEffect()` views are placed together, they need to be coordinated:

```swift
// ❌ Without container - each glass is isolated
HStack {
    Button("A") { }.glassEffect()
    Button("B") { }.glassEffect()
}

// ✅ With container - unified glass behavior
GlassEffectContainer {
    HStack {
        Button("A") { }.glassEffect()
        Button("B") { }.glassEffect()
    }
}
```

## What GlassEffectContainer Does

When you place `.glassEffect()` views inside this container:

1. **Blends overlapping shapes** automatically
2. **Applies consistent blur and lighting** effects
3. **Enables smooth morphing** transitions during layout changes
4. **Improves rendering performance** (single sampling region)

## API Signature

```swift
@MainActor @preconcurrency
struct GlassEffectContainer<Content> where Content: View {
    init(
        spacing: CGFloat? = nil,
        @ViewBuilder content: () -> Content
    )
}
```

**Availability**: iOS 26.0+, iPadOS 26.0+, Mac Catalyst 26.0+, macOS 26.0+, tvOS 26.0+, watchOS 26.0+

## Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `spacing` | `CGFloat?` | `nil` | Distance within which elements blend together |

## Basic Usage

```swift
GlassEffectContainer {
    HStack(spacing: 12) {
        Button("Edit") { }
            .glassEffect()
        Button("Share") { }
            .glassEffect()
        Button("Delete") { }
            .glassEffect()
    }
}
```

## Spacing Parameter

The `spacing` parameter controls how close elements need to be to visually blend:

```swift
// Elements within 40pt will blend together
GlassEffectContainer(spacing: 40) {
    HStack(spacing: 20) {
        Button("A") { }.glassEffect()  // These will blend
        Button("B") { }.glassEffect()  // These will blend
    }
}

// Larger spacing = more aggressive blending
GlassEffectContainer(spacing: 100) {
    // Elements further apart will still blend
}
```

## Floating Toolbar Example

```swift
struct FloatingToolbar: View {
    var body: some View {
        GlassEffectContainer {
            HStack(spacing: 16) {
                Button(action: {}) {
                    Image(systemName: "pencil")
                }
                .glassEffect()

                Button(action: {}) {
                    Image(systemName: "trash")
                }
                .glassEffect()

                Button(action: {}) {
                    Image(systemName: "square.and.arrow.up")
                }
                .glassEffect()
            }
            .padding()
        }
    }
}
```

## With Morphing Animations

Combine with `@Namespace` and `glassEffectID` for smooth transitions:

```swift
struct MorphingToolbar: View {
    @Namespace private var namespace
    @State private var isExpanded = false

    var body: some View {
        GlassEffectContainer {
            HStack(spacing: 12) {
                Button(action: { withAnimation(.bouncy) { isExpanded.toggle() }}) {
                    Image(systemName: "pencil")
                }
                .glassEffect()
                .glassEffectID("pencil", in: namespace)

                if isExpanded {
                    Button(action: {}) {
                        Image(systemName: "eraser")
                    }
                    .glassEffect()
                    .glassEffectID("eraser", in: namespace)

                    Button(action: {}) {
                        Image(systemName: "paintbrush")
                    }
                    .glassEffect()
                    .glassEffectID("brush", in: namespace)
                }
            }
        }
    }
}
```

## Nested Containers

You can nest containers for complex layouts:

```swift
GlassEffectContainer {
    VStack {
        GlassEffectContainer {
            HStack {
                // Top toolbar items
            }
        }

        Spacer()

        GlassEffectContainer {
            HStack {
                // Bottom toolbar items
            }
        }
    }
}
```

## Performance Benefits

Using `GlassEffectContainer` creates a **single sampling region** for all child glass effects, which:

- Reduces GPU overhead
- Prevents visual artifacts
- Enables optimized blur calculations

```swift
// ❌ Inefficient - 5 separate glass calculations
VStack {
    Text("One").glassEffect()
    Text("Two").glassEffect()
    Text("Three").glassEffect()
    Text("Four").glassEffect()
    Text("Five").glassEffect()
}

// ✅ Efficient - 1 unified glass calculation
GlassEffectContainer {
    VStack {
        Text("One").glassEffect()
        Text("Two").glassEffect()
        Text("Three").glassEffect()
        Text("Four").glassEffect()
        Text("Five").glassEffect()
    }
}
```

## Common Use Cases

1. **Custom Navigation** - Floating action buttons, toolbars
2. **Grouped Controls** - Related buttons that blend when close
3. **Morphing UI** - Expandable/collapsible control groups
4. **Floating Overlays** - Controls over content
