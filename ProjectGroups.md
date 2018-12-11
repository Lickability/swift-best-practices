# Project Groups
### When do we use them?
Organize Xcode groups by feature first and then by architecture component if needed.

### Key Considerations
* The example below has **3** top level directories consisting of **1** feature directory and **2** application related directories.
    * The `Application` group is a special case that is neither a feature or an architecture component. This group should contain files like the `Main.storyboard` and `AppDelegate.swift`. 
    * The `Resources` group houses supporting files to the main application itself.
    * The `Checkout` group is a feature that has its files distributed into groups related to its architecture.

### Example
<img width="336" alt="screen shot 2018-12-11 at 12 58 56 pm" src="https://user-images.githubusercontent.com/16762986/49822191-6c3acc00-fd4a-11e8-8baf-f928a6c82f0a.png">
