# Organization Within a File
### How do we organize files?
A file should only contain one major type declaration 

### Key Considerations
* Files are organized in the following order:
	* Default header created by Xcode
	* Import statements
	* Delegate protocols that are associated only with the major type declaration of the file
	* The major type declaration of the file
	* Inner type declarations
	* `IBOutlet`s
	* Properties
	* Initializers
	* Functions 

### How do we use MARKs?
Group and separate code using `MARK`s. The grouping order for each section of properties, initializers, and functions should be:
* Overridden declarations
* Declarations for protocol conformance
* Declarations being introduced in the major type of the file

### Key Considerations
* We only use `MARK`s when a file has overrides or conformances
* `MARK`s separate where things were original declared
* Always use `MARK: -` for grouping based on type
	* The text should be the type you are grouping by
* Use `MARK:` for other groupings inside of `MARK: -` 
	* e.g. `MARK: Helper Functions`
* When adding a `MARK` for an extension, ensure the `MARK` is inside the extension
