# Button Styles Reference

## Overview

iOS 26 introduces dedicated button styles for Liquid Glass, providing consistent styling without manual configuration.

## Available Styles

### `.glass`
Standard translucent glass button for secondary actions.

```swift
Button("Cancel") {
    // action
}
.buttonStyle(.glass)
```

### `.glassProminent`
Opaque, visually prominent glass button for primary actions.

```swift
Button("Save") {
    // action
}
.buttonStyle(.glassProminent)
```

## Visual Hierarchy

Use button styles to establish clear visual hierarchy:

```swift
HStack(spacing: 16) {
    // Secondary action - standard glass
    Button("Cancel") {
        dismiss()
    }
    .buttonStyle(.glass)

    // Primary action - prominent glass
    Button("Save") {
        save()
    }
    .buttonStyle(.glassProminent)
}
```

## With Tinting

Combine button styles with tinting for semantic meaning:

```swift
// Primary action with brand color
Button("Continue") {
    // action
}
.buttonStyle(.glassProminent)
.tint(.blue)

// Destructive action
Button("Delete") {
    // action
}
.buttonStyle(.glassProminent)
.tint(.red)
```

## Comparison: ButtonStyle vs glassEffect

### Use ButtonStyle When:
- Standard button appearance is desired
- You want automatic sizing and padding
- Consistent with system button behavior

```swift
Button("Action") { }
    .buttonStyle(.glass)
```

### Use glassEffect When:
- Custom content (not just text/labels)
- Need specific shape control
- Complex interactive elements

```swift
Button {
    // action
} label: {
    HStack {
        Image(systemName: "star")
        Text("Custom")
        Image(systemName: "chevron.right")
    }
    .padding()
}
.glassEffect(.regular, in: .capsule)
```

## Button Groups

```swift
GlassEffectContainer {
    HStack(spacing: 12) {
        Button("Option 1") { }
            .buttonStyle(.glass)

        Button("Option 2") { }
            .buttonStyle(.glass)

        Button("Confirm") { }
            .buttonStyle(.glassProminent)
    }
}
```

## Icon Buttons

```swift
// Using buttonStyle
Button {
    // action
} label: {
    Image(systemName: "heart.fill")
}
.buttonStyle(.glass)

// Using glassEffect for more control
Button {
    // action
} label: {
    Image(systemName: "heart.fill")
        .font(.title2)
        .padding(16)
}
.glassEffect(.regular.interactive(), in: .circle)
```

## Disabled State

Button styles automatically handle disabled states:

```swift
Button("Unavailable") { }
    .buttonStyle(.glassProminent)
    .disabled(true)  // Automatically reduces opacity
```

## Best Practices

### DO
```swift
// Clear hierarchy
Button("Cancel") { }.buttonStyle(.glass)
Button("Save") { }.buttonStyle(.glassProminent)

// Tint for meaning
Button("Delete") { }
    .buttonStyle(.glassProminent)
    .tint(.red)
```

### DON'T
```swift
// ❌ All prominent - no hierarchy
Button("Cancel") { }.buttonStyle(.glassProminent)
Button("Save") { }.buttonStyle(.glassProminent)

// ❌ Mixed approaches in same group
HStack {
    Button("A") { }.buttonStyle(.glass)
    Button("B") { }.glassEffect()  // Inconsistent!
}
```

## Platform Availability

| Style | iOS | macOS | watchOS | tvOS | visionOS |
|-------|-----|-------|---------|------|----------|
| `.glass` | ✅ 26+ | ✅ 26+ | ✅ 26+ | ✅ 26+ | ✅ 26+ |
| `.glassProminent` | ✅ 26+ | ✅ 26+ | ✅ 26+ | ✅ 26+ | ✅ 26+ |

## Known Issues

### `.glassProminent` + Circle Shape Artifacts
Using `.glassProminent` with `.buttonBorderShape(.circle)` may cause rendering artifacts.

```swift
// ❌ May have rendering artifacts
Button {
    // action
} label: {
    Image(systemName: "plus")
}
.buttonStyle(.glassProminent)
.buttonBorderShape(.circle)

// ✅ Workaround: Add clipShape
Button {
    // action
} label: {
    Image(systemName: "plus")
}
.buttonStyle(.glassProminent)
.buttonBorderShape(.circle)
.clipShape(Circle())  // Fixes artifacts
```
