# System Glass Components (Alerts, Menus, Dialogs)

> Source: [LiquidGlass-Handbook](https://github.com/jaikrishnavj/LiquidGlass-Handbook)

## Overview

System components in iOS 26 automatically get glass backgrounds. Alerts, confirmation dialogs, menus, sliders, and context menus all have glass styling by default.

## Alert

Alerts automatically have glass backgrounds:

```swift
struct AlertDemo: View {
    @State private var showAlert = false

    var body: some View {
        Button("Show Alert") {
            showAlert = true
        }
        .buttonStyle(.glass)
        .alert("Delete Item?", isPresented: $showAlert) {
            Button("Cancel", role: .cancel) { }
            Button("Delete", role: .destructive) { }
        } message: {
            Text("This action cannot be undone.")
        }
    }
}
```

## Confirmation Dialog

```swift
struct ConfirmationDialogDemo: View {
    @State private var showDialog = false

    var body: some View {
        Button("Choose Action") {
            showDialog = true
        }
        .buttonStyle(.glass)
        .confirmationDialog("Select Option", isPresented: $showDialog) {
            Button("Option 1") { }
            Button("Option 2") { }
            Button("Option 3") { }
            Button("Cancel", role: .cancel) { }
        }
    }
}
```

## Menu

```swift
struct MenuDemo: View {
    var body: some View {
        Menu {
            Button("Add to Favorites", systemImage: "star") { }
            Button("Add to Collection", systemImage: "heart") { }
            Divider()
            Button("Delete", systemImage: "trash", role: .destructive) { }
        } label: {
            Image(systemName: "ellipsis.circle")
                .font(.title2)
                .padding(12)
        }
        .buttonStyle(.glass)
    }
}
```

## Menu with Sections

```swift
Menu {
    Section("Edit") {
        Button("Cut", systemImage: "scissors") { }
        Button("Copy", systemImage: "doc.on.doc") { }
        Button("Paste", systemImage: "doc.on.clipboard") { }
    }

    Section("Share") {
        Button("AirDrop", systemImage: "airplay") { }
        Button("Messages", systemImage: "message") { }
    }

    Section {
        Button("Delete", systemImage: "trash", role: .destructive) { }
    }
} label: {
    Label("Actions", systemImage: "ellipsis.circle")
}
```

## Slider with Glass Background

```swift
struct SliderDemo: View {
    @State private var volume: Double = 0.5
    @State private var brightness: Double = 0.7

    var body: some View {
        VStack(spacing: 20) {
            // Volume slider
            HStack {
                Image(systemName: "speaker.fill")
                Slider(value: $volume)
                Image(systemName: "speaker.wave.3.fill")
            }
            .padding()
            .background(.thinMaterial)
            .clipShape(RoundedRectangle(cornerRadius: 12))

            // Brightness slider
            HStack {
                Image(systemName: "sun.min.fill")
                Slider(value: $brightness)
                Image(systemName: "sun.max.fill")
            }
            .padding()
            .background(.thinMaterial)
            .clipShape(RoundedRectangle(cornerRadius: 12))
        }
        .padding()
    }
}
```

## Context Menu

```swift
struct ContextMenuDemo: View {
    var body: some View {
        Text("Long press me")
            .font(.headline)
            .padding(24)
            .background(.ultraThinMaterial)
            .clipShape(RoundedRectangle(cornerRadius: 16))
            .contextMenu {
                Button("Copy", systemImage: "doc.on.doc") { }
                Button("Share", systemImage: "square.and.arrow.up") { }
                Divider()
                Button("Delete", systemImage: "trash", role: .destructive) { }
            }
    }
}
```

## Context Menu with Preview

```swift
struct ContextMenuWithPreview: View {
    var body: some View {
        Text("Long press for preview")
            .padding()
            .background(.thinMaterial)
            .clipShape(RoundedRectangle(cornerRadius: 12))
            .contextMenu {
                Button("Open") { }
                Button("Share") { }
            } preview: {
                // Custom preview view
                VStack {
                    Image(systemName: "photo")
                        .font(.largeTitle)
                    Text("Preview Content")
                }
                .frame(width: 200, height: 200)
            }
    }
}
```

## Toggle with Glass Style

```swift
struct ToggleDemo: View {
    @State private var isEnabled = true

    var body: some View {
        Toggle("Enable Feature", isOn: $isEnabled)
            .padding()
            .background(.thinMaterial)
            .clipShape(RoundedRectangle(cornerRadius: 12))
    }
}
```

## Stepper with Glass

```swift
struct StepperDemo: View {
    @State private var quantity = 1

    var body: some View {
        Stepper("Quantity: \(quantity)", value: $quantity, in: 1...10)
            .padding()
            .background(.thinMaterial)
            .clipShape(RoundedRectangle(cornerRadius: 12))
    }
}
```

## Key Points

- Alert dialogs have automatic glass backgrounds
- Confirmation dialogs use glass styling
- Menus appear with glass material
- Sliders can be wrapped in glass containers
- Context menus triggered by long press have glass backgrounds
- Use `.thinMaterial` for custom glass wrappers
- System colors (blue, purple, green, orange) work well
- Automatic dark/light mode adaptation
