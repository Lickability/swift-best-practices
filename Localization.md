# Localization
### How do we use them?
We use the localization tools built into Xcode e.g. `NSLocalizedString`

### Key Considerations
* All user facing text should be made localizable with the [`NSLocalizedString`](https://developer.apple.com/documentation/foundation/nslocalizedstring) family of APIs with corresponding [`Localizable.stringsdict`](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPInternational/StringsdictFileFormat/StringsdictFileFormat.html) files for plurals
* When formatting numbers and dates for display, use the "localized" API variants, such as [`setLocalizedDateFormatFromTemplate(_:)`](https://developer.apple.com/documentation/foundation/dateformatter/1417087-setlocalizeddateformatfromtempla) and [`localizedString(from:dateStyle:timeStyle:)`](https://developer.apple.com/documentation/foundation/dateformatter/1415241-localizedstring)
* Consider using[`String.variantFittingPresentationWidth(_:)`](https://developer.apple.com/documentation/foundation/nsstring/1413104-variantfittingpresentationwidth) when creating adptive width `String`s instead of using conditional logic
