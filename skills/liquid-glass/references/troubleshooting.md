# Troubleshooting Reference

## Common Issues and Solutions

### Glass Effect Not Visible

**Symptoms**: `.glassEffect()` applied but no visual change.

**Solutions**:

1. **Check deployment target**
   ```swift
   // Ensure iOS 26+ deployment target in project settings
   ```

2. **Verify element is on navigation layer**
   ```swift
   // ❌ Glass on content - may not render as expected
   List {
       Text("Item").glassEffect()
   }

   // ✅ Glass on floating controls
   Button("Action") { }
       .glassEffect()
   ```

3. **Check Reduce Transparency setting**
   - Settings > Accessibility > Display > Reduce Transparency
   - Glass becomes opaque when enabled (expected behavior)

4. **Ensure background exists**
   ```swift
   // Glass needs content behind it to be visible
   ZStack {
       Image("background")  // Background content
       Button("Action") { }
           .glassEffect()   // Now visible
   }
   ```

---

### Glass Looks Different on Different Backgrounds

**Symptoms**: Glass appearance varies across screens.

**Solution**: This is expected! Liquid Glass adapts to background content.

```swift
// Use .clear for media-heavy backgrounds
.glassEffect(.clear)

// Use .regular for standard backgrounds
.glassEffect(.regular)
```

---

### Performance Issues / Frame Drops

**Symptoms**: Stuttering, lag, dropped frames.

**Solutions**:

1. **Wrap in GlassEffectContainer**
   ```swift
   GlassEffectContainer {
       // Multiple glass elements
   }
   ```

2. **Limit glass elements**
   - Maximum 4-6 glass elements visible at once

3. **Reduce `.interactive()` usage**
   ```swift
   // Only for primary actions
   .glassEffect(.regular.interactive())
   ```

4. **Use lazy containers**
   ```swift
   LazyVStack {
       ForEach(items) { item in
           ItemView(item).glassEffect()
       }
   }
   ```

---

### Morphing Animation Not Working

**Symptoms**: Elements don't morph smoothly between states.

**Solutions**:

1. **Ensure same namespace**
   ```swift
   @Namespace private var namespace

   // Both must use same namespace
   Button("A") { }
       .glassEffectID("btn", in: namespace)
   Button("B") { }
       .glassEffectID("btn", in: namespace)  // Same namespace
   ```

2. **Wrap in GlassEffectContainer**
   ```swift
   GlassEffectContainer {
       // Morphing elements must be inside container
   }
   ```

3. **Use proper animation**
   ```swift
   withAnimation(.bouncy) {
       isExpanded.toggle()
   }
   ```

4. **Add transition modifier**
   ```swift
   .transition(.glassEffectTransition(.matchedGeometry))
   ```

---

### Animation Stuttering

**Symptoms**: Jerky, non-smooth animations.

**Solutions**:

1. **Use recommended animations**
   ```swift
   // ✅ Good
   withAnimation(.bouncy) { }
   withAnimation(.spring(response: 0.3)) { }

   // ❌ Avoid
   withAnimation(.linear) { }
   ```

2. **Animate state, not glass properties**
   ```swift
   // ✅ Good - animate state
   @State private var isExpanded = false
   withAnimation { isExpanded.toggle() }

   // ❌ Bad - animate glass directly
   @State private var tintOpacity = 0.5
   .glassEffect(.regular.tint(.blue.opacity(tintOpacity)))
   ```

3. **Reduce morphing element count**
   - Keep morphing groups to 4-6 elements max

---

### Interactive Effects Not Responding

**Symptoms**: `.interactive()` has no effect.

**Solutions**:

1. **Verify touch/pointer target size**
   ```swift
   // Ensure adequate padding
   Button { } label: {
       Image(systemName: "star")
           .padding(16)  // At least 44pt touch target
   }
   .glassEffect(.regular.interactive())
   ```

---

### Nested Glass Visual Artifacts

**Symptoms**: Visual glitches, unexpected blending.

**Solution**: Avoid nesting glass effects.

```swift
// ❌ Wrong - nested glass
VStack {
    HStack { }.glassEffect()
}.glassEffect()

// ✅ Correct - flat structure
GlassEffectContainer {
    VStack {
        HStack { }
    }
    .glassEffect()
}
```

---

### Mixed Variant Visual Issues

**Symptoms**: Inconsistent glass appearance in same view.

**Solution**: Use same variant throughout.

```swift
// ❌ Wrong
HStack {
    Button("A") { }.glassEffect(.regular)
    Button("B") { }.glassEffect(.clear)
}

// ✅ Correct
HStack {
    Button("A") { }.glassEffect(.regular)
    Button("B") { }.glassEffect(.regular)
}
```

---

### Backward Compatibility Crashes

**Symptoms**: Crash on iOS 17/18 devices.

**Solution**: Use availability checks.

```swift
if #available(iOS 26.0, *) {
    Button("Action") { }
        .glassEffect()
} else {
    Button("Action") { }
        .background(.ultraThinMaterial)
}
```

---

## Debug Checklist

When glass isn't working as expected:

1. [ ] iOS 26+ deployment target set?
2. [ ] Element on navigation layer (not content)?
3. [ ] `GlassEffectContainer` wrapping multiple glass elements?
4. [ ] Same glass variant used throughout?
5. [ ] Reduce Transparency accessibility setting checked?
6. [ ] Background content exists behind glass?
7. [ ] Namespace correctly shared for morphing?
8. [ ] Using recommended animation types?
9. [ ] Touch targets at least 44x44pt?
10. [ ] Tested on actual device (not just simulator)?
