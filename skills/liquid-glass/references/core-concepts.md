# Liquid Glass Core Concepts

## What is Liquid Glass?

Liquid Glass is iOS 26's revolutionary material system introduced at WWDC 2025. It combines the optical properties of glass with fluid, responsive interactions.

Unlike traditional blur effects, Liquid Glass **reflects and refracts** surrounding content in real-time, creating a lightweight, dynamic material that adapts to underlying content.

## Key Characteristics

| Feature | Description |
|---------|-------------|
| **Dynamic Translucency** | Real-time background content blur |
| **Adaptive Color** | Automatically changes from light to dark as users scroll through content |
| **Interactive Response** | Controls transform during interaction with dynamic effects |
| **Shape Morphing** | Smooth shape transformations via `GlassEffectContainer` |
| **Corner Concentricity** | Controls align perfectly within their containers across different displays |
| **Cross-Platform** | Consistent appearance across iOS, iPadOS, macOS, tvOS, and visionOS |

## The Navigation Layer Rule

**Critical**: Liquid Glass should ONLY be applied to the **navigation layer**, NOT content.

### DO Use For (Navigation Layer)
- Toolbars
- Tab bars
- Sidebars
- Floating action buttons
- Navigation bars
- Floating controls

### DON'T Use For (Content Layer)
- List items
- Content cards
- Full backgrounds
- Table cells
- Collection view cells

## Glass Variants

```swift
Glass.regular   // Standard UI controls (default)
Glass.clear     // Media-heavy backgrounds (photos, videos)
Glass.identity  // Disabled state (for accessibility)
```

### When to Use Each Variant

| Variant | Use Case |
|---------|----------|
| `.regular` | Most UI controls, toolbars, buttons |
| `.clear` | Controls over photos, videos, maps |
| `.identity` | When user has "Reduce Transparency" enabled |

## Design Philosophy

> "Content sits at the bottom, and glass controls float on top."

The visual hierarchy:
1. **Background** - App content (lists, images, text)
2. **Glass Layer** - Navigation controls floating above
3. **Foreground** - Modal presentations, alerts

## Corner Concentricity

Controls should align perfectly within their containers across different displays. For example, a button positioned at the bottom of a sheet should share the same corner center with the corners of the sheet.

```swift
// Automatically maintains concentricity with container
.clipShape(.rect(cornerRadius: 12, style: .continuous))

// Or use containerConcentric for automatic matching
RoundedRectangle(cornerRadius: .containerConcentric)
```

## Platform Support

- iOS 26.0+
- iPadOS 26.0+
- macOS Tahoe 26.0+
- watchOS 26.0+
- tvOS 26.0+
- visionOS 26.0+

## Automatic Adoption

When compiled with Xcode 26, these components automatically get Liquid Glass:

**iOS 26:**
- NavigationBar
- TabBar
- Sheets
- Alerts
- Search bars

**macOS Tahoe:**
- Toolbar
- Sidebar
- Menu bar

No code changes required for system components!
