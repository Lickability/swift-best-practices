# Optionals
### When do we use them?
Optionals are used when something is not known at the time of initialization 

### Key Considerations
* Evaluate if you need an optional value, and avoid them if possible
* Do not make `Bool`s optional 
	* A tristate `Bool` can be represented in a clearer way
* Avoid making `Array`s optional
	* Only do this if it provides meaning beyond it just being empty
* `URL`s made from `String`s are an exception 
	* They always return optional due to the API

### Dealing with Optionals
* Always handle unwrapping optionals safely
* We prefer continual binding (`if let`) over `flatMap`
* If a function requires an optional value to have a value, we opt to bind with `guard` statements

### Reference Docs
* [Optional - Swift Standard Library | Apple Developer Documentation](https://developer.apple.com/documentation/swift/optional/)