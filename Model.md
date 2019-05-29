# Model
### Responsibility
Models are the application's dynamic data structure, independent of the user interface. They directly manage the data and business logic of the application.

### Key Considerations
* Models can structure your data in a reliable form and prepare it based on the controller's instructions.
* They are **not** responsible for retrieving data from the persistence or network layers. 
* Mutability on models should be avoided, opting instead to recreate the model when information changes. Mutable models can create race conditions when data is being simultaneously written and read across multiple threads or queues. 

### Interaction Diagram
![architecturediagramsmodel](https://user-images.githubusercontent.com/16432044/41422895-ecc46a86-6fc7-11e8-8719-ff2d0134f47d.png)

### Code Example

```swift
/// Represents a product for sale in the store.
struct Product {
    
    /// Represents all possible product category types.
    enum Category {
        
        /// Edible items.
        case food
        
        /// Drinkable items.
        case beverage
        
        /// Other non-consumable merchandise.
        case merchandise
    }
    
    /// The unique identifier (SKU) of the product for sale.
    let serialNumber: String
    
    /// The name of the product.
    let name: String
    
    /// A detailed description of the product.
    let description: String?
    
    /// The cost of the product, represented as an Int. Value can be formatted later based on currency needed
    let price: Int
    
    /// Returns whether a product is edible or not based on its category.
    var isEdible: Bool {
        get {
            switch category {
            case .food:
                return true
            case .beverage, .merchandise:
                return false
            }
        }
    }
    
    /// The category of the product.
    let category: Category
}
```
