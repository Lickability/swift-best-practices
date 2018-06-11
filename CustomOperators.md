# Custom Operators
### When do we use them?
Custom operators should be avoided. Custom operators can reduce the readability of the code because there can be confusion around its functionally.

### Key Considerations
* Overloading of operators should be used sparingly 
	* i. e. protocol conformance such as `equatable` or  when two objects are backed by a numeric value such as `price`

### Reference Docs
* [Basic Operators — The Swift Programming Language (Swift 4.2)](https://docs.swift.org/swift-book/LanguageGuide/BasicOperators.html)