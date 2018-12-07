# Common Controller Types
### Parser
Transforms structured data into model types

```swift
/// Responsible for parsing a CocoaPods acknowledgements plist file.
open class AcknowParser {

    /**
     The root dictionary from the loaded plist file.
     */
    let rootDictionary: [String: AnyObject]

    /**
     Initializes the `AcknowParser` instance with a plist path.

     - parameter plistPath: The path to the acknowledgements plist file.

     - returns: The new `AcknowParser` instance.
     */
    public init(plistPath: String) {
        let root = NSDictionary(contentsOfFile: plistPath)
        if let root = root, root is [String: AnyObject] {
            self.rootDictionary = root as! [String: AnyObject]
        }
        else {
            self.rootDictionary = Dictionary()
        }
    }

    /**
     Parses the header and footer values.

     - return: a tuple with the header and footer values.
     */
    open func parseHeaderAndFooter() -> (header: String?, footer: String?) {
        let preferenceSpecifiers: AnyObject? = self.rootDictionary["PreferenceSpecifiers"]

        if let preferenceSpecifiers = preferenceSpecifiers, preferenceSpecifiers is [AnyObject] {
            let preferenceSpecifiersArray = preferenceSpecifiers as! [AnyObject]
            if let headerItem = preferenceSpecifiersArray.first,
                let footerItem = preferenceSpecifiersArray.last,
                let headerText = headerItem["FooterText"], headerItem is [String: String],
                let footerText = footerItem["FooterText"], footerItem is [String: String] {
                    return (headerText as! String?, footerText as! String?)
            }
        }

        return (nil, nil)
    }

    /**
     Parses the array of acknowledgements.

     - return: an array of `Acknow` instances.
     */
    open func parseAcknowledgements() -> [Acknow] {
        let preferenceSpecifiers: AnyObject? = self.rootDictionary["PreferenceSpecifiers"]

        if let preferenceSpecifiers = preferenceSpecifiers, preferenceSpecifiers is [AnyObject] {
            let preferenceSpecifiersArray = preferenceSpecifiers as! [AnyObject]

            // Remove the header and footer
            let ackPreferenceSpecifiers = preferenceSpecifiersArray.filter({ (object: AnyObject) -> Bool in
                if let firstObject = preferenceSpecifiersArray.first,
                    let lastObject = preferenceSpecifiersArray.last {
                        return (object.isEqual(firstObject) == false && object.isEqual(lastObject) == false)
                }
                return true
            })

            let acknowledgements = ackPreferenceSpecifiers.map({
                (preferenceSpecifier: AnyObject) -> Acknow in
                if let title = preferenceSpecifier["Title"] as! String?,
                    let text = preferenceSpecifier["FooterText"] as! String? {
                        return Acknow(title: title, text: text, license: preferenceSpecifier["License"] as? String)
                }
                else {
                    return Acknow(title: "", text: "", license: nil)
                }
            })

            return acknowledgements
        }

        return []
    }
}
```

### Network Controller
Handles sending of network requests and receiving response data

