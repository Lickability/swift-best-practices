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
