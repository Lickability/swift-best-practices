# Interface Builder
### How do we use Interface Builder?
* We use Interface Builder in lieu of layout code to reduce the amount of code in views and view controllers

### Key Considerations
* Use `UIStackView`s instead of explicit constraints between siblings whenever possible, unless there are noticeable performance issues.
* Each nib should have a single top level item.
    * Separate `UIView` subclasses designed in Interface Builder into their own nib files.
* Use `IBInspectable` to allow for customization of common design properties in Interface Builder, e.g. to specify a view’s corner radius or give it a border. Use `IBDesginable` only to render custom drawing in a view. Avoid using `IBDesignable` to customize outlet properties, as accessing outlets in `prepareForInterfaceBuilder()` is [not currently supported](http://www.openradar.appspot.com/radar?id=4952346488471552).
* Do not set colors in Interface Builder. It is too easy to fall out of sync with asset catalog and design system colors when they’re set in Interface Builder. Instead, exclusively use color constants provided in code as described in [Assets](https://github.com/Lickability/swift-best-practices/blob/master/Assets.md).
* Do not set fonts on text components in Interface Builder. Similar to colors, adherence to a design system can become more cumbersome when fonts are specified in both code and Interface Builder. Set all fonts in code.
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
    
    * If neither dimension takes precedence in determining the other dimension when using an aspect ratio constraint (e.g. a `60x60` view), use the width as the constraint’s first item and the height as its second item.
