# Build End-to-End Cloud Apps using Swift with Kitura - Swift

[Original URL](https://developer.ibm.com/swift/2016/02/22/building-end-end-cloud-apps-using-swift-kitura/)

> Kitura is a lightweight web framework that allows you to easily build web services with complex routes. Much of its design was inspired by Express.js based on the success of its overall design in...

Kitura is a lightweight web framework that allows you to easily build web services with complex routes. Much of its design was inspired by [Express.js](http://expressjs.com) based on the success of its overall design in particular URL routing and pluggable middleware. Kitura takes these principles and adds the advantages of Swift, which include:

- Compiled native-code
- Type-safety
- Optionals
- Multi-programming based on Grand Central Dispatch

The combination of these features provides the foundation for an extendable and robust web framework.

This enables both mobile frontend and backend portions of an application to be written in the same language.

In addition, web developers who are used to other routing frameworks such Express, Sinatra, or Flask will be able to easily create or port web apps over to the Swift language.

For this tutorial, we follow the API created for web-based to-do lists [TodoMVC](http://todomvc.com). You can view many framework and language implementations of this same API on [TodoBackend](http://www.todobackend.com). We will implement our own in Swift. If you want to grab the source of the finished project:

[Kitura-TodoList](https://github.com/IBM-Swift/Kitura-TodoList)

Along the way, you will learn to:

1. Create GET, POST, PUT, DELETE routes with URL parameters
2. Parse JSON messages
3. Handle concurrency on shared resources
4. Create custom middleware that allows cross-origin requests

1) Set up your Mac or Linux environment to run [Kitura](https://github.com/IBM-Swift/Kitura)

Kitura applications support both Mac OS X and Linux. Because of the differences in the implementations of Foundation and libDispatch on each platform, the instructions are a bit different if running on Linux.

2) We will create a directory structure:

```
 +-- Package.swift
 +-- Sources
 | +-- TodoList
 | +-- main.swift
 | +-- Controllers
 | +-- App.swift
 | +-- Models
 | +-- TodoCollection.swift
 | +-- TodoCollectionArray.swift
 | +-- TodoItem.swift
```

The [Swift Package Manager](https://github.com/apple/swift-package-manager) expects the files to be laid out in a particular manner. Every Swift project must have a directory called Sources. For every subdirectory inside of Sources, the package manager will either build an executable file or a Swift module. For example, since our subdirectory is called TodoList with a main.swift file inside, the build will produce a TodoList executable. Our choice to put the TodoCollection and TodoItem inside of Models and App.swift inside of Controllers is only a matter of organizing our code, and not enforced by Kitura or the Swift Package Manager.

3) Create a Package.swift file:

```
import PackageDescription

let package = Package(
 name: "TodoList",
 dependencies: [
 .Package(url: "https://github.com/IBM-Swift/Kitura-router.git", majorVersion: 0),
 .Package(url: "https://github.com/IBM-Swift/HeliumLogger.git", versions: Version(0,0,0)..<Version(0,1,0))
 ]
)
```

4) Create a main.swift file:

In order to use Kitura in your application, import the core modules in the Kitura project. Kitura is organized as a collection of several modules, which makes it highly extendable. For instance, KituraSys provides general-purpose utilities that are used by all the modules in the Kitura project, KituraNet provides an implementation of an HTTP Server, and KituraRouter provides support for parsing and matching regular expressions.

```
import KituraRouter
import KituraNet
import KituraSys
```

In the main.swift file, we will set up the Kitura router as well as our collection of to-do items, then instruct the HTTPServer to listen to port 8090 for new connections.

```
///
/// The Kitura router
///
let router = Router()
```

We will store the to-do items in a shared resource DAO (Data Access Object) called 'todos'. The DAO will be a protocol for which more powerful databases could be supported in the future. In future blog posts, we will leverage Cloudant using this TodoCollection DAO.

```
///
/// Setup the database
///
let todos: TodoCollection = TodoCollectionArray()

///
/// Call a helper function to create routes in App.swift
///
setupRoutes( router, todos: todos )
```

To set up an HTTP server that listens to port 8090:

```
///
/// Listen to port 8090
///
let server = HttpServer.listen(8090, delegate: router)

Server.run()
```

5) Set up middleware to parse incoming JSON in the body of client requests:

```
router.use("/*", middleware: BodyParser())
```

BodyParser gives routers the ability to parse the JSON message describing the title of the item and its position in the list.

6) Create a route to return a JSON containing all the to-do list items:

Kitura supports multiple HTTP method types such GET, POST, PUT, and DELETE. We match incoming requests on the regular expression `/`, and when this match is made, the supplied closure is invoked. The arguments entering the closure are the request, response, and next element. The request contains the header information and the body. The response object is used to serialize data, JSON, or plaintext out to the client. Content types can be specified here as well. The method 'next' is a closure that should be invoked at the end of your handler block. By invoking 'next,' the router will proceed to process the next element that matches the regular expression. If you want to ensure that no other matching routes are handled after the current route, omit the next() call.

```
router.get("/") {
 request, response, next in

 let json = JSON(TodoCollectionArray.serialize(todos.getAll()))

 response.status(HttpStatusCode.OK).sendJson(json)

 next()
}
```

In this code segment, the library SwiftyJSON is used to convert the Dictionary representation of the to-do list to a JSON String. Calling sendJson() will send the String and set the response's header 'Content-Type' to 'application/json'.

7) Create a route to add a to-do list item:

When the client needs to add a to-do list item, they add a JSON string to the body of the request, which describes the title of the item, the order (position) in the list where it needs to be inserted, and whether the task has been completed or not. Therefore, in the handler we parse the JSON message and get each of the fields:

