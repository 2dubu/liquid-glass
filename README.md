<img width="100%" alt="image" src="https://github.com/user-attachments/assets/583ee6ce-47c7-4976-a478-55101dc73a0a" />

# Liquid Glass

A comprehensive iOS 26 Liquid Glass deep-dive toolkit for SwiftUI developers.

Built for developers who want ready-to-use answers and code generation for iOS 26 Liquid Glass.

## Features

- **Complete API Reference** - Glass struct, glassEffect modifier, GlassEffectContainer
- **Code Generation** - Ready-to-use patterns and components
- **Migration Guide** - Convert iOS 17/18 materials to Liquid Glass
- **Morphing Animations** - Namespace, glassEffectID, transitions
- **System Components** - Sheets, alerts, pickers, toolbars
- **Accessibility** - Reduce Transparency, VoiceOver, Dynamic Type
- **Performance Optimization** - Best practices and profiling tips
- **Troubleshooting** - Common issues and solutions
- **Cross-Platform** - iOS, macOS, watchOS, tvOS, visionOS differences
- **Backward Compatibility** - Supporting iOS 17/18 alongside iOS 26

## Installation

### 1. Add the marketplace
```bash
/plugin marketplace add 2dubu/liquid-glass
```

### 2. Install the Skill
```bash
/plugin install liquid-glass@liquid-glass
```

## Usage

Once installed, you can ask Claude about Liquid Glass topics:

```
/liquid-glass How do I add a floating action button with Liquid Glass?
/liquid-glass Migrate this iOS 17 material background to iOS 26
/liquid-glass Why isn't my glass effect showing?
/liquid-glass How do I create morphing animations with glass?
/liquid-glass What's the difference between .regular and .clear glass?
/liquid-glass Review my Liquid Glass implementation for HIG compliance
```

Or Claude may recognize related queries and invoke the skill automatically.

## Reference Documentation

### API & Concepts (`references/`)

14 comprehensive reference files:

| File | Description |
|------|-------------|
| `core-concepts.md` | Fundamental principles, navigation layer rule |
| `glass-struct.md` | Glass struct API reference |
| `glass-effect-modifier.md` | glassEffect modifier reference |
| `glass-effect-container.md` | GlassEffectContainer usage |
| `morphing-animations.md` | Animation techniques |
| `button-styles.md` | Button style reference |
| `system-components.md` | System component styling |
| `migration-guide.md` | iOS 17/18 to iOS 26 migration |
| `accessibility.md` | Accessibility considerations |
| `performance.md` | Performance optimization |
| `best-practices.md` | Design patterns and rules |
| `troubleshooting.md` | Common issues and fixes |
| `backward-compatibility.md` | Multi-version support |
| `platform-differences.md` | Cross-platform guide |

### Component Implementation (`components/`)

10 component-specific implementation guides:

| File | Description |
|------|-------------|
| `toolbar.md` | Toolbar glass styling, grouping, dynamic toolbars |
| `tab-bar.md` | Tab bar glass, minimize behavior, alignment |
| `sheet.md` | Sheet presentations with glass backgrounds |
| `search.md` | Searchable views with glass styling |
| `picker.md` | Segmented, menu, wheel, color pickers |
| `scroll-edge.md` | Floating headers/footers, scroll blur effects |
| `system-alerts.md` | Alerts, dialogs, menus, context menus |
| `glass-overlap.md` | Overlapping glass, zIndex, depth effects |
| `material-hierarchy.md` | ultraThin, thin, regular, thick materials |
| `animations.md` | Expanding, rotating, merging animations |

## Quick Reference

### Core API
```swift
// Basic
.glassEffect()
.glassEffect(.regular)
.glassEffect(.clear)
.glassEffect(.identity)

// With shape
.glassEffect(in: .capsule)
.glassEffect(in: .circle)

// Modifiers
.glassEffect(.regular.tint(.blue))
.glassEffect(.regular.interactive())  // iOS only

// Button styles
.buttonStyle(.glass)
.buttonStyle(.glassProminent)

// Container
GlassEffectContainer { /* glass elements */ }

// Morphing
.glassEffectID("id", in: namespace)
```

### The Five Golden Rules

1. **Navigation Layer Only** - Glass for toolbars, FABs, NOT content
2. **No Glass on Glass** - Use `GlassEffectContainer`
3. **Don't Mix Variants** - Same variant throughout
4. **Tint for Meaning** - Semantic, not decorative
5. **Trust Accessibility** - System handles it automatically

## Requirements

- Claude Code CLI
- iOS 26.0+ / macOS Tahoe 26.0+ / visionOS 26.0+ project
- Xcode 26.0+

## Resources

- [WWDC25 Session 323: Build a SwiftUI app with the new design](https://developer.apple.com/videos/play/wwdc2025/323/)
- [WWDC25 Session 219: Meet Liquid Glass](https://developer.apple.com/videos/play/wwdc2025/219/)
- [Apple Developer Documentation](https://developer.apple.com/documentation/SwiftUI/Applying-Liquid-Glass-to-custom-views)
- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)

## Contributing

PRs welcome! Please ensure:
- Code examples are tested on iOS 26.0
- Follow existing formatting
- Add troubleshooting entries for new edge cases
- Update relevant reference files

## License

MIT License - see LICENSE file for details
