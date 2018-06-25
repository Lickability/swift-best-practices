# Object Communication 
### Types of Object Communication

Delegation
One to one communication initiated by the owner of the delegate

Closures
Like delegates, they are a one to one communication where the owner of the property initiates the communication. It differs in that closures can capture state which makes implementation more convenient for the client.

Notifications
One too many communication where you have subscribers subscribe to a publisher. It is a one-way relationship.

### When Do We Use Each?
* We use **Delegation** to define a set of APIs for one to one communication between two instances
* **Closure**s offer additional flexibility in the number of objects involved in communication. While each closure will be a one to one relationship, each can have a different client. Closures also provide access to state, which can be more convenient to clients
* **Notification**s are used to broadcast to any subscriber that are interested in events that a publisher advertises. 
	* e.g. the `UIApplicationDidEnterForeground` notification is advertised so that any interested parties may subscribe