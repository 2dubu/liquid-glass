# SwiftUI search and tabs

Read this when implementing or restoring search suggestions, scopes, or tab navigation. The examples use system components, so the runtime owns their material and placement. Start with [system components](system-components.md) for appearance and container ownership, and [compatibility](../compatibility.md) for version gates.

## Search suggestions and scopes

Compose `.searchable`, `.searchSuggestions`, and `.searchScopes`. The standalone suggestions and scopes modifiers are available from iOS 16; they do not require Liquid Glass. Prefer them over the older `searchable` overload that embeds a suggestions closure. A scope tag must have the same type as the scope binding. [Search suggestions](https://developer.apple.com/documentation/swiftui/view/searchsuggestions(_:)), [Search scopes](https://developer.apple.com/documentation/swiftui/view/searchscopes(_:scopes:)).

The parent supplies filtered results and unique suggestion strings, updating them when query, scope, or source data changes. Stable result IDs survive query changes. Result titles and suggestion strings here are content from the data source; UI labels use localized literals.

```swift
import SwiftUI

@available(iOS 16.0, *)
struct LibrarySearch: View {
    struct Result: Identifiable {
        let id: UUID
        let title: String
    }
    enum Scope: Hashable { case all, favorites }

    @Binding var query: String
    @Binding var scope: Scope
    let results: [Result]
    let suggestions: [String]

    var body: some View {
        NavigationStack {
            List(results) { result in
                Text(result.title)
            }
            .navigationTitle("Library")
            .searchable(text: $query, prompt: "Search titles")
            .searchSuggestions {
                ForEach(suggestions, id: \.self) { suggestion in
                    Text(suggestion).searchCompletion(suggestion)
                }
            }
            .searchScopes($scope) {
                Text("All").tag(Scope.all)
                Text("Favorites").tag(Scope.favorites)
            }
        }
    }
}
```

Scope visibility has its own activation policy. The default on iOS is `onTextEntry`; focusing an empty field or selecting a completion must not be treated as proof that the scope picker is visible. When scopes should appear as soon as search opens, the iOS 16.4+ `searchScopes(_:activation:_:)` overload accepts `.onSearchPresentation`. Preserve the iOS 16.0 fallback if needed, and verify the actual search placement and software/hardware keyboard path. [Automatic activation](https://developer.apple.com/documentation/swiftui/searchscopeactivation/automatic), [Explicit activation](https://developer.apple.com/documentation/swiftui/view/searchscopes(_:activation:_:)).

`searchCompletion` makes selecting a suggestion replace the search text. It does not fetch or filter results; the query binding must feed the app's search logic. Keep expensive result preparation outside `body` and cancel or reject stale asynchronous results when queries change. [searchCompletion](https://developer.apple.com/documentation/swiftui/view/searchcompletion(_:)), [state and performance](state-and-performance.md).

For programmatic presentation, use the iOS 17 `isPresented` binding overload of `.searchable`. Environment-based `isSearching` and `dismissSearch` must be read below the searchable modifier. Do not infer dismissal, focus, or keyboard behavior from the query being empty. [Search activation](https://developer.apple.com/documentation/swiftui/managing-search-interface-activation).

## Tab selection and the search role

For iOS 18 and later, use `Tab` declarations and typed selection values. The selected value and every tab's value must use the same type. The search role identifies the tab's purpose; a magnifying-glass image alone does not do that. [Tab](https://developer.apple.com/documentation/swiftui/tab), [TabRole.search](https://developer.apple.com/documentation/swiftui/tabrole/search).

This is an alternative to the standalone search screen above: the tab container owns `.searchable`, and its parent owns the query and filtered results. Do not add a second searchable modifier inside its search tab by composing both examples unchanged.

```swift
import SwiftUI

@available(iOS 18.0, *)
struct LibraryTabs: View {
    struct Result: Identifiable {
        let id: UUID
        let title: String
    }
    private enum Destination: Hashable { case library, search }
    @State private var selection = Destination.library
    @Binding var query: String
    let results: [Result]

    var body: some View {
        TabView(selection: $selection) {
            Tab("Library", systemImage: "books.vertical", value: Destination.library) {
                NavigationStack {
                    Text("Your library").navigationTitle("Library")
                }
            }
            Tab("Search", systemImage: "magnifyingglass",
                value: Destination.search, role: .search) {
                NavigationStack {
                    List(results) { result in
                        Text(result.title)
                    }
                    .navigationTitle("Search")
                }
            }
        }
        .searchable(text: $query)
    }
}
```

When supporting iOS 17 or earlier, guard construction of an iOS 18 tab hierarchy and preserve the app's existing tab implementation on the older branch. Do not raise the deployment target merely to remove `.tabItem`. Search role availability does not imply that iOS 18 renders the iOS 26 search-tab appearance.

On iOS 27, `.prominent` gives one tab prominent visual treatment. This is separate from `.search` routing: only one tab gets the prominent treatment, and a search tab may receive it by default when no tab explicitly uses `.prominent`. Do not substitute a visual role for the search role. [TabRole.prominent](https://developer.apple.com/documentation/swiftui/tabrole/prominent).

On iOS 26, tab minimization and bottom accessories are separate opt-ins. Keep their availability guards separate from `Tab`'s iOS 18 requirement, and adapt accessories using `tabViewBottomAccessoryPlacement` rather than a fixed offset. [System tab behavior](system-components.md#tabs-and-search).

## Modernize within the requested scope

The SwiftUI specialist's soft-deprecation reference and the installed SDK identify `.tabItem` and the older suggestions-bearing `.searchable` overloads as superseded forms. A `deprecated: 100000.0` annotation may produce no warning, so warnings-as-errors alone cannot identify every recommended migration.

Use supported replacements in new examples. In an existing app, preserve required fallback branches and unrelated views. During a migration/review, identify the replacement and its minimum OS; during a focused bug fix, avoid silently changing the navigation architecture.

Check suggestion selection, scope changes, empty-to-populated results, cancellation, keyboard focus, navigation, and tab switching in the actual app. Confirm that selecting the search tab exposes an editable field, not just the tab's content; a selected search role alone is insufficient runtime evidence. Include longer translations, larger text, and right-to-left layout when reviewing control sizing. A typecheck confirms API use, not the native search interaction on every OS build.
