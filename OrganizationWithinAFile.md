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
