# Organization Within a File
### How do we organize files?
A file should only contain one major type declaration. The idea behind this is to avoid declaring multiple classes, structs, or enums.

#### Bad Code Example

In the example below, we have declared multiple top level enums and classes within one file. The class declarations should be split across multiple files and the enums should be encapsulated within their respestive classes when it makes sense.
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
    /// some implementation
}
///A UIViewController subclass that encapsulates functionality for a Signup Screen
class SignupViewController: UIViewController {
    /// some implementation
}
```

#### Good Code Example
In the example below, the file has only 1 major type declaration. The protocol and extension are supporting the major type of `LoginViewController` and therefore allowed to be in this file.
```swift
protocol LoginViewControllerDelegate {
    /// some implementation
}
/// A UIViewController subclass that encapsulates functionality for a Login Screen
class LoginViewController: UIViewController {
    /// some implementation
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
	* Initializers
	* Functions
	* Inner type declarations
	* Properties
		* `IBOutlet`s
		* Public Properties
		* Private Properties
	 
### How do we use MARKs?
Group and separate code using `MARK`s. The grouping order for each section of properties, initializers, and functions should be:
* Overridden declarations
* Declarations for protocol conformance
* Declarations being introduced in the major type of the file

### Key Considerations
* We only use `MARK`s when a file has overrides or conformances
* `MARK`s separate where things were original declared
* Always use `MARK: -` for grouping based on type
	* The text should be the type you are grouping by
* Use `MARK:` for other groupings inside of `MARK: -` 
	* e.g. `MARK: Helper Functions`
* When adding a `MARK` for an extension, ensure the `MARK` is inside the extension

### Final Code Example
```swift
/// A UIViewController subclass that manages information about a users' profile.
class UserProfileViewController: UIViewController {
    
    @IBOutlet private weak var profileImageView: UIImageView!
    @IBOutlet private weak var usernameLabel: UILabel!
    @IBOutlet private weak var editPhotoButton: UIButton!
    
    /// A struct used to contain the information needed to configure the view managed by the UserProfileController.
    struct ViewModel {
        /// The user's username
        let username: String
    }
    /// a url pointing to where the users' profile image is located.
    var profileImageURL: URL?
    
    private var viewModel: ViewModel
    
    /// Creates a UserProfileViewController.
    ///
    /// - Parameter viewModel: A struct used to configure the view of the controller.
    init(viewModel: ViewModel) {
        self.viewModel = viewModel
        super.init(nibName: nil, bundle: nil)
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    /// The user pressed the Edit Photo button to edit their profile image
    @IBAction func editPhotoButtonPressed(_ sender: UIButton) {
        /// Launch image picker to select new profile image.
    }
}
```
