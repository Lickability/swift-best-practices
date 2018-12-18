# Project Groups
### How do we use them?
Organize Xcode groups by feature first and then by architecture component if needed.

### Key Considerations
* Every file should exist within an Xcode group, categorized first by feature, with exceptions listed below:
    * The `Application` group is a special case that is neither a feature nor an architecture component. This group should contain files associated with the application entry point, such as `Main.storyboard` and `AppDelegate.swift`. 
    * The `Resources` group houses supporting files to the main application that are more static in nature. This group should contain files like `Info.plist`, `Assets.xcassets`, and `LaunchScreen.storyboard`.
    * The `Checkout` group is a feature that has its files distributed into groups related to its architecture.

* When a feature group grows to contain more than a few files (five or greater) consider adding sub-groups to categorize the files by architecture component, e.g. `Models`, `Views`, `Controllers`, etc.

### Example
The following example shows a project structure with **3** top level directories consisting of **1** feature directory and **2** application related directories.
<img width="336" alt="screen shot 2018-12-11 at 12 58 56 pm" src="https://user-images.githubusercontent.com/16762986/49822191-6c3acc00-fd4a-11e8-8baf-f928a6c82f0a.png">