```
///
/// Add a to-do list item
///
router.post("/") {
 request, response, next in

 if let body = request.body {

 if let json = body.asJson() {

 let title = json["title"].stringValue
 let order = json["order"].intValue
 let completed = json["completed"].boolValue

 let newItem = todos.add(title, order: order, completed: completed)

 let result = JSON(newItem.serialize())

 response.status(HttpStatusCode.OK).sendJson(result)

 }
 } else {
 Log.warning("No body")
 response.status(HttpStatusCode.BAD_REQUEST)
 }

}
```

In the above code, request.body returns the body of the request as an Optional. If the Optional is nil, we return a Bad Request response. If the body exists, we attempt to parse the JSON; if successful, we use the DAO to write the item to the database and return the TodoItem of the newly created item back to the client.

8) Create a DAO protocol:

```
protocol TodoCollection {
 var count: Int { get }
 func clear()
 func getAll() -> [TodoItem]
 static func serialize(items: [TodoItem]) -> [JSONDictionary]
 func add(title: String, order: Int, completed: Bool) -> TodoItem
 func update(id: String, title: String?, order: Int?, completed: Bool?) -> TodoItem?
 func delete(id: String)
}
```

9) Create the TodoItem model:

For the model object, we need the ability to easily serialize the struct to a Swift Dictionary. We create a serialize function that handles this conversion for us:

```
struct TodoItem {
 var id: String = ""
 var order: Int = 0
 var title: String = ""
 var completed: Bool = false
 var url: String = ""

 func serialize() -> JSONDictionary {
 var result = JSONDictionary()
 result["id"] = id
 result["order"] = order
 result["title"] = title
 result["completed"] = completed
 result["url"] = url
 return result
 }
}
```

10) Implement the TodoCollection DAO:

For our implementation, we will not be using a database or in-memory database. We have libraries for integration with [Redis](https://github.com/IBM-Swift/Kitura-redis) and [CouchDB](https://github.com/IBM-Swift/Kitura-CouchDB).

Instead, we will store the items in a Dictionary in memory.

```
private var _collection = [String: TodoItem]()
```

Because Kitura is multithreaded, we need to be careful with writes to the shared object TodoCollection. We will handle writes synchronously using a Queue from KituraSys. You can create a queue using:

```
let writingQueue = Queue(type: .SERIAL, label: "Writing Queue")
```

On iOS, Queues are created using NSOperationQueue. Since open source Swift does not yet have an implementation for NSOperations and NSoperationQueue, we directly make calls to the libDispatch library for handling synchronous and asynchronous operations. At this point, Kitura's threading model is simple. There is one thread continually listening for new connections; when a connection has been established, a thread is spawned to handle the ClientRequest. In the future, we expect to move to thread pooling.

In the following code, we define a closure that modifies the collection and pass it to the synchronous queue:

```
func add(title: String, order: Int, completed: Bool) -> TodoItem {

 var original: String
 original = String(self.idCounter)

 let newItem = TodoItem(id: original,
 order: order,
 title: title,
 completed: false,
 url: self.baseURL + "/" + original
 )

 writingQueue.queueSync() {

 self.idCounter+=1

 self._collection[original] = newItem

 }

 Log.info("Added \(title)")

 return newItem

 }
```

After executing that code, we return the TodoItem of the post that was created so that it can be serialized as JSON and sent back to the client.

To delete objects that match an id:

```
func delete(id: String) {
 writingQueue.queueSync() {
 self._collection.removeValueForKey(id)
 }
}
```

The serialize function iterates through the List and converts the list of TodoItems to a list of Dictionaries:

```
static func serialize(items: [TodoItem]) -> [JSONDictionary] {
 return items.map { $0.serialize() }
}
```

## Enable cross-origin requests to your backend

We will be using an existing front-end JavaScript app that is hosted at <http://todobackend.com>. Since that app needs to make requests to your backend that's running on a different domain, we need to enable cross origin requests. To do this, we add to the response headers for every route in our application to return the header Access-Control-Allow-Origin: *. Routing middleware serves this purpose well since we can intercept a request, add these headers, then hand it over to a handler that's registered for a particular route and method. Note the next() invocation. This is important so that other routing elements can be matched next.

```
class AllRemoteOriginMiddleware: RouterMiddleware {
 func handle(request: RouterRequest, response: RouterResponse, next: () -> Void) {
 response.setHeader("Access-Control-Allow-Origin", value: "*")
 response.setHeader("Access-Control-Allow-Headers", value: "accept, content-type")
 response.setHeader("Access-Control-Allow-Methods", value: "GET,HEAD,POST,DELETE,OPTIONS,PUT")
 next()
 }
}

router.use("/*", middleware: AllRemoteOriginMiddleware())
```

We are currently working on getting a Build Script that will allow you to quickly deploy applications written with Kitura on Bluemix and other platforms.

11) Run your server:

```
 ./.build/debug/TodoList
```

After the executable is running and listening for connections on localhost:8090, you can test out the application by using the TodoBackend web page. This web page can use any backend that implements the API by supplying the address to your backend. View your to-do list at the following address:

<http://todobackend.com/client/index.html?http://localhost:8090>

When this is done, you should have a to-do list that looks like this:

[![todo-website](https://developer.ibm.com/swift/wp-content/uploads/sites/69/2016/02/todo-website-1024x780.png)](https://developer.ibm.com/swift/wp-content/uploads/sites/69/2016/02/todo-website.png)

## Next steps...

This concludes this tutorial. Keep in mind that Kitura is under active development. If you want to continue with this application, the next steps are to:

1. Add support for editing existing items
2. Add support for deleting items
