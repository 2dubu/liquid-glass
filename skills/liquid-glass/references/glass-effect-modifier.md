# glassEffect Modifier Reference

## API Signature

```swift
func glassEffect<S: Shape>(
    _ glass: Glass = .regular,
    in shape: S = DefaultGlassEffectShape,
    isEnabled: Bool = true
) -> some View
```

## Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `glass` | `Glass` | `.regular` | The glass configuration |
| `shape` | `Shape` | Auto | The shape of the glass effect |
| `isEnabled` | `Bool` | `true` | Whether the effect is enabled |

## Basic Usage

```swift
// Default (regular glass, automatic shape)
Text("Hello")
    .padding()
    .glassEffect()

// Explicit regular
Text("Hello")
    .padding()
    .glassEffect(.regular)

// Clear variant
Text("Hello")
    .padding()
    .glassEffect(.clear)
```

## Shape Parameter

Specify custom shapes for the glass effect:

```swift
// Capsule shape
Button("Action") { }
    .padding()
    .glassEffect(in: .capsule)

// Circle shape
Image(systemName: "star.fill")
    .padding()
    .glassEffect(in: .circle)

// Rounded rectangle
Text("Card")
    .padding()
    .glassEffect(in: .rect(cornerRadius: 16))

// Custom corner radii
Text("Custom")
    .padding()
    .glassEffect(in: .rect(
        cornerRadii: .init(
            topLeading: 20,
            bottomLeading: 8,
            bottomTrailing: 8,
            topTrailing: 20
        )
    ))
```

## Conditional Glass Effect

There are two ways to conditionally enable/disable the glass effect:

### Using isEnabled Parameter
```swift
@State private var useGlass = true

Button("Toggle") { }
    .glassEffect(.regular, isEnabled: useGlass)
```

### Using .identity Variant (Recommended)
```swift
@State private var useGlass = true

Button("Toggle") { }
    .glassEffect(useGlass ? .regular : .identity)
```

**Note**: The `.identity` variant applies no visual effect, making it ideal for accessibility scenarios like "Reduce Transparency" settings.

## Modifier Order

**Important**: Apply layout modifiers BEFORE glassEffect.

```swift
// ✅ Correct order
Text("Hello")
    .padding()           // Layout first
    .frame(width: 200)   // Layout second
    .glassEffect()       // Glass last

// ❌ Wrong order
Text("Hello")
    .glassEffect()       // Glass applied to small area
    .padding()           // Padding outside glass
```

## Combined with Other Modifiers

```swift
Button("Action") { }
    .padding(.horizontal, 20)
    .padding(.vertical, 12)
    .glassEffect(.regular.tint(.blue).interactive(), in: .capsule)
    .shadow(radius: 4)  // Shadow after glass is OK
```

## Conditional Glass Variants

```swift
@Environment(\.colorScheme) var colorScheme

Button("Action") { }
    .glassEffect(colorScheme == .dark ? .clear : .regular)
```

## With SF Symbols

```swift
Image(systemName: "heart.fill")
    .font(.system(size: 24))
    .foregroundStyle(.white)
    .padding(16)
    .glassEffect(.regular.tint(.pink), in: .circle)
```

## Common Mistakes

### Don't nest glass effects
```swift
// ❌ Wrong
VStack {
    Text("Hello")
        .glassEffect()
}
.glassEffect()  // Nested glass!

// ✅ Correct - use GlassEffectContainer instead
GlassEffectContainer {
    VStack {
        Text("Hello")
            .glassEffect()
    }
}
```

### Don't mix variants in same view hierarchy
```swift
// ❌ Wrong
HStack {
    Button("A") { }.glassEffect(.regular)
    Button("B") { }.glassEffect(.clear)  // Mixed!
}

// ✅ Correct - use same variant
HStack {
    Button("A") { }.glassEffect(.regular)
    Button("B") { }.glassEffect(.regular)
}
```
