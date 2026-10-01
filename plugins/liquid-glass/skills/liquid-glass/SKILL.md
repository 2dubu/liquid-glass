---
name: liquid-glass
description: Implement, migrate, review, and diagnose iOS Liquid Glass interfaces in SwiftUI and UIKit. Use for system chrome, custom glass effects, transitions, accessibility, and version-specific issues in native iOS apps.
---

# Liquid Glass for iOS

Use public APIs to preserve readable content, functioning controls, and the app's existing deployment target. This skill covers iOS; do not extrapolate its availability guidance to other Apple platforms.

## Choose the relevant path

Identify the framework, the container that owns the surface, and whether the request is implementation, migration, review, or diagnosis. For version-sensitive behavior distinguish **Xcode/SDK**, **deployment target**, and **running iOS version/build**.

Read only the references needed for the request:

| Request | Read |
|---|---|
| SwiftUI custom effects, button styles, blending, union, morphing | [SwiftUI custom effects](references/swiftui/custom-effects.md) |
| SwiftUI navigation, toolbar, tabs, search, sheets, scroll edges | [SwiftUI system components](references/swiftui/system-components.md) |
| SwiftUI search suggestions/scopes, tab selection, modern search/tab examples | [SwiftUI search and tabs](references/swiftui/search-and-tabs.md) |
| SwiftUI state resets, dynamic action IDs, unnecessary updates, list/scroll hitches | [SwiftUI state and performance](references/swiftui/state-and-performance.md) |
| UIKit custom glass, contentView, grouping, shape, interaction | [UIKit custom effects](references/uikit/custom-effects.md) |
| UIKit navigation, bars, sheets, scrolling, hosting boundaries | [UIKit system components](references/uikit/system-components.md) |
| Material choice, visual hierarchy, accessibility, performance | [Design and accessibility](references/design-and-accessibility.md) |
| Older iOS support or API availability | [Compatibility](references/compatibility.md) |
| Unexpected appearance, interaction, transitions, regressions, frame drops | [Diagnosis](references/diagnosis.md), then the relevant framework reference |

For mixed UIKit/SwiftUI screens, identify the owner of navigation, safe areas, and each glass surface. Do not assume a hosting boundary shares the same rendering group.

## Implement, migrate, or review

- For standard controls, inspect system adoption and existing appearance overrides before adding custom effects.
- Keep action handling, hit testing, shape, layout, and material configuration distinct. Use the relevant reference's public APIs and snippets.
- Preserve older-OS behavior with availability guards. Check the actual app with its own build and test workflow, including affected controls, transitions, and accessibility settings.
- Keep the requested scope: a review does not authorize app edits or publication.

## Research when needed

Use Apple Docs MCP when available for public contracts and API availability. If keyword search misses a known API, try the exact documentation URL and inspect the installed SDK rather than inventing syntax.

Use the disassembler for a specific unexplained system behavior or version difference. Discover the tools available in the current host; a SwiftUI-related implementation may live in SwiftUICore. An index miss does not prove API absence. Treat internal implementation as version-specific evidence, not a public contract or a production workaround.

Keep documented behavior, compiler results, implementation observations, and runtime reproduction distinct. If a tool or runtime is unavailable, state the resulting limit and continue with the evidence available.
