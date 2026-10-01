# UIKit system components and migration

Start by running the app with the current SDK and target runtime, then identify which container owns each navigation bar, toolbar, tab bar, and scroll view. Standard UIKit components adopt the new appearance when built with the supporting SDK and run on iOS 26 or later. This does not require raising the app's minimum deployment target. [Adopting Liquid Glass](https://developer.apple.com/documentation/technologyoverviews/adopting-liquid-glass), [Build a UIKit app with the new design](https://developer.apple.com/videos/play/wwdc2025/284/).

## Check the iOS 27 launch requirement

Before investigating appearance after an SDK upgrade, confirm the app adopts UIKit's scene-based life cycle. Apple documents that apps built with the latest SDK must use it to launch on iOS 27. Configure scenes through the app's scene manifest or dynamic scene configuration, and move window/UI life-cycle work to its scene owner. This is an SDK-linked launch requirement, not a reason to raise the deployment target or add custom glass. [UIKit updates](https://developer.apple.com/documentation/updates/uikit), [Scene migration](https://developer.apple.com/documentation/uikit/transitioning-to-the-uikit-scene-based-life-cycle).

## Keep system bars under their container's control

| UI | Public integration point | Review when migrating |
| --- | --- | --- |
| Navigation bar | `UINavigationController`, each screen's `navigationItem` | Background appearance overrides, custom title/item layout, large-title scroll geometry, push/pop transitions |
| Toolbar | A navigation controller's toolbar and each screen's `toolbarItems` | Semantic item grouping, spacers, custom views, and visibility |
| Tab bar | `UITabBarController` and its tabs | Custom background overlays, inset assumptions, minimize behavior, and bottom accessories |
| Standard buttons | `UIButton.Configuration.glass()` / `.prominentGlass()` | Meaningful emphasis, enabled state, accessible names, and layout margins |
| Custom floating controls | App-owned content plus explicit public glass/scroll-edge APIs | Effect ownership, scroll-view association, clipping, safe area, and hit testing |

Do not add another glass background behind a system bar to make it “more glass.” Apple's migration guidance recommends reviewing and generally removing old custom background effects that interfere with the system appearance. Treat that as a screen-level migration decision: identify the purpose of each existing override before removing it, and compare the result on older supported iOS versions too. [Adoption guide](https://developer.apple.com/documentation/technologyoverviews/adopting-liquid-glass), [UIKit WWDC25, 10:42](https://developer.apple.com/videos/play/wwdc2025/284/).

Audit `standardAppearance`, `scrollEdgeAppearance`, compact variants, background images/colors, and app-wide `UIAppearance` configuration. An opaque background can be intentional; it should not be mistaken for a broken glass renderer. Avoid replacing all appearance objects globally as a first debugging step.

## Navigation, toolbar grouping, and scrolling

Assign `UIBarButtonItem` values to `navigationItem` or `toolbarItems`. The system determines default glass grouping from the items and their styles. Fixed-space items can separate groups. Flexible-space items normally separate toolbar backgrounds; set their `hidesSharedBackground` to false when evenly distributed items should share a background. Review existing spacer tricks and use layout margins when providing a custom bar-item view. [UIKit WWDC25, 7:12–11:09](https://developer.apple.com/videos/play/wwdc2025/284/).

Use symbol items with accessibility labels. A bar item's `tintColor` colors its content; the `.prominent` style can emphasize its background. Do not create a custom visual-effect hierarchy just to color one standard action. [UIKit WWDC25, 8:53](https://developer.apple.com/videos/play/wwdc2025/284/).

Large titles and scroll-edge treatment depend on the actual scroll view extending under the navigation bar. Do not pin every scroll view to the top safe-area edge as a universal fix. Preserve the container's automatic content-inset behavior and review both frame geometry and adjusted content insets. With custom containers, confirm which scroll view is associated with the bar. [UIKit WWDC25, 9:29–11:48](https://developer.apple.com/videos/play/wwdc2025/284/).

## Scroll-edge effects are separate from glass effects

System navigation bars and toolbars coordinate scroll-edge treatment automatically. For a custom overlay container, register the overlay using `UIScrollEdgeElementContainerInteraction`, set its `scrollView`, and choose the edge. Descendant labels, images, glass views, and controls then contribute to the treatment. [UIScrollEdgeElementContainerInteraction](https://developer.apple.com/documentation/uikit/uiscrolledgeelementcontainerinteraction).

```swift
import UIKit

@MainActor
@available(iOS 26.0, *)
func registerBottomOverlay(_ overlay: UIView, over scrollView: UIScrollView) {
    let interaction = UIScrollEdgeElementContainerInteraction()
    interaction.scrollView = scrollView
    interaction.edge = .bottom
    overlay.addInteraction(interaction)
}
```

This registers the visual relationship; the app still owns overlay layout, safe-area accommodation, and hit testing. Do not register the same overlay repeatedly during layout. Choose `scrollView.topEdgeEffect.style = .hard` when the denser treatment fits the interface, rather than assuming a separate blur view is required. A scroll-edge effect does not turn ordinary content into Liquid Glass. [UIKit WWDC25, 11:20–11:48](https://developer.apple.com/videos/play/wwdc2025/284/).

## Tab bars and bottom accessories

Use `UITabBarController` for a system tab bar. On iOS 26, `tabBarMinimizeBehavior` can opt into scroll-driven minimization, and `UITabAccessory(contentView:)` provides a bottom accessory. A minimizing tab bar changes available space; adapt accessory content using the public `tabAccessoryEnvironment` trait instead of looking for private subviews or fixed tab-bar heights. Test scroll direction changes and accessory transitions in the actual container. [UIKit WWDC25, 2:31–3:35](https://developer.apple.com/videos/play/wwdc2025/284/).

Do not add an independent glass effect over the tab bar or hard-code insets from a screenshot. A SwiftUI `TabView`, UIKit tab controller, and custom app tab strip have different owners and adaptation paths even if they look similar.

## Vertical bars in the iOS 27.1 beta SDK

Supported device contexts can place bar content vertically. Continue to use container-owned items and safe areas instead of assuming all navigation and toolbar controls occupy the top or bottom edge. `preferredVerticalBarBehavior` can opt a controller into a stable horizontal-bar layout when the UI requires it; hiding bars for a screen remains a visibility concern. Avoid repeatedly toggling the layout preference with transient screen state. Recheck beta availability and behavior on the target device. [preferredVerticalBarBehavior](https://developer.apple.com/documentation/uikit/uiviewcontroller/preferredverticalbarbehavior), [UIKit updates](https://developer.apple.com/documentation/updates/uikit).

## UIKit and SwiftUI hosting

Use [UIHostingController](https://developer.apple.com/documentation/swiftui/uihostingcontroller) for a SwiftUI hierarchy presented by UIKit, and [UIViewControllerRepresentable](https://developer.apple.com/documentation/swiftui/uiviewcontrollerrepresentable) for a UIKit controller in SwiftUI.

Choose the navigation owner explicitly:

- If a UIKit navigation controller owns the bar, configure the hosted screen's `navigationItem` through that UIKit controller integration.
- If SwiftUI owns navigation, configure its toolbar/navigation APIs in that hierarchy.
- When embedding a complete UIKit navigation controller, avoid adding an extra SwiftUI navigation bar unless two separate navigation regions are intentional.

For UIKit child containment, perform the normal `addChild` / add view and constraints / `didMove(toParent:)` sequence. For a representable, preserve controller identity and update state in its update method; use a coordinator for callbacks where needed. A successful bridge does not establish that all glass grouping or adaptation crosses the framework boundary. Reproduce the actual mixed hierarchy before claiming equivalence.

## Diagnose ownership and appearance

| Symptom | Inspect first |
| --- | --- |
| Bar stays opaque | Local and global appearance overrides, background views, actual system/custom bar owner |
| Large title or edge effect looks wrong | Scroll-view frame under the bar, inset adjustment, associated scroll view, content length |
| Empty glass bar item | Whether only its custom content was hidden rather than the whole item |
| Extra glass outline or doubled bar | Nested navigation containers, app overlay plus system background, custom button background inside a system item |
| Accessory clips after minimization | Trait-driven accessory layout and available space, not a fixed tab-bar height |
| Glass differs only inside hosting | Which hierarchy owns navigation and scrolling, bridge sizing/safe areas, inherited appearance |

Change one cause at a time. Record the runtime build and compare with a minimal standard-container example. Use disassembler evidence only to explain a specific version-scoped path after checking public contracts and actual hierarchy; private view names are not stable integration points.
