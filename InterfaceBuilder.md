# Interface Builder
### How do we use Interface Builder?
* We use Interface Builder in lieu of layout code to reduce the amount of code in views and view controllers

### Key Considerations
* Use `UIStackViews` instead of explicit constraints between siblings whenever possible, unless noticeable performance issues are encountered
* Each nib should have a single top-level item
   * Separate `UIView` subclasses designed in Interface Builder into their own nib files
* Use `IBDesignable`& `IBInspectable` for common design properties 
	* e.g. specifying a view’s corner radius or giving it a border
* When setting colors in Interface Builder use the ones defined in the Assets catalog
* Whenever possible, design and layout views in Interface Builder, and load them from their corresponding nibs from code. 
    * For `UITableViewCell` and `UICollectionViewCells`, register the cell with the `UITableView` or `UICollectionView` using the nib name. For other views, refer to Lickability/code-snippets#45. (Replace with actual example once implemented).

### Evaluation
* We are currently evaluating how we use Interface Builder and Storyboards as a whole
    * Refer to the [pro and con list](https://github.com/Lickability/swift-style-guide/issues/14)
* Right now we are not using Storyboards and instead favoring just using nibs
    * This is being tested in Scorecard
	
