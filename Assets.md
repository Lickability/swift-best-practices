# Assets
### How do we use them?
We use asset catalogs to organize our application’s assets.

### Key Considerations
* Organize assets using folders inside asset catalogs.
* Whenever possible, use images and colors from asset catalogs using Interface Builder.
* When assets cannot be used from Interface Builder (i.e. the assets to use from the catalog are not known at compile time), make use of code generation solutions such as [SwiftGen](https://github.com/SwiftGen/SwiftGen#asset-catalog) to provide non-optional constants for assets.
* Avoid use of image and color literals, as they tend to be difficult to edit in Xcode, and potentially difficult to see depending on your source editor colors.
