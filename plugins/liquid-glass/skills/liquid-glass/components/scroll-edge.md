# Scroll Edge Effects Component

## Overview

Scroll edge effects create depth by blurring content as it scrolls behind glass headers and footers. This creates a layered glass hierarchy.

## Basic Scroll with Glass Header/Footer

```swift
struct ScrollWithGlassEdges: View {
    var body: some View {
        ZStack {
            // Background
            LinearGradient(
                colors: [.blue, .purple, .pink],
                startPoint: .topLeading,
                endPoint: .bottomTrailing
            )
            .ignoresSafeArea()

            // Scrollable content
            ScrollView {
                LazyVStack(spacing: 0) {
                    ForEach(0..<30, id: \.self) { index in
                        ScrollListItem(index: index)
                    }
                }
                .padding(.top, 80)   // Space for header
                .padding(.bottom, 80) // Space for footer
            }

            // Glass overlays
            VStack {
                FloatingGlassHeader()
                Spacer()
                FloatingGlassFooter()
            }
        }
    }
}
```

## Floating Glass Header

```swift
struct FloatingGlassHeader: View {
    var body: some View {
        HStack {
            Text("Items")
                .font(.headline)

            Spacer()

            Button {
                // Filter action
            } label: {
                Image(systemName: "line.3.horizontal.decrease.circle")
            }
            .buttonStyle(.glass)
        }
        .padding()
        .background(.ultraThinMaterial)
        .clipShape(RoundedRectangle(cornerRadius: 16))
        .padding(.horizontal)
        .padding(.top, 8)
    }
}
```

## Floating Glass Footer

```swift
struct FloatingGlassFooter: View {
    var body: some View {
        HStack(spacing: 16) {
            Button("Cancel") {
                // Cancel action
            }
            .buttonStyle(.glass)

            Spacer()

            Button("Apply") {
                // Apply action
            }
            .buttonStyle(.glassProminent)
        }
        .padding()
        .background(.thinMaterial)
        .clipShape(RoundedRectangle(cornerRadius: 16))
        .padding(.horizontal)
        .padding(.bottom, 8)
    }
}
```

## Scroll List Item

```swift
struct ScrollListItem: View, Equatable {
    let index: Int

    var body: some View {
        HStack {
            Circle()
                .fill(colors[index % colors.count])
                .frame(width: 40, height: 40)
                .overlay(
                    Text("\(index + 1)")
                        .foregroundStyle(.white)
                        .font(.caption.bold())
                )

            VStack(alignment: .leading) {
                Text("Item \(index + 1)")
                    .font(.body)
                Text("Description for item")
                    .font(.caption)
                    .foregroundStyle(.secondary)
            }

            Spacer()
        }
        .padding()
        .background(.ultraThinMaterial.opacity(0.5))

        Divider()
            .padding(.leading, 60)
    }

    private var colors: [Color] {
        [.blue, .purple, .pink, .orange, .green]
    }
}
```

## Content Clipping Under Glass

When content scrolls, it naturally blurs under the glass layers:

```swift
struct ClippedScrollContent: View {
    var body: some View {
        ScrollView {
            VStack(spacing: 16) {
                ForEach(0..<20, id: \.self) { index in
                    ContentCard(index: index)
                }
            }
            .padding()
        }
        // Content automatically blurs under navigation bar
        .toolbarBackground(.ultraThinMaterial, for: .navigationBar)
    }
}
```

## Layered Glass Hierarchy

Create depth with multiple glass layers:

```swift
struct LayeredGlassView: View {
    var body: some View {
        ZStack {
            // Layer 1: Background content
            BackgroundImage()

            // Layer 2: Main content with light material
            ScrollView {
                ContentStack()
            }
            .background(.ultraThinMaterial.opacity(0.3))

            // Layer 3: Floating controls with stronger material
            VStack {
                Spacer()
                ControlBar()
                    .background(.regularMaterial)
                    .clipShape(Capsule())
            }
        }
    }
}
```

## ScrollView with Safe Area Handling

```swift
struct SafeAreaScrollView: View {
    var body: some View {
        ScrollView {
            LazyVStack {
                ForEach(items) { item in
                    ItemRow(item: item)
                }
            }
            .safeAreaPadding(.top, 60)    // For floating header
            .safeAreaPadding(.bottom, 80)  // For floating footer
        }
        .overlay(alignment: .top) {
            FloatingGlassHeader()
        }
        .overlay(alignment: .bottom) {
            FloatingGlassFooter()
        }
    }
}
```

## Key Points

- Content blurs naturally when scrolling behind glass layers
- Layered glass creates depth and visual hierarchy
- Use `.ultraThinMaterial` for headers (maximum transparency)
- Use `.thinMaterial` or `.regularMaterial` for footers
- Add padding to content for header/footer space
- Use `safeAreaPadding` for proper spacing
- Smooth blur transitions enhance the experience
