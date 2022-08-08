# SwiftUI Best Practices

# File Order

### `body` placed after `init`

``` swift
import Foundation
import SwiftUI

/// Displays the settings related to voice activation.

struct SettingsView: View {
    
    private enum Constants {
        static let stepperValue = 0.1
        static let stepperRange = 0.0...2.0
    }
    
    @AppStorage(AppStorageKeys.keyOne) private var propertyOne = 0.5
    @AppStorage(AppStorageKeys.keyTwo) private var propertyTwo = 0.75
    @AppStorage(AppStorageKeys.keyThree) private var propertyThree = 0.5
    
    @Binding private var sliderValue: Double
    @Binding private var wasSessionPreviouslyRunning: Bool
    
    @StateObject private var object = DeviceObserver()
    
    private let numberFormatter = NumberFormatter.fractionFormatter
    private let wasSessionPreviouslyRunning: Bool
    private let settingsViewModel: SettingsViewModel
    
    // MARK: - SettingsView
    
    init(sliderValue: Binding<Double>, wasSessionPreviouslyRunning: Binding<Bool>, settingsViewModel: SettingsViewModel) {
        self._sliderValue = sliderValue
        self._wasSessionPreviouslyRunning = wasSessionPreviouslyRunning
        self.settingsViewModel = settingsViewModel
    }
    
    // MARK: - View
    
    var body: some View {
        List {...}
            .listStyle(InsetGroupedListStyle())
            .navigationTitle("Voice Activation")
            .navigationBarTitleDisplayMode(.inline)
            .onAppear {
                startSessionIfNeeded()
            }
            .onDisappear {
                stopSessionIfNeeded()
            }
            .toolbar {
                ToolbarItem(placement: .primaryAction) {
                    PickerView()
                }
            }
    }
    
    // MARK: - SettingsView
    
    private func startAudioSessionIfNeeded() {
        guard !wasSessionPreviouslyRunning else {
            return
        }
        
        audioSession.start()
    }
    
    private func stopAudioSessionIfNeeded() {
        guard !wasSessionPreviouslyRunning else {
            return
        }
        
        audioSession.stop()
    }
}

struct VoiceActivationSettingsView_Previews: PreviewProvider {
    static var previews: some View {
        NavigationView {
            SettingsView(audioSession: AVAudioSession())
        }
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

## Creating Initializers

### A synthesized initializer

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
```swift 
struct SettingsView: View {
    
    @Binding private(set) var sliderValue: Double
    @Binding private(set) var wasSessionPreviouslyRunning: Bool
    
    private let settingsViewModel: SettingsViewModel
    private let numberFormatter = NumberFormatter.fractionFormatter
}
```

### Regular initializer- without private set.

In this example we the values of the `@Binding` value is able to be mutated outside of this view.

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
Comments explaining the use of modifiers should be placed to the right of the modifiers. 

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
