# Architecture Diagram

![architecturediagramsmvcvm](https://user-images.githubusercontent.com/16432044/41361406-f5b5c55c-6efc-11e8-886c-bcd3adbd7294.png)

### Model
*Model*s are responsible for representing the data of the application.

### View
*View*s are responsible for rendering content and handling user interaction with that content.

### Controller
*Controller*s are the primary connection between *Model*s, *ViewModel*s, and *View*s.

### ViewModel
A *ViewModel* is a *View*’s model. It encapsulates the data needed to populate a particular kind of view and the presentation logic needed to transform the data into properties that can be rendered.
