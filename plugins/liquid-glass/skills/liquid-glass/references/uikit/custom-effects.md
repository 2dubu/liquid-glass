# UIKit custom Liquid Glass effects

Use this reference for custom UIKit controls on iOS 26 and later. Keep the app's existing deployment target; place new API usage behind availability checks when the app supports earlier iOS versions. Prefer a standard `UIButton.Configuration.glass()` or `.prominentGlass()` for an ordinary button. Introduce a custom effect view when its composition or behavior requires one. [Apple adoption guide](https://developer.apple.com/documentation/technologyoverviews/adopting-liquid-glass), [UIKit WWDC25, 17:52 and 19:28](https://developer.apple.com/videos/play/wwdc2025/284/).

## Configure the effect and its content separately

`UIGlassEffect` describes the material; `UIVisualEffectView` presents it. Add app content to `contentView`, never directly to the effect view. Give the effect view real bounds through layout and use dynamic content colors such as `.label` or `.secondaryLabel`. UIKit adapts glass and its content to the background and size. Do not expect a fixed blur radius or opacity. [UIGlassEffect](https://developer.apple.com/documentation/uikit/uiglasseffect), [UIVisualEffectView](https://developer.apple.com/documentation/uikit/uivisualeffectview), [UIKit WWDC25, 20:28–21:49](https://developer.apple.com/videos/play/wwdc2025/284/).

```swift
import UIKit

@MainActor
@available(iOS 26.0, *)
func makeGlassControl(content: UIView) -> UIVisualEffectView {
    let effect = UIGlassEffect(style: .regular)
    effect.isInteractive = true
    let effectView = UIVisualEffectView(effect: effect)
    effectView.cornerConfiguration = .capsule()
    content.translatesAutoresizingMaskIntoConstraints = false
    effectView.contentView.addSubview(content)
    NSLayoutConstraint.activate([
        content.leadingAnchor.constraint(equalTo: effectView.contentView.leadingAnchor),
        content.trailingAnchor.constraint(equalTo: effectView.contentView.trailingAnchor),
        content.topAnchor.constraint(equalTo: effectView.contentView.topAnchor),
        content.bottomAnchor.constraint(equalTo: effectView.contentView.bottomAnchor),
    ])
    return effectView
}
```

This function does not supply a size, action, accessibility label, or button semantics. The caller must provide them. Interactive glass changes material feedback; it does not turn an arbitrary view into an actionable control. Use a real `UIButton` or `UIControl` and define its action and accessible name. [isInteractive](https://developer.apple.com/documentation/uikit/uiglasseffect/isinteractive).

### Style, tint, and state

- Start with `.regular`; assess `.clear` against the intended background and contrast requirements. A style selection is not a promise of fixed translucency.
- Set `UIGlassEffect.tintColor` when the material should convey emphasis. A content label/button's tint or text color is a separate setting. Use semantic colors and review light/dark and accessibility configurations.
- `UIVisualEffectView.effect` has copy semantics. Configure a new effect and assign it when the state changes; do not rely on mutating a previously assigned instance.
- To materialize or dematerialize glass, animate assigning an effect or `nil`. Keep the effect view and its ancestors at alpha 1. Alpha on content inside `contentView` can be animated separately.
- Removing an effect does not remove its content or disable a control. Manage availability, hit testing, and accessibility state explicitly.

The effect-copy contract is declared by `UIVisualEffectView.h` in the iOS 26.5 SDK. Material transitions and tint updates follow [UIKit WWDC25, 22:15–23:20](https://developer.apple.com/videos/play/wwdc2025/284/). Alpha constraints are documented on [UIVisualEffectView](https://developer.apple.com/documentation/uikit/uivisualeffectview).

## Shape and nested containers

UIKit glass defaults to a capsule. Use `cornerConfiguration` to request `.capsule()`, a fixed radius such as `.fixed(16)`, or `.containerRelative()` when the corners should adapt to a surrounding container. Do not replace this with a guessed private mask or a hard-coded screen radius. [UIKit WWDC25, 20:49–21:23](https://developer.apple.com/videos/play/wwdc2025/284/).

Distinguish three situations:

| Situation | Guidance |
| --- | --- |
| Independent floating controls overlap in the visual design | Rework placement, presentation, or visibility so the control layer stays legible. Adding a container does not automatically fix the hierarchy. |
| A glass effect view lives in another glass effect view's `contentView` | UIKit explicitly demonstrates this composition and adapts its appearance. Review content and corner relationships in the actual hierarchy. |
| Multiple sibling glass elements should merge and adapt together | Use `UIGlassContainerEffect`; this has different semantics from a parent with an ordinary `UIGlassEffect`. |

A blanket “never nest glass” API rule would reject Apple's UIKit example. Its existence also does not justify decorating every content card with glass. Keep composition guidance separate from visual design intent. [UIKit WWDC25, 19:28, 21:02, and 23:52](https://developer.apple.com/videos/play/wwdc2025/284/).

## Group multiple glass elements

Create a `UIVisualEffectView` with `UIGlassContainerEffect`. Add each child `UIVisualEffectView(effect: UIGlassEffect(...))` to the container's `contentView`. Add each child's app content to that child's `contentView`. The container renders the glass elements together behind its content. [UIGlassContainerEffect](https://developer.apple.com/documentation/uikit/uiglasscontainereffect).

`spacing` specifies the distance at which elements begin merging. It does not position views. Set layout constraints or frames separately. To change the merge threshold, assign a newly configured container effect; to change element distance, update layout. [spacing](https://developer.apple.com/documentation/uikit/uiglasscontainereffect/spacing), [UIKit WWDC25, 24:12–24:33](https://developer.apple.com/videos/play/wwdc2025/284/).

Do not promise identical shapes for a given spacing across OS versions, element sizes, or accessibility settings. Grouping supports uniform adaptation and transitions; any quantified performance improvement needs measurement.

## Diagnose before changing the material

1. Confirm the API is available in the selected SDK and the failing runtime is iOS 26 or later.
2. Inspect actual ownership and hierarchy: outer container effect, child effects, and content views.
3. Verify nonzero bounds, separate layout distance from merge threshold, and look for ancestor alpha, masks, clipping, or opaque sibling overlays.
4. Check ordinary control behavior independently: hit testing, target-action, accessibility label, enabled state.
5. Compare dynamic colors, regular/clear, light/dark, Reduce Transparency, Increase Contrast, and Reduce Motion. System adaptation does not validate custom text, drawing, or animation automatically.
6. Capture the containing window or full screen. A snapshot of only the effect view may omit its effect.
7. If the result differs across versions, record OS build, SDK, device/Simulator, hierarchy, settings, and a minimal reproduction before inspecting version-scoped binary implementation.

The alpha, mask, and snapshot checks come from [UIVisualEffectView](https://developer.apple.com/documentation/uikit/uivisualeffectview). Accessibility review follows [Apple's adoption guide](https://developer.apple.com/documentation/technologyoverviews/adopting-liquid-glass). Keep internal symbols in diagnostic evidence, never in app implementation instructions.
