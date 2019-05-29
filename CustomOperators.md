# Custom Operators
### When do we use them?
Custom operators should be avoided. Custom operators can reduce the readability of the code because there can be confusion around their functionality.

### Key Considerations
* Overloading operators should be used sparingly, e.g. protocol conformance such as `Equatable` or  when two objects are backed by a numeric value such as `Price`.

* Place the implementation of any custom operators in an extension on the type it operates on.

* Be sure to refer to our [Extensions](https://github.com/Lickability/swift-style-guide/blob/master/Extensions.md) document when deciding how to name the extension.

### Reference Docs
* [Basic Operators — The Swift Programming Language](https://docs.swift.org/swift-book/LanguageGuide/BasicOperators.html)
