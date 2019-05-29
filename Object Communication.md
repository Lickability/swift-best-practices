# Object Communication 
## Types of Object Communication

### Delegation

One-to-one communication initiated by the owner of the delegate.

#### Example
We use delegation to define a set of APIs for one-to-one communication between two instances. In this case, we can conform to a protocol that will notify an object that the application finished launching.

```swift
public protocol UIApplicationDelegate : NSObjectProtocol {
    optional func applicationDidFinishLaunching(_ application: UIApplication)
}
```

### Closures
Like delegates, they are a one-to-one communication where the owner of the property initiates the communication. Closures differ in that they can capture state, which makes implementation more convenient for the client.

#### Example
Closures offer additional flexibility in the number of objects involved in communication. While each closure will be a one-to-one relationship, each can have a different client. Closures also provide access to state, which can be more convenient to clients. In this case, we can define a closure that can be set, which will be called when the application finished launching.


```swift
var applicationDidFinishLaunching: ((UIApplication) -> Void)?
```

### Notifications 

One-to-many communication where subscribers subscribe to a publisher. It is a one-way relationship.

#### Example
Notifications are used to broadcast to any subscribers that are interested in events that a publisher advertises. In this case, a notification is defined that broadcasts that the application finished launching.

```swift
public class let didFinishLaunchingNotification: NSNotification.Name
```
