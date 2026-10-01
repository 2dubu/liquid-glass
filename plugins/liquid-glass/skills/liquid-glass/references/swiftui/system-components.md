# SwiftUI system components on iOS

Use system containers before adding custom glass. Build against the current supported SDK and inspect the app on each supported runtime: standard controls and navigation adopt Liquid Glass from iOS 26, while later releases add their own behavior. Existing custom backgrounds can obscure those effects. Read [compatibility](../compatibility.md) for deployment and SDK distinctions, and [Apple's adoption guide](https://developer.apple.com/documentation/technologyoverviews/adopting-liquid-glass) for system behavior.

## Navigation and toolbars

Start with `NavigationStack`, a real scrollable content view, and semantic toolbar placements. Leave the navigation and toolbar backgrounds under system control while establishing the baseline. Adding `.glassEffect` to every toolbar button or replacing the bar with `.ultraThinMaterial` is not a required migration step. Standard toolbars already manage their glass surfaces and scroll-edge behavior. See [Adopting Liquid Glass](https://developer.apple.com/documentation/technologyoverviews/adopting-liquid-glass).

Use `ToolbarItemGroup` for related actions. Use `ToolbarSpacer(.fixed, placement:)` to separate groups that should not share one background, or `.flexible` when the purpose is flexible space. A spacer is `ToolbarContent`, so put it directly in the `.toolbar` builder beside items. `ToolbarItem { ToolbarSpacer(...) }` fails because the item requires view content. See [ToolbarSpacer](https://developer.apple.com/documentation/swiftui/toolbarspacer).

`sharedBackgroundVisibility(_:)` belongs to `ToolbarContent`. Hiding an item's shared background also separates it into its own grouping; it is not a view-level opacity adjustment. Use it for a concrete design requirement, and check the resulting grouping. See [sharedBackgroundVisibility](https://developer.apple.com/documentation/swiftui/toolbarcontent/sharedbackgroundvisibility(_:)).

### Additional toolbar controls in iOS 27

Keep these APIs behind iOS 27 availability checks; they are not prerequisites for iOS 26 glass:

| Need | API and behavior |
|---|---|
| Prefer an action when width is constrained | `ToolbarContent.visibilityPriority(_:)`; lower-priority items move into overflow first. |
| Keep secondary actions in overflow | `ToolbarOverflowMenu` in the toolbar builder; its contents always belong to the overflow menu. |
| Pin a trailing action | `.topBarPinnedTrailing`; even pinned items can enter overflow when search is active and space is insufficient. |
| Minimize navigation chrome while scrolling | `toolbarMinimizationBehavior(_:for:)` with `.navigationBar`; an integrated top tab bar also minimizes. |

Navigation-bar minimization and iOS 26 tab-bar minimization are different APIs. Navigation minimization adjusts the safe area by default; inspect `toolbarMinimizationSafeAreaAdjustment(_:for:)` only when that adjustment conflicts with the intended layout. Test overflow actions, active search, and changing width together. [Visibility priority](https://developer.apple.com/documentation/swiftui/toolbarcontent/visibilitypriority(_:)), [Overflow menu](https://developer.apple.com/documentation/swiftui/toolbaroverflowmenu), [Pinned placement](https://developer.apple.com/documentation/swiftui/toolbaritemplacement/topbarpinnedtrailing), [Navigation minimization](https://developer.apple.com/documentation/swiftui/view/toolbarminimizationbehavior(_:for:)).

The iOS 27.1 beta APIs also allow vertical bars in supported device contexts. Read `toolbarVerticalEdge` to position app-owned controls relative to the system's preferred edge; it does not prove a bar is currently visible. Keep `toolbarVerticalBehavior(.disabled)` a stable layout choice for screens that need horizontal bars, rather than toggling it to hide chrome. Use toolbar visibility APIs for visibility. Check the current beta SDK and runtime before adopting these APIs. [Vertical edge](https://developer.apple.com/documentation/swiftui/environmentvalues/toolbarverticaledge), [Vertical behavior](https://developer.apple.com/documentation/swiftui/view/toolbarverticalbehavior(_:)).

## Scroll edges and custom bars

A scroll edge effect maintains separation where scrolling content meets controls. It is not equivalent to a Material rectangle layered over a ScrollView. Use the relevant API for the job:

| Need | iOS 26+ API and responsibility |
|---|---|
| Keep the system navigation/toolbar behavior | Standard containers supply their edge behavior; inspect existing background overrides first. |
| Install a custom top or bottom bar | `safeAreaBar(edge:alignment:spacing:content:)` measures the bar, adjusts safe area, and extends affected scroll edge effects. |
| Choose an edge style | `scrollEdgeEffectStyle(_:for:)` changes the automatic style for specified edges. |
| Hide an edge effect deliberately | `scrollEdgeEffectHidden(_:for:)` controls visibility for specified edges. |

The [safeAreaBar documentation](https://developer.apple.com/documentation/swiftui/view/safeareabar(edge:alignment:spacing:content:)) has horizontal and vertical overloads; use the vertical-edge overload for `.top` or `.bottom`. See also [scrollEdgeEffectStyle](https://developer.apple.com/documentation/swiftui/view/scrolledgeeffectstyle(_:for:)) and [scrollEdgeEffectHidden](https://developer.apple.com/documentation/swiftui/view/scrolledgeeffecthidden(_:for:)).

Do not use a hard-coded 60pt or 80pt content padding as a general solution for a custom bar. Verify the measured bar, safe-area ownership, keyboard, Dynamic Type, and rotation. `safeAreaInset` can reserve space on earlier iOS versions, but that does not promise the iOS 26 scroll-edge behavior of `safeAreaBar`. See [safeAreaInset](https://developer.apple.com/documentation/swiftui/view/safeareainset(edge:alignment:spacing:content:)).

## Tabs and search

For complete examples of suggestions, scopes, and typed tab selection, read [search and tabs](search-and-tabs.md). It also covers replacing older API forms without dropping support for earlier iOS versions.

For tab-based navigation, use `TabView` and the standard tab APIs. `.tabViewStyle(.tabBarOnly)` chooses a tab-bar presentation where possible; it does not remove the selected tab's content and is available from iOS 18. See [TabBarOnlyTabViewStyle](https://developer.apple.com/documentation/swiftui/tabbaronlytabviewstyle).

When search belongs to a tab, give that tab the `.search` role. This is a semantic role, not merely a magnifying-glass icon. Searchable tab views use the role to route search; without a designated search tab, search state can reset as selection changes. See [TabRole.search](https://developer.apple.com/documentation/swiftui/tabrole/search).

On iOS 26, `tabBarMinimizeBehavior(_:)` opts into the documented scrolling behavior. A `tabViewBottomAccessory` can adapt between a position above the expanded bar and an inline position when it collapses on iPhone. Use `tabViewBottomAccessoryPlacement` to adapt the accessory's content instead of guessing offsets. Verify the actual scrolling container and device; do not infer minimization from the modifier's presence alone. See [minimization](https://developer.apple.com/documentation/swiftui/view/tabbarminimizebehavior(_:)) and [bottom accessories](https://developer.apple.com/documentation/swiftui/view/tabviewbottomaccessory(content:)).

For ordinary navigation search, use `.searchable`. A parent can control presentation with `searchable(text:isPresented:placement:prompt:)`, available from iOS 17. Bind presentation and query state separately, and prepare expensive search results outside the rendering path. See [the searchable overload](https://developer.apple.com/documentation/swiftui/view/searchable(text:ispresented:placement:prompt:)).

If you instead use `@Environment(\.isSearching)` or `@Environment(\.dismissSearch)`, read those values inside a child wrapped by `.searchable`. Reading them in the parent that applies the modifier does not observe the environment supplied to its descendants. This can compile successfully while failing to detect or dismiss search. Apple's [search activation guide](https://developer.apple.com/documentation/swiftui/managing-search-interface-activation) explains the scope requirement.

Search placement is contextual. Begin with automatic placement and test keyboard entry, cancellation, navigation, and tab changes. A custom search background can change the behavior you intended to adopt; remove it while establishing the system baseline.

## Sheets, alerts, menus, and controls

Use `.sheet`, `presentationDetents`, and a semantic sheet hierarchy. Let the system choose the default sheet background. Partial-height sheets in the iOS 26 design are inset, and their appearance changes as they expand. `.presentationBackground(.ultraThinMaterial)` is an explicit older-Material override, not an API that turns on Liquid Glass. See [adoption guidance](https://developer.apple.com/documentation/technologyoverviews/adopting-liquid-glass) and [presentationBackground](https://developer.apple.com/documentation/swiftui/view/presentationbackground(_:)).

Keep ordinary `.alert`, `.confirmationDialog`, `Menu`, `Picker`, `Toggle`, and `Slider` semantics. Their system appearance depends on control, placement, and interaction state. In particular, a slider or toggle's glass response during interaction is not a reason to wrap the entire control in a blur card. Test pressed, focused, disabled, and selected states instead of describing every control as having an identical permanent glass background. See [Adopting Liquid Glass](https://developer.apple.com/documentation/technologyoverviews/adopting-liquid-glass).

For a standalone custom action, [custom effects](custom-effects.md) covers `.glass` and `.glassProminent` button styles. Both start at iOS 26.0; the explicit `GlassButtonStyle(_:)` initializer starts at 26.1. Do not generalize that initializer's availability to unrelated controls or attribute an observed menu issue to iOS 26.1 without reproduction. See [GlassButtonStyle](https://developer.apple.com/documentation/swiftui/glassbuttonstyle) and [its Glass initializer](https://developer.apple.com/documentation/swiftui/glassbuttonstyle/init(_:)).

## Verify the ownership boundary

| Symptom | Inspect first |
|---|---|
| System bar looks opaque or unlike the default | Custom backgrounds, appearance modifiers, SDK/OS combination, and compatibility settings. |
| Toolbar grouping is wrong | Item placement, spacer kind/placement, and shared-background visibility. |
| Scrolling content obscures a custom bar | Safe-area/bar registration and edge effect ownership before adding another blur layer. |
| Search state never changes | Binding wiring or the Environment reader's position below `.searchable`. |
| Sheet appearance differs by height | Detents, custom presentation background, and the current runtime's default behavior. |
| Control behavior differs on another device | Container, size class, accessibility settings, and interaction state. |

Use [diagnosis](../diagnosis.md) for a reproducible investigation and [design and accessibility](../design-and-accessibility.md) for appearance/accessibility checks. A screenshot proves one rendered state; it does not establish a framework contract across versions.
