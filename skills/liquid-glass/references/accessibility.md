# Accessibility Reference

## Overview

Liquid Glass automatically respects system accessibility settings. No additional code is required for basic compliance.

## Automatic Accessibility Support

The system automatically handles:

| Setting | Liquid Glass Behavior |
|---------|----------------------|
| Reduce Transparency | Glass becomes opaque |
| Increase Contrast | Enhanced color contrast |
| Reduce Motion | Morphing animations disabled |
| Bold Text | Text remains readable |
| Larger Text | Glass scales appropriately |

## Reduce Transparency

When "Reduce Transparency" is enabled, Liquid Glass automatically becomes opaque.

### Automatic Handling
```swift
// This works automatically
Button("Action") { }
    .glassEffect()
// Glass becomes opaque when Reduce Transparency is on
```

### Manual Handling (if needed)
```swift
@Environment(\.accessibilityReduceTransparency) var reduceTransparency

Button("Action") { }
    .glassEffect(reduceTransparency ? .identity : .regular)
```

## Reduce Motion

Morphing animations are automatically disabled when "Reduce Motion" is enabled.

```swift
// Morphing works normally
GlassEffectContainer {
    Button("Toggle") { }
        .glassEffect()
        .glassEffectID("btn", in: namespace)
}
// When Reduce Motion is on, transitions are instant
```

## Color Contrast

Ensure sufficient contrast for text and icons on glass:

### Recommended Approach
```swift
// Use primary/secondary styles - they adapt automatically
Text("Label")
    .foregroundStyle(.primary)
    .glassEffect()

// Or use semantic colors
Image(systemName: "star.fill")
    .foregroundStyle(.white)  // High contrast on glass
```

### Check Contrast
```swift
// Low contrast - hard to read
Text("Low Contrast")
    .foregroundStyle(.gray.opacity(0.5))
    .glassEffect()

// High contrast - readable
Text("High Contrast")
    .foregroundStyle(.primary)
    .glassEffect()
```

## Touch Targets

Ensure adequate touch target sizes (44x44pt minimum):

```swift
// ✅ Good - adequate touch target
Button(action: {}) {
    Image(systemName: "star")
        .padding(16)  // Results in ~44pt touch target
}
.glassEffect()

// ❌ Bad - too small
Button(action: {}) {
    Image(systemName: "star")
        .padding(4)  // Too small!
}
.glassEffect()
```

## VoiceOver

Glass elements work with VoiceOver automatically, but ensure proper labels:

```swift
Button(action: favorite) {
    Image(systemName: "heart.fill")
        .padding()
}
.glassEffect()
.accessibilityLabel("Add to favorites")
```

## Dynamic Type

Glass containers scale with Dynamic Type:

```swift
// Text scales, glass adapts
Text("Scalable")
    .font(.body)  // Respects Dynamic Type
    .padding()
    .glassEffect()
```

## Testing Checklist

Test your Liquid Glass UI with these settings enabled:

### Settings > Accessibility > Display & Text Size
- [ ] Reduce Transparency
- [ ] Increase Contrast
- [ ] Bold Text
- [ ] Larger Text (all sizes)

### Settings > Accessibility > Motion
- [ ] Reduce Motion

### Settings > Accessibility > VoiceOver
- [ ] VoiceOver enabled
- [ ] Navigate through glass elements

## Best Practices

### DO
```swift
// Use semantic colors
.foregroundStyle(.primary)
.foregroundStyle(.secondary)

// Adequate padding
.padding(16)

// Clear labels
.accessibilityLabel("Description")
```

### DON'T
```swift
// Hard-coded low contrast colors
.foregroundStyle(Color(white: 0.7))

// Tiny touch targets
.padding(2)

// Missing accessibility labels
// (no .accessibilityLabel)
```

## Platform Considerations

| Platform | Accessibility Notes |
|----------|---------------------|
| iOS | Full support for all settings |
| macOS | Reduce Transparency supported |
| watchOS | Limited glass on small screens |
| visionOS | Spatial accessibility considerations |

## Resources

- [Apple Human Interface Guidelines - Accessibility](https://developer.apple.com/design/human-interface-guidelines/accessibility)
- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
