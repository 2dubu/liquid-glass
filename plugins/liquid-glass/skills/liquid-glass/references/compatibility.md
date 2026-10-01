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
| SwiftUI Observation integration | 17.0 | [Observation migration](https://developer.apple.com/documentation/swiftui/migrating-from-the-observable-object-protocol-to-the-observable-macro) |
| `.searchable(text:isPresented:placement:prompt:)` | 17.0 | [Presentation binding](https://developer.apple.com/documentation/swiftui/view/searchable(text:ispresented:placement:prompt:)) |
| `Tab`, `TabRole.search` | 18.0 | [Tab](https://developer.apple.com/documentation/swiftui/tab), [Search role](https://developer.apple.com/documentation/swiftui/tabrole/search) |
| `AnimatableValues` | 26.0 | [AnimatableValues](https://developer.apple.com/documentation/swiftui/animatablevalues) |

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

## Verify both paths

Compile against the intended deployment target, then run the new and fallback paths on representative supported OS versions. Check interaction and state preservation, not just the absence of a crash. Record unavailable runtimes as untested. A 26.5 SDK build running on 27.0 does not validate APIs introduced only in a 27 SDK.

The core Liquid Glass availability table was checked with Xcode 26.5 / iOS SDK 26.5 and Apple documentation on 2026-09-22. The companion API table was checked against Apple documentation and the iOS 27.1 SDK on 2026-10-01. Recheck availability when adopting newer overloads; these checks do not establish runtime behavior.
