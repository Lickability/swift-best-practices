# Documentation and Comments
### How do we document?
Anything with access level `internal` or higher requires documentation with the exception of declarations made for `protocol` conformance or `override`s in subclasses. This allows developers to understand the intention and function of the public interface of types, regardless of the module they're currently working in.

### Key Considerations
* Use Xcode’s auto documentation in most cases (`option + command + /`).
* Document `private` types if you want to add clarity, but it is not required.
* The order in which parameters appear in documentation should match the order in which they appear in the corresponding API.

### Exceptions 

**Enum**
* Should have a single line of documentation at the top.
* Documentation on each case should be in the same format as functions where each associated value is documented as a parameter.

```swift
/// Represents all possible product category types.
enum Category {
    
    /// Edible items.
    case food
    
    /// Drinkable items.
    case beverage
    
    /// Other non-consumable merchandise.
    case merchandise
    
    /// An item category that falls outside of the other cases.
    /// - Parameter description: A string description of what that category is.
    case other(description: String)
}
```
	
**Closure Signature Type Aliases**
* Use the same documentation format as functions with parameters when documenting `typealias`es for closures.

```swift
/// Signature for a closure that is called when a button is tapped.
/// - Parameter button: The button that was tapped.
typealias ButtonTapHandler = (_ button: UIButton) -> Void
```

**Extensions**
* When adding an extension to add common functionality to a type, document the extension with a comment about the type of functionality it is adding.
```swift
/// A `UIView` extension that adds layer properties as `@IBInspectable` properties of the view itself so that they can be set within Interface Builder.
extension UIView {
    
    /// The receiver’s `layer` corner radius.
    /// - SeeAlso:
    /// [CALayer.cornerRadius](https://developer.apple.com/documentation/quartzcore/calayer/1410818-cornerradius)
    @IBInspectable var cornerRadius: CGFloat {
        get {
            return layer.cornerRadius
        }
        set {
            layer.cornerRadius = newValue
        }
    }
}
```

### How do we comment?
Use comments when trying to explain edge cases where code may require complexity or unfamiliar patterns.

### Key Considerations
* Focus on making your code as easy to understand as possible with clear variable names.
* Start comments with double slashes followed by a space e.g. `// Here is a comment`.

### Example
```swift
// This is intentionally implemented. The default implementation for RawRepresentable, outlined here: https://github.com/apple/swift/blob/f19aca6cb0c8ea8f392a78a56136151b25f8713e/stdlib/public/core/CompilerProtocols.swift#L187, does not use the Swift 4.2 auto synthesis for Hashable, and instead provides its own hashValue, which only uses the rawValue.
var hashValue: Int {
    var hasher = Hasher()
    self.hash(into: &hasher)
    return hasher.finalize()
}
```
