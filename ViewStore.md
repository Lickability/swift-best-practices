# View Store

View Store is an architecture pattern and a protocol used in SwiftUI development inspired by [The Composable Architecture](https://github.com/pointfreeco/swift-composable-architecture). A view store is an `ObservableObject` that allows us to separate view-specific logic and the rendering of a corresponding view in a way that is repeatable, prescriptive, flexible, and testable by default. It is available as a [Swift Package](https://github.com/Lickability/ViewStore).

## The Protocol

The protocol declaration itself is quite simple:

```swift
protocol ViewStore: ObservableObject {
    associatedtype ViewState
    associatedtype Action

    var viewState: ViewState { get }
    func send(_ action: Action)
}
```

### `ViewState`

The `viewState` property on the `ViewStore` protocol is the single source of truth for data that the corresponding `View` uses. It should always be declared as a `@Published` property. Similar to a [view model](https://github.com/Lickability/swift-best-practices/blob/main/ViewModel.md), the properties on `ViewState` should not require the corresponding `View` to perform any additional transformation logic or formatting.

### `Action`

`Action`s can be perfomed by the corresponding `View` using the `send(_ action:)` API. `Action` is typically modeled as an `enum`. When an action is performed, it typically has an effect on the view state. For example, `.refresh` might be an action that a `View` triggers on a view store, resulting in it re-fetching data and updating its corresponding `viewState`.

# Updating View State

A view store can combine many different sources of data into its single `viewState` property. Typically, this data is sourced from networking, persistence, or input from user actions (via the `send(_ action:)` API). The `Combine` framework is used to combine the data sources using `combineLatest` in a single stream that updates the `viewState` property when _any_ of the data sources produce new values. This creates a single pipeline for all changes that is predictable, repeatable, and guarantees that we have the latest values from each data source. Because `viewState` is a `@Published` property and the `ViewStore` is an `ObservableObject`, all changes will cause the corresponding `View` to update.

## Performing `Action`s

To perform an action, we typically create a `PassthroughSubject`, which is then used in the `Combine` pipeline to update the `viewState`. We call the `send(_ action:)` API to perform the action, and then typically send a new value to the `PassthroughSubject`.

```swift
enum Action {
    case toggleShowsPhotoCount(Bool)
}

private let showsPhotosCountPublisher = PassthroughSubject<Bool, Never>()

func send(_ action: Action) {
    switch action {
    case let .toggleShowsPhotoCount(showsPhotoCount):
        showsPhotosCountPublisher.send(showsPhotoCount)
    }
}
```

Additionally, `PassthroughSubject`s are prepended with an initial value, since their usage in `combineLatest` requires that a value be emitted before the `combineLatest` can emit.

```swift
let showsPhotosCountPublisher = self.showsPhotosCountPublisher.prepend(ViewState.initial.showsPhotoCount)
photoPublisher
    .combineLatest(showsPhotosCountPublisher)
    .map { /* transformation to ViewState */ }
    .assign(to: &$viewState)
```

## Bindings

Many SwiftUI APIs accept bindings for state that is both read and written to. Binding properties or methods are frequently declared on view stores as a convenience for working with these APIs. To keep a single source of truth, the binding typically reads a property on the `viewState` and performs an action that results in an update to the view state.

```swift
var showsPhotoCount: Binding<Bool> {
    return Binding<Bool> {
        self.viewState.showsPhotoCount
    } set: { newValue in
        self.send(.toggleShowsPhotoCount(newValue))
    }
}
```

Rather than using `send(_ action:)` directly, the `View` would instead use this binding.

```swift
Toggle("Show Count", isOn: store.showsPhotoCount)
```

As a convenience, a [`makeBinding` API](https://github.com/Lickability/ViewStore/blob/main/Sources/ViewStore/ViewStore%2BBindingAdditions.swift) is provided in an extension of the `ViewStore` protocol to create a succinct syntax for this common case. This extension uses [CasePaths](https://github.com/pointfreeco/swift-case-paths).

```swift
var showsPhotoCount: Binding<Bool> {
    makeBinding(viewStateKeyPath: \.showsPhotoCount, actionCasePath: /Action.toggleShowsPhotoCount)
}
```

# Example Project

An example usage of the View Store pattern can be found in the [ViewStore package repository](https://github.com/Lickability/ViewStore). In this project, [`PhotoList.swift`](https://github.com/Lickability/ViewStore/blob/main/Example/Photos/PhotoList.swift) makes use of [`PhotoListViewStore`](https://github.com/Lickability/ViewStore/blob/main/Example/Photos/PhotoListViewStore.swift) to perform network requests and format data for display in the list, as well as update the source of truth via actions (searching and a `Toggle`) performed by the user. `PhotoListOriginal.swift`, for the sake of comparison, does _not_ use a view store.

# When to Use a View Store

Not every SwiftUI `View` will have a corresponding view store. Some views are simple enough to pass all information in on `init` without any added complexity. However, the data passed to these simple views should originate from a view store of a parent view. For example, when displaying a grid of photos fetched from the network, a view store could be used to fetch and transform network data into the `viewState` used to populate the grid. Each grid element, however, doesn’t require additional data transformation, so a grid element’s corresponding `View` need not have a view store.

The same can be done with minor actions on these simple views. The actions should be handled by a view store, but for simple views, that action can be performed by passing in a closure that the parent can specify.

```swift
struct PhotoGrid: View {
    @StateObject private var store: PhotoListViewStore

    init(provider: Provider) {
        self._store = StateObject(wrappedValue: PhotoListViewStore(provider: provider))
    }

    var body: some View {
        ScrollView {
            LazyVGrid(columns: columns, alignment: .center, spacing: 10) {
                ForEach(store.viewState.photos) { photo in
                    PhotoGridElement(thumbnailUrl: photo.thumbnailUrl) {
                        store.send(.tapPhoto(id: photo.id))
                    }
                }
            }
        }
    }
}

struct PhotoGridElement: View {
    let thumbnailUrl: URL
    let onTap: () -> Void

    var body: some View {
        AsyncImage(url: thumbnailUrl) { image in
            image.resizable()
                .aspectRatio(contentMode: .fit)
        } placeholder: {
            ProgressView()
        }
        .onTapGesture {
            onTap()
        }
    }
}
```
