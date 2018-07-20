# Documentation and Comments
### How do we document?
Anything with access level `internal` or higher requires documentation with the exception of declarations made for `protocol` conformance or `override`s in subclasses

### Key Considerations
* Use Xcode’s auto documentation in most cases (`option + command + /`)
* Document private types if you want to add clarity but it is not required

### Exceptions 

**Enum**
* Should have a single line of documentation at the top
* Documentation on each case should be in the same format as functions where each associated value is documented as a parameter

```swift
/// Represents all possible product category types.
enum Category {
    
    /// Edible items.
    case food
    
    /// Drinkable items.
    case beverage
    
    /// Other non-consumable merchandise.
    case merchandise
    
    /// An item category that fall outside of the other cases.
    ///
    /// - Parameter description: A string description of what that category is.
    case other(description: String)
}
```
	
**Closure Signature Typealiases**
* Use the same documentation format as functions with parameters and return values (where appropriate) when documenting `typealias`es for closures.

### How do we comment?
Use comments when trying to explain edge cases where code may require complexity or unfamiliar patterns

### Key Considerations
* Focus on making your code as easy to understand as possible with clear variable names
* Start comments with double slashes followed by a space e.g. `// Here is a comment`
