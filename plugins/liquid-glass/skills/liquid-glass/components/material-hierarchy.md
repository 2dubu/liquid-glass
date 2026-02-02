# Material Hierarchy Component

> Source: [LiquidGlass-Handbook](https://github.com/jaikrishnavj/LiquidGlass-Handbook)

## Overview

iOS provides four material levels for creating visual hierarchy. Each level has different blur intensity and transparency, allowing you to create depth in your UI.

## Four Material Levels

| Material | Blur | Transparency | Use Case |
|----------|------|--------------|----------|
| `.ultraThinMaterial` | Minimal | Maximum | Floating headers, overlays needing see-through |
| `.thinMaterial` | Light | High | Subtle separation, secondary controls |
| `.regularMaterial` | Standard | Medium | Most UI elements, cards, toolbars |
| `.thickMaterial` | Heavy | Low | Strong emphasis, modal backgrounds |

## Visual Comparison

```swift
struct MaterialHierarchyDemo: View {
    var body: some View {
        ZStack {
            // Colorful background
            LinearGradient(
                colors: [.blue, .purple, .pink, .orange],
                startPoint: .topLeading,
                endPoint: .bottomTrailing
            )
            .ignoresSafeArea()

            // Material cards
            VStack(spacing: 16) {
                MaterialCard(
                    title: "Ultra Thin",
                    subtitle: "Minimal blur, maximum transparency",
                    material: .ultraThinMaterial
                )

                MaterialCard(
                    title: "Thin",
                    subtitle: "Light blur for subtle separation",
                    material: .thinMaterial
                )

                MaterialCard(
                    title: "Regular",
                    subtitle: "Standard blur for most UI elements",
                    material: .regularMaterial
                )

                MaterialCard(
                    title: "Thick",
                    subtitle: "Heavy blur for strong emphasis",
                    material: .thickMaterial
                )
            }
            .padding()
        }
    }
}
```

## Material Card Component

```swift
struct MaterialCard<M: ShapeStyle>: View {
    let title: String
    let subtitle: String
    let material: M

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            Text(title)
                .font(.headline)
            Text(subtitle)
                .font(.caption)
                .foregroundStyle(.secondary)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding()
        .background(material)
        .clipShape(RoundedRectangle(cornerRadius: 16))
    }
}
```

## When to Use Each Material

### Ultra Thin Material
```swift
// Floating headers that need maximum see-through
FloatingHeader()
    .background(.ultraThinMaterial)

// Subtle overlays
OverlayContent()
    .background(.ultraThinMaterial)
```

### Thin Material
```swift
// Secondary controls, action bars
ActionBar()
    .background(.thinMaterial)

// Light separation between sections
SectionDivider()
    .background(.thinMaterial)
```

### Regular Material
```swift
// Standard cards and containers
ContentCard()
    .background(.regularMaterial)

// Toolbars and navigation
Toolbar()
    .background(.regularMaterial)
```

### Thick Material
```swift
// Modal backgrounds
ModalContent()
    .background(.thickMaterial)

// Strong visual separation
HighlightedSection()
    .background(.thickMaterial)
```

## Combining with Glass Effects

Materials work alongside Liquid Glass:

```swift
struct CombinedEffectsDemo: View {
    var body: some View {
        ZStack {
            // Background with material
            VStack {
                // Content area
            }
            .background(.ultraThinMaterial)

            // Floating glass controls
            VStack {
                Spacer()
                HStack {
                    Button("Cancel") { }
                        .buttonStyle(.glass)

                    Button("Save") { }
                        .buttonStyle(.glassProminent)
                }
                .padding()
                .background(.thinMaterial)
                .clipShape(Capsule())
            }
        }
    }
}
```

## Hierarchical Layering

Create depth with multiple material levels:

```swift
struct HierarchicalView: View {
    var body: some View {
        ZStack {
            // Layer 1: Background content
            Image("background")
                .resizable()
                .aspectRatio(contentMode: .fill)

            // Layer 2: Content with thick material (most blur)
            VStack {
                Text("Background Layer")
            }
            .frame(maxWidth: .infinity, maxHeight: .infinity)
            .background(.thickMaterial)
            .clipShape(RoundedRectangle(cornerRadius: 24))
            .padding(40)

            // Layer 3: Card with regular material
            VStack {
                Text("Middle Layer")
            }
            .padding()
            .background(.regularMaterial)
            .clipShape(RoundedRectangle(cornerRadius: 16))

            // Layer 4: Controls with thin material (least blur)
            VStack {
                Spacer()
                HStack {
                    Button("Action") { }
                        .buttonStyle(.glassProminent)
                }
                .padding()
                .background(.thinMaterial)
                .clipShape(Capsule())
            }
        }
    }
}
```

## Key Points

- Four material levels: ultraThin, thin, regular, thick
- Higher blur = more emphasis, less see-through
- Lower blur = subtle, more transparent
- Use hierarchy to create depth:
  - Back layers: `.thickMaterial`
  - Middle layers: `.regularMaterial`
  - Front layers: `.thinMaterial` or `.ultraThinMaterial`
- Materials automatically adapt to light/dark mode
- Combine with Liquid Glass for floating controls
- All materials work on colorful backgrounds
