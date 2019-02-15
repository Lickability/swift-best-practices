# Optionals
### When do we use them?
Optionals are used when something is not known at the time of initialization, when an API can fail, or when the absence of a value provides additional meaning.

### Key Considerations
* Evaluate if you need an optional value, and avoid them if possible.
* Do not make `Bool`s optional.
	*  A tri-state `Bool` can be represented in a more structured way, such as an `enum` with three well-named `case`s.
* Avoid making `Array`s optional.
	* Only do this if it provides meaning beyond it just being empty.
* System APIs may require us to use optional values since they return optional values.
	* `URL`s created using `init?(string:)` are a common example.
* `weak` properties must be optional by definition because they can become `nil`.

### Dealing with Optionals
* Always handle unwrapping optionals safely.
* We prefer conditional binding (`if let`) over `flatMap`.
* If a function requires an optional value to have a value, we opt to bind with `guard` statements and return early.

### Reference Docs
* [The Basics — The Swift Programming Language](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html#ID330)
