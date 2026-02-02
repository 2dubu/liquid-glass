# Performance Optimization Reference

## Overview

Liquid Glass effects are GPU-intensive. Proper optimization ensures smooth 60fps performance.

## Key Performance Principles

1. **Use GlassEffectContainer** for multiple glass elements
2. **Limit glass layers** per screen (4-6 maximum)
3. **Use `.interactive()` sparingly**
4. **Implement Equatable** for glass views
5. **Avoid nested glass effects**

## GlassEffectContainer Performance

### Single Sampling Region

`GlassEffectContainer` creates ONE sampling region for all children:

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

## Limiting Glass Layers

Each glass layer adds computational cost:

```swift
// ❌ Too many glass layers
ZStack {
    BackgroundView()
    GlassLayer1().glassEffect()
    GlassLayer2().glassEffect()
    GlassLayer3().glassEffect()
    GlassLayer4().glassEffect()
    GlassLayer5().glassEffect()
    GlassLayer6().glassEffect()
    GlassLayer7().glassEffect()  // Performance impact!
}

// ✅ Reasonable glass usage
ZStack {
    BackgroundView()
    GlassEffectContainer {
        ToolbarView()  // One logical glass group
    }
}
```

## Interactive Effects

`.interactive()` is the most expensive modifier:

```swift
// ❌ Expensive - interactive on everything
ForEach(items) { item in
    ItemView(item)
        .glassEffect(.regular.interactive())  // GPU heavy!
}

// ✅ Selective - interactive only where needed
ForEach(items) { item in
    ItemView(item)
        .glassEffect(.regular)  // Standard glass
}
// Only primary action is interactive
Button("Main Action") { }
    .glassEffect(.regular.interactive())
```

## Equatable Protocol

Prevent unnecessary re-renders:

```swift
struct GlassCard: View, Equatable {
    let title: String
    let subtitle: String

    static func == (lhs: GlassCard, rhs: GlassCard) -> Bool {
        lhs.title == rhs.title && lhs.subtitle == rhs.subtitle
    }

    var body: some View {
        VStack {
            Text(title)
            Text(subtitle)
        }
        .padding()
        .glassEffect()
    }
}

// Usage with EquatableView
ForEach(cards) { card in
    EquatableView(content: GlassCard(title: card.title, subtitle: card.subtitle))
}
```

## Avoid Nested Glass

```swift
// ❌ Nested glass - very expensive
VStack {
    HStack {
        Button("A") { }
            .glassEffect()
    }
    .glassEffect()  // Nested!
}
.glassEffect()  // Double nested!

// ✅ Flat glass structure
GlassEffectContainer {
    VStack {
        HStack {
            Button("A") { }.glassEffect()
        }
    }
}
```

## Lazy Loading

Use lazy containers for lists with glass elements:

```swift
// ✅ Lazy loading - only visible items rendered
LazyVStack {
    ForEach(items) { item in
        ItemView(item)
            .glassEffect()
    }
}

// ❌ Eager loading - all items rendered
VStack {
    ForEach(items) { item in
        ItemView(item)
            .glassEffect()
    }
}
```

## Animation Performance

```swift
// ✅ Good - animate container state
@State private var isExpanded = false

GlassEffectContainer {
    // content
}
.onChange(of: isExpanded) {
    withAnimation(.bouncy) { }  // Single animation
}

// ❌ Bad - animate glass properties directly
@State private var opacity = 1.0

Button("Action") { }
    .glassEffect(.regular.tint(.blue.opacity(opacity)))  // Animating glass!
    .animation(.default, value: opacity)
```

## Profiling Tips

### Instruments
1. Open Instruments
2. Select "Core Animation" template
3. Look for:
   - Frame drops during glass transitions
   - High GPU usage with glass elements
   - Offscreen rendering passes

### Common Issues
| Symptom | Likely Cause | Solution |
|---------|--------------|----------|
| Frame drops on scroll | Too many glass layers | Use GlassEffectContainer |
| Slow transitions | Animating glass properties | Animate state, not glass |
| High GPU usage | Interactive on many elements | Limit `.interactive()` |
| Memory spikes | Nested glass effects | Flatten hierarchy |

## Platform-Specific Considerations

### iOS
- Device GPU varies significantly
- Test on oldest supported device
- `.interactive()` most impactful

### macOS
- Generally more GPU headroom
- Window resizing can trigger re-renders
- Test with multiple displays

### watchOS
- Very limited GPU
- Minimize glass usage
- Avoid animations

## Performance Checklist

- [ ] All related glass elements wrapped in GlassEffectContainer
- [ ] Maximum 4-6 glass elements visible at once
- [ ] `.interactive()` used only for primary actions
- [ ] Glass views implement Equatable where possible
- [ ] No nested glass effects
- [ ] Lazy containers used for lists
- [ ] Tested on lowest-spec target device
- [ ] Profiled with Instruments
