# Interface Builder
### How do we use Interface Builder?
* We use Interface Builder in lieu of layout code to reduce the amount of code in views and view controllers

### Key Considerations
* Use `UIStackView`s instead of explicit constraints between siblings whenever possible, unless noticeable performance issues are encountered
* Each nib should have a single top-level item
    * Separate `UIView` subclasses designed in Interface Builder into their own nib files
* Use `IBDesignable` and `IBInspectable` for common design properties 
    * e.g. specifying a view’s corner radius or giving it a border
* When setting colors in Interface Builder, use the ones defined in asset catalogs to ensure that use of common colors are updated from a single source
* Whenever possible, design and layout views in Interface Builder, and load them from their corresponding nibs from code 
    * For `UITableViewCell` and `UICollectionViewCells`, register the cell with the `UITableView` or `UICollectionView` using the nib name
    * For other views, refer to [#45](https://github.com/Lickability/code-snippets/issues/45)
### Evaluation
* We are currently evaluating how we use Interface Builder and Storyboards as a whole
    * Refer to the [pro and con list](https://github.com/Lickability/swift-style-guide/issues/14)
* Right now we are not using Storyboards and instead favoring just using nibs
    * This is being tested in Scorecard
	