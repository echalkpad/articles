# AngularJS + MongoDB: Goodbye middle tier? (part 1 of 2)

[Original URL](http://www.codebulb.ch/2015/05/angularjs-mongodb-goodbye-middle-tier-part-1.html)

> With the advent of client side JavaScript frameworks, it seems like the middle tier has become Java's last resort. But as a Java developer myself, I have to admit there are compelling...

<span>
  <em>With the advent of client side JavaScript frameworks, it seems like the middle tier has become Java’s last resort. But as a Java developer myself, I have to admit there are compelling alternatives to a full-blown enterprise server. For this blog post, I want to push this thought to the limit and try out whether we really need the middle tier at all…</em>
</span>

<br>
[](http://www.codebulb.ch/2015/05/angularjs-mongodb-goodbye-middle-tier-part-1.html#tool)

## The tool set

For the build environment:

- I use the [**Yeoman**](http://yeoman.io/) generator for project scaffolding which is built on top of [**Node.js**](https://nodejs.org/) + **npm** and which sets up the JavaScript build system based on **Grunt** and dependency management based on **Bower**.
- During development, the application is loaded into Grunt's static server which provides live reload on source change. It runs on default port 9000.
- I use **NetBeans** as file editor / IDE.

For the application stack:<br>
I use [**MongoDB**](http://www.mongodb.org/) 3.1.3 as the database. It runs on default port 27017.

- I use [**Mongo Express**](https://github.com/andzdroid/mongo-express) 0.21.0 as a simple web-based UI frontend for MongoDB. It runs on default port 8081.

I use [**RESTHeart**](http://restheart.org/) 0.10.2 as the MongoDB REST frontend. It runs on default port 8080.

- RESTHeart includes the **HAL browser** as a simple web-based UI frontend for RESTHeart. It is accessible through RESTHeart's port.
- For convenience, I use the [**httpie**](https://github.com/jakubroztocil/httpie) 0.9.2 tool parallel to the HAL browser to test RESTHeart queries through RESTful HTTP requests (it's basically a more user-friendly version of curl).

I use [**Restangular**](https://github.com/mgonto/restangular) 1.5.1 as an AngularJS abstraction layer to do RESTful HTTP requests which offers a far more sophisticated although more concise API than AngularJS's built-in `$http` and `$resource`. Finally, the GUI is developed with [**AngularJS**](https://angularjs.org/) 1.3.0.

### A RESTful stack

Thus, the tech stack looks like this:<br>
[![](http://1.bp.blogspot.com/-piOLQjfTXrY/VWm1i7g_hwI/AAAAAAAAANI/cFVMkI1_HuA/s1600/angularjs-and-mongodb-components.png)](http://1.bp.blogspot.com/-piOLQjfTXrY/VWm1i7g_hwI/AAAAAAAAANI/cFVMkI1_HuA/s1600/angularjs-and-mongodb-components.png)

You can clearly see that something is lacking here – there's no such thing as a middle tier. As MongoDB has been extended with a REST interface through RESTHeart and AngularJS has been extended with a REST interface through Restangular, these two tiers can communicate directly with each other.

The beauty of a RESTful API which is taken advantage of here is that

- REST is CRUD. As the example application will do CRUD only, this is a perfect fit. A "middle tier" would apparently not add any value.
- REST is de-facto-standardized. Even without any formal service descriptor, the API can be used really intuitively and concisely. There's no need for further abstraction; a "middle tier" would just be an empty delegate.

### Technology choice

Please skip this section unless you're interested in why I chose the technologies mentioned for my stack.

- I chose **MongoDB** because it's considered [by far the most widespread "NoSQL" databases](http://db-engines.com/en/ranking) at the moment. I didn't go for a "proper" SQL database because the application's requirements should be covered by a simple document store. The fact that MongoDB doesn't provide a RESTful HTTP interface out of the box (in contrast to e.g. CouchDB) doesn't really matter; I wouldn't choose a DB based on the interfaces it provides, as long as it's extensible.
- After choosing MongoDB though I needed a RESTful interface for that. [Of all available options](http://docs.mongodb.org/ecosystem/tools/http-interfaces/), **RESTHeart** seemed the most mature one.
- **AngularJS**, as well, was chosen based on its popularity as well as on its functionality.
- With some experience from plain AngularJS HTTP handling, I wanted to try a more high-level API, as provided by **Restangular**. I will explain throughout the text why I quickly became very happy with my choice.

[](http://www.codebulb.ch/2015/05/angularjs-mongodb-goodbye-middle-tier-part-1.html#example)

## The example application

Based on above tech stack, I created a simple web application with CRUD functionality for these two business entities:<br>
[![](http://1.bp.blogspot.com/-Vi_7w5jowV4/VVofHBlygwI/AAAAAAAAAMo/FgGKIj6ub-U/s1600/JSF%2BREST%2BPrd%2BModels.png)](http://1.bp.blogspot.com/-Vi_7w5jowV4/VVofHBlygwI/AAAAAAAAAMo/FgGKIj6ub-U/s1600/JSF%2BREST%2BPrd%2BModels.png)

Each entity is uniquely identified by its id which is auto-generated on persist.

A customer can have one or more payments, and one payment is associated with exactly one customer. A customer of employment type _unemployed_ must not have a company name.

There will be a master view (list of all entities) and a detail view (edit page for current entity) for the customer entity. The payments master view will be integrated in the detail view of the parent customer entity which provides editing options for its payments though popup windows.

This is actually a recreation of [the very same example application I created just two weeks ago with the server-side JSF stack](http://www.codebulb.ch/2015/05/restful-jsf-with-post-redirect-get-part-1.html) which, for me, added to the fun of creating a middle-tier-less application. (In fact, I even reused the HTML markup generated by the JSF application for this one.)<br>
[](http://www.codebulb.ch/2015/05/angularjs-mongodb-goodbye-middle-tier-part-1.html#setup)

## Setup

Setting up the whole stack really is just a matter of following the tutorials provided with each tool.

However, setting up Restangular for use with RESTHeart deserves some more explanation.

### Restangular + RESTHeart

RESTHeart's HAL+JSON document format doesn't quite match Restangular's default expectations, but luckily, Restangular provides a clever API to tweak its configuration.

Having Restangular included into the AngularJS application (script included, dependencies declared), one can configure Restangular like so:<br>
angular.config(function (RestangularProvider) { RestangularProvider.setBaseUrl('<http://localhost:8080/restangularOnMongoDb/>');

```
 // we are mapping the id of each element to the _id field.
 RestangularProvider.setRestangularFields({
 id: "_id.$oid",
 });

 RestangularProvider.addResponseInterceptor(function (data, operation, what, url, response, deferred) {
 var extractedData;
 // look for getList operations
 if (operation === "getList") {
 // the actual entities are in the _embedded section
 extractedData = data._embedded['rh:doc'];
 } else {
 extractedData = data;
 }
 return extractedData;
 });

 RestangularProvider.addFullRequestInterceptor(function (data, operation, what, url) {
 if (operation !== "getList") {
 if (typeof data._etag !== 'undefined') {
 return {headers: {"If-Match": data._etag.$oid}};
 }
 if (typeof data._id.$oid === 'undefined') {
 data._id = null;
 }
 }
 return {};
 });
});
```

- `RestangularProvider` is the dependency-injected Restangular config service.
- First of all, we set the base URL to the server / port of RESTHeart and point to the database which contains all document collections.
- We have to tell Restangular that the "id" of a JSON document is _not_ stored in the property named `id`, but in `_id.$oid`, as defined by MongoDB.
- We have to tell Restangular that when querying for a list of documents, the actual payload (the list) is stored in the `_embedded.rh:doc` property.
- Finally, we have to tell Restangular where to find the ETag property of a JSON document which must be included in a "write" request in the "If-Match" HTTP header. [ETag is an HTTP concept](http://en.wikipedia.org/wiki/HTTP_ETag) which is used by RESTHeart to implicitly provide _optimistic locking_. Similar to the "id" problem described above, we must tell Restangular that this information is not stored in `etag`, but in `_etag.$oid`.
