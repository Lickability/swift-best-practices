# Project Groups
### How do we use them?
Organize Xcode groups first by feature and then by architecture component, if needed.

### Example
The following example shows a project structure with **three** top level directories consisting of **one** feature directory and **two** application-related directories.

<img width="309" alt="49822191-6c3acc00-fd4a-11e8-8baf-f928a6c82f0a" src="https://user-images.githubusercontent.com/1043416/56973607-2e90e100-6b3b-11e9-91df-bddc734c6251.png">

### Key Considerations
* Every file should exist within an Xcode group, categorized first by feature, with exceptions listed below:
    * The `Application` group is a special case that is neither a feature nor an architecture component. This group should contain files associated with the application entry point, such as `Main.storyboard` and `AppDelegate.swift`. 
    * The `Resources` group houses supporting files to the main application that are more static in nature. This group should contain files like `Info.plist`, `Assets.xcassets`, and `LaunchScreen.storyboard`.
    
* The above `Checkout` group is a feature that has its files distributed into groups related to its architecture.

* When a feature group grows to contain more than five files consider adding sub-groups to categorize the files by architecture component, e.g. `Models`, `Views`, `Controllers`, etc.
