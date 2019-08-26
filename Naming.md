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
