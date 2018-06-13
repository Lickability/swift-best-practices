# Documentation and Comments
### How do we document?
Anything access level internal or higher requires documentation. An exception is declarations made for protocol conformance.

### Key Considerations
* Use Xcode’s auto documentation in most cases (`option + command + /`)
* Document private types if you want to add clarity but it is not required

### Exceptions 

**Enum**
	* Should have a single line of documentation at the top
	* Each case should documentation in the same format as functions where each associated value is documented as a parameter

```swift
    /// Represents all possible product category types.
    enum Category {
        
        /// Edible items.
        case food
        
        /// Drinkable items.
        case beverage
        
        /// Other non-consumable merchandise.
        case merchandise
    }
```
	
**Closure Signatures & Typealias**
	* Documentation in the same format as functions

### How do we comment?
Use comments when trying to explain edge cases where code may require complexity or unfamiliar patterns

### Key Considerations
* Focus on making your code as easy to understand as possible with clear variable names
* Start comments with double slashes followed by a space i. e. `// Here is a comment`