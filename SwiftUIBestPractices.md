# SwiftUI Best Practices
## File Order - Dictates how individual View components are placed within a file.

### `body` placed after `init`
We recommend placing the body of the view after the initializer, since this helps to establish a predictable location for the body with in the file. 

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

 `body` placed after properties
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
`@StateObject`, `@State`, and `@AppStorage` properties are marked as private, because they do not need to be mutated outside of their containing file. Property wrappers of the same type should be grouped together. 
In the example below property wrappers are listed first followed by properties without property wrappers. 

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
### A synthesized initializer
In the example below we use a synthesized initializer to create our view. We pass our binding with in our initializer to clean up our property call sites.

```swift 
struct SettingsView: View {
    @Binding private var sliderValue: Double
    @Binding private var wasSessionPreviouslyRunning: Bool
    
    private let settingsViewModel: SettingsViewModel
    private let numberFormatter = NumberFormatter.fractionFormatter
    
    init(sliderValue: Binding<Double>, wasSessionPreviouslyRunning: Binding<Bool>, settingsViewModel: SettingsViewModel) {
        self._sliderValue = sliderValue
        self._wasSessionPreviouslyRunning = wasSessionPreviouslyRunning
        self.settingsViewModel = settingsViewModel
    }
}
```

### A member-wise initializer
If a `@Binding`  property is not being modified within the view that references it, consider marking it a `private(set)` . This ensures that our property won’t be mutated outside of the context we want it to be.

#### Example One
```swift 
struct SettingsView: View {
    
    @Binding private(set) var sliderValue: Double
    @Binding private(set) var wasSessionPreviouslyRunning: Bool
    
    private let settingsViewModel: SettingsViewModel
    private let numberFormatter = NumberFormatter.fractionFormatter
}
```

In this example we the values of the `@Binding` value is able to be mutated outside of this view, hence we don’t mark it `private(set)`.

#### Example Two
```swift 
struct SettingsView: View {
    
    @Binding var sliderValue: Double
    @Binding var wasSessionPreviouslyRunning: Bool
    
    private let settingsViewModel: SettingsViewModel
    private let numberFormatter = NumberFormatter.fractionFormatter
}

SettingsView(sliderValue: sliderValue, wasSessionPreviouslyRunning:wasSessionPreviouslyRunning, settingsViewModel: SettingsViewModel())
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