```swift
import Decodable

/// A type that provides additional information about the response from a network request, including the result of that request.
struct NetworkResponse<ResultType: Decodable> {
    
    /// The `URLResponse` from the network request.
    let response: URLResponse?
    
    /// The raw, unmodified data from the network request.
    let data: Data?
    
    /// The result of the network request, including the deserialized type if successful.
    let result: Result<ResultType>
}

/// Describes a type capable of performing network requests.
protocol NetworkControlling {
    
    /// Performs a network request, returning a `Result` type via completion handler on success or failure.
    ///
    /// - Parameters:
    ///   - request: The network request to perform.
    ///   - deserializer: The deserializer that converts response data into the result type.
    ///   - completionQueue: The queue on which the completion handler will be called.
    ///   - completion: The completion handler called upon success or failure.
    func performRequest<ResultType: Decodable, DeserializerType: NetworkResponseDeserializing>(_ request: NetworkRequest, deserializer: DeserializerType, completionQueue: OperationQueue, completion: @escaping (Result<ResultType>) -> Void) where DeserializerType.DecodedType == ResultType
    
    /// Performs a network request, returning a full `NetworkResponse` type via completion handler on success or failure.
    ///
    /// - Parameters:
    ///   - request: The network request to perform.
    ///   - deserializer: The deserializer that converts response data into the result type.
    ///   - completionQueue: The queue on which the completion handler will be called.
    ///   - completion: The completion handler called upon success or failure.
    /// - Returns: Returns the `URLSessionTask` that is handling the request in order to cancel or suspend as necessary. Discardable.
    @discardableResult
    func performFullResponseRequest<ResultType: Decodable, DeserializerType: NetworkResponseDeserializing>(_ request: NetworkRequest, deserializer: DeserializerType, completionQueue: OperationQueue, completion: @escaping (NetworkResponse<ResultType>) -> Void) -> URLSessionTask where DeserializerType.DecodedType == ResultType

    /// Performs a network request, deserializing the response as JSON, returning a `Result` type via completion handler on success or failure.
    ///
    /// - Parameters:
    ///   - request: The network request to perform.
    ///   - completionQueue: The queue on which the completion handler will be called.
    ///   - completion: The completion handler called upon success or failure.
    func performRequest<ResultType: Decodable>(_ request: NetworkRequest, completionQueue: OperationQueue, completion: @escaping (Result<ResultType>) -> Void)
    
    /// Performs a network request, deserializing the response as JSON, returning a full `NetworkResponse` type via completion handler on success or failure.
    ///
    /// - Parameters:
    ///   - request: The network request to perform.
    ///   - completionQueue: The queue on which the completion handler will be called.
    ///   - completion: The completion handler called upon success or failure.
    /// - Returns: Returns the `URLSessionTask` that is handling the request in order to cancel or suspend as necessary. Discardable.
    @discardableResult
    func performFullResponseRequest<ResultType: Decodable>(_ request: NetworkRequest, completionQueue: OperationQueue, completion: @escaping (NetworkResponse<ResultType>) -> Void) -> URLSessionTask
}

/// A concrete implementation of `NetworkControlling` that wraps the `URLSession` APIs.
final class NetworkController: NetworkControlling {
    
    private let urlSession: URLSession
    private let networkRequestSerializer: NetworkRequestSerializing
    
    /// Creates a new network controller.
    ///
    /// - Parameters:
    ///   - urlSession: The `URLSession` to use for performing requests.
    ///   - networkRequestSerializer: The network request serializer used to finalize the requests that are made.
    init(urlSession: URLSession = .shared, networkRequestSerializer: NetworkRequestSerializing = NetworkRequestSerializer()) {
        self.urlSession = urlSession
        self.networkRequestSerializer = networkRequestSerializer
    }
    
    // MARK: - NetworkControlling
    
    @discardableResult
    func performFullResponseRequest<ResultType: Decodable>(_ request: NetworkRequest, completionQueue: OperationQueue, completion: @escaping (NetworkResponse<ResultType>) -> Void) -> URLSessionTask {
        let deserializer = JSONDeserializer<ResultType>()
        return performFullResponseRequest(request, deserializer: deserializer, completionQueue: completionQueue, completion: completion)
    }
    
    func performRequest<ResultType: Decodable>(_ request: NetworkRequest, completionQueue: OperationQueue, completion: @escaping (Result<ResultType>) -> Void) where ResultType: Decodable {
        let deserializer = JSONDeserializer<ResultType>()
        performRequest(request, deserializer: deserializer, completionQueue: completionQueue, completion: completion)
    }
    
    @discardableResult
    func performFullResponseRequest<ResultType: Decodable, DeserializerType: NetworkResponseDeserializing>(_ request: NetworkRequest, deserializer: DeserializerType, completionQueue: OperationQueue, completion: @escaping (NetworkResponse<ResultType>) -> Void) -> URLSessionTask where DeserializerType: NetworkResponseDeserializing, DeserializerType.DecodedType == ResultType {
        let serializedRequest = networkRequestSerializer.serializedRequest(from: request)
        
        let dataTask = urlSession.dataTask(with: serializedRequest) { (data, response, error) in
            let result: Result<ResultType> = self.result(with: deserializer, from: data, urlResponse: response, networkError: error)
            
            completionQueue.addOperation {
                let response = NetworkResponse(response: response, data: data, result: result)
                completion(response)
            }
        }
        
        dataTask.resume()
        
        return dataTask
    }
    
    func performRequest<ResultType: Decodable, DeserializerType: NetworkResponseDeserializing>(_ request: NetworkRequest, deserializer: DeserializerType, completionQueue: OperationQueue, completion: @escaping (Result<ResultType>) -> Void) where DeserializerType.DecodedType == ResultType  {
        performFullResponseRequest(request, deserializer: deserializer, completionQueue: completionQueue) { (response: NetworkResponse<ResultType>) in
            completion(response.result)
        }
    }
    
    // MARK: - NetworkController
    
    private func result<ResultType: Decodable, DeserializerType: NetworkResponseDeserializing>(with deserializer: DeserializerType, from responseData: Data?, urlResponse: URLResponse?, networkError: Swift.Error?) -> Result<ResultType> where DeserializerType.DecodedType == ResultType {
        let result: Result<ResultType>
        
        if let error = networkError {
            result = Result.failure(error)
        } else if let data = responseData {
            do {
                let resultObject = try deserializer.deserialize(data: data)
                
                result = Result.success(resultObject)
            } catch {
                result = Result.failure(error)
            }
        } else {
            result = Result.failure(Error.emptyResponseData)
        }
        
        return result
    }
}

/// Extends `NetworkController` to add a custom `Error` type.
extension NetworkController {
    
    /// Custom errors that can occur when using `NetworkController`.
    enum Error: Swift.Error {
        
        /// There was no response data.
        case emptyResponseData
    }
}
```

