# View
### Responsibility
*View*s are responsible for rendering content and handling user interaction with that content.

### Key Considerations
* *View*s are responsible for the styling and layout of user interface components.
* *Views* are a visual representation of their models.
* Custom *View*s that are composed of other views define an interface for configuring display properties of their contents through their [*View Model*](https://github.com/Lickability/swift-style-guide/blob/master/ViewModel.md).
* For some views, user interaction is communicated to *Controller*s through delegation or closures.
* For views that inherit from *UIControl* such as (*UISwitch*, *UIButton*, *UISlider*, etc...), user interaction is communicated via a [Target-Action](https://developer.apple.com/library/archive/documentation/General/Conceptual/Devpedia-CocoaApp/TargetAction.html#//apple_ref/doc/uid/TP40009071-CH3) mechanism to notify your app when an interaction has taken place. See below for a code example.

### User Interaction Code Example

```swift
/// Protocol to specify what must be implemented in order to conform to a ForgotPasswordButtonDelegate
protocol ForgotPasswordButtonDelegate: class {
    func forgotPswdButtonWasPressed()
}

final class LoginViewController: UIViewController {
    
    /// Button that user taps on to perform the login operation
    private let loginButton: UIButton = {
        let button = UIButton()
        /// Here we add the Target-Action mechanism to the button.
        /// We will call the performLogin method when the user taps on the login button and releases the button while their finger is inside the bounds of the button.
        button.addTarget(self, action: #selector(performLogin(_:)), for: .touchUpInside)
        return button
    }()
    
    /// Button that user taps on to perform password recovery operation
    private let forgotPassword: UIButton = {
        let button = UIButton()
        button.addTarget(self, action: #selector(performPasswordRecovery), for: .touchUpInside)
        return button
    }()
    
    /// Property that represents the actions that can be performed on behalf of this class
    weak var delegate: ForgotPasswordButtonDelegate?
    
    /// Login method with the sender specified. This is optional and not needed
    @objc private func performLogin(_ sender: UIButton) {
        /// Logic that performs a login operation goes here
    }
    
    /// Logout method without a sender specified.
    @objc private func performPasswordRecovery() {
        /// Inform the delegate that this user interaction took place and the forgot password button was pressed.
        /// The class that conforms to this method will actually implement this method
        delegate?.forgotPswdButtonWasPressed()
    }
}
```

### Interaction Diagram  
![architecturediagramscvvm](https://user-images.githubusercontent.com/16432044/41423446-5a6c2aaa-6fc9-11e8-9a57-5b31492f59b2.png)

###Interface Builder

Per apple, the Interface Builder editor within Xcode makes it simple to design a full user interface without writing any code. Simply drag and drop windows, buttons, text fields, and other objects onto the design canvas to create a functioning user interface.

*Views* designed in the Interface Builder editor are referenced in code via a *IBOutlet* or *IBAction*. For more information, checkout [How to Connect UI to Code](https://developer.apple.com/library/archive/referencelibrary/GettingStarted/DevelopiOSAppsSwift/ConnectTheUIToCode.html).

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
