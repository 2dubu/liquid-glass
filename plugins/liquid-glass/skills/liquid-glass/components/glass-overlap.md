# Glass Overlap & Layering Component

## Overview

Overlapping glass elements create natural depth effects. Use `zIndex()` to control layering order. Glass dynamically adapts to background content when overlapped.

## Draggable Overlap Demo

```swift
struct DraggableOverlapDemo: View {
    @State private var offset1 = CGSize.zero
    @State private var offset2 = CGSize(width: 50, height: 50)

    var body: some View {
        ZStack {
            // First glass layer
            Circle()
                .fill(.clear)
                .frame(width: 120, height: 120)
                .glassEffect()
                .offset(offset1)
                .gesture(
                    DragGesture()
                        .onChanged { value in
                            offset1 = value.translation
                        }
                )
                .zIndex(1)

            // Second glass layer
            Circle()
                .fill(.clear)
                .frame(width: 120, height: 120)
                .glassEffect()
                .offset(offset2)
                .gesture(
                    DragGesture()
                        .onChanged { value in
                            offset2 = CGSize(
                                width: 50 + value.translation.width,
                                height: 50 + value.translation.height
                            )
                        }
                )
                .zIndex(0)
        }
    }
}
```

## Stacked Glass Cards

```swift
struct StackedGlassDemo: View {
    var body: some View {
        ZStack {
            // Bottom card
            RoundedRectangle(cornerRadius: 20)
                .fill(.clear)
                .frame(width: 200, height: 120)
                .glassEffect()
                .offset(x: 0, y: 0)
                .zIndex(0)

            // Middle card
            RoundedRectangle(cornerRadius: 20)
                .fill(.clear)
                .frame(width: 200, height: 120)
                .glassEffect()
                .offset(x: 20, y: 20)
                .zIndex(1)

            // Top card
            RoundedRectangle(cornerRadius: 20)
                .fill(.clear)
                .frame(width: 200, height: 120)
                .glassEffect()
                .offset(x: 40, y: 40)
                .zIndex(2)
        }
    }
}
```

## Intersecting Glass Circles

```swift
struct IntersectingGlassDemo: View {
    var body: some View {
        HStack(spacing: -30) {  // Negative spacing for overlap
            Circle()
                .fill(.clear)
                .frame(width: 80, height: 80)
                .glassEffect(.regular.tint(.blue))
                .zIndex(2)

            Circle()
                .fill(.clear)
                .frame(width: 80, height: 80)
                .glassEffect(.regular.tint(.purple))
                .zIndex(1)

            Circle()
                .fill(.clear)
                .frame(width: 80, height: 80)
                .glassEffect(.regular.tint(.pink))
                .zIndex(0)
        }
    }
}
```

## Layered Glass with Depth

```swift
struct LayeredDepthDemo: View {
    var body: some View {
        ZStack {
            // Background layer - most blur
            RoundedRectangle(cornerRadius: 24)
                .fill(.clear)
                .frame(width: 280, height: 180)
                .background(.thickMaterial)
                .clipShape(RoundedRectangle(cornerRadius: 24))

            // Middle layer
            RoundedRectangle(cornerRadius: 20)
                .fill(.clear)
                .frame(width: 240, height: 140)
                .background(.regularMaterial)
                .clipShape(RoundedRectangle(cornerRadius: 20))

            // Front layer - least blur
            RoundedRectangle(cornerRadius: 16)
                .fill(.clear)
                .frame(width: 200, height: 100)
                .background(.ultraThinMaterial)
                .clipShape(RoundedRectangle(cornerRadius: 16))
                .overlay(
                    Text("Front")
                        .font(.headline)
                )
        }
    }
}
```

## Interactive Overlap with Animation

```swift
struct AnimatedOverlapDemo: View {
    @State private var isOverlapped = false

    var body: some View {
        ZStack {
            Circle()
                .fill(.clear)
                .frame(width: 100, height: 100)
                .glassEffect(.regular.tint(.blue))
                .offset(x: isOverlapped ? 0 : -40)
                .zIndex(0)

            Circle()
                .fill(.clear)
                .frame(width: 100, height: 100)
                .glassEffect(.regular.tint(.purple))
                .offset(x: isOverlapped ? 0 : 40)
                .zIndex(1)
        }
        .onTapGesture {
            withAnimation(.spring(response: 0.4, dampingFraction: 0.7)) {
                isOverlapped.toggle()
            }
        }
    }
}
```

## Floating Buttons with Overlap

```swift
struct FloatingOverlapButtons: View {
    var body: some View {
        HStack(spacing: -10) {
            ForEach(["star", "heart", "bookmark"], id: \.self) { icon in
                Button {
                    // Action
                } label: {
                    Image(systemName: icon)
                        .font(.title3)
                        .padding(14)
                }
                .glassEffect(.regular.interactive())
            }
        }
    }
}
```

## Key Points

- Overlapping glass creates natural depth effects
- Use `zIndex()` to control layering order
- Higher zIndex = closer to viewer
- Glass dynamically adapts to background content
- Negative spacing creates overlap in stacks
- Use different materials for depth hierarchy:
  - `.ultraThinMaterial` - front (least blur)
  - `.regularMaterial` - middle
  - `.thickMaterial` - back (most blur)
- Animate overlap with `.spring()` for fluid motion
- Tinted glass creates colorful blending when overlapped
