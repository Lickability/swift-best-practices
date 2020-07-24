# Architecture Diagram

![architecturediagramsmvcvm](https://user-images.githubusercontent.com/16432044/41361406-f5b5c55c-6efc-11e8-886c-bcd3adbd7294.png)

### [Model](https://github.com/Lickability/swift-style-guide/blob/master/Model.md)
Models are responsible for representing the data of the application.

### [View](https://github.com/Lickability/swift-style-guide/blob/master/View.md)
Views are responsible for rendering content and handling user interaction with that content.

### [Controller](https://github.com/Lickability/swift-style-guide/blob/master/Controller.md)
Controllers are the primary connection between models, view models, and views.

### [View Model](https://github.com/Lickability/swift-style-guide/blob/master/ViewModel.md)
A view model is a view’s model. It encapsulates the data needed to populate a particular kind of view and the presentation logic needed to transform that data into properties that can be rendered.

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

# View
### Responsibility
Views are responsible for rendering content and handling user interaction with that content.

### Key Considerations
* Views are responsible for the styling and layout of user interface components.
* Views are a visual representation of their models.
* Custom views that are composed of other views define an interface for configuring display properties of their contents through their [view model](https://github.com/Lickability/swift-style-guide/blob/master/ViewModel.md).
* For some views, user interaction is communicated to controllers through delegation or closures.
* For views that inherit from `UIControl` (such as `UISwitch`, `UIButton`, `UISlider`, etc...), user interaction is communicated via a [target-action](https://developer.apple.com/library/archive/documentation/General/Conceptual/Devpedia-CocoaApp/TargetAction.html#//apple_ref/doc/uid/TP40009071-CH3) mechanism to notify your app when an interaction has taken place.
* The [target-action](https://developer.apple.com/library/archive/documentation/General/Conceptual/Devpedia-CocoaApp/TargetAction.html#//apple_ref/doc/uid/TP40009071-CH3) mechanism can be combined with [delegation](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html) or [closures](https://docs.swift.org/swift-book/LanguageGuide/Closures.html) to delegate the responsibility of handling the action to another controller. See below for a code example.

### User Interaction Code Example

```swift
/// Protocol to specify what must be implemented in order to conform to a `LoginViewControllerDelegate`.
protocol LoginViewControllerDelegate: class {
    func loginViewControllerPasswordRecoveryRequested(_ loginViewController: LoginViewController)
}

/// A controller that manages the actions for login and forgot password buttons.
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
        button.addTarget(self, action: #selector(performPasswordRecovery(_:)), for: .touchUpInside)
        return button
    }()

    /// Property that represents the actions that can be performed on behalf of this class
    weak var delegate: LoginViewControllerDelegate?

    @objc private func performLogin(_ sender: UIButton) {
        /// Logic that performs a login operation goes here
    }

    @objc private func performPasswordRecovery(_ sender: UIButton) {
        /// Inform the delegate that this user interaction took place and the forgot password button was pressed.
        /// The class that conforms to this method will actually implement this method
        delegate?.passwordRecoveryRequested(from: sender)
    }
}
```

### Interaction Diagram  
![architecturediagramscvvm](https://user-images.githubusercontent.com/16432044/41423446-5a6c2aaa-6fc9-11e8-9a57-5b31492f59b2.png)

### Interface Builder

Views designed in the Interface Builder editor are referenced in code via a `IBOutlet` or `IBAction`. For more information, check out [How we use Interface Builder](InterfaceBuilder.md).

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
* When defining a width or height constraint in relation to the opposite dimension, use the “Aspect Ratio” option in Interface Builder (found in the “Add New Constraints” menu, or in the popup that appears when dragging a width or height constraint).
    * The Multiplier value for the constraint should always be defined as a ratio (e.g. `4:3`), and not a decimal value (e.g. `1.333`).
    * The constraint’s first item should represent the dimension that effectively determines the other. For example, if an image view is full-width, and the height is defined as half of the width, the first item should be the image view’s width, and the second item should be its height, with a ratio of `2:1`:
    <img width="739" alt="aspect-ratio-constraint" src="https://user-images.githubusercontent.com/7883805/74057707-8013a980-49b2-11ea-8abc-5f31d011b5ab.png" align="middle">
    
    * If neither dimension takes precedence in determining the other dimension when using an aspect ratio constraint (e.g. a `60x60` view), use the width as the constraint’s first item and the height as its second item.

# Controller
### Responsibility
Controllers are responsible for controlling the flow of the application execution.

### Key Considerations
* Controllers often utilize other controllers to fulfill their responsibility.

### Interaction Diagram
![architecturediagramscvvm](https://user-images.githubusercontent.com/16432044/41422678-6d3caaa8-6fc7-11e8-9ee6-bd48d3343711.png)

### Common Controller Types
Here are some of the [common types of controllers](https://github.com/Lickability/swift-style-guide/blob/master/CommonControllerTypes.md) you will use.

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

        /// A button the user interacts with.
        let action: Action
    }

    /// A struct used to contain the properties associated with the action button.
    struct Action {

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

    // MARK: - viewDidLoad
    override func viewDidLoad() {
        super.viewDidLoad()
        configureView()
    }

    private func configureView() {
        emptyStateLabel.text = viewModel.message
        emptyStateButton.setTitle(viewModel.action.actionText, for: .normal)
    }

    // MARK: - IBActions
    @IBAction private func emptyStateButtonTapped(_ sender: Any) {
        viewModel.action.actionHandler()
    }
}
```

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

# Common Controller Types
### Parser
Transforms structured data into model types.

```swift
/// Responsible for transformation information into `Game` objects.
struct GameParser {

    /// Parses a `Game` model from the specified JSON.
    ///
    /// - Parameter json: The JSON to parse.
    /// - Returns: Returns a parsed `Game` object.
    /// - Throws: Throws an error if the input is invalid.
    func parse(json: [String: Any]) throws -> Game {
        guard let name = json["name"] as? String else {
            throw ParsingError.invalidInput
        }

        let description = json["description"] as? String

        return Game(name: name, description: description)
    }
}
```

### Network Controller
Handles sending network requests and receiving response data.

```swift
/// Describes a type capable of performing network requests.
protocol Networker {

    /// Performs a network request, returning a `Result` type via completion handler on success or failure.
    ///
    /// - Parameters:
    ///   - request: The network request to perform.
    ///   - completionQueue: The queue on which the completion handler will be called.
    ///   - completion: The completion handler called upon success or failure.
    func performRequest(_ request: URLRequest, completionQueue: OperationQueue, completion: @escaping (Result<Any, Error>) -> Void)
}

/// A concrete implementation of `Networker` that wraps the `URLSession` APIs.
final class NetworkController: Networker {

    // MARK: - NetworkController

    private let urlSession: URLSession

    // MARK: - Networker

    func performRequest(_ request: URLRequest, completionQueue: OperationQueue, completion: @escaping (Result<Any, Error>) -> Void) {
        // ...
    }

    // MARK: - NetworkController

    /// Creates a new network controller.
    ///
    /// - Parameters:
    ///   - urlSession: The `URLSession` to use for performing requests.
    init(urlSession: URLSession = .shared) {
        self.urlSession = urlSession
    }    
}
```

### Persistence Controller
Reads and writes models to / from the data layer.

```swift
protocol Persister {

    /// Synchronously persists an object on disk.
    ///
    /// - Parameters:
    ///   - object: The object to persist.
    ///   - key: The key used to persist and later retrieve the object.
    func persist<T: NSCoding>(object: T, forKey key: String)

    /// Synchronously retrieves an object from disk stored under a given key.
    ///
    /// - Parameter key: The key used to access the persisted object.
    /// - Returns: The persisted object, or `nil` if the object wasn’t found under the specified key.
    func retrieveObject<T: NSCoding>(forKey key: String) -> T?
}

/// Manages interaction with objects stored on and retrieved from disk.
final class PersistenceController: Persister {

    // MARK: - PersistenceController

    private let cache: DiskCache<NSString, NSCoding>

    // MARK: - Persister

    func persist<T: NSCoding>(object: T, forKey key: String) {
        cache.setObject(object, forKey: key as NSString)
    }

    func retrieveObject<T: NSCoding>(forKey key: String) -> T? {
        return cache.object(forKey: key as NSString) as? T
    }

    // MARK: - PersistenceController

    /// Creates a new persistence controller.
    ///
    /// - Parameters:
    ///   - identifier: The unique identifier of the persistence controller. It is safe to create multiple instances that share the same unique identifier to access a single data set.
    init(identifier: String) {
        self.cache = DiskCache(rootDirectoryURL: rootDirectoryURL)
    }
}
```

### Translator
Handles complex conversions of models to view models.

```swift
/// Translator responsible for translating models into `GameCell.ViewModel` instances.
final class GameCellViewModelTranslator {

    /// Creates a new GameCellViewModelTranslator.
    init() {
        // ...
    }

    /// Translates a `Game` into a `GameCell.ViewModel`.
    ///
    /// - Parameter game: The `Game` to translate into a view model.
    /// - Returns: The translated view model.
    func translate(game: Game) -> GameCell.ViewModel {
        return GameCell.ViewModel(titleText: game.name, description: game.description)
    }
}
```

### View Controller
Manages a view hierarchy and UI logic for your app and coordinates with other controllers to keep it up to date.

```swift
/// A view controller that displays an example.
class ExampleViewController: UIViewController {

    // MARK: - UIViewController

    override var extendedLayoutIncludesOpaqueBars: Bool {
        set {

        }
        get {
            return true
        }
    }

    // MARK: - UITraitEnvironment

    override var traitCollection: UITraitCollection {
        return UITraitCollection(userInterfaceIdiom: .phone)
    }

    // MARK: - ExampleViewController

    @IBOutlet private weak var exampleView: UIView!

    private let urlSession: URLSession

    // MARK: - UIViewController

    override func viewDidLoad() {
        super.viewDidLoad()

        view.backgroundColor = .green
    }

    // MARK: - NSCoding

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    // MARK: - UITraitEnvironment

    override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
        view.backgroundColor = .red
    }


    // MARK: - ExampleViewController

    /// Initializes the `ExampleViewController` with the required parameters.
    ///
    /// - Parameter urlSession: The url session the controller should use.
    init(urlSession: URLSession) {
        self.urlSession = urlSession

        super.init(nibName: nil, bundle: nil)
    }

    private func retrieveExample() {
        // ...
    }
}

extension ExampleViewController: UINavigationControllerDelegate {

    // MARK: - UINavigationControllerDelegate

    func navigationController(_ navigationController: UINavigationController, didShow viewController: UIViewController, animated: Bool) {
        // ...
    }
}
```

### Updater
Makes use of network and persistence controllers to fetch and persist data from the network.

```swift
/// Retrieves and persists game data from the network.
final class GameUpdater {

    private let networker: Networker
    private let persister: Persister

    /// Creates a new `GameUpdater`
    ///
    /// - Parameters:
    ///   - networker: The network controller responsible for making network requests.
    ///   - persister: The persistence controller responsible for storing updated objects to disk.
    init(networker: Networker = NetworkController(), persister: Persister = PersistenceController(identifier: "default")) {
        self.networker = networker
        self.persister = persister
    }

    /// Attempts to update a game using the specified URL.
    ///
    /// - Parameters:
    ///   - url: The API URL from which to retrieve the game.
    ///   - completion: The completion handler that delivers the result. Called on the main queue.
    func updateGame(from url: URL, completion: @escaping (Result<Game, Error>) -> Void) {
        let request = URLRequest(url: url)

        networkController.performRequest(request, completionQueue: .main) { (result: Result<Game, Error>) in
            switch result {
            case .success(game):
                persister.persist(object: game, forKey: game.identifier)
            case .failure(error):
                // ...
            }

            completion(result)
        }
    }
}
```

### DataSource
Manages collections of data to power UI-related collections.

```swift
/// A data source for a table view interface that displays a generic collection of homogenous elements.
final class TableViewDataSource<CollectionType: Collection>: NSObject, UITableViewDataSource where CollectionType.Index == Int {

    ///  A closure that is called when a cell needs to be configured.
    ///
    /// - Parameters:
    ///   - element: The model object to configure the cell with.
    ///   - indexPath: The index path of the cell being configured.
    ///   - tableView: The table view to configure the cell for.
    /// - Returns: A `UITableViewCell` or subclass configured.
    typealias CellConfiguration = (_ element: CollectionType.Element, _ indexPath: IndexPath, _ tableView: UITableView) -> UITableViewCell

    // MARK: - TableViewDataSource

    /// A closure that is called when a cell needs to be configured.
    var cellConfiguration: CellConfiguration?

    private var collection: CollectionType

    // MARK: - UITableViewDataSource

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return collection.count
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        guard collection.indices.contains(indexPath.row) else {
            logAssertionFailure(message: "The index path \(indexPath) provided is out of range. This is unexpected.`")
            return UITableViewCell()
        }

        guard let cellConfiguration = cellConfiguration else {
            logAssertionFailure(message: "It is expected that the cell configuration closure has been set and is not nil.")
            return UITableViewCell()
        }

        let element = collection[indexPath.row]

        return cellConfiguration(element, indexPath, tableView)
    }

    // MARK: - TableViewDataSource

    /// Creates a new `TableViewDataSource<Element>`.
    ///
    /// - Parameters:
    ///   - collection: The collection of elements the data source will manage.
    init(collection: CollectionType) {
        self.collection = collection
    }

    /// Accesses the element at the specified position.
    ///
    /// - Parameter indexPath: The position of the element to access.
    /// - Returns: The element at the specified position.
    func element(at indexPath: IndexPath) -> CollectionType.Element? {
        // ...
    }
}
```

# Assets
### How do we use them?
We use asset catalogs to organize our application’s assets.

### Key Considerations
* Organize assets using folders inside asset catalogs.
* All colors and images that are known at compile time should exist in asset catalogs, unless they are system colors, in which case they do not need to be duplicated in the asset catalog. 
* Asset catalog colors should come from a project’s design system for project-wide consistency, ease of small tweaks, and to assist in supporting color-related features like dark mode. Avoid adding colors for specific use cases without verifying it with the project’s designers and updating the design system.
* Whenever possible, set images from asset catalogs in Interface Builder. 
* Always set UI component colors in code, never in Interface Builder. It is too easy to fall out of sync with asset catalog and design system colors when they’re set in Interface Builder.
* When accessing asset catalog colors and images in code, make use of code generation solutions such as [SwiftGen](https://github.com/SwiftGen/SwiftGen#asset-catalog) to provide non-optional constants for assets.
* Avoid use of image and color literals, as they tend to be difficult to edit in Xcode, and potentially difficult to see depending on your source editor colors.


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

# Extensions
### How do we use extensions?
We use extensions to break up our code into logical groups, e.g. when separating code specifically for protocol conformance into its own extension.

### Key Considerations
* Declare protocol conformance to a type as an extension in the same file as that type declaration, if possible.
* Declare an extension that will only be used in a single file, in the same file that will use it, e.g. an `Array` extension constrained to a specific type of element used elsewhere in that file.
* Declare general-purpose extensions for other frameworks in their own files.
	* Name the file in the format `<Extended type>+<Word or phrase about the extension>`, e.g. `Array+MergeSort.swift`.

### Example
```swift
extension UITableView {

    /// Hides the empty cells at the bottom of the table view.
    func hideEmptyCellsFooter() {
        tableFooterView = UIView()
    }
}
```

# Imports
### How do we use them?
Sort imports by system frameworks, followed by third party, and then our own frameworks.

### Key Considerations
* `@testable` imports should fall below all other imports.
* There should not be empty lines between imports.

### Code Example

```swift
import UIKit
import MobileCoreServices
import AVFoundation
import Alamofire
```
### Test Target Example
```swift
import XCTest
import CoreData
import Alamofire
@testable import Scorecard
```

# Organization Within a File
### How do we organize files?
A file should only contain one major type declaration. Other types are allowed in support of the main type that is represented by the file, which typically shares the name of the file, e.g. `LoginViewController.swift` would have a major type of `LoginViewController`.

#### Bad Code Example

In the example below, we have declared multiple top level enums and classes within one file. The class declarations should be split across multiple files and the enums should be encapsulated within their respective classes when it makes sense.
```swift
/// An enum to track whether or not the user is logged in or not
enum AuthState {
    case loggedIn
    case loggedOut
}

/// An enum to track where the user is in the signup / onboarding process
enum SignupState {
    case onboardingComplete
    case signupComplete
}

/// A UIViewController subclass that encapsulates functionality for a Login Screen
class LoginViewController: UIViewController {
    /// ...
}

///A UIViewController subclass that encapsulates functionality for a Signup Screen
class SignupViewController: UIViewController {
    /// ...
}
```

#### Good Code Example
In the example below, the file has only one major type declaration. The protocol and extension are supporting the major type of `LoginViewController` and therefore allowed to be in this file.
```swift
protocol LoginViewControllerDelegate {
    /// ...
}

/// A UIViewController subclass that encapsulates functionality for a Login Screen
class LoginViewController: UIViewController {
    /// ...
}

extension LoginViewController {
    // MARK: - LoginViewController
}
```

### Key Considerations
* Files are organized in the following order:
	* Default header created by Xcode
	* Import statements
	* Delegate protocols that are associated only with the major type declaration of the file
	* The major type declaration of the file
	* Nested type declarations
	* Properties
	    * Inherited
	    * Protocol
	    * `IBOutlet`s
	    * Open
	    * Public
	    * Internal
	    * Private
	* Functions
	    * Inherited
	    * Protocol
	    * Open
	    * Public
	    * Internal
	    * Private
	* Extension Protocol Conformances

* Initializers, when implemented, should be the first declaration(s) in each group (inherited, protocol, open, etc.) of functions.
* `deinit`, when implemented, should come directly after the last initializer. If no initializers exist, `deinit` should come before all other function declarations.
* For `Codable` conformance, [it may be necessary to implement the special nested type `CodingKeys`, which conforms to `CodingKey`](https://developer.apple.com/documentation/foundation/archives_and_serialization/encoding_and_decoding_custom_types). When present, this nested type should be declared after all other nested types. Since `CodingKeys` and `CodingKey` are not documented as part of the `Codable` protocols, no `MARK` is necessary.

### How do we use MARK?
Group and separate code using `MARK`. The grouping order for each section of properties and functions should be:
* Overridden declarations
* Declarations for protocol conformance
* Declarations being introduced in the major type of the file

### Key Considerations
* We only use `MARK` when a file has overrides or conformances.
* `MARK` separates where things were originally declared.
* Always use `MARK: -` for grouping based on type.
	* The text should be the type you are grouping by.
* Use `MARK:` for other groupings inside of `MARK: -`, e.g. `MARK: Helper Functions`.
* When adding a `MARK` for an extension, make sure it is inside the extension.

### Code Example
```swift
/// A view controller that displays an example.
class ExampleViewController: UIViewController {

    // MARK: - UIViewController

    override var extendedLayoutIncludesOpaqueBars: Bool {
        set {

        }
        get {
            return true
        }
    }

    // MARK: - UITraitEnvironment

    override var traitCollection: UITraitCollection {
        return UITraitCollection(userInterfaceIdiom: .phone)
    }

    // MARK: - ExampleViewController

    @IBOutlet private weak var exampleView: UIView!

    private let urlSession: URLSession

    // MARK: - UIViewController

    override func viewDidLoad() {
        super.viewDidLoad()

        view.backgroundColor = .green
    }

    // MARK: - NSCoding

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    // MARK: - UITraitEnvironment

    override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
        view.backgroundColor = .red
    }


    // MARK: - ExampleViewController

    /// Initializes the `ExampleViewController` with the required parameters.
    ///
    /// - Parameter urlSession: The url session the controller should use.
    init(urlSession: URLSession) {
        self.urlSession = urlSession

        super.init(nibName: nil, bundle: nil)
    }

    private func retrieveExample() {
        // ...
    }
}

extension ExampleViewController: UINavigationControllerDelegate {

    // MARK: - UINavigationControllerDelegate

    func navigationController(_ navigationController: UINavigationController, didShow viewController: UIViewController, animated: Bool) {
        // ...
    }
}
```

# Formatting
### How do we format code?
Most formatting-related guidelines are enforced by [SwiftLint](https://github.com/realm/SwiftLint) using [our documented configuration file](https://github.com/Lickability/swift-best-practices/blob/master/.swiftlint.yml). Xcode’s default behaviors and preferences are preferred for considerations not covered by SwiftLint rules.

### Key Considerations
* Resolve any added SwiftLint warnings prior to opening a pull request with your changes.
* In the rare case in which you need to opt out of a SwiftLint rule, use [swiftlint:disable:this](https://github.com/realm/SwiftLint#disable-rules-in-code) on the lines that require the exception, along with a comment explaining the exception(s).
* Use Xcode’s default indentation preferences (spaces, not tabs, with a tab width of four spaces).
* Use Xcode’s Re-Indent feature (Editor → Structure → Re-Indent, or `⌃I`) to ensure code and documentation is properly indented.
    * There are times in which Xcode indents something in an unfavorable way. These occurrences are fairly rare. Rather than fighting the tools, it is still preferred to use Xcode’s behavior in these cases. For example, when a function takes multiple closures as parameters and capture list is used at the callsite, the closure bodies indent differently from each other:

    ```swift
    // Without capture list:
    updateFeedFromNetwork(networkCompletion: { result in
        // code…
    }, persistenceCompletion: { result in
        // code…
    })

    // With capture list:
    updateFeedFromNetwork(networkCompletion: { [weak self] result in
        // code…
        }, persistenceCompletion: { result in
            // code…
    })
    ```

# Project Groups
### How do we use them?
Organize Xcode groups first by feature and then by architecture component, if needed.

### Example
The following example shows a project structure with **three** top level directories consisting of **one** feature directory and **two** application-related directories.

<img width="309" alt="49822191-6c3acc00-fd4a-11e8-8baf-f928a6c82f0a" src="https://user-images.githubusercontent.com/1043416/56973607-2e90e100-6b3b-11e9-91df-bddc734c6251.png">

### Key Considerations
* Every file should exist within an Xcode group, categorized first by feature, with exceptions listed below:
    * The `Application` group is a special case that is neither a feature nor an architecture component. This group should contain files associated with the application entry point, such as `Main.storyboard` and `AppDelegate.swift`.
    * The `Resources` group houses supporting files to the main application that are more static in nature. This group should contain files like `Info.plist`, `Assets.xcassets`, and `LaunchScreen.storyboard`.

* The above `Checkout` group is a feature that has its files distributed into groups related to its architecture.

* When a feature group grows to contain more than five files consider adding sub-groups to categorize the files by architecture component, e.g. `Models`, `Views`, `Controllers`, etc.

# Custom Operators
### When do we use them?
Custom operators should be avoided. Custom operators can reduce the readability of the code because there can be confusion around their functionality.

### Key Considerations
* Overloading operators should be used sparingly, e.g. protocol conformance such as `Equatable` or  when two objects are backed by a numeric value such as `Price`.

* Place the implementation of any custom operators in an extension on the type it operates on.

* Be sure to refer to our [Extensions](https://github.com/Lickability/swift-style-guide/blob/master/Extensions.md) document when deciding how to name the extension.

### Reference Docs
* [Basic Operators — The Swift Programming Language](https://docs.swift.org/swift-book/LanguageGuide/BasicOperators.html)

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

# Localization
### How do we use it?
We use the localization tools and APIs provided by Apple, e.g. `NSLocalizedString`.

### Key Considerations
* All user-facing text should be made localizable with the [`NSLocalizedString`](https://developer.apple.com/documentation/foundation/nslocalizedstring) family of APIs with corresponding [`Localizable.stringsdict`](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPInternational/StringsdictFileFormat/StringsdictFileFormat.html) files for plurals.
* The `key` parameter of [`NSLocalizedString(_:comment:)`](https://developer.apple.com/documentation/foundation/1418095-nslocalizedstring) should be the text as it appears in English. Do not use other constants or identifiers, like `"button.log-in.forgot-password"`. 
* Always fill out the `comment` parameter of [`NSLocalizedString(_:comment:)`](https://developer.apple.com/documentation/foundation/1418095-nslocalizedstring) with a detailed description of the text with enough information such that a translator could understand the text without further context. Add detailed descriptions of positional parameters, and when multiple parameters are present, refer to them in the text based on their position in the English translation.
    * Examples: 
    ```swift
    NSLocalizedString("%d comments", comment: "Label displayed at the top of a thread. Parameter is the number of comments in the thread.")
    
    NSLocalizedString("Welcome to %@, %@!", comment: "Message shown at the top of the home screen after logging in. First parameter is the app name. Second parameter is the logged in user’s first name.")
    ```
* When formatting numbers and dates for display, use the “localized” API variants, such as [`setLocalizedDateFormatFromTemplate(_:)`](https://developer.apple.com/documentation/foundation/dateformatter/1417087-setlocalizeddateformatfromtempla) and [`localizedString(from:dateStyle:timeStyle:)`](https://developer.apple.com/documentation/foundation/dateformatter/1415241-localizedstring).
* Consider using [`String.variantFittingPresentationWidth(_:)`](https://developer.apple.com/documentation/foundation/nsstring/1413104-variantfittingpresentationwidth) when creating adaptive width `String`s instead of using conditional logic.

# Naming
## General Guidelines
Follow the official [Swift API Design Guidelines section on naming](https://swift.org/documentation/api-design-guidelines/#naming).

## UI Components
When naming types and instances of views, view controllers, layers, and other components of the user interface, include type information in the name of the type and instance to disambiguate from non-user interface data at usage sites.

### Examples
```swift
let name: UITextField // 🛑
name.delegate = self // Unclear at usage.

let nameTextField: UITextField // ✅
nameTextField.delegate = self // Clear at usage.
```

```swift
final class Settings: UIViewController { } // 🛑
let settings = Settings() // 🛑
show(settings, sender: self) // Unclear at usage.

final class SettingsViewController: UIViewController { } // ✅
let settingsViewController = SettingsViewController() // ✅
show(settingsViewController, sender: self) // Clear at usage.
```

# Interface Builder
### How do we use Interface Builder?
* We use Interface Builder in lieu of layout code to reduce the amount of code in views and view controllers

### Key Considerations
* Use `UIStackView`s instead of explicit constraints between siblings whenever possible, unless there are noticeable performance issues.
* Each nib should have a single top level item.
    * Separate `UIView` subclasses designed in Interface Builder into their own nib files.
* Use `IBInspectable` to allow for customization of common design properties in Interface Builder, e.g. to specify a view’s corner radius or give it a border. Use `IBDesginable` only to render custom drawing in a view. Avoid using `IBDesignable` to customize outlet properties, as accessing outlets in `prepareForInterfaceBuilder()` is [not currently supported](http://www.openradar.appspot.com/radar?id=4952346488471552).
* Do not set colors in Interface Builder. It is too easy to fall out of sync with asset catalog and design system colors when they’re set in Interface Builder. Instead, exclusively use color constants provided in code as described in [Assets](https://github.com/Lickability/swift-best-practices/blob/master/Assets.md).
* Do not set fonts on text components in Interface Builder. Similar to colors, adherence to a design system can become more cumbersome when fonts are specified in both code and Interface Builder. Set all fonts in code.
* Whenever possible, design and layout views in Interface Builder, and load them from their corresponding nibs from code.
    * For `UITableViewCell` and `UICollectionViewCell`s, register the cell with the `UITableView` or `UICollectionView` using the nib name.
    * For other views, you can refer to the following code:

    ```swift
    extension UIView {
        static func defaultNibName() -> String {
            return String(describing: self)
        }

        static func instantiateViewFromNib<T: UIView>(_ nibName: String, bundle: Bundle? = nil) -> T? {
            return UINib(nibName: nibName, bundle: bundle).instantiate(withOwner: nil, options: nil).first as? T
        }

        static func instantiateViewFromNib(bundle: Bundle? = nil) -> Self? {
            return instantiateViewFromNib(defaultNibName(), bundle: bundle)
        }
    }
    ```

    # Object Communication
## Types of Object Communication

### Delegation

One-to-one communication initiated by the owner of the delegate.

#### Example
We use delegation to define a set of APIs for one-to-one communication between two instances. In this case, we can conform to a protocol that will notify an object that the application finished launching.

```swift
public protocol UIApplicationDelegate : NSObjectProtocol {
    optional func applicationDidFinishLaunching(_ application: UIApplication)
}
```

### Closures
Like delegates, they are a one-to-one communication where the owner of the property initiates the communication. Closures differ in that they can capture state, which makes implementation more convenient for the client.

#### Example
Closures offer additional flexibility in the number of objects involved in communication. While each closure will be a one-to-one relationship, each can have a different client. Closures also provide access to state, which can be more convenient to clients. In this case, we can define a closure that can be set, which will be called when the application finished launching.


```swift
var applicationDidFinishLaunching: ((UIApplication) -> Void)?
```

### Notifications

One-to-many communication where subscribers subscribe to a publisher. It is a one-way relationship.

#### Example
Notifications are used to broadcast to any subscribers that are interested in events that a publisher advertises. In this case, a notification is defined that broadcasts that the application finished launching.

```swift
public class let didFinishLaunchingNotification: NSNotification.Name
```

# Optionals
### When do we use them?
Optionals are used when something is not known at the time of initialization, when an API can fail, or when the absence of a value provides additional meaning.

### Key Considerations
* Evaluate if you need an optional value, and avoid them if possible.
* Do not make `Bool`s optional.
	*  A tri-state `Bool` can be represented in a more structured way, such as an `enum` with three well-named `case`s.
* Avoid making `Array`s optional.
	* Only do this if it provides meaning beyond it just being empty.
* System APIs may require us to use optional values since they return optional values.
	* `URL`s created using `init?(string:)` are a common example.
* `weak` properties must be optional by definition because they can become `nil`.

### Dealing with Optionals
* Always handle unwrapping optionals safely.
* We prefer conditional binding (`if let`) over `flatMap`.
* If a function requires an optional value to have a value, we opt to bind with `guard` statements and return early.

### Reference Docs
* [The Basics — The Swift Programming Language](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html#ID330)

# Protocols
## What are they?
Protocols define a blueprint of methods, properties, and other requirements that suit a particular task or piece of functionality.

### How do we use them?
Some uses of protocols are as follows:
* As delegates of views and controllers.
* To provide a common interface for shared functionality.
* To wrap dependencies so that swapping out a 3rd-party library with a new library or implementation can be extended to implement the functionality we need without a massive project-wide change.
* For testing controllers using dependency injection (with protocol types) such that these dependencies can be swapped out in unit tests to focus only on the logic in the controller being tested.

## Code Example
```swift
/// Defines a common point of configuration through a view model for classes. Generally these classes are reusable cells.
protocol ViewModelDisplaying: class {

    /// The type of view model the class has defined.
    associatedtype ViewModel

    /// A optional instance of the view model that can be set to configure the view.
    var viewModel: ViewModel? { get set }
}
```

# Testing

### Unit Testing
Unit testing is used to verify that a single unit of work is behaving as expected. We use [XCTest](https://developer.apple.com/documentation/xctest) to write unit tests.

Example:
```swift
func testAddition() {
    let simpleAddition = "5 + 5"
    let negativeAddition = "2 + -8"
    let doubleNegative = "-7 + -3"

    XCTAssertEqual(calculator.evaluate(simpleAddition), 10)
    XCTAssertEqual(calculator.evaluate(negativeAddition), -6)
    XCTAssertEqual(calculator.evaluate(doubleNegative), -10)
}
```

### UI Testing
UI testing is used to ensure that the interface that is displayed to the user is the one that is expected to be displayed. We use [Xcode UI Testing](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/09-ui_testing.html) to write UI tests.

Example:
```swift
func testEmptyState() {
    let emptyStateLabel = app.staticTexts["You don't have any games yet!"]
    XCTAssertTrue(emptyStateLabel.exists)

    let addAGameButton = app.buttons["Add a game"]
    XCTAssertTrue(addAGameButton.exists)

    addAGameButton.tap()
    waitForExpectations(timeout: 2.0, handler: nil)

    let createAGameLabel = app.staticTexts["Create a game"]
    XCTAssertTrue(createAGameLabel.exists)
}
```

### Integration Testing
Integration testing is used to verify work between multiple entities. Instead of mocking or stubbing dependencies of objects we create, we allow the actual implementations of dependencies to interact with the object that we're testing. This allows us to verify that our objects work together as we expect, and provides an additional layer of confidence on top of unit testing.

### Contract Testing
Contract testing is used to verify that an API outside of your direct control is behaving as expected. This is often used to verify that information being sent from a server, matches a specification that your application expects. These tests usually require network connections, as you're verifying that the actual environment you're communicating with is behaving properly.

### Blackbox Testing
Blackbox testing is performed from the perspective of a user of the application, to verify that functionality the user expects to work does, in fact, work. Always perform blackbox testing on an actual device, to match the environment a user would experience as close as possible.

# Zero Usage

### How do we use `zero`?

`zero` exists as a representation of the zero value of a given structure or primitive such as `CGPoint` or `Int`.

* `zero` should never be used as an extension on a primitive such as `Int` or `Double`. Instead, use the raw `0` value.
* `zero` should be used on non-primitive types such as `CGPoint` or `NSDecimalNumber`.

### Key Considerations
* By making a distinction on when to use `zero`, it allows for easier comprehension of code. At quick glance, seeing a `0` representation can relate to the reader that the type that is being dealt with is a primitive, vs. seeing a `zero` implying a non-primitive.

## Code Example

```swift
let point: CGPoint = .zero  // ✅
let integer: Int = 0 // ✅
let rect: CGRect = .zero // ✅

let point = CGPoint(x: 0, y: 0) // 🛑
let integer: Int = .zero // 🛑
let rect = CGRect(x: 0, y: 0, width: 0, height: 0) // 🛑
```
