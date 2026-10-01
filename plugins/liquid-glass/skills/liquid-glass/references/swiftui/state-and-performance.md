# SwiftUI identity, state, and performance

Read this when a glass control loses state, an effect transition is interrupted, or scrolling and animation become expensive. Keep [glass composition](custom-effects.md) separate from the SwiftUI updates that feed it; adding a glass container does not fix every kind of hitch.

## Preserve the identity of the control

Changing a visual property and inserting a control are different operations. For a tint or effect change on the same control, vary the modifier's argument. A custom `.if` modifier that branches between `transform(self)` and `self` changes structural identity when toggled and can reset descendant state or replace an animation with a removal/insertion.

For example, a control can choose between two `Glass` values while keeping the same modifier chain. Use `Glass.identity` only when removing the material is intended. Do not remove glass merely to respond to an accessibility preference. `GlassEffectTransition.identity` instead disables the glass transition; it is a different type and operation. [Glass.identity](https://developer.apple.com/documentation/swiftui/glass/identity), [glassEffectTransition](https://developer.apple.com/documentation/swiftui/view/glasseffecttransition(_:)).

Keep intentional insertion/removal, such as `if expanded`, when that is the interaction being animated. Keep `#available` branches that protect newer APIs. The warning about conditional styling is not a ban on these branches.

For ordinary foreground/background styles with incompatible types, `AnyShapeStyle` can keep the modifier in one branch. Use it only if the expression actually needs type erasure; do not mistake it for `AnyView` or use it to erase a `Glass` value. [AnyShapeStyle](https://developer.apple.com/documentation/swiftui/anyshapestyle).

## Give dynamic actions stable identities

Three identifiers have different jobs:

| Identity | Responsibility |
|---|---|
| `ForEach` element ID | Matches the data element and its view across updates, preserving associated state. |
| `glassEffectID` | Identifies a glass effect within its namespace for effect transitions. |
| `glassEffectUnion` ID | Groups compatible effects into a shared surface; it deliberately identifies a group. |

Use stable, unique model IDs for actions that may be inserted, removed, or reordered. Avoid positional IDs, a freshly computed `UUID()`, or an ID derived from a mutable title. `id: \.self` is appropriate when the value itself is a stable, unique key; it is not inherently wrong. A glass effect ID does not repair an unstable `ForEach` ID. [ForEach](https://developer.apple.com/documentation/swiftui/foreach), [glassEffectID](https://developer.apple.com/documentation/swiftui/view/glasseffectid(_:in:)).

This example receives persistent IDs and localizable titles from its caller. Highlighting varies the material without replacing the button. The caller owns the action and any animated changes to the command collection.

```swift
import SwiftUI

@available(iOS 26.0, *)
struct GlassCommand: Identifiable {
    let id: UUID
    let title: LocalizedStringResource
}

@available(iOS 26.0, *)
struct GlassCommandGroup: View {
    @Namespace private var namespace
    let commands: [GlassCommand]
    let highlightedID: GlassCommand.ID?
    let perform: (GlassCommand.ID) -> Void

    var body: some View {
        GlassEffectContainer(spacing: 20) {
            HStack(spacing: 12) {
                ForEach(commands) { command in
                    Button { perform(command.id) } label: {
                        Text(command.title)
                    }
                    .padding(12)
                    .glassEffect(command.id == highlightedID
                        ? .regular.tint(.blue).interactive()
                        : .regular.interactive())
                    .glassEffectID(command.id, in: namespace)
                }
            }
            .buttonStyle(.plain)
        }
    }
}
```

## Bound the work caused by state changes

Factor independently updating sections into separate `View` types with the inputs they need. Moving a section into a computed property on the same view does not create a separate update boundary. Keep view initializers cheap: decoding, file access, and expensive result preparation belong outside repeatedly constructed views. [SwiftUI performance](https://developer.apple.com/documentation/xcode/understanding-and-improving-swiftui-performance).

For value-type inputs, avoid passing a large model when a control needs only its title and enabled state. With `@Observable`, inspect the properties actually read: looking up one item through a whole collection still depends on that collection. A computed property that performs the lookup does not narrow that dependency. Pass the row's needed value or an appropriate observable item. Adopt Observation within the task's scope and supported OS range, without requiring an unrelated app-wide migration. [Observation migration](https://developer.apple.com/documentation/swiftui/migrating-from-the-observable-object-protocol-to-the-observable-macro).

Prepare expensive filtering and sorting when their inputs change, and reuse the results during rendering. Keep caches synchronized with query, scope, and source-data changes; do not introduce a stale second source of truth. For `List` and lazy containers, preserve a constant number of top-level views per element. Filter excluded rows upstream; wrapping an empty row is not equivalent to removing it. Avoid erasing each row to `AnyView`. [ForEach](https://developer.apple.com/documentation/swiftui/foreach).

## Inspect environment and side-effect dependencies

- Avoid broadcasting per-frame scroll offsets, drag positions, or geometry through custom environment values. Keep state near its consumers, or expose the semantic change they need, such as crossing a collapsed/expanded threshold.
- An `@Observable` container only helps at the granularity of the property read. Every row reading one shared visibility set still depends on that set; consider per-item state when the measured cost justifies it.
- Check custom environment defaults for fresh allocations or changing values such as `Model()`, `UUID()`, or `Date()`. Use deliberate ownership, a stable default, or an optional default with explicit injection. Do not accidentally share a mutable default model between independent screens.
- Closures in custom environment keys can prevent reliable comparison. Prefer a stable action representation there; this does not prohibit `Button` action closures or framework-provided `DismissAction` and `OpenURLAction` values.
- If `.onChange` reads a frequently changing value solely for a side effect on an expensive parent, isolate that read and effect in a focused view or modifier. It provides no isolation benefit when the parent already needs the value to render.

These checks adapt Apple's `swiftui-specialist` structure, data-flow, environment, modifier, and collection guidance exported with Xcode 27.0 beta 6 (27A5252f). They are investigation criteria, not proof that a particular app has an update problem.

## Separate update cost from material cost

Record the failing interaction with Instruments. Compare long view-body work, frequent short updates, and rendering hitches before deciding whether to change dependencies, layout, or glass composition. Use the SwiftUI instrument's causes to find the update source, then record the same interaction after the change. A lower container count alone does not establish an improvement. [SwiftUI performance](https://developer.apple.com/documentation/xcode/understanding-and-improving-swiftui-performance).

For a suspected non-constant row builder, Apple's `ForEach` documentation describes the `-LogForEachSlowPath YES` launch argument. Use it to locate candidates, then profile the actual cost; a log message is not a performance measurement. [ForEach diagnostics](https://developer.apple.com/documentation/swiftui/foreach).
