### Responsibility
*Models* are responsible for representing the data of the application.

### Key Considerations
* Models should be as simplistic as possible without any logic beyond transforming its own properties i.e. computed properties.
* They are **not** responsible for retrieving data from the persistence or network layers. 
* Mutability on models should be avoided, opting instead to recreate the model when information changes.

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
    
    /// The cost of the product, as represented in USD.
    let price: Double
    
    /// The category of the product.
    let category: Category
}
```
