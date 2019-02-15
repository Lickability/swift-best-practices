# Protocols
## What are they?
Protocols define a blueprint of methods, properties, and other requirements that suit a particular task or piece of functionality.

### How do we use them?
* As delegates of views and controllers.
* To wrap dependencies so that swapping out a 3rd-party library with a new library or implementation can be extended to implement the functionality we need without a massive project-wide change.
* For testing controllers using dependency injection (with protocol types) such that these dependencies can be swapped out in unit tests to focus only on the logic in the controller being tested.

### Key Considerations
* Support value types (and classes).
* Support static type relationships (and dynamic dispatch).
* Support retroactively modeling.
* Do not expose instance data on models.
* Do not import initilization burdens on models.
* Make clear what to implement.

## Code Example
```swift
/// A protocol that determines whether or not a view controller can be dismissed.
@objc protocol Dismissable {
    var canBeDismissed: Bool { get }
}

extension UIViewController: Dismissable {
    /// By default, all view controllers can be dismissed unless you specify otherwise.
    var canBeDismissed: Bool {
        return true
    }
}
```