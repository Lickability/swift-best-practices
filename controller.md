# Controller
### Responsibility
*Controller*s are the primary connection between *Model*s, *ViewModel*s, and *View*s.

### Key Considerations
* *Controller*s come in many different varieties each with a single responsibility. 
* *Controller*s will often utilize other controllers to fulfill their responsibility.

### Interaction Diagram
![architecturediagramscvvm](https://user-images.githubusercontent.com/16432044/41422678-6d3caaa8-6fc7-11e8-9ee6-bd48d3343711.png)

### Common Controller Types
[Examples](https://github.com/Lickability/swift-style-guide/blob/master/CommonControllerTypes.md)

### Code Example

```swift
/// Fetches and updates product data on disk.
final class ProductUpdater {
    
    /// Custom errors that can occur when using `ProductUpdater`.
    enum Error: Swift.Error {
        
        /// The updater was deallocated, but must be kept around to complete the operation.
        case objectDeallocated
    }
    
    private let networkController: NetworkController
    private let productPersistenceController: PersistenceController
    
    /// Creates a new `ProductUpdater`
    ///
    /// - Parameters:
    ///   - networkController: The network controller responsible for making network requests.
    ///   - productPersistenceController: The persistence controller responsible for storing and retrieving products.
    init(networkController: NetworkController = NetworkController(), productPersistenceController: PersistenceController = PersistenceController(identifier: .productCache, rootDirectoryURL: .persistedDataRootDirectory)) {
        self.networkController = networkController
        self.productPersistenceController = productPersistenceController
    }
    
    /// Updates the specified product from the API.
    ///
    /// - Parameters:
    ///   - url: The API URL from which to retrieve the product.
    ///   - completion: The completion handler that delivers the result. Called on the main queue.
    /// - Returns: Returns the `URLSessionTask` that is handling the request in order to cancel or suspend as necessary. Discardable.
    @discardableResult
    func updateProduct(from url: URL, completion: @escaping (Result<Product>) -> Void) -> URLSessionTask {
        return networkController.performRequest(url, completionQueue: OperationQueue.main) { [weak self] networkResponse in
            guard let strongSelf = self else {
                completion(.failure(Error.objectDeallocated))
                return
            }
            
            switch networkResponse {
            case let .success(newProduct):
                strongSelf.productPersistenceController.persist(object: newProduct, forKey: url.absoluteString, completion: completion)
            case let .failure(error):
                completion(.failure(error))
            }
        }
    }
    
    /// Retrieves the product from disk for the specified URL, if it exists.
    ///
    /// - Parameter url: The URL of the product on the network, used as a key to retrieve the existing product.
    /// - Returns: The product from disk, if it exists.
    func product(from url: URL) -> Product? {
        return productPersistenceController.retrieveObject(forKey: url.absoluteString)
    }
}
```
