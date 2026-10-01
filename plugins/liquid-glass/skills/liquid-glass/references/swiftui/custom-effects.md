# SwiftUI custom effects on iOS

Use this reference for custom controls, explicit glass shapes, blending, and transitions. For navigation bars, tabs, sheets, or search, start with [system components](system-components.md). Read [design and accessibility](../design-and-accessibility.md) before deciding that a custom surface needs glass.

## Select the smallest public API

| Intent | Public API | iOS availability |
|---|---|---|
| Style an ordinary button | `.buttonStyle(.glass)` or `.buttonStyle(.glassProminent)` | 26.0+ |
| Supply a Glass value to a button style | `GlassButtonStyle(_:)` | 26.1+ |
| Give a custom control an explicit glass shape | `glassEffect(_:in:)` | 26.0+ |
| Render nearby glass shapes together | `GlassEffectContainer(spacing:content:)` | 26.0+ |
| Coordinate appearing/disappearing effects | `glassEffectID(_:in:)`, `glassEffectTransition(_:)` | 26.0+ |
| Join compatible effects into one surface | `glassEffectUnion(id:namespace:)` | 26.0+ |

Prefer a standard button style for a standalone button. Custom effects are useful when the control needs a specific shape or participates in a coordinated container. Applying `buttonStyle` inside a button's label does not style the enclosing button. [GlassButtonStyle](https://developer.apple.com/documentation/swiftui/glassbuttonstyle) and [custom effects](https://developer.apple.com/documentation/swiftui/applying-liquid-glass-to-custom-views) describe these distinct paths.

