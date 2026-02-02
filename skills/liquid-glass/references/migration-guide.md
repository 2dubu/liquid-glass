# Migration Guide: iOS 17/18 to iOS 26

## Overview

This guide helps you migrate existing material-based UI to iOS 26 Liquid Glass.

## Quick Reference

| Old (iOS 17/18) | New (iOS 26) | Notes |
|-----------------|--------------|-------|
| `.background(.ultraThinMaterial)` | `.glassEffect()` | For navigation elements only |
| `.background(.regularMaterial)` | Keep as-is | For content backgrounds |
| `.background(.thickMaterial)` | Keep as-is | For content backgrounds |
| Custom blur effects | `.glassEffect(.clear)` | Consider if appropriate |
| `.blur(radius:)` on controls | `.glassEffect()` | For navigation controls |

## Key Principle

**Liquid Glass is for navigation layer, NOT content.**

- Toolbars, tab bars, FABs → Migrate to `.glassEffect()`
- Content cards, list items → Keep using materials

## Step-by-Step Migration

### Step 1: Identify Navigation Elements

Find UI elements that "float" above content:
- Toolbars
- Floating action buttons
- Navigation bars (if custom)
- Floating controls
- Overlay buttons

### Step 2: Replace Materials with glassEffect

```swift
// BEFORE (iOS 17/18)
Button("Action") { }
    .padding()
    .background(.ultraThinMaterial)
    .clipShape(Capsule())

// AFTER (iOS 26)
Button("Action") { }
    .padding()
    .glassEffect(in: .capsule)
```

### Step 3: Group Multiple Glass Elements

```swift
// BEFORE
HStack {
    Button("A") { }
        .background(.ultraThinMaterial)
    Button("B") { }
        .background(.ultraThinMaterial)
}

// AFTER
GlassEffectContainer {
    HStack {
        Button("A") { }.glassEffect()
        Button("B") { }.glassEffect()
    }
}
```

### Step 4: Add Interactivity

Add touch/pointer feedback for interactive elements:

```swift
// BEFORE
Button("Action") { }
    .background(.ultraThinMaterial)

// AFTER - with touch feedback
Button("Action") { }
    .glassEffect(.regular.interactive())

// Conditionally enable interactivity
Button("Action") { }
    .glassEffect(.regular.interactive(isInteractive))
```

**Availability**: Supported on iOS 26.0+, iPadOS 26.0+, macOS 26.0+, tvOS 26.0+, watchOS 26.0+.

### Step 5: Test Accessibility

Verify behavior with:
- "Reduce Transparency" enabled
- "Reduce Motion" enabled
- Various background colors/images

## Common Migration Patterns

### Floating Action Button

```swift
// BEFORE
Button(action: add) {
    Image(systemName: "plus")
        .font(.title)
        .padding()
        .background(.ultraThinMaterial)
        .clipShape(Circle())
}

// AFTER
Button(action: add) {
    Image(systemName: "plus")
        .font(.title)
        .padding()
}
.glassEffect(.regular.tint(.blue).interactive(), in: .circle)
```

### Toolbar Overlay

```swift
// BEFORE
HStack {
    ForEach(tools) { tool in
        Button(tool.icon) { }
            .padding(8)
    }
}
.padding()
.background(.regularMaterial)
.cornerRadius(12)

// AFTER
GlassEffectContainer {
    HStack {
        ForEach(tools) { tool in
            Button(tool.icon) { }
                .padding(8)
                .glassEffect()
        }
    }
}
```

### Media Player Controls

```swift
// BEFORE
HStack {
    Button("Prev") { }
    Button("Play") { }
    Button("Next") { }
}
.background(.ultraThinMaterial)

// AFTER - use .clear for media
GlassEffectContainer {
    HStack {
        Button("Prev") { }.glassEffect(.clear)
        Button("Play") { }.glassEffect(.clear)
        Button("Next") { }.glassEffect(.clear)
    }
}
```

## What NOT to Migrate

Keep using materials for:

### Content Cards
```swift
// Keep as-is
VStack {
    Text("Card Content")
}
.background(.regularMaterial)  // ✅ Keep for content
```

### List Backgrounds
```swift
// Keep as-is
List {
    // items
}
.scrollContentBackground(.hidden)
.background(.thinMaterial)  // ✅ Keep for content
```

### Full-Screen Overlays
```swift
// Keep as-is for content overlays
Color.black.opacity(0.5)
    .background(.ultraThinMaterial)
```

## Backward Compatibility

For apps supporting iOS 17/18 AND iOS 26:

```swift
Button("Action") { }
    .padding()
    .modifier(GlassBackgroundModifier())

struct GlassBackgroundModifier: ViewModifier {
    func body(content: Content) -> some View {
        if #available(iOS 26.0, *) {
            content.glassEffect()
        } else {
            content
                .background(.ultraThinMaterial)
                .clipShape(Capsule())
        }
    }
}
```

## Migration Checklist

- [ ] Identify all navigation-layer elements
- [ ] Replace `.background(.ultraThinMaterial)` with `.glassEffect()`
- [ ] Wrap multiple glass elements in `GlassEffectContainer`
- [ ] Add `.interactive()` for tappable elements (iOS)
- [ ] Use `.clear` variant for media overlays
- [ ] Test with "Reduce Transparency" enabled
- [ ] Test with "Reduce Motion" enabled
- [ ] Test on various background colors/images
- [ ] Keep materials for content-layer elements
- [ ] Add `#available` checks for backward compatibility
