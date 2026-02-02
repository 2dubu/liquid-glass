# System Components Reference

## Overview

iOS 26 introduces Liquid Glass styling for many system components. Some are automatic, others require explicit style application.

## Automatic Glass (No Code Required)

When compiled with Xcode 26, these components automatically adopt Liquid Glass:

### iOS 26
- NavigationBar
- TabBar
- Sheets (partial height)
- Alerts
- Search bars
- Toolbars

### macOS Tahoe
- Toolbar
- Sidebar
- Menu bar

## Explicit Glass Styles

### Sheets & Presentations

Partial height sheets automatically get Liquid Glass background in iOS 26. **Apple recommends removing custom `presentationBackground` modifiers** to let the new material shine.

```swift
// ✅ Recommended: Let system apply glass automatically
.sheet(isPresented: $showSheet) {
    ContentView()
        .presentationDetents([.medium, .large])
}

// ⚠️ Only if you need explicit glass (usually not needed)
.sheet(isPresented: $showSheet) {
    ContentView()
        .presentationBackground(.glass)
}
```

**Sheet Behavior**: At partial heights, bottom edges pull in and nest in display corners. When transitioning to full height, the glass becomes opaque and anchors to screen edges.

### Tab Views

```swift
TabView {
    HomeView()
        .tabItem { Label("Home", systemImage: "house") }
    SettingsView()
        .tabItem { Label("Settings", systemImage: "gear") }
}
.tabViewStyle(.glass)
```

### Scroll Edge Effect

Control the blur/dim effect at scroll view edges. Use `scrollEdgeEffectStyle(_:for:)`:

```swift
ScrollView {
    // Content
}
.scrollEdgeEffectStyle(.soft, for: .top)
.scrollEdgeEffectStyle(.hard, for: .bottom)
```

**Available Styles:**
| Style | Description |
|-------|-------------|
| `.automatic` | Default scroll edge style |
| `.hard` | Sharp edge with visible dividing line |
| `.soft` | Rounded, diffused effect (more fluid) |

**Note**: ScrollView, List, and Form automatically have scroll edge effects in iOS 26. Use this modifier to customize or disable (`.hard`) the effect.

### Pickers

```swift
Picker("Options", selection: $selected) {
    ForEach(options) { option in
        Text(option.title)
    }
}
.pickerStyle(.glass)
```

### Search Fields

```swift
TextField("Search...", text: $searchText)
    .searchFieldStyle(.glass)
```

### Alerts

```swift
.alert("Title", isPresented: $showAlert) {
    Button("OK") { }
    Button("Cancel", role: .cancel) { }
}
.alertStyle(.glass)
```

### Confirmation Dialogs

```swift
.confirmationDialog("Choose Action", isPresented: $showDialog) {
    Button("Option 1") { }
    Button("Option 2") { }
    Button("Cancel", role: .cancel) { }
}
.confirmationDialogStyle(.glass)
```

## Toolbars

Toolbar items automatically get Liquid Glass treatment:

```swift
.toolbar {
    ToolbarItemGroup(placement: .bottomBar) {
        Button("Edit", systemImage: "pencil") { }
        Spacer()
        Button("Share", systemImage: "square.and.arrow.up") { }
    }
}
```

### Custom Toolbar Styling

```swift
.toolbar {
    ToolbarItem(placement: .primaryAction) {
        Button("Save") { }
            .buttonStyle(.glassProminent)
    }
}
```

## Navigation Bar

Navigation bars automatically adopt glass, but you can customize:

```swift
NavigationStack {
    ContentView()
        .navigationTitle("Title")
        .toolbarBackground(.glass, for: .navigationBar)
}
```

## Sidebar (macOS/iPadOS)

```swift
NavigationSplitView {
    List(items) { item in
        NavigationLink(item.title, value: item)
    }
    .listStyle(.sidebar)  // Automatically glass on macOS Tahoe
} detail: {
    DetailView()
}
```

## Inspector

```swift
ContentView()
    .inspector(isPresented: $showInspector) {
        InspectorContent()
    }
    .inspectorBackground(.glass)
```

## Best Practices

### Let System Handle It
Most components work best with automatic glass styling. Avoid over-customizing.

```swift
// ✅ Let system handle it
NavigationStack {
    List { }
}

// ❌ Over-customizing
NavigationStack {
    List { }
        .background(.glass)  // Unnecessary
}
```

### Consistent Styling
Use the same glass style across related components:

```swift
// ✅ Consistent
.sheet { }.presentationBackground(.glass)
.alert { }.alertStyle(.glass)

// ❌ Mixed
.sheet { }.presentationBackground(.glass)
.alert { }  // No glass style - inconsistent
```
