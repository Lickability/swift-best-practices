# Extensions
### How do we use extensions?
We use extensions to break up our code into logical groups, e.g. when separating code specifically for protocol conformance into its own extension.

### Key Considerations
* Declare protocol conformance to a type as an extension in the same file as that type declaration, if possible.
* Declare an extension that will only be used in a single file, in the same file that will use it, e.g. an `Array` extension constrained to a specific type of element used elsewhere in that file.
* Declare general-purpose extensions for other frameworks in their own files.
	* Name the file in the format `<Extended type>+<Word or phrase about the extension>`, e.g. `Array+MergeSort.swift`.

### Example
```swift
extension UITableView {
    
    /// Hides the empty cells at the bottom of the table view.
    func hideEmptyCellsFooter() {
        tableFooterView = UIView()
    }
}
```