### Persistence Controller
Reads and writes models from / to the data layer

```swift
/// Manages interaction with objects stored on and retrieved from disk.
final class PersistenceController {
    
    /// Identifiers for commonly used persistence controllers.
    enum Identifier: String {
        
        /// General cache for storing objects from network responses that don’t require any special storage rules.
        case generalCache = "generalCache"
        
        /// Saved articles container.
        case bookmarks = "bookmarks"
        
        /// Saved issues container.
        case issues = "issues"
        
        /// Container for images and other assets used in issues.
        case issueAssets = "issueAssets"
        
        /// Container for CSS styles used in articles.
        case articleStyles = "articleStyles"
        
        /// Container for images and other assets used in articles.
        case articleAssets = "articleAssets"
        
        /// Container for the current logged-in user of the app.
        case user = "user"
    }
    
    /// Errors potentially encountered when using `PersistenceController`
    enum Error: Swift.Error {
        
        /// The object failed to be persisted.
        ///
        /// - Parameter object: The object that failed to be persisted.
        case objectFailedToPersist(object: NSCoding)
        
        /// The object for the requested key was not found.
        case objectNotFound
    }
    
    private let cache: PINCache
    
    /// The total byte count of the persisted objects on disk.
    var diskByteCount: UInt {
        return cache.diskByteCount
    }
    
    /// Creates a new persistence controller.
    ///
    /// - Parameters:
    ///   - identifier: The unique identifier of the persistence controller. It is safe to create multiple instances that share the same unique identifier to access a single data set.
    ///   - rootDirectoryURL: The url for the root directory to store the persisted data on disk. If `nil`, the data is stored in the "Caches" directory. Defaults to `nil`.
    init(identifier: String, rootDirectoryURL: URL? = nil) {
        if let rootDirectoryURL = rootDirectoryURL {
            cache = PINCache(name: identifier, rootPath: rootDirectoryURL.path)
        } else {
            cache = PINCache(name: identifier)
        }
    }
    
    /// Creates a new persistence controller.
    ///
    /// - Parameters:
    ///   - identifier: The unique identifier of the persistence controller. It is safe to create multiple instances that share the same unique identifier to access a single data set.
    ///   - rootDirectoryURL: The url for the root directory to store the persisted data on disk. If `nil`, the data is stored in the "Caches" directory. Defaults to `nil`.
    convenience init(identifier: Identifier, rootDirectoryURL: URL? = nil) {
        self.init(identifier: identifier.rawValue, rootDirectoryURL: rootDirectoryURL)
    }
    
    /// Asynchronously persists an object on disk.
    ///
    /// - Parameters:
    ///   - object: The object to persist.
    ///   - key: The key used to persist and later retrieve the object.
    ///   - completionQueue: The queue on which the completion handler is called. Defaults to `.main`.
    ///   - completion: The completion handler called when persistence completes successfully or fails.
    func persist<T: NSCoding>(object: T, forKey key: String, completionQueue: OperationQueue = .main, completion: @escaping (Result<T>) -> Void) {
        cache.setObject(object, forKey: key) { _, _, persistedObject in
            let result: Result<T>
            if let persistedObject = persistedObject as? T {
                result = .success(persistedObject)
            } else {
                result = .failure(Error.objectFailedToPersist(object: object))
            }
            
            completionQueue.addOperation {
                completion(result)
            }
        }
    }
    
    /// Synchronously persists an object on disk.
    ///
    /// - Parameters:
    ///   - object: The object to persist.
    ///   - key: The key used to persist and later retrieve the object.
    func persist<T: NSCoding>(object: T, forKey key: String) {
        cache.setObject(object, forKey: key)
    }
    
    /// Asynchronously retrieves an object from disk stored under a given key.
    ///
    /// - Parameters:
    ///   - key: The key used to access the persisted object.
    ///   - completionQueue: The queue on which the completion handler is called. Defaults to `.main`.
    ///   - completion: The completion handler called when the object is retrieved, or no object is found.
    func retrieveObject<T: NSCoding>(forKey key: String, completionQueue: OperationQueue = .main, completion: @escaping (Result<T>) -> Void) {
        cache.object(forKey: key) { _, _, object in
            let result: Result<T>
            if let object = object as? T {
                result = .success(object)
            } else {
                result = .failure(Error.objectNotFound)
            }
            
            completionQueue.addOperation {
                completion(result)
            }
        }
    }
    
    /// Asynchronously retrieves a list of object from disk stored under the given keys.
    ///
    /// - Parameters:
    ///   - keys: The keys used to access the persisted objects.
    ///   - completionQueue: The queue on which the completion handler is called. Defaults to `.main`.
    ///   - completion: The completion handler called when the objects are retrieved, or if objects are found.
    func retrieveObjects<T: NSCoding>(forKeys keys: [String], completionQueue: OperationQueue = .main, completion: @escaping (Result<[T]>) -> Void) {
        DispatchQueue(label: "PersistenceControllerQueue").async {
            var objects = [T]()
            let operationQueue = OperationQueue()
            
            let arrayDispatchQueue = DispatchQueue(label: "ArrayDispatchQueue")
            
            let operations = keys.flatMap { key in
                BlockOperation {
                    guard let object: T = self.retrieveObject(forKey: key) else { return }
                    
                    arrayDispatchQueue.sync {
                        objects.append(object)
                    }
                }
            }
            
            operationQueue.addOperations(operations, waitUntilFinished: true)
            
            let result: Result<[T]>
            if objects.isEmpty {
                result = .failure(Error.objectNotFound)
            } else {
                result = .success(objects)
            }
            
            completionQueue.addOperation {
                completion(result)
            }
        }
    }
    
    /// Synchronously retrieves an object from disk stored under a given key.
    ///
    /// - Parameter key: The key used to access the persisted object.
    /// - Returns: The persisted object, or `nil` if the object wasn’t found under the specified key.
    func retrieveObject<T: NSCoding>(forKey key: String) -> T? {
        return cache.object(forKey: key) as? T
    }
    
    /// Synchronously retrieves objects from disk stored under the given keys.
    ///
    /// - Parameter keys: The keys used to access the persisted objects.
    /// - Returns: The persisted objects, or `nil` if no objects were found under the specified keys.
    func retrieveObjects<T: NSCoding>(forKeys keys: [String]) -> [T]? {
        let objects: [T] = keys.flatMap { retrieveObject(forKey: $0) }
        
        return objects.isEmpty ? nil : objects
    }
    
    /// Asynchronously removes an object stored under a given key from disk.
    ///
    /// - Parameters:
    ///   - key: The key used to access the persisted object.
    ///   - completionQueue: The queue on which the completion handler is called. Defaults to `.main`.
    ///   - completion: The completion handler called once the object has been removed.
    func removeObject(forKey key: String, completionQueue: OperationQueue = .main, completion: @escaping () -> Void) {
        cache.removeObject(forKey: key) { _, _, _ in
            completionQueue.addOperation {
                completion()
            }
        }
    }
    
    /// Retrieves the byte count on disk of an object at the specified key.
    ///
    /// - Parameter key: The key of object for which to retrieve the byte count.
    /// - Returns: The byte count on disk of the object at the specified key, or nil if there is no object at that key or no file infromation could be retrieved.
    func diskByteCount(forObjectAtKey key: String) -> Int? {
        guard let fileURL = cache.diskCache.fileURL(forKey: key) else { return nil }
        
        let values = try? fileURL.resourceValues(forKeys: [.totalFileAllocatedSizeKey])
        return values?.totalFileAllocatedSize
    }
}
```

