# Organization Within a File
### How do we organize files?
A file should only contain one major type declaration. 

### Key Considerations
* Files are organized in the following order:
	* Default header created by Xcode
	* Import statements
	* Protocols that are directly related to the file i.e. delegate protocols
	* The major type declaration of the file
	* Inner type(s) declaration(s)
	* IBOutlets
	* Properties
	* Initializers
	* Functions 

### How do we use MARKs?
Separate MARKs by if it is an override, conformance, and then custom type

### Key Considerations
* We only use MARKs when a file has overrides or conformances
* MARKs separate where things were original declared
* Always use `MARK: -` for grouping based on type
	* The text should be the type you are grouping by
* Use `MARK:` for other groupings inside of `MARK: -` 
	* i.e.  `MARK: Helper Functions`
* When marking an extensions, we mark inside the extension
