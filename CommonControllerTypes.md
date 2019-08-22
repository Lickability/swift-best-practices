# Common Controller Types
### Parser
Transforms structured data into model types.

```swift
/// Responsible for transformation information into `Game` objects.
struct GameParser {
    
    /// Parses a `Game` model from the specified JSON.
    ///
    /// - Parameter json: The JSON to parse.
    /// - Returns: Returns a parsed `Game` object.
    /// - Throws: Throws an error if the input is invalid.
    func parse(json: [String: Any]) throws -> Game {
        guard let name = json["name"] as? String else {
            throw ParsingError.invalidInput
        }
        
        let description = json["description"] as? String
        
        return Game(name: name, description: description)
    }
}
```

### Network Controller
Handles sending network requests and receiving response data.

```swift
/// Describes a type capable of performing network requests.
protocol Networker {
    
    /// Performs a network request, returning a `Result` type via completion handler on success or failure.
    ///
    /// - Parameters:
    ///   - request: The network request to perform.
    ///   - completionQueue: The queue on which the completion handler will be called.
    ///   - completion: The completion handler called upon success or failure.
    func performRequest(_ request: URLRequest, completionQueue: OperationQueue, completion: @escaping (Result<Any, Error>) -> Void)
}

/// A concrete implementation of `Networker` that wraps the `URLSession` APIs.
final class NetworkController: Networker {
    
    // MARK: - NetworkController
    
    private let urlSession: URLSession
    
    // MARK: - Networker
    
    func performRequest(_ request: URLRequest, completionQueue: OperationQueue, completion: @escaping (Result<Any, Error>) -> Void) {
        // ...
    }
    
    // MARK: - NetworkController
    
    /// Creates a new network controller.
    ///
    /// - Parameters:
    ///   - urlSession: The `URLSession` to use for performing requests.
    init(urlSession: URLSession = .shared) {
        self.urlSession = urlSession
    }    
}
```

### Persistence Controller
Reads and writes models to / from the data layer.

```swift
protocol Persister {
    
    /// Synchronously persists an object on disk.
    ///
    /// - Parameters:
    ///   - object: The object to persist.
    ///   - key: The key used to persist and later retrieve the object.
    func persist<T: NSCoding>(object: T, forKey key: String)
    
    /// Synchronously retrieves an object from disk stored under a given key.
    ///
    /// - Parameter key: The key used to access the persisted object.
    /// - Returns: The persisted object, or `nil` if the object wasn’t found under the specified key.
    func retrieveObject<T: NSCoding>(forKey key: String) -> T?
}

/// Manages interaction with objects stored on and retrieved from disk.
final class PersistenceController: Persister {
    
    // MARK: - PersistenceController
    
    private let cache: DiskCache<NSString, NSCoding>
    
    // MARK: - Persister
    
    func persist<T: NSCoding>(object: T, forKey key: String) {
        cache.setObject(object, forKey: key as NSString)
    }
    
    func retrieveObject<T: NSCoding>(forKey key: String) -> T? {
        return cache.object(forKey: key as NSString) as? T
    }
    
    // MARK: - PersistenceController
    
    /// Creates a new persistence controller.
    ///
    /// - Parameters:
    ///   - identifier: The unique identifier of the persistence controller. It is safe to create multiple instances that share the same unique identifier to access a single data set.
    init(identifier: String) {
        self.cache = DiskCache(rootDirectoryURL: rootDirectoryURL)
    }
}
```

### Translator
Handles complex conversions of models to view models.

```swift
/// Translator responsible for translating models into `GameCell.ViewModel` instances.
final class GameCellViewModelTranslator {
    
    /// Creates a new GameCellViewModelTranslator.
    init() {
        // ...
    }
    
    /// Translates a `Game` into a `GameCell.ViewModel`.
    ///
    /// - Parameter game: The `Game` to translate into a view model.
    /// - Returns: The translated view model.
    func translate(game: Game) -> GameCell.ViewModel {
        return GameCell.ViewModel(titleText: game.name, description: game.description)
    }
}
```

### View Controller
Manages a view hierarchy and UI logic for your app and coordinates with other controllers to keep it up to date.

```swift
/// A view controller that displays an example.
class ExampleViewController: UIViewController {

    // MARK: - UIViewController
    
    override var extendedLayoutIncludesOpaqueBars: Bool {
        set {
        
        }
        get {
            return true
        }
    }
    
    // MARK: - UITraitEnvironment
    
    override var traitCollection: UITraitCollection {
        return UITraitCollection(userInterfaceIdiom: .phone)
    }
    
    // MARK: - ExampleViewController

    @IBOutlet private weak var exampleView: UIView!
    
    private let urlSession: URLSession
    
    // MARK: - UIViewController
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        view.backgroundColor = .green
    }
    
    // MARK: - NSCoding
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    // MARK: - UITraitEnvironment
    
    override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
        view.backgroundColor = .red
    }
    
    
    // MARK: - ExampleViewController
    
    /// Initializes the `ExampleViewController` with the required parameters.
    ///
    /// - Parameter urlSession: The url session the controller should use.
    init(urlSession: URLSession) {
        self.urlSession = urlSession

        super.init(nibName: nil, bundle: nil)
    }
    
    private func retrieveExample() {
        // ...
    }
}

extension ExampleViewController: UINavigationControllerDelegate {
    
    // MARK: - UINavigationControllerDelegate
    
    func navigationController(_ navigationController: UINavigationController, didShow viewController: UIViewController, animated: Bool) {
        // ...
    }
}
```

### Updater
Makes use of network and persistence controllers to fetch and persist data from the network. 

```swift
/// Retrieves and persists game data from the network.
final class GameUpdater {
    
    private let networker: Networker
    private let persister: Persister
    
    /// Creates a new `GameUpdater`
    ///
    /// - Parameters:
    ///   - networker: The network controller responsible for making network requests.
    ///   - persister: The persistence controller responsible for storing updated objects to disk.
    init(networker: Networker = NetworkController(), persister: Persister = PersistenceController(identifier: "default")) {
        self.networker = networker
        self.persister = persister
    }
    
    /// Attempts to update a game using the specified URL.
    ///
    /// - Parameters:
    ///   - url: The API URL from which to retrieve the game.
    ///   - completion: The completion handler that delivers the result. Called on the main queue.
    func updateGame(from url: URL, completion: @escaping (Result<Game, Error>) -> Void) {
        let request = URLRequest(url: url)
        
        networkController.performRequest(request, completionQueue: .main) { (result: Result<Game, Error>) in
            switch result {
            case .success(game):
                persister.persist(object: game, forKey: game.identifier)
            case .failure(error):
                // ...
            }
            
            completion(result)
        }
    }
}
```

### DataSource
Manages collections of data to power UI-related collections.

```swift
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
    
    // MARK: - TableViewDataSource
    
    /// A closure that is called when a cell needs to be configured.
    var cellConfiguration: CellConfiguration?
    
    private var collection: CollectionType
    
    // MARK: - UITableViewDataSource
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return collection.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        guard collection.indices.contains(indexPath.row) else {
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
    
    // MARK: - TableViewDataSource
    
    /// Creates a new `TableViewDataSource<Element>`.
    ///
    /// - Parameters:
    ///   - collection: The collection of elements the data source will manage.
    init(collection: CollectionType) {
        self.collection = collection
    }
    
    /// Accesses the element at the specified position.
    ///
    /// - Parameter indexPath: The position of the element to access.
    /// - Returns: The element at the specified position.
    func element(at indexPath: IndexPath) -> CollectionType.Element? {
        // ...
    }
}
```
