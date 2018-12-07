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
import Result
```
### Test Target Example
```swift
import XCTest
import CoreData
import Result
@testable import Scorecard
```