### Translator
Handles complex conversions of models to view models

```swift
/// Translator responsible for translating `Team` models into `ScoreListTableViewCell.ViewModel` instances.
final class TeamToScoreListTableViewCellViewModelTranslator {
    private let imageManager: LocalImageManager?
    
    /// Creates a new TeamToScoreListTableViewCellViewModelTranslator
    ///
    /// - Parameter imageManager: An optional instance of `LocalImageManager` to get images from. 
    init(imageManager: LocalImageManager?) {
        self.imageManager = imageManager
    }
    
    /// Translates a `Team` into a `ScoreListTableViewCell.ViewModel`.
    ///
    /// - Parameter teamScore: The team score to translate into a view model.
    /// - Returns: The translated view model.
    func translate(teamScore: TeamScore, isEditable: Bool) -> ScoreListTableViewCell.ViewModel {
        let playerInformation: [ScoreListTableViewCell.ViewModel.PlayerInformation] = teamScore.team.playerData.map { player in
            let image = imageManager?.cachedImage(forKey: player.identifier.rawValue)
            
            return ScoreListTableViewCell.ViewModel.PlayerInformation(identifier: player.identifier.rawValue, avatarImage: image, color: player.color?.color, name: player.displayName)
        }
        
        return ScoreListTableViewCell.ViewModel(teamScoreIdentifier: teamScore.identifier.rawValue, playerInformation: playerInformation, score: teamScore.score, isEditable: isEditable)
    }
}
```

