# Design and accessibility on iOS

Read this for material choice, visual hierarchy, accessibility, or a design review. For API syntax or a rendering failure, use the relevant framework reference or [diagnosis](diagnosis.md).

## Choose the role before the effect

Use standard navigation, bars, presentations, and controls where they fit the product. Rebuild and inspect their system appearance before adding custom glass. Custom backgrounds can interfere with the material or scroll edge treatment. The first migration step is to understand the component's existing owner and appearance, rather than replace every material modifier. [Adopting Liquid Glass](https://developer.apple.com/documentation/technologyoverviews/adopting-liquid-glass)

Liquid Glass separates controls and navigation from content. Keep ordinary content surfaces in the content layer. This is not a ban on controls located inside content: standard sliders and toggles can gain glass during interaction. [HIG Materials](https://developer.apple.com/design/human-interface-guidelines/materials)

Separate these questions when reviewing multiple effects:

- **Design:** Do the surfaces form a clear functional layer, or obscure each other and compete with content?
- **Rendering:** Are related custom effects grouped with the framework's glass container?

`GlassEffectContainer` shares sampling between nearby SwiftUI effects. It is not a guarantee that arbitrary stacked surfaces form a good hierarchy. UIKit also distinguishes ordinary glass nesting from `UIGlassContainerEffect` grouping; do not turn the design warning into a ban on every nested effect view. [SwiftUI WWDC25](https://developer.apple.com/videos/play/wwdc2025/323/), [UIKit WWDC25, 21:02 and 23:52](https://developer.apple.com/videos/play/wwdc2025/284/)

## Choose regular or clear

Prefer regular for general controls and text-heavy surfaces. Consider clear over rich media when preserving the background matters and foreground controls remain legible. For bright backgrounds, HIG suggests considering dark dimming at 35% opacity. Sufficiently dark content or AVKit's own dimming can make an additional layer unnecessary. [HIG Materials](https://developer.apple.com/design/human-interface-guidelines/materials)

The `Glass.clear` documentation uses a 30% black background as an example. Neither that example nor the HIG value is a universal opacity to apply to every scene. Inspect the actual background, foreground, and existing treatments. [Glass.clear](https://developer.apple.com/documentation/swiftui/glass/clear)

WWDC25 advises against mixing variants. Keep related controls visually consistent, but distinguish this design advice from an API prohibition: the presentation does not define an app-wide scope covering unrelated screens. A union has its own documented shape and variant requirements. [Meet Liquid Glass](https://developer.apple.com/videos/play/wwdc2025/219/), [glassEffectUnion](https://developer.apple.com/documentation/swiftui/view/glasseffectunion(id:namespace:))

Use tint to convey emphasis or meaning, especially a primary action. Avoid tinting every control merely to add branding; put expressive color in the content where appropriate. [Meet Liquid Glass](https://developer.apple.com/videos/play/wwdc2025/219/)

## Preserve system adaptation; verify app-owned behavior

The material responds to the person's preferred Liquid Glass appearance as well as accessibility preferences, including Reduce Transparency, Increase Contrast, and Reduce Motion. A system appearance preference and the app's `.regular`/`.clear` variant are separate inputs; do not infer one from the other. This does not establish that custom layout, colors, animations, or accessibility semantics are correct. Apple explicitly asks developers to test custom elements, colors, and animations under different settings. [Meet Liquid Glass](https://developer.apple.com/videos/play/wwdc2025/219/), [Adopting Liquid Glass](https://developer.apple.com/documentation/technologyoverviews/adopting-liquid-glass)

Do not replace all system glass with a solid fill or another material merely because Reduce Transparency is enabled. First observe the system result and identify the remaining app-owned problem. Likewise, do not remove a custom accessibility treatment solely because the material adapts automatically.

Choose checks that exercise the changed behavior:

| App-owned behavior | Verify |
| --- | --- |
| Custom movement or morphing | Reduce Motion, interruption, state changes, and an understandable reduced-motion result |
| Tint or custom foreground colors | Actual media backgrounds, light/dark and Liquid Glass appearance preferences, Increase Contrast, and Reduce Transparency |
| Icon-only actions and expanding groups | VoiceOver labels, focus order, selection/expanded state, and which actions remain available |
| Custom control layout | Dynamic Type, clipping, hit area, and content obscured by floating controls |

For custom SwiftUI animation, read `accessibilityReduceMotion`; Apple specifically advises avoiding large animations, especially simulated three-dimensional motion, when it is enabled. The framework's glass animation and an app's explicit transforms are separate things to assess. [accessibilityReduceMotion](https://developer.apple.com/documentation/swiftui/environmentvalues/accessibilityreducemotion)

## Keep action labels localizable

Preserve localization context in app-owned labels. SwiftUI localized literals are appropriate for fixed UI copy; use `LocalizedStringKey` or `LocalizedStringResource` when a model carries localizable action titles, with an initializer supported by the deployment target. Do not mistake user content for a localization key or resolve all labels eagerly into `String`. For packages/frameworks, use the correct resource bundle with APIs supported by the toolchain. [LocalizedStringKey](https://developer.apple.com/documentation/swiftui/localizedstringkey), [LocalizedStringResource](https://developer.apple.com/documentation/foundation/localizedstringresource).

Review longer translations and right-to-left layout alongside Dynamic Type. Use semantic leading/trailing alignment and avoid fixed text bounds that clip labels. A shorter translation should not require a different control identity. The [search examples](swiftui/search-and-tabs.md) keep UI scope labels separate from data-source strings.

## Make performance claims from measurements

Group related custom effects as documented. Avoid multiplying containers or applying effects indiscriminately; both excessive containers and effects outside containers can increase rendering cost. The cited guidance gives no fixed maximum number of glass views or morphing elements; do not invent one. [Applying Liquid Glass to custom views](https://developer.apple.com/documentation/swiftui/applying-liquid-glass-to-custom-views)

For SwiftUI hitches, also inspect [state and performance](swiftui/state-and-performance.md). Unnecessary view updates and result computation need different corrections from expensive material rendering.

For a performance investigation, compare the same interaction, content, and device before and after the change. Record the iOS build, toolchain, device, measurement method, and relevant accessibility state. Use representative physical iPhones for claims about iPhone responsiveness or energy. A simulator result, successful compile, or decompiled implementation is not a device performance measurement.
