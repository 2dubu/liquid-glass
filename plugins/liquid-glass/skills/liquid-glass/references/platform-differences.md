# Platform Differences Reference

## Overview

Liquid Glass is available across Apple platforms, but with varying features and behaviors.

## Feature Availability Matrix

| Feature | iOS | macOS | watchOS | tvOS | visionOS |
|---------|-----|-------|---------|------|----------|
| `.glassEffect()` | ✅ 26+ | ✅ 26+ | ✅ 26+ | ✅ 26+ | ✅ 26+ |
| `.glassEffect(.regular)` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `.glassEffect(.clear)` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `.glassEffect(.identity)` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `.interactive()` | ✅ | ✅ | ✅ | ✅ | ⚠️ Not listed |
| `.tint()` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `GlassEffectContainer` | ✅ | ✅ | ✅ | ✅ | ✅ |
| Morphing animations | ✅ | ✅ | ⚠️ Limited | ⚠️ Limited | ✅ |
| Motion-based highlights | ✅ | ❌ | ❌ | ❌ | ✅ Spatial |
| `.buttonStyle(.glass)` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `.buttonStyle(.glassProminent)` | ✅ | ✅ | ✅ | ✅ | ✅ |

**Note**: `.interactive()` is supported on iOS 26.0+, iPadOS 26.0+, Mac Catalyst 26.0+, macOS 26.0+, tvOS 26.0+, and watchOS 26.0+. The visual feedback may vary by platform (touch vs pointer vs focus-based).

## iOS 26

### Full Feature Set
iOS has the most complete Liquid Glass implementation:

- All glass variants
- Interactive effects with touch feedback
- Motion-based specular highlights (device tilting)
- Full morphing animation support
- Automatic system component glass (NavigationBar, TabBar, etc.)

### iOS-Specific Code
```swift
// Interactive only works on iOS
Button("Action") { }
    .glassEffect(.regular.interactive())  // Touch feedback

// Motion highlights automatic when device tilts
```

### Automatic Glass Components
- NavigationBar
- TabBar
- Sheets (partial height)
- Alerts
- Search bars
- Toolbars

## macOS Tahoe (26)

### Differences from iOS
- `.interactive()` responds to pointer interactions instead of touch
- No motion-based highlights (no accelerometer)
- Window chrome integration

### macOS-Specific Considerations
```swift
// Interactive responds to pointer on macOS
Button("Action") { }
    .glassEffect(.regular.interactive())  // Pointer feedback

// Hover states work automatically
```

### Automatic Glass Components
- Toolbar
- Sidebar
- Menu bar
- Window chrome

### Window Integration
```swift
// Glass integrates with window appearance
WindowGroup {
    ContentView()
}
.windowStyle(.hiddenTitleBar)  // Glass extends to title bar area
```

## watchOS 26

### Limited Implementation
Due to small screen and limited GPU:

- Basic glass effects supported
- Limited morphing (simplified animations)
- Performance-constrained

### watchOS Best Practices
```swift
// Keep glass minimal on watchOS
Button("Action") { }
    .glassEffect()  // Use sparingly

// Avoid complex morphing
// Limit to 1-2 glass elements per screen
```

## tvOS 26

### Focus-Based Interaction
tvOS uses focus system instead of touch:

- Glass responds to focus state
- No touch-based interaction
- Remote-driven navigation

### tvOS-Specific Code
```swift
// Glass responds to focus
Button("Action") { }
    .glassEffect()
    .focusable()  // Glass highlights on focus
```

## visionOS 26

### Spatial Computing
visionOS has unique spatial considerations:

- Glass adapts to 3D environment
- Spatial highlights respond to head/hand position
- Depth-aware rendering

### visionOS-Specific Features
```swift
// Glass in 3D space
Button("Action") { }
    .glassEffect()
    // Automatically adapts to spatial context

// Spatial interactive feedback
.glassEffect(.regular.interactive())  // Hand tracking feedback
```

### Depth Considerations
```swift
// Glass appearance changes with depth
RealityView { content in
    // Glass renders differently at various depths
}
```

## Cross-Platform Code

### Platform-Adaptive Modifier
```swift
struct PlatformAdaptiveGlass: ViewModifier {
    func body(content: Content) -> some View {
        #if os(watchOS)
        content.glassEffect()  // Minimal for performance
        #elseif os(tvOS)
        content.glassEffect(.regular.interactive()).focusable()
        #else
        content.glassEffect(.regular.interactive())
        #endif
    }
}

extension View {
    func platformGlass() -> some View {
        modifier(PlatformAdaptiveGlass())
    }
}
```

### Simplified Cross-Platform Usage
Since `.interactive()` is supported on all platforms (iOS, macOS, tvOS, watchOS 26.0+), you can use it consistently:

```swift
// Works on all platforms - feedback adapts to input method
Button("Action") { }
    .glassEffect(.regular.interactive())
```

## Performance by Platform

| Platform | GPU Power | Recommended Max Glass Elements |
|----------|-----------|-------------------------------|
| iOS (iPhone) | Medium-High | 4-6 |
| iOS (iPad) | High | 6-8 |
| macOS | High | 8-10 |
| watchOS | Low | 1-2 |
| tvOS | Medium | 4-6 |
| visionOS | High | 6-8 |

## Testing Matrix

When developing cross-platform:

1. **iOS** - Primary development target
2. **macOS** - Test pointer interactions
3. **watchOS** - Test minimal glass usage
4. **tvOS** - Test focus-based navigation
5. **visionOS** - Test spatial behavior (if targeting)

## Platform-Specific Gotchas

### iOS
- Test on oldest supported device for performance
- Verify motion highlights on physical device

### macOS
- Window resizing can trigger re-renders
- Test with multiple displays

### watchOS
- Complications don't support glass
- Battery impact significant

### tvOS
- Test with Siri Remote
- Verify focus ring behavior

### visionOS
- Test at various distances
- Verify hand tracking feedback