### View Controller
Manages a view hierarchy and UI logic for your app and coordinates with other controllers to keep it up to date

```swift
/// A `UIViewController` subclass that represents an empty state with an action button.
final class EmptyStateViewController: UIViewController {
    @IBOutlet private weak var emptyStateLabel: UILabel!
    @IBOutlet private weak var emptyStateButton: UIButton!
    
    /// A struct used to contain the information need to configure the view of the empty state.
    struct ViewModel {
        
        /// The text to display on screen.
        let message: String
        
        /// The text for the action button.
        let actionText: String
        
        /// A closure that handles responding to a user's tap.
        let actionHandler: () -> Void
    }
    
    private let viewModel: ViewModel
    
    /// Creates a EmptyStateViewController.
    ///
    /// - Parameter viewModel: A struct used to configure the view of the controller.
    init(viewModel: ViewModel) {
        self.viewModel = viewModel
        super.init(nibName: nil, bundle: nil)
    }
    
    @available(*, unavailable, message: "init is unavailable, use init(viewModel:)")
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        configureView()
    }
    
    private func configureView() {
        emptyStateLabel.text = viewModel.message
        emptyStateButton.setTitle(viewModel.actionText, for: .normal)
    }
    
    @IBAction private func emptyStateButtonTapped(_ sender: Any) {
        viewModel.actionHandler()
    }
}
```

