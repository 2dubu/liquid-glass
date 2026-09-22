# Diagnose Liquid Glass behavior on iOS

Read this for a concrete failure or unexplained difference. Follow the evidence needed for the symptom; an ordinary API correction does not require disassembly.

## Establish what differs

Record the expected and observed behavior, the triggering interaction, and whether it reproduces consistently. Identify:

- iOS version and build; device or Simulator runtime; Xcode/SDK; deployment target.
- SwiftUI, UIKit, or a hosting boundary; standard component or custom effect; the view that owns the bar, presentation, or background.
- Relevant glass containers and ancestry, safe areas, scroll views, clipping, opacity, transforms, and custom backgrounds.
- Glass variant, tint, interaction configuration, and relevant appearance/accessibility settings.

Inspect existing code and the environment before asking for information that is already available. Reduce the scene to public APIs while preserving the suspected boundary. Keep the failing and working versions comparable; removing a navigation or hosting container may remove the cause.

## Choose the first discriminating check

| Symptom | Useful first check |
| --- | --- |
| Code does not compile | Read the installed SDK declaration, exact argument labels, availability, and target platform |
| Effect is missing or unexpectedly opaque | Identify the actual component owner, custom backgrounds, clipping/opacity, and active accessibility settings |
| Nearby effects look inconsistent | Inspect their sampling/grouping scope; compare sibling grouping with independent effects |
| Nested or overlapping effects look wrong | Distinguish design overlap, ordinary UIKit glass nesting, and glass-container grouping |
| Morphing does not occur | Compare container scope, identifiers, namespace, spacing, view insertion/removal, and chosen transition |
| Touch feedback does not occur | Separate action/gesture delivery and hit testing from the material's interactive configuration |
| A menu, sheet, or bar differs by OS | Reproduce with the standard component and record both runtime builds before adding a workaround |
| Stuttering or excess rendering cost | Capture a repeatable interaction and profile it; vary one cause at a time |

These are investigation paths, not diagnoses. A symptom alone does not establish that a container, `zIndex`, forced background, or clipping modifier will fix it.

## Resolve the public contract and SDK

Use available Apple documentation capabilities to find the relevant API, then read its exact page. Prefer the specific API/article and the applicable WWDC explanation to a broad search snippet. Keep current documentation separate from a dated presentation.

Discover the tools exposed in the current environment instead of assuming a server prefix or requiring a particular MCP installation. If documentation search returns no results, try the known official page URL. If an article response omits code or inline symbols, consult the Apple page and installed SDK rather than reconstructing missing syntax.

Check the SDK when a signature, availability, default, or bridge is material to the diagnosis. The installed SDK establishes what can compile with that toolchain; it does not establish the behavior of every runtime. A compile probe should use the actual framework and target configuration involved.

If a tool is unavailable, continue with the sources and local checks that are available. Report the resulting evidence gap. Do not describe an unqueried source or unavailable runtime as having confirmed a finding.

## Reproduce before generalizing

Use a small runnable scene for the disputed behavior. Capture the relevant screenshot, interaction recording, log, or trace and identify the runtime used. For a version-specific claim, compare the same scene on the versions in question when available; otherwise retain that limitation.

Test only the settings that discriminate the current hypotheses. A transparency problem may need bright/dark media and Reduce Transparency; a morphing problem may need container spacing, view identity, and Reduce Motion. A broad settings matrix is useful for a release review, not a prerequisite to every local fix.

For UIKit, add content through the effect view's documented content view and distinguish `UIGlassEffect` from `UIGlassContainerEffect`. For SwiftUI, distinguish the effect container from layout containers and the effect's transition from unrelated view animation. [UIGlassContainerEffect](https://developer.apple.com/documentation/uikit/uiglasscontainereffect), [Applying Liquid Glass to custom views](https://developer.apple.com/documentation/swiftui/applying-liquid-glass-to-custom-views)

## Use disassembly for a specific unresolved question

State the question first: for example, whether ordinary glass and grouped glass take different material paths, or where a relevant setting enters an observed path. Then:

1. Check which framework and OS version the provider exposes, then inspect the relevant public entry point or narrowly related implementation.
2. Distinguish a symbol search hit from an available implementation. A search miss is not proof of API absence; a SwiftUI-related implementation may be in SwiftUICore or UIKitCore.
3. Limit conclusions to the inspected version and code path. Preserve uncertain decompiler output and untraced branches as unknowns.
4. Compare the observation with the public-API reproduction. A plausible internal path does not establish the rendered result or the cause of the app's failure.

Use private symbols only as investigative evidence. Do not turn them into calls, selectors, KVC paths, or other private-API workarounds in application code.

## Report the result at the strength supported

Separate documented behavior, SDK/compile results, binary observations, runtime reproduction, and inference. Include the environment, the smallest corrective change, validation performed, and what remains unresolved. If proposing a workaround, record the version-specific reproduction, public API used, side effects, and the condition for removing it.
