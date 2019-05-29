# Default Parameter Values
### When do we use them?
Use default parameter values instead of creating convenience functions that pass common constant values to the original function

### Key Considerations
* The default value of a parameter should be the most common use case for the parameter
* Always document the value of the default parameter

### Example

```swift
/// Animate changes to one or more views using the specified duration and completion handler.
///
/// - Parameters:
///   - duration: The total duration of the animations, measured in seconds. If you specify a negative value or 0, the changes are made without animating them.
///   - animations: A block object containing the changes to commit to the views.
///   - completion: A block object to be executed when the animation sequence ends. Defaults to nil.
func animate(withDuration duration: TimeInterval, animations: @escaping () -> Void, completion: ((Bool) -> Void)? = nil)
```

### Reference Docs
* [Functions — The Swift Programming Language](https://docs.swift.org/swift-book/LanguageGuide/Functions.html#ID166)