### Updater
Makes use of network and persistence controllers to fetch and persist data from the network 

```swift
/// Fetches and updates issue data from the network.
final class MagazineIssueUpdater {
    
    private let networkController: NetworkControlling
    
    /// Creates a new `MagazineIssueUpdater`.
    ///
    /// - Parameter networkController: The network controller responsible for making network requests.
    init(networkController: NetworkControlling = NetworkController()) {
        self.networkController = networkController
    }
    
    /// Updates the specified issue from the API.
    ///
    /// - Parameters:
    ///   - url: The API URL from which to retrieve the issue.
    ///   - completion: The completion handler that delivers the result. Called on the main queue.
    /// - Returns: Returns the `URLSessionTask` that is handling the request in order to cancel or suspend as necessary. Discardable.
    @discardableResult
    func updateIssue(from url: URL, completion: @escaping (Result<Issue>) -> Void) -> URLSessionTask {
        let request = URLRequest(url: url)

        return networkController.performFullResponseRequest(request, completionQueue: .main) { (networkResponse: NetworkResponse<Issue>) in
            completion(networkResponse.result)
        }
    }
    
    /// Loads an issue's asset from the network at the specified URL and returns an asset response.
    ///
    /// - Parameters:
    ///   - url: The URL of the asset to load.
    ///   - issueIdentifier: The identifier of the issue that contains the asset.
    ///   - completion: The completion handler that delivers the result. Called on the main queue.
    /// - Returns: Returns the `URLSessionTask` that is handling the request in order to cancel or suspend as necessary. Discardable.
    @discardableResult
    func loadAsset(from url: URL, inIssueWithIdentifier issueIdentifier: Int64, completion: @escaping (Result<SavedAssetResponse>) -> Void) -> URLSessionTask {
        let request = URLRequest(url: url)
        
        return networkController.performFullResponseRequest(request, deserializer: DataDeserializer(), completionQueue: .main) { response in
            switch response.result {
            case let .success(assetData):
                guard let urlResponse = response.response else {
                    assertionFailure()
                    completion(.failure(NetworkController.Error.emptyResponseData))
                    return
                }
                
                let assetResponse = SavedAssetResponse(response: urlResponse, data: assetData, associatedIdentifier: issueIdentifier)
                completion(.success(assetResponse))
                
            case let .failure(error):
                completion(.failure(error))
            }
        }
    }
}
```

### DataSource
Manages collections of data to power UI related collections

