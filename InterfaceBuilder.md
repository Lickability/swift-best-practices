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
