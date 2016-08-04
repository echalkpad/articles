# Routing RESTful controllers

[Original URL](https://medium.com/@matryer/golang-advent-calendar-day-five-routing-restful-controllers-edb74e7d4101)

> When building APIs in Go you usually have the same kinds of mappings for many types of data. In particular, if you're writing RESTful web services, then you'll probably want to map GET to read, POST...

When building APIs in Go you usually have the same kinds of mappings for many types of data. In particular, if you're writing RESTful web services, then you'll probably want to map GET to read, POST to create, PUT to update etc.

An interesting approach to this, is to introduce the idea of a controller into your project. A controller is just a collection of methods (on a struct) that do RESTful things for a particular type of data or resource.

A typical controller might look like this:

```go
type PresentsController struct{}

func (p PresentsController) ReadMany(w http.ResponseWriter, r *http.Request) {
 // TODO: handle read many request
}
```

The `ReadMany` method would be mapped to `GET /presents`.

We aren't mapping the methods to a pointer of the controller, because we don't need to share any state between our RESTful methods.

Once the number of controllers stacks up, our mapping code will quickly become a little ugly and unmanageable. We can instead, add some interfaces and a helper to do the mappings for us.

The interface for a `ReadMany` method could look like this:

```go
type RestManyReader interface {
 ReadMany(w http.ResponseWriter, r *http.Request)
}
```

Then, in a helper function, we can check to see if a controller implements that method and make the appropriate mapping:

```go
func Route(path string, controller interface{}, router Router) {
 if c, ok := controller.(RestManyReader); ok {
 router.Route(path, “GET”, http.HandlerFunc(c.ReadMany))
 }
}
```

For cases where an ID is required (such as reading an individual resource), we can standardise that too:

```go
type RestReader interface {
 Read(w http.ResponseWriter, r *http.Request)
}

func Route(path string, controller interface{}, router Router) {
 if c, ok := controller.(RestReader); ok {
 router.Route(path+”/{id}”, “GET”, http.HandlerFunc(c.Read))
 }
 if c, ok := controller.(RestManyReader); ok {
 router.Route(path, “GET”, http.HandlerFunc(c.ReadMany))
 }
}
```

It's perfectly fine for controllers to only implement either Read or ReadMany depending on what makes sense.

The whole set of interfaces might look like this:

```go
type RestReader interface {
 Read(w http.ResponseWriter, r *http.Request)
}

type RestManyReader interface {
 ReadMany(w http.ResponseWriter, r *http.Request)
}

type RestCreator interface {
 Create(w http.ResponseWriter, r *http.Request)
}

type RestUpdater interface {
 Update(w http.ResponseWriter, r *http.Request)
}

type RestReplacer interface {
 Replace(w http.ResponseWriter, r *http.Request)
}

type RestDeleter interface {
 Delete(w http.ResponseWriter, r *http.Request)
}
```

Having each method in its own interface is what allows controllers to only implement a subset.

And a more complete Route helper function might go something like this:

```go
func Route(path string, controller interface{}, router Router) {

 if c, ok := controller.(RestReader); ok {
 router.Route(path+”/{id}”, “GET”, http.HandlerFunc(c.Read))
 }

 if c, ok := controller.(RestManyReader); ok {
 router.Route(path, “GET”, http.HandlerFunc(c.ReadMany))
 }

 if c, ok := controller.(RestCreator); ok {
 router.Route(path, “POST”, http.HandlerFunc(c.Create))
 }

 if c, ok := controller.(RestDeleter); ok {
 router.Route(path+”/{id}”, “DELETE”, http.HandlerFunc(c.Delete))
 }

 if c, ok := controller.(RestReplacer); ok {
 router.Route(path+”/{id}”, “PUT”, http.HandlerFunc(c.Replace))
 }

 if c, ok := controller.(RestUpdater); ok {
 router.Route(path+”/{id}”, “PATH”, http.HandlerFunc(c.Update))
 }

 router.Route(path, “*”, http.NotFoundHandler())

}
```

You can tell the router to map all the remaining HTTP methods at that path to a 404 handler, to indicate that it isn't supported.

Other ideas include:

- Extracting the ID path parameter and changing the methods to take it as a parsed argument: `Delete(w http.ResponseWriter, r *http.Request, id string)`

- Count the number of mapping made, and at the end of Route make sure at least one was done. If not, panic -- because the developer probably made a mistake.

- If you're looking for a router that supports path parameters (like `{id}`) then check out [Gorilla's mux package](http://www.gorillatoolkit.org/pkg/mux).

- Add another method and interface pair that lets your controller map additional routes that go beyond a traditional RESTful design.

Check back tomorrow for another festive nugget. :)
