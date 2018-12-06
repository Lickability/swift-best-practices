# Controller
### Responsibility
*Controller*s update their views based on changes in their *Models* by creating and passing *View Models* to them. They also receive input from views and update the *Models* accordingly. 

### Key Considerations
* *Controller*s will often utilize other controllers to fulfill their responsibility.

### Interaction Diagram
![architecturediagramscvvm](https://user-images.githubusercontent.com/16432044/41422678-6d3caaa8-6fc7-11e8-9ee6-bd48d3343711.png)

### Common Controller Types
Here are some of the common types of controllers you will use - [Common Controller Types](https://github.com/Lickability/swift-style-guide/blob/master/CommonControllerTypes.md)

### Code Example

```swift
/// A `UIViewController` subclass that represents an empty state with an action button.
final class EmptyStateViewController: UIViewController {
    @IBOutlet private weak var emptyStateLabel: UILabel!
    @IBOutlet private weak var emptyStateButton: UIButton!
    
    /// A struct used to contain the information need to configure the view of the empty state.
    struct ViewModel {
        
        /// The text to display on screen.
        let message: String
        
        /// The text for the action button.
        let actionText: String
        
        /// A closure that handles responding to a user's tap.
        let actionHandler: () -> Void
    }
    
    private let viewModel: ViewModel
    
    /// Creates a EmptyStateViewController.
    ///
    /// - Parameter viewModel: A struct used to configure the view of the controller.
    init(viewModel: ViewModel) {
        self.viewModel = viewModel
        super.init(nibName: nil, bundle: nil)
    }
    
    @available(*, unavailable, message: "init is unavailable, use init(viewModel:)")
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        configureView()
    }
    
    private func configureView() {
        emptyStateLabel.text = viewModel.message
        emptyStateButton.setTitle(viewModel.actionText, for: .normal)
    }
    
    @IBAction private func emptyStateButtonTapped(_ sender: Any) {
        viewModel.actionHandler()
    }
```
