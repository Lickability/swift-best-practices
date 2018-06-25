# Optionals
### When do we use them?
Optionals are used when something is not known at the time of initialization 

### Key Considerations
* Evaluate if you need an optional value, and avoid them if possible
* Do not make `Bool`s optional 
	*  A tri-state `Bool` can be represented in a more structured way, such as an `enum` with three well-named `case`s
* Avoid making `Array`s optional
	* Only do this if it provides meaning beyond it just being empty
* `URL`s created using `init?(string:)` are a common exception 
	* They always return optional due to the API

### Dealing with Optionals
* Always handle unwrapping optionals safely
* We prefer conditional binding (`if let`) over `flatMap`
* If a function requires an optional value to have a value, we opt to bind with `guard` statements and return early

### Reference Docs
* [The Basics — The Swift Programming Language](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html#ID330)
