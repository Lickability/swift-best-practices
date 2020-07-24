# Assets
### How do we use them?
We use asset catalogs to organize our application’s assets.

### Key Considerations
* Organize assets using folders inside asset catalogs.
* All colors and images that are known at compile time should exist in asset catalogs, unless they are system colors, in which case they do not need to be duplicated in the asset catalog. 
* Asset catalog colors should come from a project’s design system for project-wide consistency, ease of small tweaks, and to assist in supporting color-related features like dark mode. Avoid adding colors for specific use cases without verifying it with the project’s designers and updating the design system.
* Whenever possible, set images from asset catalogs in Interface Builder. 
* Always set UI component colors in code, never in Interface Builder. It is too easy to fall out of sync with asset catalog and design system colors when they’re set in Interface Builder.
* When accessing asset catalog colors and images in code, make use of code generation solutions such as [SwiftGen](https://github.com/SwiftGen/SwiftGen#asset-catalog) to provide non-optional constants for assets.
* Avoid use of image and color literals, as they tend to be difficult to edit in Xcode, and potentially difficult to see depending on your source editor colors.
