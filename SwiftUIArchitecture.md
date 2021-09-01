# SwiftUI Architecture

## Access Levels 
- Properties marked `@State` and `@StateObject` should be marked private.

`@State private var isShowingSheet`
`@StateObject private var reachabilityObserver = ReachabilityObserver()`

- Properties mark `@Binding` should be marked using `private(set)`

## Initializing SwiftUI Views
- When creating a view with all `let` properties, there is no need to create a new initializer, since `Struct`'s create a member-wise initializer. 

## Comments related to modifiers

When providing clarification around why a modifier is used, Place the comment immediately after the modifier 

`HStack {
    // …
}
.someModifier1()
.someModifier2() // we use this because blah blah blah
.someModifier3()
`
