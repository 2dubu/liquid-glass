# iOS compatibility

Read this when adding a version gate, preserving older iOS support, or reconciling API availability with observed behavior.

## Three different version questions

| Value | What it determines |
|---|---|
| Xcode and its SDK | Which declarations the compiler knows |
| Deployment target | The oldest OS the app supports; availability guards protect newer APIs |
| Running iOS version and build | The system implementation and appearance actually in use |

Do not raise the deployment target to 26 merely to adopt Liquid Glass conditionally. Preserve the app's support policy. A recent SDK can compile both the new implementation and an older-OS fallback. An older SDK cannot recognize a new declaration simply because it is inside `#available`.

## Public availability checkpoints

| API | iOS introduced | Source |
|---|---|---|
| `Glass`, `glassEffect(_:in:)`, `GlassEffectContainer` | 26.0 | [glassEffect](https://developer.apple.com/documentation/swiftui/view/glasseffect(_:in:)), [container](https://developer.apple.com/documentation/swiftui/glasseffectcontainer) |
| `glassEffectID`, `glassEffectUnion`, `glassEffectTransition` | 26.0 | [transition](https://developer.apple.com/documentation/swiftui/view/glasseffecttransition(_:)) |
| `.buttonStyle(.glass)`, `.glassProminent` | 26.0 | [GlassButtonStyle](https://developer.apple.com/documentation/swiftui/glassbuttonstyle), [GlassProminentButtonStyle](https://developer.apple.com/documentation/swiftui/glassprominentbuttonstyle) |
| `GlassButtonStyle.init(_:)` with a `Glass` configuration | 26.1 | [initializer](https://developer.apple.com/documentation/swiftui/glassbuttonstyle/init(_:)) |
| `UIGlassEffect`, `UIGlassContainerEffect` | 26.0 | [effect](https://developer.apple.com/documentation/uikit/uiglasseffect), [container](https://developer.apple.com/documentation/uikit/uiglasscontainereffect) |

These are introduction versions, not a claim that every later build looks or behaves identically. Recheck the SDK declaration when using an overload not listed here. This skill makes no compatibility claim for macOS, tvOS, watchOS, or visionOS.

### APIs used alongside Liquid Glass

| API | iOS introduced | Source |
|---|---|---|
| `.searchSuggestions`, `.searchScopes(_:scopes:)` | 16.0 | [Suggestions](https://developer.apple.com/documentation/swiftui/view/searchsuggestions(_:)), [Scopes](https://developer.apple.com/documentation/swiftui/view/searchscopes(_:scopes:)) |
| `searchScopes(_:activation:_:)` | 16.4 | [Scope activation](https://developer.apple.com/documentation/swiftui/view/searchscopes(_:activation:_:)) |
| SwiftUI Observation integration | 17.0 | [Observation migration](https://developer.apple.com/documentation/swiftui/migrating-from-the-observable-object-protocol-to-the-observable-macro) |
| `.searchable(text:isPresented:placement:prompt:)` | 17.0 | [Presentation binding](https://developer.apple.com/documentation/swiftui/view/searchable(text:ispresented:placement:prompt:)) |
| `Tab`, `TabRole.search` | 18.0 | [Tab](https://developer.apple.com/documentation/swiftui/tab), [Search role](https://developer.apple.com/documentation/swiftui/tabrole/search) |
| `AnimatableValues` | 26.0 | [AnimatableValues](https://developer.apple.com/documentation/swiftui/animatablevalues) |
| `ToolbarOverflowMenu`, `.topBarPinnedTrailing`, `visibilityPriority(_:)` on toolbar content | 27.0 | [Overflow](https://developer.apple.com/documentation/swiftui/toolbaroverflowmenu), [Pinned placement](https://developer.apple.com/documentation/swiftui/toolbaritemplacement/topbarpinnedtrailing), [Priority](https://developer.apple.com/documentation/swiftui/toolbarcontent/visibilitypriority(_:)) |
| `toolbarMinimizationBehavior(_:for:)`, `TabRole.prominent` | 27.0 | [Minimization](https://developer.apple.com/documentation/swiftui/view/toolbarminimizationbehavior(_:for:)), [Prominent role](https://developer.apple.com/documentation/swiftui/tabrole/prominent) |
| `toolbarVerticalBehavior(_:)`, `toolbarVerticalEdge`, UIKit `preferredVerticalBarBehavior` | 27.1 beta | [SwiftUI behavior](https://developer.apple.com/documentation/swiftui/view/toolbarverticalbehavior(_:)), [Edge](https://developer.apple.com/documentation/swiftui/environmentvalues/toolbarverticaledge), [UIKit behavior](https://developer.apple.com/documentation/uikit/uiviewcontroller/preferredverticalbarbehavior) |

Do not conflate these introduction versions with the runtime's glass appearance. Keep the appropriate older-OS path when adopting the [search/tab examples](swiftui/search-and-tabs.md). A compiler macro such as `@Animatable` also requires a toolchain that supplies it; check the macro and generated code rather than inferring its deployment requirement from `AnimatableValues` alone.

## Structure the fallback at the call site

Put the actual newer API inside an availability branch, or put a newer-OS component behind an annotated type/function and guard its construction. A computed Bool such as `supportsGlass` is not a compiler availability check.

```swift
import SwiftUI

struct SaveButton: View {
    let save: () -> Void

    var body: some View {
        if #available(iOS 26.1, *) {
            Button("Save", action: save)
                .buttonStyle(GlassButtonStyle(.regular))
        } else if #available(iOS 26.0, *) {
            Button("Save", action: save)
                .buttonStyle(.glass)
        } else {
            Button("Save", action: save)
                .buttonStyle(.bordered)
        }
    }
}
```

For UIKit, construct `UIGlassEffect` only inside the supported branch; the older branch can preserve the app's existing control style or use an appropriate `UIBlurEffect`. Add foreground content to `UIVisualEffectView.contentView` in either case. The fallback should preserve the action, label, enabled state, and layout, even though the material differs.

For a SwiftUI Button, apply the chosen button style to the Button. Applying `.buttonStyle(.glass)` to `ButtonStyle.Configuration.label` does not provide a reliable implementation of an adaptive enclosing Button style.

Choose the fallback by UI purpose. A standard bordered button may need no blur at all. `Material` can be useful for an older custom surface, but it is a distinct material rather than an equivalent Liquid Glass renderer. Do not replace system accessibility adaptation with `.identity`; `Glass.identity` removes the effect. [Identity contract](https://developer.apple.com/documentation/swiftui/glass/identity)

## SDK-linked migration requirements

`UIDesignRequiresCompatibility` is an iOS 26 compatibility mechanism. Apple documents that the system ignores it when building for iOS 27 or later. Do not recommend it as a persistent opt-out after an SDK upgrade; distinguish the linked SDK from the running OS. [Compatibility key](https://developer.apple.com/documentation/bundleresources/information-property-list/uidesignrequirescompatibility).

UIKit apps built with the latest SDK must adopt the scene-based life cycle to launch on iOS 27. Check this before attributing a launch failure to Liquid Glass. An availability branch around a glass control does not satisfy the app's life-cycle requirement. [UIKit updates](https://developer.apple.com/documentation/updates/uikit), [Scene migration](https://developer.apple.com/documentation/uikit/transitioning-to-the-uikit-scene-based-life-cycle).

## Verify both paths

Compile against the intended deployment target, then run the new and fallback paths on representative supported OS versions. Check interaction and state preservation, not just the absence of a crash. Record unavailable runtimes as untested. A 26.5 SDK build running on 27.0 does not validate APIs introduced only in a 27 SDK.

These availability checkpoints were reviewed against Apple documentation and the iOS 27.1 SDK on 2026-10-01. The documentation review also included the iOS 27 release notes and iOS 27.2 beta 2 release notes; the installed SDK is not the newest published beta SDK. Beta availability and behavior require rechecking. Source and compiler checks do not establish runtime behavior. [iOS 27 release notes](https://developer.apple.com/documentation/ios-ipados-release-notes/ios-ipados-27-release-notes), [iOS 27.2 beta release notes](https://developer.apple.com/documentation/ios-ipados-release-notes/ios-ipados-27_2-release-notes).
