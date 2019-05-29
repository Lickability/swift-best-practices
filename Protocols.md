# Protocols
## What are they?
Protocols define a blueprint of methods, properties, and other requirements that suit a particular task or piece of functionality.

### How do we use them?
Some uses of protocols are as follows:
* As delegates of views and controllers.
* To provide a common interface for shared functionality.
* To wrap dependencies so that swapping out a 3rd-party library with a new library or implementation can be extended to implement the functionality we need without a massive project-wide change.
* For testing controllers using dependency injection (with protocol types) such that these dependencies can be swapped out in unit tests to focus only on the logic in the controller being tested.

## Code Example
```swift
/// Defines a common point of configuration through a view model for classes. Generally these classes are reusable cells.
protocol ViewModelDisplaying: class {
    
    /// The type of view model the class has defined.
    associatedtype ViewModel
    
    /// A optional instance of the view model that can be set to configure the view.
    var viewModel: ViewModel? { get set }
}
```
