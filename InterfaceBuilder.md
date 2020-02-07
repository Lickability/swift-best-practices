# Interface Builder
### How do we use Interface Builder?
* We use Interface Builder in lieu of layout code to reduce the amount of code in views and view controllers

### Key Considerations
* Use `UIStackView`s instead of explicit constraints between siblings whenever possible, unless there are noticeable performance issues.
* Each nib should have a single top level item.
    * Separate `UIView` subclasses designed in Interface Builder into their own nib files.
* Use `IBDesignable` and `IBInspectable` for common design properties, e.g. specifying a view’s corner radius or giving it a border.
* When setting colors in Interface Builder, use the ones defined in asset catalogs to ensure that all use of common colors is updated from a single source.
* Whenever possible, design and layout views in Interface Builder, and load them from their corresponding nibs from code. 
    * For `UITableViewCell` and `UICollectionViewCell`s, register the cell with the `UITableView` or `UICollectionView` using the nib name.
    * For other views, you can refer to the following code:
    
    ```swift
    extension UIView {
        static func defaultNibName() -> String {
            return String(describing: self)
        }

        static func instantiateViewFromNib<T: UIView>(_ nibName: String, bundle: Bundle? = nil) -> T? {
            return UINib(nibName: nibName, bundle: bundle).instantiate(withOwner: nil, options: nil).first as? T
        }

        static func instantiateViewFromNib(bundle: Bundle? = nil) -> Self? {
            return instantiateViewFromNib(defaultNibName(), bundle: bundle)
        }
    }
    ```	
* When defining a width or height constraint in relation to the opposite dimension, use the “Aspect Ratio” option in Interface Builder (found in the “Add New Constraints” menu, or in the popup that appears when dragging a width or height constraint).
    * The Multiplier value for the constraint should always be defined as a ratio (e.g. `4:3`), and not a decimal value (e.g. `1.333`).
    * The constraint’s first item should represent the dimension that effectively determines the other. For example, if an image view is full-width, and the height is defined as half of the width, the first item should be the image view’s width, and the second item should be its height, with a ratio of `2:1`:
    <img width="739" alt="aspect-ratio-constraint" src="https://user-images.githubusercontent.com/7883805/74057707-8013a980-49b2-11ea-8abc-5f31d011b5ab.png" align="middle">
    
    * If neither dimension takes precedence in determining the other when using an aspect ratio constraint (e.g. a `60x60` view), specify the width as the constraint’s first item and the height as its second as a consistent fallback.
