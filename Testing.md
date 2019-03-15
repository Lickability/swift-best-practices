# Testing

### Unit Testing
Unit testing is used to verify that a single unit of work is behaving as expected. We use [XCTest](https://developer.apple.com/documentation/xctest) to write unit tests.

Example:
```swift
func testAddition() {
    let simpleAddition = "5 + 5"
    let negativeAddition = "2 + -8"
    let doubleNegative = "-7 + -3"
        
    XCTAssertEqual(calculator.evaluate(simpleAddition), 10)
    XCTAssertEqual(calculator.evaluate(negativeAddition), -6)
    XCTAssertEqual(calculator.evaluate(doubleNegative), -10)
}
```

### UI Testing
UI testing is used to ensure that the interface that is displayed to the user is the one that is expected to be displayed. We use [Xcode UI Testing](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/09-ui_testing.html) to write UI tests.

Example:
```swift
func testEmptyState() {
    let emptyStateLabel = app.staticTexts["You don't have any games yet!"]
    XCTAssertTrue(emptyStateLabel.exists)
    
    let addAGameButton = app.buttons["Add a game"]
    XCTAssertTrue(addAGameButton.exists)
    
    addAGameButton.tap()
    waitForExpectations(timeout: 2.0, handler: nil)
    
    let createAGameLabel = app.staticTexts["Create a game"]
    XCTAssertTrue(createAGameLabel.exists)
}
```

### Integration Testing
Integration testing is used to verify work between multiple entities. Instead of mocking or stubbing dependencies of objects we create, we allow the actual implementations of dependencies to interact with the object that we're testing. This allows us to verify that our objects work together as we expect, and provides an additional layer of confidence on top of unit testing.

### Contract Testing
Contract testing is used to verify that an API outside of your direct control is behaving as expected. This is often used to verify that information being sent from a server, matches a specification that your application expects. These tests usually require network connections, as you're verifying that the actual environment you're communicating with is behaving properly.

### Blackbox Testing
Blackbox testing is performed from the perspective of a user of the application, to verify that functionality the user expects to work does, in fact, work. Always perform blackbox testing on an actual device, to match the environment a user would experience as close as possible. 
