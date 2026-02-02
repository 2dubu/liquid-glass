# Search Glass Component

> Source: [LiquidGlass-Handbook](https://github.com/jaikrishnavj/LiquidGlass-Handbook)

## Overview

Search fields in iOS 26 automatically get glass material backgrounds. The `.searchable()` modifier integrates seamlessly with NavigationStack and NavigationSplitView.

## Basic Searchable List

```swift
struct SearchableListView: View {
    @State private var searchText = ""

    let items = ["iPhone", "iPad", "Mac", "Apple Watch", "AirPods"]

    var filteredItems: [String] {
        if searchText.isEmpty {
            return items
        }
        return items.filter {
            $0.localizedCaseInsensitiveContains(searchText)
        }
    }

    var body: some View {
        NavigationStack {
            List(filteredItems, id: \.self) { item in
                SearchResultRow(title: item)
            }
            .navigationTitle("Products")
            .searchable(text: $searchText, prompt: "Search products")
        }
    }
}
```

## Search Result Row

```swift
struct SearchResultRow: View {
    let title: String

    var body: some View {
        HStack {
            Image(systemName: "apple.logo")
                .foregroundStyle(.secondary)
                .frame(width: 30)

            Text(title)

            Spacer()

            Image(systemName: "chevron.right")
                .foregroundStyle(.tertiary)
        }
        .padding(.vertical, 4)
    }
}
```

## Search with Suggestions

```swift
struct SearchWithSuggestions: View {
    @State private var searchText = ""

    var body: some View {
        NavigationStack {
            ContentView()
                .searchable(text: $searchText) {
                    ForEach(suggestions, id: \.self) { suggestion in
                        Text(suggestion)
                            .searchCompletion(suggestion)
                    }
                }
        }
    }

    var suggestions: [String] {
        guard !searchText.isEmpty else { return [] }
        return ["iPhone 16", "iPhone 16 Pro", "iPhone 16 Pro Max"]
            .filter { $0.localizedCaseInsensitiveContains(searchText) }
    }
}
```

## Search with Scopes

```swift
enum SearchScope: String, CaseIterable {
    case all = "All"
    case products = "Products"
    case services = "Services"
}

struct ScopedSearchView: View {
    @State private var searchText = ""
    @State private var searchScope = SearchScope.all

    var body: some View {
        NavigationStack {
            List {
                // Filtered content
            }
            .searchable(text: $searchText)
            .searchScopes($searchScope) {
                ForEach(SearchScope.allCases, id: \.self) { scope in
                    Text(scope.rawValue).tag(scope)
                }
            }
        }
    }
}
```

## Search in NavigationSplitView

```swift
struct SplitViewSearch: View {
    @State private var searchText = ""

    var body: some View {
        NavigationSplitView {
            List {
                // Sidebar content
            }
            .searchable(text: $searchText, placement: .sidebar)
        } detail: {
            DetailView()
        }
    }
}
```

## Search Placement Options

```swift
// Default - adapts to context
.searchable(text: $searchText)

// Explicit placements
.searchable(text: $searchText, placement: .navigationBarDrawer)
.searchable(text: $searchText, placement: .navigationBarDrawer(displayMode: .always))
.searchable(text: $searchText, placement: .sidebar)  // For split views
.searchable(text: $searchText, placement: .toolbar)
```

## Programmatic Search Control

```swift
struct ProgrammaticSearch: View {
    @State private var searchText = ""
    @Environment(\.isSearching) private var isSearching
    @Environment(\.dismissSearch) private var dismissSearch

    var body: some View {
        NavigationStack {
            VStack {
                if isSearching {
                    Text("Searching...")
                    Button("Cancel") {
                        dismissSearch()
                    }
                    .buttonStyle(.glass)
                }
            }
            .searchable(text: $searchText)
        }
    }
}
```

## Key Points

- `.searchable(text:prompt:)` adds search with glass background
- Case-insensitive filtering is common pattern
- Supports NavigationStack and NavigationSplitView
- Search suggestions with `.searchCompletion()`
- Search scopes for categorized search
- Use `@Environment(\.isSearching)` to detect search state
- Use `@Environment(\.dismissSearch)` to programmatically dismiss
