# Backward Compatibility Reference

## Overview

When supporting both iOS 26+ (Liquid Glass) and earlier versions (iOS 17/18), use availability checks and fallback strategies.

## Basic Availability Check

```swift
if #available(iOS 26.0, *) {
    Button("Action") { }
        .glassEffect()
} else {
    Button("Action") { }
        .background(.ultraThinMaterial)
        .clipShape(Capsule())
}
```

## Custom ViewModifier Approach

Create a reusable modifier for consistent fallback:

```swift
struct AdaptiveGlassModifier: ViewModifier {
    let shape: AnyShape

    init<S: Shape>(shape: S = Capsule()) {
        self.shape = AnyShape(shape)
    }

    func body(content: Content) -> some View {
        if #available(iOS 26.0, *) {
            content.glassEffect(in: shape)
        } else {
            content
                .background(.ultraThinMaterial)
                .clipShape(shape)
        }
    }
}

extension View {
    func adaptiveGlass<S: Shape>(in shape: S = Capsule()) -> some View {
        modifier(AdaptiveGlassModifier(shape: shape))
    }
}

// Usage
Button("Action") { }
    .padding()
    .adaptiveGlass()

Button("Circle") { }
    .padding()
    .adaptiveGlass(in: Circle())
```

## Interactive Fallback

```swift
struct AdaptiveInteractiveGlassModifier: ViewModifier {
    func body(content: Content) -> some View {
        if #available(iOS 26.0, *) {
            content.glassEffect(.regular.interactive())
        } else {
            content
                .background(.ultraThinMaterial)
                .clipShape(Capsule())
                .scaleEffect(1.0)  // Add manual press effect if needed
        }
    }
}
```

## Container Fallback

```swift
struct AdaptiveGlassContainer<Content: View>: View {
    let content: Content

    init(@ViewBuilder content: () -> Content) {
        self.content = content()
    }

    var body: some View {
        if #available(iOS 26.0, *) {
            GlassEffectContainer {
                content
            }
        } else {
            content  // No container needed for materials
        }
    }
}

// Usage
AdaptiveGlassContainer {
    HStack {
        Button("A") { }.adaptiveGlass()
        Button("B") { }.adaptiveGlass()
    }
}
```

## Tinting Fallback

```swift
struct AdaptiveTintedGlassModifier: ViewModifier {
    let tintColor: Color

    func body(content: Content) -> some View {
        if #available(iOS 26.0, *) {
            content.glassEffect(.regular.tint(tintColor))
        } else {
            content
                .background(
                    ZStack {
                        Color.clear.background(.ultraThinMaterial)
                        tintColor.opacity(0.2)
                    }
                )
                .clipShape(Capsule())
        }
    }
}

extension View {
    func adaptiveGlass(tint: Color) -> some View {
        modifier(AdaptiveTintedGlassModifier(tintColor: tint))
    }
}
```

## Button Style Fallback

```swift
struct AdaptiveGlassButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        if #available(iOS 26.0, *) {
            configuration.label
                .buttonStyle(.glass)
        } else {
            configuration.label
                .padding(.horizontal, 16)
                .padding(.vertical, 10)
                .background(.ultraThinMaterial)
                .clipShape(Capsule())
                .opacity(configuration.isPressed ? 0.7 : 1.0)
        }
    }
}
```

## Complete Example: Floating Action Button

```swift
struct FloatingActionButton: View {
    let icon: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: icon)
                .font(.title2)
                .fontWeight(.semibold)
                .foregroundStyle(.white)
                .padding(18)
        }
        .floatingButtonStyle()
    }
}

extension View {
    @ViewBuilder
    func floatingButtonStyle() -> some View {
        if #available(iOS 26.0, *) {
            self.glassEffect(.regular.tint(.blue).interactive(), in: .circle)
        } else {
            self
                .background(
                    Circle()
                        .fill(.blue)
                        .background(.ultraThinMaterial)
                        .clipShape(Circle())
                )
                .shadow(radius: 4)
        }
    }
}
```

## Morphing Fallback

Morphing is iOS 26+ only. Provide simpler transitions for earlier versions:

```swift
struct AdaptiveMorphingView: View {
    @State private var isExpanded = false

    var body: some View {
        if #available(iOS 26.0, *) {
            MorphingGlassView(isExpanded: $isExpanded)
        } else {
            SimpleFadeView(isExpanded: $isExpanded)
        }
    }
}

@available(iOS 26.0, *)
struct MorphingGlassView: View {
    @Binding var isExpanded: Bool
    @Namespace private var namespace

    var body: some View {
        GlassEffectContainer {
            // Full morphing implementation
        }
    }
}

struct SimpleFadeView: View {
    @Binding var isExpanded: Bool

    var body: some View {
        HStack {
            // Simple fade/scale transitions
        }
        .animation(.easeInOut, value: isExpanded)
    }
}
```

## Version Detection Helper

```swift
struct PlatformInfo {
    static var supportsLiquidGlass: Bool {
        if #available(iOS 26.0, macOS 26.0, *) {
            return true
        }
        return false
    }
}

// Usage
if PlatformInfo.supportsLiquidGlass {
    // iOS 26+ specific features
}
```

## Testing Strategy

1. **Test on iOS 26+ simulator/device** - Verify glass effects
2. **Test on iOS 17/18 simulator/device** - Verify fallbacks
3. **Test transitions** - Ensure no crashes during version checks

## Deprecation Timeline

Consider when to drop iOS 17/18 support:

| iOS Version | End of Support (typical) |
|-------------|-------------------------|
| iOS 17 | ~2026 |
| iOS 18 | ~2027 |

Plan your deprecation timeline accordingly to simplify codebase.
