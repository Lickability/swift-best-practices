# Testing
### Unit Testing
Unit testing is used to verify that a single unit of work is behaving as expected.

Example:
```swift
func testAddition() {
    let simpleAddition = "5 + 5"
    let negativeAddtion = "2 + -8"
    let doubleNegative = "-7 + -3"
        
    XCTAssertEqual(calculator.evaluate(simpleAddition), 10)
    XCTAssertEqual(calculator.evaluate(negativeAddtion), -6)
    XCTAssertEqual(calculator.evaluate(doubleNegative), -10)
}
```

### UI Testing
UI testing is used to ensure that the interface that is displayed to the user is the one that is expected to be displayed.

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
Integration testing is used to verify work between multiple entities.

### Contract Testing
Contract testing is used to verify that an API outside of your direct control is behaving as expected.

### Blackbox Testing
Blackbox testing is performed from the perspective of a user of the application, to verify that functionality the user expects to work does, in fact, work.
