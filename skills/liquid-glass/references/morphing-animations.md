# Morphing Animations Reference

## Overview

Liquid Glass supports smooth morphing transitions between glass elements using `@Namespace` and `glassEffectID`.

## Core APIs

### glassEffectID

Links glass elements for morphing transitions:

```swift
func glassEffectID<ID: Hashable>(
    _ id: ID,
    in namespace: Namespace.ID
) -> some View
```

### glassEffectUnion

Groups multiple elements to morph as one unit:

```swift
func glassEffectUnion(
    id: (some Hashable & Sendable)?,
    namespace: Namespace.ID
) -> some View
```

The system automatically merges all Liquid Glass effects that share identical shape properties and glass variants into one combined shape.

### glassEffectTransition

Specifies the transition animation style. `GlassEffectTransition` provides three options:

| Option | Description |
|--------|-------------|
| `.identity` | No visual changes applied |
| `.matchedGeometry` | Synchronizes geometry with other glass effects (default) |
| `.materialize` | Fade-in animation for content, animates glass material appearance/disappearance |

```swift
// Matched geometry transition (most common)
.transition(.glassEffect(.matchedGeometry))

// Materialize effect for appearing/disappearing
.transition(.glassEffect(.materialize))

// No transition effect
.transition(.glassEffect(.identity))
```

## Basic Morphing Setup

Three steps to enable morphing:

```swift
struct MorphingView: View {
    // Step 1: Declare namespace
    @Namespace private var namespace
    @State private var isExpanded = false

    var body: some View {
        // Step 2: Wrap in GlassEffectContainer
        GlassEffectContainer {
            if isExpanded {
                ExpandedView()
                    .glassEffect()
                    // Step 3: Apply glassEffectID
                    .glassEffectID("panel", in: namespace)
            } else {
                CollapsedView()
                    .glassEffect()
                    .glassEffectID("panel", in: namespace)
            }
        }
        .onTapGesture {
            withAnimation(.bouncy) {
                isExpanded.toggle()
            }
        }
    }
}
```

## Expandable Button Group

```swift
struct ExpandableActions: View {
    @Namespace private var namespace
    @State private var isExpanded = false

    let icons = ["star.fill", "heart.fill", "bookmark.fill"]

    var body: some View {
        GlassEffectContainer(spacing: 40) {
            HStack(spacing: 12) {
                // Main toggle button
                Button {
                    withAnimation(.bouncy) {
                        isExpanded.toggle()
                    }
                } label: {
                    Image(systemName: isExpanded ? "xmark" : "plus")
                        .font(.title2)
                        .padding(16)
                }
                .glassEffect(.regular.interactive())
                .glassEffectID("toggle", in: namespace)

                // Expandable items
                if isExpanded {
                    ForEach(icons, id: \.self) { icon in
                        Button {
                            // Action
                        } label: {
                            Image(systemName: icon)
                                .font(.title2)
                                .padding(16)
                        }
                        .glassEffect(.regular.interactive())
                        .glassEffectID(icon, in: namespace)
                        .transition(.glassEffectTransition(.matchedGeometry))
                    }
                }
            }
        }
    }
}
```

## Union Morphing

Group multiple elements to animate as one:

```swift
struct WeatherControls: View {
    @Namespace private var namespace
    let symbols = ["cloud.bolt.rain.fill", "sun.rain.fill", "snowflake"]

    var body: some View {
        GlassEffectContainer(spacing: 20) {
            ForEach(symbols, id: \.self) { symbol in
                Image(systemName: symbol)
                    .font(.title)
                    .padding()
                    .glassEffect()
                    .glassEffectUnion(id: "weather", namespace: namespace)
            }
        }
    }
}
```

## Tab-like Morphing

```swift
struct MorphingTabs: View {
    @Namespace private var namespace
    @State private var selectedTab = 0

    let tabs = ["Home", "Search", "Profile"]

    var body: some View {
        GlassEffectContainer {
            HStack(spacing: 0) {
                ForEach(tabs.indices, id: \.self) { index in
                    Button {
                        withAnimation(.spring(response: 0.3)) {
                            selectedTab = index
                        }
                    } label: {
                        Text(tabs[index])
                            .padding(.horizontal, 20)
                            .padding(.vertical, 12)
                    }
                    .glassEffect(
                        selectedTab == index ? .regular.tint(.blue) : .regular
                    )
                    .glassEffectID("tab-\(index)", in: namespace)
                }
            }
        }
    }
}
```

## Animation Recommendations

### Recommended Animations
```swift
withAnimation(.bouncy) { }           // Best for most morphing
withAnimation(.spring(response: 0.3)) { }  // Quick transitions
withAnimation(.smooth) { }           // Subtle changes
```

### Avoid
```swift
withAnimation(.linear) { }  // Too mechanical
withAnimation(.easeIn) { }  // Unnatural feel
```

## Performance Tips

1. **Animate the container, not content**
   ```swift
   // ✅ Good - animate container state
   withAnimation { isExpanded.toggle() }

   // ❌ Bad - animate individual properties
   withAnimation { offset = 100 }
   ```

2. **Limit morphing elements**
   - Keep morphing groups to 4-6 elements max
   - Complex morphs impact performance

3. **Use `glassEffectUnion` for groups**
   - Reduces calculation complexity
   - Smoother animations

## Troubleshooting Morphing

### Animation stuttering
- Wrap state changes in `withAnimation(.bouncy)`
- Reduce number of morphing elements
- Use `GlassEffectContainer` properly

### Elements not morphing
- Ensure same `namespace` is used
- Check `glassEffectID` values match
- Verify `GlassEffectContainer` wraps both states

### Unexpected transitions
- Use `.transition(.glassEffect(.matchedGeometry))`
- Ensure IDs are unique within namespace

## Known Issues

### Menu in GlassEffectContainer (iOS 26.1)
As of iOS 26.1, placing a `Menu` inside a `GlassEffectContainer` will break the morphing animation. Avoid placing `Menu` in `GlassEffectContainer` on iOS 26.1, even though it works on iOS 26.0.

```swift
// ❌ Avoid on iOS 26.1
GlassEffectContainer {
    Menu("Options") {
        Button("Edit") { }
        Button("Delete") { }
    }
    .glassEffect()
}

// ✅ Use outside container instead
Menu("Options") {
    Button("Edit") { }
    Button("Delete") { }
}
.glassEffect()
```
