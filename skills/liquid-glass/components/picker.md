# Picker Glass Component

> Source: [LiquidGlass-Handbook](https://github.com/jaikrishnavj/LiquidGlass-Handbook)

## Overview

Pickers in iOS 26 automatically get glass styling. Segmented controls use glass backgrounds, and picker wheels have translucent styling.

## Segmented Picker

```swift
struct SegmentedPickerDemo: View {
    @State private var viewMode = "List"
    let options = ["List", "Grid", "Card"]

    var body: some View {
        Picker("View Mode", selection: $viewMode) {
            ForEach(options, id: \.self) { option in
                Text(option).tag(option)
            }
        }
        .pickerStyle(.segmented)
        .padding()
        .background(.thinMaterial)
        .clipShape(RoundedRectangle(cornerRadius: 12))
    }
}
```

## Menu Picker

```swift
struct MenuPickerDemo: View {
    @State private var selectedSize = "Medium"
    let sizes = ["Small", "Medium", "Large", "Extra Large"]

    var body: some View {
        Picker("Size", selection: $selectedSize) {
            ForEach(sizes, id: \.self) { size in
                Text(size).tag(size)
            }
        }
        .pickerStyle(.menu)
        .padding()
        .background(.regularMaterial)
        .clipShape(RoundedRectangle(cornerRadius: 12))
    }
}
```

## Wheel Picker

```swift
struct WheelPickerDemo: View {
    @State private var selectedNumber = 5

    var body: some View {
        Picker("Number", selection: $selectedNumber) {
            ForEach(1...10, id: \.self) { number in
                Text("\(number)").tag(number)
            }
        }
        .pickerStyle(.wheel)
        .frame(height: 150)
        .background(.ultraThinMaterial)
        .clipShape(RoundedRectangle(cornerRadius: 16))
    }
}
```

## Color Picker with Glass

```swift
struct ColorPickerDemo: View {
    @State private var selectedColor = "Blue"
    let colors = ["Red", "Green", "Blue", "Purple", "Orange"]

    var body: some View {
        HStack(spacing: 12) {
            ForEach(colors, id: \.self) { color in
                Circle()
                    .fill(colorValue(for: color))
                    .frame(width: 44, height: 44)
                    .overlay(
                        Circle()
                            .stroke(selectedColor == color ? .white : .clear, lineWidth: 3)
                    )
                    .scaleEffect(selectedColor == color ? 1.1 : 1.0)
                    .onTapGesture {
                        withAnimation(.spring(response: 0.3)) {
                            selectedColor = color
                        }
                    }
            }
        }
        .padding()
        .background(.thinMaterial)
        .clipShape(RoundedRectangle(cornerRadius: 20))
    }

    func colorValue(for name: String) -> Color {
        switch name {
        case "Red": return .red
        case "Green": return .green
        case "Blue": return .blue
        case "Purple": return .purple
        case "Orange": return .orange
        default: return .gray
        }
    }
}
```

## Date Picker

```swift
struct DatePickerDemo: View {
    @State private var selectedDate = Date()

    var body: some View {
        DatePicker(
            "Select Date",
            selection: $selectedDate,
            displayedComponents: [.date]
        )
        .datePickerStyle(.graphical)
        .padding()
        .background(.ultraThinMaterial)
        .clipShape(RoundedRectangle(cornerRadius: 16))
    }
}
```

## Picker in Form

```swift
struct FormPickerDemo: View {
    @State private var priority = "Normal"
    @State private var category = "Work"

    var body: some View {
        Form {
            Section {
                Picker("Priority", selection: $priority) {
                    Text("Low").tag("Low")
                    Text("Normal").tag("Normal")
                    Text("High").tag("High")
                }

                Picker("Category", selection: $category) {
                    Text("Work").tag("Work")
                    Text("Personal").tag("Personal")
                    Text("Shopping").tag("Shopping")
                }
            }
        }
        .scrollContentBackground(.hidden)
        .background(.ultraThinMaterial)
    }
}
```

## Picker Styles

| Style | Description |
|-------|-------------|
| `.automatic` | System chooses based on context |
| `.segmented` | Horizontal segmented control |
| `.menu` | Dropdown menu |
| `.wheel` | Spinning wheel |
| `.inline` | Inline in lists |
| `.navigationLink` | Pushes to selection screen |

## Key Points

- Segmented controls automatically use glass backgrounds
- Picker wheels have translucent styling
- Color pickers and menu pickers support automatic dark/light mode
- Use `.thinMaterial` or `.regularMaterial` for custom backgrounds
- Spring animations for selection feedback
- All pickers adapt to system appearance automatically
