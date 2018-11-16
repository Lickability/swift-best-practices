# View
### Responsibility
*View*s are responsible for rendering content and handling user interaction with that content.

### Key Considerations
* *View*s are responsible for the styling and layout of user interface components.
* Custom *View*s define an interface for configuring display properties of their contents through their *ViewModel*.
* User interaction is communicated to *Controller*s through delegation or closures.

### Interaction Diagram  
![architecturediagramscvvm](https://user-images.githubusercontent.com/16432044/41423446-5a6c2aaa-6fc9-11e8-9a57-5b31492f59b2.png)

### Code Example

```swift
/// A cell that displays information for purchasable products.
final class ProductCell: UITableViewCell {
    
    @IBOutlet private weak var productImageView: UIImageView!
    @IBOutlet private weak var productNameLabel: UILabel!
    @IBOutlet private weak var priceLabel: UILabel!
    
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
    
    /// The view’s view model. Set this value to update the data displayed in the view.
    var viewModel: ViewModel? {
        didSet {
            productImageView.image = viewModel?.image
            productNameLabel.text = viewModel?.productName
            priceLabel.text = viewModel?.formattedPrice
        }
    }
}
```
