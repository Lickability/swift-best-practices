# View Store

View Store is an architecture pattern and a protocol used in SwiftUI development inspired by [The Composable Architecture](https://github.com/pointfreeco/swift-composable-architecture). A view store is an `ObservableObject` that allows us to separate view-specific logic and the rendering of a corresponding view in a way that is repeatable, prescriptive, flexible, and testable by default.

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

To perform an action, we typically create a `PassthroughSubject`, which is then used in the `Combine` pipeline to update the `viewState`. We call the `send(_:)` function to perform the action, and then typically send a new value to the `PassthroughSubject`.

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
    .map { /* … */ }
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

As a convenience, a [`makeBinding` API](https://github.com/Lickability/ViewStore/blob/main/Sources/ViewStore/ViewStore%2BBindingAdditions.swift) is provided on the `ViewStore` protocol to create a succinct syntax for this common case.

```swift
var showsPhotoCount: Binding<Bool> {
    makeBinding(viewStateKeyPath: \.showsPhotoCount, actionCasePath: /Action.toggleShowsPhotoCount)
}
```

* Example: TK
* PassthroughSubject usage

# Example

An example usage of the View Store pattern can be found [here](https://github.com/Lickability/view-store-lwl). In this project, [`PhotoList.swift`](https://github.com/Lickability/view-store-lwl/blob/main/ViewStoreLWL/Photos/PhotoList.swift) makes use of [`PhotoListViewStore`](https://github.com/Lickability/view-store-lwl/blob/main/ViewStoreLWL/Photos/PhotoListViewStore.swift) to perform network requests and format data for display in the list, as well as update the source of truth via actions (searching and a `Toggle`) performed by the user. `PhotoListOriginal.swift`, for the sake of comparison, does _not_ use a view store.

# Things left to organize / cover
* not every view has a view store. When to use one?
* Certainly need to cover `Combine` and how it’s used.
* Discuss the implementation of a view store in detail. Cover how it combines data from external sources and input from the view via `send(_ action:)` to produce the single source of truth.
* Link (bi-directionally) to the ViewStore package readme. And possibly the example project.
* Update View Store main description in all other locations.
Talk about Bindings