# Testing
### Unit Testing
Unit testing is used to verify that a single unit of work is behaving as expected

```swift
func testAddition() {
    let simpleAddition = "5 + 5"
    let negativeAddtion = "2 + -8"
    let doubleNegative = "-7 + -3"
    let notSupportedString = "10 + 6 + 4 + pi"
        
    XCTAssertEqual(calculator.evaluate(simpleAddition), 10)
    XCTAssertEqual(calculator.evaluate(negativeAddtion), -6)
    XCTAssertEqual(calculator.evaluate(doubleNegative), -10)
}
```

### UI Testing
UI testing is used to ensure that the user interface that is displayed to the user is the one that is expected to be displayed

### Integration Testing
Integration testing is used to verify work between multiple entities

### Contract Testing
Contract testing is used to verify that API outside of your direct control is behaving as expected

### Blackbox Testing
Blackbox testing is performed from the perspective of a user of the application, to verify that functionality the user expects to work, works