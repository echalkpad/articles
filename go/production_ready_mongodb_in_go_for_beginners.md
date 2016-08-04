# Production ready MongoDB in Go (for beginners)

[Original URL](https://medium.com/@matryer/production-ready-mongodb-in-go-for-beginners-ef6717a77219)

> In this tutorial, we will build an API in Go that interacts with MongoDB. The code won't be illustrative, and will actually form the basis for a real production ready project.The source code...

In this tutorial, we will build an API in Go that interacts with MongoDB. The code won't be illustrative, and will actually form the basis for a real production ready project.

> [The source code for this article can be found on GitHub](https://github.com/matryer/articles/blob/master/mongodb-in-go/commentsapp/main.go).

MongoDB is a document datastore. Rather than storing spreadsheet like tables (columns and rows), it's more like a set of folders (or buckets) into which JSON files (documents) can be put, then queried.

In this tutorial, we'll write a simple comments API in Go that:

- Connects to MongoDB

- Inserts some comments data

- Reads that comments data

Once you know how to do these basic things, you'll be able to build the foundation of any kind of data-backed app. Then, as you need more advanced things, you can build on that knowledge where appropriate.

> We'll use the [Adapter pattern described in "Writing middleware in #golang and how Go makes it so much fun"](https://medium.com/@matryer/writing-middleware-in-golang-and-how-go-makes-it-so-much-fun-4375c1246e81), so please make yourself familiar with that before proceeding (there's no need to watch the video -- just read the article).

Before we get started, be sure to:

- [Install MongoDB](https://docs.mongodb.org/manual/installation/)

- Get the [mgo package](https://labix.org/mgo) -- a 'driver' that will let us interact with MongoDB

Create a new folder called `commentsapp` -- this is where our Go code will live. Inside that, create a subfolder called `db` which is where we'll ask MongoDB to keep the data.

Start MongoDB by running the following in a command terminal after navigating to the `commentsapp` folder:

```go
mongod --dbpath=”./db”
```

You should see some output including something like the line: "waiting for connections on port 27017" -- then we konw we're good to go.

Before we can write the code that interacts with the data, we need to put together the architecture for our project.

Inside `commentsapp`create a main.go file and add the following boilerplate code:

```go
package main

func main() {

}
```

Following the [Adapter pattern](https://medium.com/@matryer/writing-middleware-in-golang-and-how-go-makes-it-so-much-fun-4375c1246e81), add this interface and helper method:

```go
type Adapter func(http.Handler) http.Handler

func Adapt(h http.Handler, adapters ...Adapter) http.Handler {
 for _, adapter := range adapters {
 h = adapter(h)
 }
 return h
}
```

This Adapter function type will allow us to write code that can be run before and/or after HTTP requests that come into our API. We're going to use it to create a session that connects to MongoDB before the request handler code runs, and then clean up that session once the handler code has finished.

> Be sure to import the appropriate packages as you go along, or check out the [goimports](https://godoc.org/golang.org/x/tools/cmd/goimports) tool to have your computer do it for you.

We're going to write a function that returns an Adapter that will setup (and teardown) the database session for our handlers and store it in a context (import [github.com/gorilla/context](https://github.com/gorilla/context)) ready for our handlers to get it later.

Add the following code to main.go:

```go
func withDB(db *mgo.Session) Adapter {

 // return the Adapter
 return func(h http.Handler) http.Handler {

 // the adapter (when called) should return a new handler
 return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {

 // copy the database session 
 dbsession := db.Copy()
 defer dbsession.Close() // clean up 

 // save it in the mux context
 context.Set(r, "database", dbsession)

 // pass execution to the original handler
 h.ServeHTTP(w, r)

 })
 }
}
```

This is as complicated as our code is going to get, I promise. Things are a little abstract, but essentially we can use our `withDB` function to get an Adapter, which we can then use to decorate our handlers in order to give them access (via the context) to a database session through which we can interact with MongoDB. We store the copy of the database session in the context with a key of "database" -- which we can use later to retrieve it.

> This code makes heavy use of closures in Go, whereby nearby variables (specifically `db` and `h`) are captured and made available to the other functions we define alongside them. This is a very simple way of storing state for our functions without having to add the complexity of structs or other data structures.

The key piece is the `db.Copy()` and `defer dbsession.Close()` code, which copies the `mgo.Session` and cleans up afterwards. Since we're calling the ServeHTTP method on the original handler (which we haven't written yet) we know that the request will be finished by the time execution passes back to our function, which can exit allowing the deferred `Close` call to run.

> If you're really confused by what's going on here -- you can always add some `log.Println` lines once we've got everything wired up to inspect it further.

Routing essentially refers to deciding which code to run in response to significant things in the request, such as the HTTP method. There are some great packages that do this for us, but for our case we can keep things very simple indeed.

Add the following method to main.go:

```go
func handle(w http.ResponseWriter, r *http.Request) {
 switch r.Method {
 case "GET":
 handleRead(w, r)
 case "POST":
 handleInsert(w, r)
 default:
 http.Error(w, “Not supported”, http.StatusMethodNotAllowed)
 }
}
```

This simple router will call either `handleRead` or `handleInsert` depending on the HTTP method.

Since this is the only handler our API will use, we can now modify our main function to connect to MongoDB, adapt the handle function we just added, and tell the http package to serve it on port :8080.

Modify the main function:

```go
func main() {

 // connect to the database
 db, err := mgo.Dial("localhost")
 if err != nil {
 log.Fatal("cannot dial mongo", err)
 }
 defer db.Close() // clean up when we’re done

 // Adapt our handle function using withDB
 h := Adapt(http.HandlerFunc(handle), withDB(db))

 // add the handler
 http.Handle("/comments", context.ClearHandler(h))

 // start the server
 if err := http.ListenAndServe(":8080", nil); err != nil {
 log.Fatal(err)
 }

}
```

The `mgo.Dial` function is how we connect to MongoDB. It returns an `mgo.Session` object, which is what we pass into `withDB`. Once we're connected, we defer the closing of the database connection, which will keep things nice and tidy.

We then use the Adapt helper function to adapt our `handle` function using the Adapter returned from our call to `withDB`. The `h` variable now contains an http.Handler that, when called, will copy the database session, and then call the `handle` function.

`http.Handle` allows us to tell the standard library to use our handler for all requests beginning with `/comments`.

`context.ClearHandler` is another Adapter provided by the context package that, after calling the handler, will clean up any memory used by the `context.Set` method.

Finally we `ListenAndServe` our code on port 8080.

Now that the structure for our program is in place, we are ready to write the two handlers that will actually interact with MongoDB to do the work for us.

Now we will write our `handleInsert` function that will take the data from an http.Request, and insert it into the database. We're going to decode the request body manually here, but you might want to consider some [patterns for decoding and validating input](https://medium.com/@matryer/patterns-for-decoding-and-validating-input-in-go-data-apis-152291ac7372). Once we've decided the comment data, we'll set the time and a give it a unique ID before inserting it into the database. Finally, we'll redirect the user to a path that uniquely describes the new comment.

First let's create a type that will hold our data:

```go
type comment struct {

 ID bson.ObjectId `json:"id" bson:"_id"`
 Author string `json:"author" bson:"author"`
 Text string `json:"text" bson:"text"`
 When time.Time `json:"when" bson:"when"`

}
```

Our `comment` struct will hold the data that represents a single comment. Most of the field types are obvious, except for the ID. `bson.ObjectId` is a special type provided by the mgo package (actually by the mgo/bson package) that represents a MongoDB identifier.

> [BSON](https://en.wikipedia.org/wiki/BSON) is a binary JSON format that MongoDB uses.

The tags (in backtics) after each field definition describes how we want our data to look in different contexts. So the ID in JSON will be in a field called "id", but in the database (when encoded using BSON) it will be known as "_id".

Add the following code to main.go:

```go
func handleInsert(w http.ResponseWriter, r *http.Request) {
 db := context.Get(r, “database”).(*mgo.Session)

 // decode the request body
 var c comment
 if err := json.NewDecoder(r.Body).Decode(&c); err != nil {
 http.Error(w, err.Error(), http.StatusBadRequest)
 return
 }

 // give the comment a unique ID and set the time
 c.ID = bson.NewObjectId()
 c.When = time.Now()

 // insert it into the database
 if err := db.DB(“commentsapp”).C(“comments”).Insert(&c); err != nil {
 http.Error(w, err.Error(), http.StatusBadRequest)
 return
 }

 // redirect to it
 http.Redirect(w, r, “/comments/”+c.ID.Hex(), http.StatusTemporaryRedirect)

}
```

The first thing we do is get, from the context, the database session copy that our adapter put in there for us. Notice that we don't have to worry about closing the database session -- our adapted handler does all that for us.

We then use the encoding/json package from the standard library to decode the request body into the `c` variable which has the type `comment`.

Assuming everything is fine, we then give the comment a unique ID (using the helpful bson.NewObjectId function) and set the When field to now.

Then comes the interesting piece where we actually perform the insertion.

The mgo package uses a fluent API; chained method calls on an object (each returning the object itself or a more specialised object) allow you to write code in a single line, usually in a way that is easy to read or even speak.

> I would argue that this isn't idiomatic Go -- and actually wouldn't recommend you write APIs like this.

We use the DB function to specify that we want to deal with the "commentsapp" database and the C method lets us specify that we want to deal with the "comments" collection. We are then free to call the `Insert` method, which will communicate to the database to actually perform the insertion. It may return an error, and if it does, we'll pass this back to the client.

Finally we redirect the client to a path that includes the unique ID that we just created for our comment.

Once we have data being inserted into the database, it seems sensible to want to read it back. Our handleRead function will get the database session copy in the same way our handleInsert did. It will then use the API to perform a Find query on the comments, sorting and limiting the output before reading it all into a slice of `comment` objects. We'll then respond manually using a json.Encoder type.

> If you're interested in patterns that assist with responding with data, check out [API responses in Go](https://medium.com/@matryer/api-responses-in-go-1ef8f7b74997).

Add the `handleRead` code to main.go:

```go
func handleRead(w http.ResponseWriter, r *http.Request) {

 db := context.Get(r, “database”).(*mgo.Session)

 // load the comments
 var comments []*comment
 if err := db.DB(“commentsapp”).C(“comments”).
 Find(nil).Sort(“-when”).Limit(100).All(&comments); err != nil {

 http.Error(w, err.Error(), http.StatusInternalServerError)
 return

 }

 // write it out
 if err := json.NewEncoder(w).Encode(comments); err != nil {
 http.Error(w, err.Error(), http.StatusInternalServerError)
 return
 }

}
```

Our `comments` variable is a slice of `comment` objects, which is where we'll store the data once we've read it from the database.

Where we previously called Insert, we now call Find to indicate that we want to perform a query. The nil argument we pass in means that we do not want to filter the comments in any way -- rather we want to select them all.

We then use Sort and Limit to indicate that we want our comments to be returned newest first, and up to a maximum of 100 comments.

> More advanced APIs will implement paging which will use the URL parameters to call Skip and Limit appropriately.

Finally, the call to the `All` method will initiate the operation, loading the data into the aforementioned comments slice.

All being well, we then use the json.Encoder to write the data out through the http.ResponseWriter.

While we could (and indeed should) use unit tests to assert that our API is functioning correctly, we will instead use simple curl commands to see it in action.

Since we have everything in one file, to execute our program we simply need to open a command line terminal and use `go run` to build and run it:

```go
go run main.go
```

> In reality, your APIs will grow beyond a single file -- so you should probably get used to using `go build` to build multi-file programs.

Once the program is running (provided you get no errors), we can then start to hit the endpoints that we have just created.

In another terminal window, run the following (single line) command:

```go
curl -i -X POST -d '{"text":"Hi there","author":"me"}' http://localhost:8080/comments
```

This will perform a POST operation to /comments, sending the specified JSON as the request body. Repeat this command a few times to insert many comments.

You will see output like this:

```go
HTTP/1.1 307 Temporary Redirect
Location: /comments/564b2a3f1f3adf0bc4000001
Date: Tue, 17 Nov 2015 13:23:11 GMT
Content-Length: 0
Content-Type: text/plain; charset=utf-8
```

The above response indicates that the new comment was created, and has a unique ID of 564b2a3f1f3adf0bc4000001.

> Content-Type being text/plain isn't right -- it should be application/json, but you can fix that yourself :)

Now we are ready to read the comments. This time, we'll use a web browser to see them. Open your favourite browser and access <http://localhost:8080/comments.>

All being well, you will see a JSON array containing the most recent 100 comments.

We have built the foundation of most web APIs backed by MongoDB. There is more work to do, but you have the basis on which to build that.

If you wish to stretch yourself, then see if you can solve the following challenges by tweaking or enhancing your code.

1. EASY: Allow the client to indicate how many comments to return using a URL parameter, like /comments?limit=10

2. EASY: Allow the client to specify the order of the comments as either ascending or descending. Perhaps with /comments?sort=asc|desc

3. MEDIUM: Implement another handler so that /comments/{commentID} actually works -- and returns only the single comment referred to

4. MEDIUM: Validate that incoming comments MUST have an author, or else return an error

5. HARD: Using more advanced routing (homegrown or by using an existing library) allow comments to be grouped by topic. GETting /comments/topic1 should give different comments to GETting /comments/topic2\. Topics should be inserted by POSTing to /comments/{topicname}
