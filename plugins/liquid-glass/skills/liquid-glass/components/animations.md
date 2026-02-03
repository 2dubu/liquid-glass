# Glass Animations Component

## Overview

Glass elements can be animated using SwiftUI's animation system. This includes expanding/collapsing, rotating, merging, and continuous loop animations.

## Expanding Glass Animation

```swift
struct ExpandingGlassDemo: View {
    @State private var isExpanded = false

    var body: some View {
        Circle()
            .fill(.clear)
            .frame(width: isExpanded ? 150 : 80, height: isExpanded ? 150 : 80)
            .glassEffect(.regular.tint(.blue))
            .onTapGesture {
                withAnimation(.spring(response: 0.4, dampingFraction: 0.6)) {
                    isExpanded.toggle()
                }
            }
    }
}
```

## Auto-Expanding Loop Animation

```swift
struct AutoExpandingGlass: View {
    @State private var isExpanded = false

    var body: some View {
        Circle()
            .fill(.clear)
            .frame(width: isExpanded ? 120 : 60, height: isExpanded ? 120 : 60)
            .glassEffect(.regular.tint(.purple))
            .task {
                while true {
                    try? await Task.sleep(for: .seconds(1.5))
                    withAnimation(.easeInOut(duration: 0.8)) {
                        isExpanded.toggle()
                    }
                }
            }
    }
}
```

## Rotating Glass Animation

```swift
struct RotatingGlassDemo: View {
    @State private var rotation: Double = 0

    var body: some View {
        RoundedRectangle(cornerRadius: 16)
            .fill(.clear)
            .frame(width: 80, height: 80)
            .glassEffect(.regular.tint(.orange))
            .rotationEffect(.degrees(rotation))
            .task {
                while true {
                    try? await Task.sleep(for: .seconds(0.8))
                    withAnimation(.easeInOut(duration: 0.5)) {
                        rotation += 90
                    }
                }
            }
    }
}
```

## Merging Glass Animation

Two glass elements that merge and separate:

```swift
struct MergingGlassDemo: View {
    @State private var isMerged = false

    var body: some View {
        HStack(spacing: isMerged ? -20 : 40) {
            Circle()
                .fill(.clear)
                .frame(width: 60, height: 60)
                .glassEffect(.regular.tint(.green))

            Circle()
                .fill(.clear)
                .frame(width: 60, height: 60)
                .glassEffect(.regular.tint(.mint))
        }
        .task {
            while true {
                try? await Task.sleep(for: .seconds(1.2))
                withAnimation(.spring(response: 0.5, dampingFraction: 0.7)) {
                    isMerged.toggle()
                }
            }
        }
    }
}
```

## Pulse Animation

```swift
struct PulseGlassDemo: View {
    @State private var scale: CGFloat = 1.0
    @State private var opacity: Double = 1.0

    var body: some View {
        ZStack {
            // Pulse ring
            Circle()
                .fill(.clear)
                .frame(width: 100, height: 100)
                .glassEffect(.regular.tint(.blue.opacity(0.5)))
                .scaleEffect(scale)
                .opacity(opacity)

            // Center button
            Circle()
                .fill(.clear)
                .frame(width: 60, height: 60)
                .glassEffect(.regular.tint(.blue).interactive())
        }
        .task {
            while true {
                withAnimation(.easeOut(duration: 1.5)) {
                    scale = 1.5
                    opacity = 0
                }
                try? await Task.sleep(for: .seconds(1.5))
                scale = 1.0
                opacity = 1.0
            }
        }
    }
}
```

## Floating Animation

```swift
struct FloatingGlassDemo: View {
    @State private var offset: CGFloat = 0

    var body: some View {
        RoundedRectangle(cornerRadius: 20)
            .fill(.clear)
            .frame(width: 120, height: 80)
            .glassEffect()
            .offset(y: offset)
            .task {
                while true {
                    withAnimation(.easeInOut(duration: 2.0)) {
                        offset = -10
                    }
                    try? await Task.sleep(for: .seconds(2.0))
                    withAnimation(.easeInOut(duration: 2.0)) {
                        offset = 10
                    }
                    try? await Task.sleep(for: .seconds(2.0))
                }
            }
    }
}
```

## Morphing with Animation

```swift
struct MorphingShapeDemo: View {
    @State private var isCircle = true

    var body: some View {
        Group {
            if isCircle {
                Circle()
                    .fill(.clear)
                    .frame(width: 100, height: 100)
            } else {
                RoundedRectangle(cornerRadius: 20)
                    .fill(.clear)
                    .frame(width: 140, height: 80)
            }
        }
        .glassEffect(.regular.tint(.purple))
        .onTapGesture {
            withAnimation(.spring(response: 0.4, dampingFraction: 0.7)) {
                isCircle.toggle()
            }
        }
    }
}
```

## Staggered Animation

```swift
struct StaggeredGlassDemo: View {
    @State private var isVisible = false
    let icons = ["star", "heart", "bookmark", "bell"]

    var body: some View {
        HStack(spacing: 16) {
            ForEach(Array(icons.enumerated()), id: \.offset) { index, icon in
                Image(systemName: icon)
                    .font(.title2)
                    .padding(14)
                    .glassEffect(.regular.interactive())
                    .scaleEffect(isVisible ? 1 : 0)
                    .opacity(isVisible ? 1 : 0)
                    .animation(
                        .spring(response: 0.4, dampingFraction: 0.7)
                        .delay(Double(index) * 0.1),
                        value: isVisible
                    )
            }
        }
        .onAppear {
            isVisible = true
        }
    }
}
```

## Animation Best Practices

### Recommended Animations
```swift
// Spring - best for interactive elements
withAnimation(.spring(response: 0.4, dampingFraction: 0.7)) { }

// Bouncy - best for morphing
withAnimation(.bouncy) { }

// EaseInOut - best for loops
withAnimation(.easeInOut(duration: 0.5)) { }
```

### Avoid
```swift
// Linear feels mechanical
withAnimation(.linear) { }

// Too fast can cause jank
withAnimation(.easeInOut(duration: 0.1)) { }
```

## Key Points

- Use `.task` for continuous loop animations
- Spring animations work best for glass elements
- Glass elements blend when overlapping during animation
- Use staggered delays for sequential reveals
- Combine scale, opacity, and offset for rich effects
- Keep animations smooth (0.3-0.8s duration typically)
- Test performance with multiple animated glass elements