```swift
/// A delegate that communicates information about the data source to its delegate.
protocol DataSourceDelegate: class {
    
    /// Called when the data source's collection changes it state.
    ///
    /// - Parameters:
    ///   - dataSource: A `UITableViewDataSource` or subclass that is managing a collection.
    ///   - collectionState: The `DataSourceCollectionState` the collection is changed too.
    func dataSource(_ dataSource: UITableViewDataSource, collectionStateDidChangeTo collectionState: DataSourceCollectionState)
}

/// Represents the states a data source's collections can be in.
enum DataSourceCollectionState {
    
    /// The data source's collection is empty.
    case empty
    
    /// The data source's collection is not empty.
    case nonEmpty
}

/// A data source for a table view interface that displays a generic collection of homogenous elements.
final class TableViewDataSource<CollectionType: Collection>: NSObject, UITableViewDataSource where CollectionType.Index == Int {
    
    ///  A closure that is called when a cell needs to be configured.
    ///
    /// - Parameters:
    ///   - element: The model object to configure the cell with.
    ///   - indexPath: The index path of the cell being configured.
    ///   - tableView: The table view to configure the cell for.
    /// - Returns: A `UITableViewCell` or subclass configured.
    typealias CellConfiguration = (_ element: CollectionType.Element, _ indexPath: IndexPath, _ tableView: UITableView) -> UITableViewCell
    
    private weak var delegate: DataSourceDelegate?

    /// A closure that is called when a cell needs to be configured.
    var cellConfiguration: CellConfiguration?
    private var collection: CollectionType {
        didSet {
            if oldValue.isEmpty && !collection.isEmpty {
                delegate?.dataSource(self, collectionStateDidChangeTo: .nonEmpty)
            } else if !oldValue.isEmpty && collection.isEmpty {
                delegate?.dataSource(self, collectionStateDidChangeTo: .empty)
            }
        }
    }
    
    /// Whether the data source's collection state.
    var collectionState: DataSourceCollectionState {
        return collection.isEmpty ? .empty : .nonEmpty
    }
    
    /// Creates a new `DataSource<Element>`.
    ///
    /// - Parameters:
    ///   - collection: The collection of elements the data source will manage.
    ///   - delegate: An optional `DataSourceDelegate`.
    init(collection: CollectionType, delegate: DataSourceDelegate?) {
        self.collection = collection
        self.delegate = delegate
    }
    
    /// Accesses the element at the specified position.
    ///
    /// - Parameter indexPath: The position of the element to access.
    /// - Returns: The element at the specified position.
    func element(at indexPath: IndexPath) -> CollectionType.Element {
        return collection[indexPath.row]
    }
    
    // MARK: - UITableViewDataSource
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return collection.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        guard indexPath.row < collection.count, indexPath.row >= 0 else {
            logAssertionFailure(message: "The index path \(indexPath) provided is out of range. This is unexpected.`")
            return UITableViewCell()
        }
        
        guard let cellConfiguration = cellConfiguration else {
            logAssertionFailure(message: "It is expected that the cell configuration closure has been set and is not nil.")
            return UITableViewCell()
        }
        
        let element = collection[indexPath.row]
        
        return cellConfiguration(element, indexPath, tableView)
    }
    
}

extension TableViewDataSource: Collection {
    
    subscript(position: Int) -> CollectionType.Element {
        return collection[position]
    }
    
    var startIndex: Int {
        return collection.startIndex
    }
    
    var endIndex: Int {
        return collection.endIndex
    }
    
    func index(after i: Int) -> Int {
        return collection.index(after: i)
    }
    
}

extension TableViewDataSource: BidirectionalCollection where CollectionType: BidirectionalCollection {
    
    func index(before i: Int) -> Int {
        return collection.index(before: i)
    }
    
}

extension TableViewDataSource: RandomAccessCollection where CollectionType: RandomAccessCollection {}

extension TableViewDataSource where CollectionType: RangeReplaceableCollection {
    
    /// Adds the provided element to the collection.
    ///
    /// - Parameter element: The object that is to be added to collection.
    /// - Returns: The index path corresponding to the location where the object was inserted into the collection.
    func add(element: CollectionType.Element) -> IndexPath {
        collection.append(element)
        return IndexPath(item: collection.count - 1, section: 0)
    }

    /// Removes the element for at the index path.
    ///
    /// - Parameter indexPath: The index path for the element to remove.
    func removeElement(at indexPath: IndexPath) {
        guard indexPath.row < collection.count, indexPath.row >= 0 else {
            assertionFailure("The index path\(indexPath) provided is out of range. This is unexpected.`")
            return
        }
        
        collection.remove(at: indexPath.row)
    }
    
}
```
