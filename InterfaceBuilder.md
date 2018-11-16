# Interface Builder
### How do we use Interface Builder?
* We use Interface Builder to separate layout code into their own files and handle auto layout 

### Key Considerations
* Leverage stack views whenever possible
* Each nib should have a single view
	* Separate multiple views into separate nibs
* Use  `IBDesignable`& `IBInspectable` for common design properties 
	* e.g. making a view a circle or giving it a border
* Use asset catalog to manage images and colors used 
* Load views from nibs

### Evaluation
* We are currently evaluating how we use Interface Builder and Storyboards as a whole
    * Refer to the pro and con list in [here](https://github.com/Lickability/swift-style-guide/issues/14)
* Right now we are not using Storyboards and instead favoring just using nibs 
        * This is being tested in Scorecard
	
