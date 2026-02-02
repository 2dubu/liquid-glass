# Toolbar Glass Component

> Source: [LiquidGlass-Handbook](https://github.com/jaikrishnavj/LiquidGlass-Handbook)

## Overview

Toolbars automatically get Liquid Glass treatment in iOS 26. Items are placed on a glass surface that floats above content.

## Basic Toolbar

```swift
NavigationStack {
    ContentView()
        .toolbar {
            ToolbarItem(placement: .primaryAction) {
                Button("Save", systemImage: "checkmark") { }
            }
        }
}
```

## Toolbar with Glass Button Styles

```swift
.toolbar {
    ToolbarItemGroup(placement: .bottomBar) {
        Button("Edit", systemImage: "pencil") { }
            .buttonStyle(.glass)

        Spacer()

        Button("Share", systemImage: "square.and.arrow.up") { }
            .buttonStyle(.glass)

        Button("Delete", systemImage: "trash") { }
            .buttonStyle(.glassProminent)
            .tint(.red)
    }
}
```

## Toolbar Grouping

Group related actions together with `ToolbarItemGroup`:

```swift
.toolbar {
    // Left group - drawing tools
    ToolbarItemGroup(placement: .topBarLeading) {
        Button("Draw", systemImage: "pencil") { }
        Button("Erase", systemImage: "eraser") { }
    }

    // Flexible spacer
    ToolbarItem(placement: .principal) {
        ToolbarSpacer(.flexible)
    }

    // Right group - actions
    ToolbarItemGroup(placement: .topBarTrailing) {
        Button("Save", systemImage: "checkmark") { }
    }
}
```

## Dynamic Toolbar (Selection Mode)

Show/hide toolbar items based on state:

```swift
struct SelectableListView: View {
    @State private var isSelecting = false
    @State private var selectedItems: Set<UUID> = []

    var body: some View {
        List {
            // List content
        }
        .toolbar {
            ToolbarItem(placement: .topBarTrailing) {
                Button(isSelecting ? "Done" : "Select") {
                    withAnimation { isSelecting.toggle() }
                }
                .buttonStyle(.glass)
            }

            // Show only when items are selected
            if !selectedItems.isEmpty {
                ToolbarItemGroup(placement: .bottomBar) {
                    Button("Star", systemImage: "star") { }
                        .buttonStyle(.glass)

                    Spacer()

                    Button("Delete", systemImage: "trash") { }
                        .buttonStyle(.glassProminent)
                        .tint(.red)
                }
            }
        }
    }
}
```

## Toolbar with Menu

```swift
.toolbar {
    ToolbarItem(placement: .topBarTrailing) {
        Menu {
            Button("Sort A-Z", systemImage: "arrow.up") { }
            Button("Sort Z-A", systemImage: "arrow.down") { }
            Divider()
            Button("Filter", systemImage: "line.3.horizontal.decrease") { }
        } label: {
            Image(systemName: "ellipsis.circle")
        }
    }
}
```

## Key Points

- Toolbar items automatically get glass backgrounds
- Use `ToolbarItemGroup` to group related actions
- Use `ToolbarSpacer(.flexible)` for dynamic spacing
- `.glass` for secondary actions, `.glassProminent` for primary
- Glass background provides visual unity for grouped items