The Glass-taking initializer requires iOS 26.1 even though the no-argument glass style exists in 26.0. See [the initializer's availability](https://developer.apple.com/documentation/swiftui/glassbuttonstyle/init(_:)) and [compatibility](../compatibility.md) for an availability-guarded button. An OS-specific appearance observation is not evidence that an API has the same version requirement.

## Size and shape are separate decisions

Apply padding and sizing that define the control before `glassEffect`. Supply its intended shape through `in:`. The default is a capsule; putting the modifier on a transparent `RoundedRectangle` does not transfer that rectangle's corner radius to the effect. Clipping an already rendered effect is also a different operation from defining its shape. [DefaultGlassEffectShape](https://developer.apple.com/documentation/swiftui/defaultglasseffectshape) documents the default.

Start with `.regular`. Treat `.clear` and tint as design choices that require the actual underlying content and contrast checks, rather than interchangeable ways to make glass visible. `Glass` is distinct from the older SwiftUI `Material` styles. A `.thinMaterial` background remains a Material background; it does not implement Liquid Glass interaction or morphing. See [Glass](https://developer.apple.com/documentation/swiftui/glass) and [Material](https://developer.apple.com/documentation/swiftui/material).

`interactive(_:)` adds the effect's touch response. It does not create an action, a button role, or a meaningful accessibility label. Keep actionable content inside `Button` or another semantic control, and retain a meaningful label for icon-only actions. See [interactive(_:)](https://developer.apple.com/documentation/swiftui/glass/interactive(_:)).

Do not invent an `isEnabled:` argument on `glassEffect`. Use the declared signature, and consider `Glass.identity` when the product actually needs to switch the effect off. An accessibility preference alone does not require removing all system glass; first inspect the system's own adaptation. [glassEffect(_:in:)](https://developer.apple.com/documentation/swiftui/view/glasseffect(_:in:)) and [Glass.identity](https://developer.apple.com/documentation/swiftui/glass/identity) define the supported values.

## Distinguish three kinds of composition

**Proximity blending.** Place related effects in one `GlassEffectContainer`. Its spacing controls how close shapes must be to interact; layout spacing controls where the views are placed. Increasing container spacing can make nearby effects blend even at rest. Negative HStack spacing and `zIndex` alone do not establish this composition contract. [GlassEffectContainer](https://developer.apple.com/documentation/swiftui/glasseffectcontainer) explains the relationship.

**Insertion and removal.** Give effects stable, distinct `glassEffectID` values in a shared namespace and animate the state change that inserts or removes them. Use `.matchedGeometry` when a neighboring shape should provide transition geometry, `.materialize` for an appearance/disappearance transition, or `.identity` when no effect transition is wanted. These are `GlassEffectTransition` values, not general SwiftUI `AnyTransition` values; `.opacity` and `.scale` are not valid substitutes. The container's geometry and spacing remain relevant. See [applying custom effects](https://developer.apple.com/documentation/swiftui/applying-liquid-glass-to-custom-views), [matchedGeometry](https://developer.apple.com/documentation/swiftui/glasseffecttransition/matchedgeometry), and [GlassEffectTransition](https://developer.apple.com/documentation/swiftui/glasseffecttransition).

**A unified surface.** Use `glassEffectUnion(id:namespace:)` when compatible effects should contribute to one persistent surface. Sharing a union identifier has a different purpose from assigning transition identities. The documented union behavior depends on compatible shapes and Glass variants. See [glassEffectUnion](https://developer.apple.com/documentation/swiftui/view/glasseffectunion(id:namespace:)).

Do not put every surface in one global container. Group the effects that need to interact, then measure the real composition. A container is not a guarantee of a particular frame rate; too many containers or independent effects can still be expensive. Apple's [custom-effects guide](https://developer.apple.com/documentation/swiftui/applying-liquid-glass-to-custom-views) recommends limiting simultaneous effects and profiling rendering.

For changing tint or material on an existing control, preserve its structural identity by varying the modifier's argument. For dynamic action collections, distinguish stable `ForEach` IDs from glass effect IDs and union IDs. Read [state and performance](state-and-performance.md) for an example and the cases where insertion/removal is intentional.

## Make motion intentional and cancellable

Use `accessibilityReduceMotion` to adjust app-owned transitions while preserving the available actions and understandable state. For example, an expanding action group can select an identity glass transition and disable its explicit animation when Reduce Motion is enabled. This does not establish that every system-provided animation is absent. See [accessibilityReduceMotion](https://developer.apple.com/documentation/swiftui/environmentvalues/accessibilityreducemotion).

```swift
import SwiftUI

@available(iOS 26.0, *)
struct GlassActions: View {
    @Environment(\.accessibilityReduceMotion) private var reduceMotion
    @Namespace private var namespace
    @State private var expanded = false
    let bookmark: () -> Void

    var body: some View {
        GlassEffectContainer(spacing: 24) {
            HStack(spacing: 12) {
                Button(expanded ? "Less" : "More") {
                    var transaction = Transaction(animation: reduceMotion ? nil : .spring())
                    transaction.disablesAnimations = reduceMotion
                    withTransaction(transaction) { expanded.toggle() }
                }
                .padding(12)
                .glassEffect(.regular.interactive())
                .glassEffectID("toggle", in: namespace)

                if expanded {
                    Button("Bookmark", action: bookmark)
                        .padding(12)
                        .glassEffect(.regular.interactive())
                        .glassEffectID("bookmark", in: namespace)
                        .glassEffectTransition(reduceMotion ? .identity : .matchedGeometry)
                }
            }
            .buttonStyle(.plain)
        }
    }
}
```

If another design requires async repetition, honor task cancellation. `try? await Task.sleep(...)` inside `while true` discards cancellation and can leave the body running after the view disappears. Exit on cancellation or propagate it through an appropriate task boundary. Duration alone does not explain rendering jank. See [Task.sleep](https://developer.apple.com/documentation/swift/task/sleep(for:tolerance:clock:)).

## Custom shape interpolation

When a custom `Shape` needs its own animatable properties, prefer `@Animatable` synthesis with a supporting toolchain. Mark non-animating stored properties with `@AnimatableIgnored`. This is separate from the system's glass morphing APIs; ordinary `glassEffectID` transitions do not require a custom `Animatable` conformance. [Animatable macro](https://developer.apple.com/documentation/swiftui/animatable()).

Implement `animatableData` explicitly when interpolation needs custom setter behavior such as clamping or updating a derived value. `AnimatableValues` requires iOS 26; retain an appropriate `AnimatablePair` implementation for earlier deployment targets. The macro's compiler/SDK support and the availability of the generated types are separate checks. [AnimatableValues](https://developer.apple.com/documentation/swiftui/animatablevalues), [AnimatablePair](https://developer.apple.com/documentation/swiftui/animatablepair).

## Diagnose before changing effects

| Symptom | First check |
|---|---|
| API fails to compile | Exact SDK declaration, argument labels, and the enclosing availability guard; distinguish 26.0 from 26.1. |
| Rounded rectangle looks like a capsule | The shape supplied to `glassEffect`, not just the source view's shape. |
| Shapes overlap but do not perform the intended merge | Shared container, layout geometry, and container spacing. |
| Insertion has the wrong transition | Stable IDs, shared namespace, actual hierarchy change, and the `GlassEffectTransition` value. |
| A tint change resets control state | Conditional view replacement, changing model IDs, or a recreated parent/namespace; see [state and performance](state-and-performance.md). |
| Glass looks wrong only over some content | Underlay, contrast, appearance/accessibility settings, and any extra background or opacity modifier. |
| Motion continues after dismissal | Task cancellation and ownership before assuming a rendering bug. |

Use [diagnosis](../diagnosis.md) for a reproducible investigation. Check transitions and accessible actions in the actual app, including relevant motion, transparency, contrast, and text-size settings. Internal symbols do not become production API recommendations.
