# Tab Bar Glass Component

> Source: [LiquidGlass-Handbook](https://github.com/jaikrishnavj/LiquidGlass-Handbook)

## Overview

Tab bars in iOS 26 float above content with glass backgrounds. They can be customized for alignment and minimize behavior.

## Basic Tab Bar

```swift
TabView {
    HomeView()
        .tabItem {
            Label("Home", systemImage: "house")
        }

    SearchView()
        .tabItem {
            Label("Search", systemImage: "magnifyingglass")
        }

    SettingsView()
        .tabItem {
            Label("Settings", systemImage: "gear")
        }
}
```

## Tab Bar Style

```swift
TabView {
    // tabs...
}
.tabViewStyle(.tabBarOnly)  // Tab bar only, no page content
```

## Minimize on Scroll

Tab bar can minimize when user scrolls:

```swift
TabView {
    ScrollView {
        // Long content
    }
    .tabItem {
        Label("Feed", systemImage: "list.bullet")
    }
}
.tabBarMinimizeBehavior(.onScrollDown)
```

### Minimize Behaviors

| Behavior | Description |
|----------|-------------|
| `.automatic` | System decides based on context |
| `.onScrollDown` | Minimize when scrolling down, expand when scrolling up |
| `.onScrollUp` | Minimize when scrolling up, expand when scrolling down |
| `.never` | Always visible |

## Custom Tab Selection

```swift
struct CustomTabView: View {
    @State private var selectedTab = 0

    var body: some View {
        TabView(selection: $selectedTab) {
            HomeView()
                .tag(0)
                .tabItem {
                    Label("Home", systemImage: selectedTab == 0 ? "house.fill" : "house")
                }

            ProfileView()
                .tag(1)
                .tabItem {
                    Label("Profile", systemImage: selectedTab == 1 ? "person.fill" : "person")
                }
        }
    }
}
```

## Tab Bar with Badge

```swift
TabView {
    MessagesView()
        .tabItem {
            Label("Messages", systemImage: "message")
        }
        .badge(5)  // Shows "5" badge

    NotificationsView()
        .tabItem {
            Label("Alerts", systemImage: "bell")
        }
        .badge("New")  // Shows "New" text badge
}
```

## Hiding Tab Bar

```swift
NavigationStack {
    DetailView()
        .toolbarVisibility(.hidden, for: .tabBar)
}
```

## Key Points

- Tab bars float above content with glass backgrounds
- Use `.tabViewStyle(.tabBarOnly)` for tab bar only mode
- `.tabBarMinimizeBehavior(.onScrollDown)` for scroll-to-minimize
- Content extends behind the tab bar
- Use badges for notifications/counts
- Hide with `.toolbarVisibility(.hidden, for: .tabBar)`
