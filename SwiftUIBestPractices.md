# SwiftUI Best Practices
## File Order - Dictates how individual View components are placed within a file.

### `body` placed after `init`
We recommend placing the body of the view after the initializer, since this helps to establish a predictable location for the body within the file. 

``` swift
/// Displays the settings related to voice activation.
struct SettingsView: View {
    // …
    
    init(sliderValue: Binding<Double>, wasSessionPreviouslyRunning: Binding<Bool>, settingsViewModel: SettingsViewModel) {
        self._sliderValue = sliderValue
        self._wasSessionPreviouslyRunning = wasSessionPreviouslyRunning
        self.settingsViewModel = settingsViewModel
    }
    
    // MARK: - View
    
    var body: some View {
        List {...}
            .listStyle(.insetGrouped)
    }
}
```

### `body` placed after properties
When no initializer is present the body can be placed immediately after the properties listed.

``` swift
/// Displays the settings related to voice activation.
struct SettingsView: View {
    // …
    @AppStorage(AppStorageKeys.keyTwo) private var propertyTwo = 0.75
    @AppStorage(AppStorageKeys.keyThree) private var propertyThree = 0.5
    @StateObject private var object = DeviceObserver()
    
    private let numberFormatter = NumberFormatter.fractionFormatter
    private let wasSessionPreviouslyRunning: Bool
    
    // MARK: - View
    
    var body: some View {
        List {...}
            .listStyle(.insetGrouped)
    }
}
```

### Property Wrappers grouped together
`@StateObject`, `@State`, and `@AppStorage` properties are marked as private because they do not need to be mutated outside of their containing file. Property wrappers of the same type should be grouped together. 
In the example below, property wrappers are listed first followed by properties without property wrappers. 

```swift 
struct SettingsView: View {
    @Binding private(set) var isDisplayingCertificateView: Bool
    @ObservableObject private(set) var settingsViewModel: SettingViewModel
    @AppStorage(AppStorageKeys.keyOne) private var propertyOne = 0.5
    @AppStorage(AppStorageKeys.keyTwo) private var propertyTwo = 0.75
    @AppStorage(AppStorageKeys.keyThree) private var propertyThree = 0.5
    @StateObject private var object = DeviceObserver()
    
    private let numberFormatter = NumberFormatter.fractionFormatter
    private let wasSessionPreviouslyRunning: Bool
}
```

# Initializing SwiftUI Views
### A manually written initializer
In the example below we use a manually written initializer to create our view. We pass our binding to our initializer to clean up our property call sites.

```swift 
public struct SettingsView: View {

    @Binding private var sliderValue: Double
    @Binding private var wasSessionPreviouslyRunning: Bool
    
    private let settingsViewModel: SettingsViewModel
    private let numberFormatter = NumberFormatter.fractionFormatter
    
    public init(sliderValue: Binding<Double>, wasSessionPreviouslyRunning: Binding<Bool>, settingsViewModel: SettingsViewModel) {
        self._sliderValue = sliderValue
        self._wasSessionPreviouslyRunning = wasSessionPreviouslyRunning
        self.settingsViewModel = settingsViewModel
    }
}
```

When using a manually written initializer, if the first parameter type and label would be obvious at the call site, the parameter label can be omitted.

```swift
// declaration
init(_ text: String)

// usage
Button("Tap here")
```

### A syntesized member-wise initializer

In most cases, you should rely on syntesized member-wise initializers. Any `var` properties set on initialization should be marked `private(set)`.

```swift 
struct SettingsView: View {
    
    @Binding private(set) var sliderValue: Double
    @Binding private(set) var wasSessionPreviouslyRunning: Bool
    
    let settingsViewModel: SettingsViewModel
    let numberFormatter = NumberFormatter.fractionFormatter
}
```

## Comments related to modifiers
### Single line Modifiers
If usage of a particular modifier is unclear at the call site, add a comment explaining its usage. Comments explaining the use of modifiers should be placed to the right of the modifiers.

```swift 
HStack {
    // …
}
.someModifier1()
.someModifier2() // we use this because blah blah blah
.someModifier3()
```

## Modifiers containing closures
When adding comments to modifiers containing closures, the comment should be placed at the beginning of the closure’s body.

```swift 
func body(content: Content) -> some View {
    content
        .onTapGesture {
            // sets the state property when content tapped
            self.liked = !self.liked
        }
}
```
