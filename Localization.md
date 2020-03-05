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
