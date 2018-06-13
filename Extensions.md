# Extensions
### How do we use extensions?
We use extensions to breakup our code into logical groups i. e. protocol conformance 

### Key Considerations
* Declare protocol conformance to a type as an extension in the same file as that type declaration, if possible
* Declare extensions only used in one file, not meant to be general purpose, in the file that it is used
	* i. e. an array extension that constrained to a specific type of element used elsewhere in the file
* Declare general-purpose extensions for other frameworks in their own files
	* Name the file, extension type + word or phrase about the extension  i. e. `Array+MergeSort.swift`
