# Sheet Glass Component

> Source: [LiquidGlass-Handbook](https://github.com/jaikrishnavj/LiquidGlass-Handbook)

## Overview

Sheets in iOS 26 support glass backgrounds with `.presentationBackground()`. Partial height sheets are inset with glass by default.

## Basic Sheet with Glass

```swift
struct ContentView: View {
    @State private var showSheet = false

    var body: some View {
        Button("Show Sheet") {
            showSheet = true
        }
        .sheet(isPresented: $showSheet) {
            SheetContent()
                .presentationBackground(.ultraThinMaterial)
        }
    }
}
```

## Sheet with Detents

```swift
.sheet(isPresented: $showSheet) {
    SheetContent()
        .presentationDetents([.medium, .large])
        .presentationBackground(.ultraThinMaterial)
}
```

### Available Detents

| Detent | Description |
|--------|-------------|
| `.medium` | Half screen |
| `.large` | Full screen |
| `.fraction(0.3)` | 30% of screen |
| `.height(200)` | Fixed 200pt |

## Glass Sheet Content

```swift
struct SheetContent: View {
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        NavigationStack {
            VStack {
                // Sheet content
                Toggle("Option 1", isOn: .constant(true))
                Toggle("Option 2", isOn: .constant(false))

                Spacer()

                // Floating action bar
                FloatingGlassActionBar()
            }
            .padding()
            .navigationTitle("Settings")
            .toolbar {
                ToolbarItem(placement: .cancellationAction) {
                    Button("Cancel") { dismiss() }
                        .buttonStyle(.glass)
                }
                ToolbarItem(placement: .confirmationAction) {
                    Button("Done") { dismiss() }
                        .buttonStyle(.glassProminent)
                }
            }
        }
    }
}
```

## Floating Glass Action Bar

```swift
struct FloatingGlassActionBar: View {
    var body: some View {
        HStack(spacing: 16) {
            Button {
                // Favorite action
            } label: {
                Image(systemName: "heart")
                    .padding(12)
            }
            .buttonStyle(.glass)

            Button {
                // Share action
            } label: {
                Image(systemName: "square.and.arrow.up")
                    .padding(12)
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
    }
}
```

## Presentation Background Options

```swift
// Material backgrounds (iOS 16.4+)
.presentationBackground(.ultraThinMaterial)  // Maximum transparency
.presentationBackground(.thinMaterial)       // High transparency
.presentationBackground(.regularMaterial)    // Standard blur
.presentationBackground(.thickMaterial)      // Heavy blur

// Custom color with material
.presentationBackground {
    Color.blue.opacity(0.1)
        .background(.ultraThinMaterial)
}
```

## Drag Indicator

Drag indicator automatically adapts to background:

```swift
.sheet(isPresented: $showSheet) {
    SheetContent()
        .presentationDragIndicator(.visible)  // Shows drag handle
        .presentationBackground(.ultraThinMaterial)
}
```

## Key Points

- Sheets support `.presentationBackground()` with materials
- Drag indicator automatically adapts to background color
- Partial height sheets have glass backgrounds by default
- At smaller heights, bottom edges pull in to nest in display curves
- Full height transitions glass to opaque
- Use `.thinMaterial` or `.ultraThinMaterial` for floating action bars
