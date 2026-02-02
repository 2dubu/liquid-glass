# Best Practices Reference

## The Five Golden Rules

### 1. Navigation Layer Only

Glass is for controls that float above content, NOT the content itself.

```swift
// ✅ DO - Navigation elements
Toolbar { }.glassEffect()
FloatingActionButton { }.glassEffect()
TabBar { }.glassEffect()

// ❌ DON'T - Content elements
ListRow { }.glassEffect()
ContentCard { }.glassEffect()
ProfileHeader { }.glassEffect()
```

### 2. No Glass on Glass

Never stack glass effects. Use `GlassEffectContainer` instead.

```swift
// ❌ DON'T
HStack {
    Button("A") { }.glassEffect()
    Button("B") { }.glassEffect()
}

// ✅ DO
GlassEffectContainer {
    HStack {
        Button("A") { }.glassEffect()
        Button("B") { }.glassEffect()
    }
}
```

### 3. Don't Mix Variants

Use the same glass variant within a view hierarchy.

```swift
// ❌ DON'T - Mixed variants
HStack {
    Button("A") { }.glassEffect(.regular)
    Button("B") { }.glassEffect(.clear)  // Different!
}

// ✅ DO - Consistent variant
HStack {
    Button("A") { }.glassEffect(.regular)
    Button("B") { }.glassEffect(.regular)
}
```

### 4. Tint for Meaning Only

Use tinting to convey semantic meaning, not decoration.

```swift
// ✅ DO - Semantic tinting
Button("Save") { }
    .glassEffect(.regular.tint(.blue))  // Primary action
Button("Delete") { }
    .glassEffect(.regular.tint(.red))   // Destructive action

// ❌ DON'T - Decorative tinting
Button("Option 1") { }.glassEffect(.regular.tint(.purple))
Button("Option 2") { }.glassEffect(.regular.tint(.orange))
Button("Option 3") { }.glassEffect(.regular.tint(.green))
```

### 5. Trust Automatic Accessibility

Let the system handle accessibility. Don't override.

```swift
// ✅ DO - Let system handle it
Button("Action") { }
    .glassEffect()
// Automatically adapts to Reduce Transparency, etc.

// ❌ DON'T - Manual overrides
@Environment(\.accessibilityReduceTransparency) var reduce
Button("Action") { }
    .glassEffect()
    .opacity(reduce ? 1 : 0.8)  // Unnecessary!
```

## Design Patterns

### Floating Action Button

```swift
struct FloatingActionButton: View {
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: "plus")
                .font(.title2)
                .fontWeight(.semibold)
                .padding(18)
        }
        .glassEffect(.regular.tint(.blue).interactive(), in: .circle)
    }
}
```

### Expandable Toolbar

```swift
struct ExpandableToolbar: View {
    @Namespace private var namespace
    @State private var isExpanded = false

    var body: some View {
        GlassEffectContainer {
            HStack(spacing: 12) {
                Button {
                    withAnimation(.bouncy) { isExpanded.toggle() }
                } label: {
                    Image(systemName: isExpanded ? "xmark" : "ellipsis")
                        .padding(12)
                }
                .glassEffect(.regular.interactive())
                .glassEffectID("toggle", in: namespace)

                if isExpanded {
                    ForEach(["pencil", "trash", "square.and.arrow.up"], id: \.self) { icon in
                        Button { } label: {
                            Image(systemName: icon)
                                .padding(12)
                        }
                        .glassEffect(.regular.interactive())
                        .glassEffectID(icon, in: namespace)
                    }
                }
            }
        }
    }
}
```

### Media Player Controls

```swift
struct MediaControls: View {
    var body: some View {
        GlassEffectContainer {
            HStack(spacing: 24) {
                Button { } label: {
                    Image(systemName: "backward.fill")
                }
                .glassEffect(.clear)

                Button { } label: {
                    Image(systemName: "play.fill")
                        .font(.title)
                }
                .glassEffect(.clear.interactive())

                Button { } label: {
                    Image(systemName: "forward.fill")
                }
                .glassEffect(.clear)
            }
            .padding()
        }
    }
}
```

## Common Mistakes

### Mistake 1: Glass on Content

```swift
// ❌ Wrong
List {
    ForEach(items) { item in
        ItemRow(item)
            .glassEffect()  // Glass on list content!
    }
}

// ✅ Correct
List {
    ForEach(items) { item in
        ItemRow(item)  // No glass on content
    }
}
.toolbar {
    ToolbarItem {
        Button("Add") { }
            .glassEffect()  // Glass on toolbar only
    }
}
```

### Mistake 2: Too Much Glass

```swift
// ❌ Wrong - Glass everywhere
VStack {
    Header().glassEffect()
    Content().glassEffect()
    Footer().glassEffect()
    Sidebar().glassEffect()
    Toolbar().glassEffect()
}

// ✅ Correct - Glass only on navigation
VStack {
    Header()
    Content()
    Footer()
    GlassEffectContainer {
        Toolbar().glassEffect()
    }
}
```

### Mistake 3: Inconsistent Styling

```swift
// ❌ Wrong - Mixed approaches
HStack {
    Button("A") { }.buttonStyle(.glass)
    Button("B") { }.glassEffect()
    Button("C") { }.background(.ultraThinMaterial)
}

// ✅ Correct - Consistent approach
GlassEffectContainer {
    HStack {
        Button("A") { }.glassEffect()
        Button("B") { }.glassEffect()
        Button("C") { }.glassEffect()
    }
}
```

## Testing Checklist

### Visual Testing
- [ ] Test on light and dark backgrounds
- [ ] Test on colorful images
- [ ] Test on solid colors
- [ ] Test with dynamic content behind glass

### Accessibility Testing
- [ ] Reduce Transparency enabled
- [ ] Increase Contrast enabled
- [ ] Reduce Motion enabled
- [ ] VoiceOver navigation

### Performance Testing
- [ ] Smooth scrolling with glass elements
- [ ] Smooth morphing animations
- [ ] No frame drops during transitions
- [ ] Tested on oldest supported device
