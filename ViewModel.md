# View Model
### Responsibility
A view model is a view's model. It has the data needed to populate a particular kind of view and the presentation logic needed to transform that data into properties that can be rendered.

### Key Considerations
* View models define the single point of configuration for displayable properties.
* Most of the properties can easily be mapped from similar properties on the Model.
* View models contain the logic for transforming their own properties into displayable versions (e.g. a date object into localizable, human-readable text).
* Interaction logic (eg. user touch event) should be handled by the view itself, not the view model.

### Interaction Diagram
![architecturediagramscvvm](https://user-images.githubusercontent.com/16432044/41423586-afbc2640-6fc9-11e8-86ba-3a565de95dae.png)

### Code Example
```swift
/// Holds the data and logic needed to populate a `ProductCell`.
struct ViewModel {
    
    /// The image representation of the corresponding product.
    let image: UIImage?
    
    /// The name of the corresponding product.
    let productName: String
    
    /// The cost of the corresponding product in USD.
    let price: Double
    
    /// The price of the corresponding product formatted as a `String`.
    var formattedPrice: String {
        return NumberFormatter.localizedString(from: NSNumber(value: price), number: .currency)
    }
}

```
