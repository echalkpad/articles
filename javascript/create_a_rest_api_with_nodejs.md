# Create a REST API with Node.js

[Original URL](http://bigspaceship.github.io/blog/2014/05/14/how-to-create-a-rest-api-with-node-dot-js/)

> For a pitch, we prototyped one of our concepts with a Natural Language search feature. I quickly implemented a REST style GET call on search submit with Node.js via major article skimming. I still...

For a pitch, we prototyped one of our concepts with a [Natural Language](https://github.com/NaturalNode/natural) search feature. I quickly implemented a REST style GET call on search submit with Node.js via major [article](http://coenraets.org/blog/2012/10/creating-a-rest-api-using-node-js-express-and-mongodb/) skimming. I still didn't have a clear mental map of how Node.js worked with a REST API, so I broke it down into a [presentation](http://brittneykernan.github.io/noderest-presentation/) for the BSS Tech team. You can follow along, or skim.

## PHP vs Node.js REST Components

If you've come from PHP, your brain may already hold a diagram of what a REST API looks like in that language. Let's make one for Node.js:

### PHP

- Apache - HTTP server
- .php controllers - Controls what your app does with data
- Content-Type JSON Headers - Sends your data back readable
- .htaccess - Map URLs to controller action methods for pretty URLs
- MySQLi - Interfaces with Data
- MySQL - Stores data

### Node.js

- Node.js - HTTP Server
- JavaScript modules - Controllers for what your app does with data
- Express - Framework to easily build for web with Node.js
- Routes - Map URLs to actions for pretty URLs
- Mongoose.js - Interfaces with data
- Mongo - Stores data

## Start a Node Server

[Install Node](http://nodejs.org/) first. Make a directory to play in, ex: _noderest_, and put _server.js_ in the root.

### server.js

```
1
2
3
4
5
6``` | ```
var http = require('http');
http.createServer(function (req, res) {
 res.writeHead(200, {'Content-Type': 'text/plain'});
 res.end('Hello New York\n');
}).listen(3001);
console.log('Server running at [http://localhost:3001/'](http://localhost:3001/&#39););```
------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### What's going on here

HTTP is the module we'll use to read the incoming Requests' HTTP action - GET, POST, PUT or DELETE - and Headers. It will also create a server for us, listening for Requests at the available port of your choice (here 3001). Pass `createServer()` a callback function that takes two arguments `req` and `res`. `req` for Request and `res` for Response. Add a `console.dir()` or two to print to Terminal what these two variables hold. Response is an object you can use to reply back to the Request. Forget to return something with Response and your Request will simply time out. Here any URL path we type at <http://localhost:3001/> will print back _Hello New York_ with a _OK_ header as plain text content.

### Try it

In Terminal:

```
1
2``` | ```
$ cd noderest/
$ node server.js```
---------- | --------------------------------------

Terminal should print `Server running at http://127.0.0.1:3001/` back at you. So easy (as long as you didn't have anything else running in that port). Open that URL. You should see `Hello New York`.

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/browser-node.gif "Node Server ")

### Add Express

Why add Express? Same reason you use jQuery, makes things faster and easier. What the hell is it? I read the [home page](http://expressjs.com/) maybe 5 times and used it in 3 prototypes before my brain mapped it.

To add Express as a dependency to your project, we'll need to first [install NPM](http://blog.npmjs.org/post/85484771375/how-to-install-npm) (it should have installed with Node actually, [if not](http://blog.npmjs.org/post/85484771375/how-to-install-npm)). NPM will read our _[package.json](https://www.npmjs.org/doc/json.html)_ file saved to the root of our project directory, and then download and install Express for us.

### package.json

```
1
2
3
4
5
6
7
8
9``` | ```
{
 "name": "noderest",
 "description": "Demo REST API with Node",
 "version": "0.0.1",
 "private": true,
 "dependencies": {
 "express": "3.x"
 }
}```
------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------

### Install Express in Terminal

Watch NPM in Terminal fetch the files. Your directory now holds a new folder called _node_modules_, with the _express_ folder inside. Let's use Express.

### Use Express

Like the HTTP module you required in your first version of _server.js_, you'll require Express. Open _server.js_, clear it out. Add:

```
1
2
3
4
5
6
7``` | ```
var express = require('express');
var app = express();
app.get('/', function(req, res) {
 res.send('Hello Seattle\n');
});
app.listen(3001);
console.log('Listening on port 3001...');```
-------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### What's changed

Nothing! Not really. Our server is actually doing the same thing, though. Listening to HTTP requests sent to locally to port 3001 and returning updated text _Hello Seattle_. Only difference is we now have Express' framework to work with - a slightly cleaner and faster way to handle requests and set up our server. The `app` variable holds an instance of the Express application object, which has many useful methods.

### Run Express

Changes to Node.js server files require a full server restart in order to be run the updated version.

In Terminal, use `CTRL-C` to stop your running Node server. Restart with `node server.js`.

You'll see Terminal respond with _Listening on port 3001_. And _Hello Seattle_ at your localhost in the browser. You may notice the font family is different than before. Express is sending a text/html Content-Type Header instead of text/plain (Express is for web after all).

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/browser-express.gif "Express Server")

## REST

We're ready to build our REST API. We'll need:

- A URL route schema to map requests to app actions
- A Controller to handle each action
- Data to respond with
- Place to store the data
- Interface to access and change data

## Routes

Routes are the predefined URL paths your API responds to. Think of each Route as listening to three parts:

- A specific HTTP Action
- A specific URL path
- A handler method

```
1
2
3``` | ```
app.get('/', function(req, res) {
 res.send('Return JSON or HTML View');
});```
------------ | -----------------------------------------------------------------------------------

This example of routing handles all GET Requests. The URL path is the root of the site, the homepage. The handling method is an anonymous function, and responds with plain text in this case.

## Request

```
1
2
3
4
5
6
7
8
9``` | ```
app.get('/musician/:name', function(req, res) {
 // Get /musician/Matt
 console.log(req.params.name)
 // => Matt
 res.send('{"id": 1,"name":"Matt",
 "band":"BBQ Brawlers"}');
});```
------------------------ | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Paths are string patterns broken up by `/` and `:`, and translated into RegEx by Express. In this example, `:name` is anything that comes after `/musician/` in the url. This parameter will be available in our Request object `req` under the params property. It will be keyed the same name as our param in the route path.

FYI `req.body` can also be used to refer to the HTTP Request Body. You'll need to tell Express to look out for Requests Body's in your _server.js_ file via `configure()`. We'll do this later.

```
1
2
3``` | ```
app.configure(function(){
 app.use(express.bodyParser());
});```
------------ | --------------------------------------------------------------------

## Response

`res.send()` is awesome. You can pass this method JSON, Strings and HTML, and it will interoperate and send back the appropriate Content-Type and other Headers for you. If you want control, that's cool too. To send a custom HTTP Status pass that number in before the response content. Add other headers, use `res.set()` to add in your headers before calling `res.send()`.

```
1
2
3
4
5``` | ```
res.send({ "some": "json" });
res.send("html for Maximum Pain's web page");
res.send(404, 'No musicians here');
res.send(500, { error: 'you blew it' });
res.send(200);```
---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

There's much more to Express than detailed here. For more read the well designed and written [docs](http://expressjs.com/4x/api.html).

## Routes.js

Add a new file at the root called _routes.js_. Drop in this routing module:

```
1
2
3
4
5
6
7
8``` | ```
module.exports = function(app){
 var musicians = require('./controllers/musicians');
 app.get('/musicians', musicians.findAll);
 app.get('/musicians/:id', musicians.findById);
 app.post('/musicians', musicians.add);
 app.put('/musicians/:id', musicians.update);
 app.delete('/musicians/:id', musicians.delete);
}```
---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Here we are working with a Musician data model. We've created a Musicians Controller and placed all our Request event handling methods inside the controller. The main REST HTTP actions are handled. We've modeled our URL routes off of REST API conventions, and named our handling methods clearly.

## Controllers

To keep code organized, create a folder called _controllers_ inside your project directory. Create a new file inside of that called _musicians.js_. We'll add each request handling method for Musicians data to this file one by one. For now add these placeholders to _musicians.js_ so we can restart the server without errors:

```
1
2
3
4
5``` | ```
exports.findAll = function() {};
exports.findById = function() {};
exports.add = function() {};
exports.update = function() {};
exports.delete = function() {};```
---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------

Our Controller exports all of these methosd so the router can refer to them.

### Find All

Update `findAll`'s definition' to the function below:

```
1
2
3
4
5
6
7
8``` | ```
exports.findAll = function(req, res){
 res.send([{
 "id": 1,
 "name": "Max",
 "band": "Maximum Pain",
 "instrument": "guitar"
 }]);
};```
---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------

Like the anonymous functions we declared in our _server.js_, request handler methods accept `req` and `res` arguments and return JSON with `res.send()`. Our JSON is hard coded, which will do for now. Let's test this route in our app.

### Import Routes and JS Modules

Update _server.js_ to require our routes file. The `.js` file extension can be omitted.

```
1
2
3
4
5
6
7
8``` | ```
var express = require('express');
var app = express();
require('./routes')(app);
app.listen(3001);
console.log("Jammin\' on port 3001...");```
---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------

`CTRL+C` to stop the Node server and `node server.js` to restart. You should now see JSON at [localhost:3001/musicians](http://localhost:3001/musicians).

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/browser-musicians.gif "Musicians JSON")

## Mongo

Data would be more fun to play with. I made fake bands for all the techers on the team for my musicians data. Put thought into it.

### Start Mongo

Mongo Database felt right for my light data and it is super fast to set up (if you already have Mongo installed, [install](http://docs.mongodb.org/manual/installation/) it if not, yuck). Run `mongod` in another Terminal tab if it's not running already.

## Mongoose.js

Mongo is easy enough to communicate with, but if you like a little bit more structure to your data and data interface, try a Mongo Driver. I used Mongoose.js because I liked the Schema style of defining document structure.

Ask NPM to install this dependency for you, and update your package.json file with this dependency for you with the `--save-dev` option.

```
1``` | ```
npm install mongoose --save-dev```
-------- | --------------------------------------

### Use Mongoose.js

Update _server.js_:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22``` | ```
var express = require('express'),
mongoose = require('mongoose'),
fs = require('fs');
var mongoUri = 'mongodb://localhost/noderest';
mongoose.connect(mongoUri);
var db = mongoose.connection;
db.on('error', function () {
 throw new Error('unable to connect to database at ' + mongoUri);
});
var app = express();
app.configure(function(){
 app.use(express.bodyParser());
});
require('./models/musician');
require('./routes')(app);
app.listen(3001);
console.log('Listening on port 3001...');```
--------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

We're requiring the Mongoose module which will communicate with Mongo for us. The `mongoUri` is a location to the Mongo DB that Mongoose will create if there is not one there already. We added an error handler there to help debug issues connecting to Mongo collections. We also configured Express to parse requests' bodies (we'll use that for POST requests). Lastly, we require the Musician model which we'll make next.

## Define Data Models

Create a new folder called _models_ and add a new file _musician.js_ for our Musician Model.

```
1
2
3
4
5
6
7
8
9
10``` | ```
var mongoose = require('mongoose'),
Schema = mongoose.Schema;
var MusicianSchema = new Schema({
 name: String,
 band: String,
 instrument: String
});
mongoose.model('Musician', MusicianSchema);```
--------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Require Mongoose into this file, and create a new Schema object. This schema helps Mongoose make sure it's getting and setting the right and well-formed data from and to the Mongo collection. Our schema has three String properties which define a Musician object. The last line creates the Musician model object, with built in Mongo interfacing methods. We'll refer to this Musician object in other files.

### Find All

Update _controllers/musicians.js_ to require Mongoose, so we can create an instance of our Musician model to work with. Update `findAll()` to query Mongo with the `find()` data model method.

```
1
2
3
4
5
6
7
8
9
10
11
12``` | ```
var mongoose = require('mongoose'),
Musician = mongoose.model('Musician');
exports.findAll = function(req, res){
 Musician.find({},function(err, results) {
 return res.send(results);
 });
};
exports.findById = function() {};
exports.add = function() {};
exports.update = function() {};
exports.delete = function() {};```
--------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Passing `find()` `{}` means we are not filtering data by any of its properties, so please return all of it. Once Mongoose looks up the data it returns an error message and a result set. Use `res.send()` to return the raw results.

#### Start Mongoose

Restart the server with `CTRL+C` and `node server.js`. Visit the API endpoint for all musicians [localhost:3001/musicians](http://localhost:3001/musicians). You'll get JSON data back, in the form of an empty array.

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/browser-no-musicians.gif "Empty Musicians JSON")

## Data

Since I didn't feel like messing with Mongo command-line, I decided to import musician data with our REST API. Add a new route endpoint to _routes.js_.

```
1``` | ```
app.get('/import', musicians.import);```
-------- | --------------------------------------------

Now to define the import method in our Musicians Controller _controllers/musicians.js_:

```
1
2
3
4
5
6
7
8
9
10
11``` | ```
exports.import = function(req, res){
 Musician.create(
 { "name": "Ben", "band": "DJ Code Red", "instrument": "Reason" },
 { "name": "Mike D.","band": "Kingston Kats", "instrument": "drums" },
 { "name": "Eric", "band": "Eric", "instrument": "piano" },
 { "name": "Paul", "band": "The Eyeliner", "instrument": "guitar" }
 , function (err) {
 if (err) return console.log(err);
 return res.send(202);
 });
};```
------------------------------ | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

This import method adds four documents out of the hard-coded JSON to a _musicians_ collection. The Musician model is referenced here to call its create method. `create()` takes one or more documents in JSON form, and a callback to run on completion. If an error occurs, Terminal will spit the error out, and the request will timeout in the browser. On success, 202 Accepted HTTP status code is returned to the browser. Restart your node server and visit this new endpoint to import data.

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/browser-import.gif "Empty Musicians JSON")

## Returning Data

Now visit your `musicians/` endpoint to view all new musicians data. You'll see an array of JSON objects, each in the defined schema, with an additional generated unique private `_id` and internal `__v` version key. Don't edit those.

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/browser-data.gif "Musicians JSON")

### Find By Id

Recall our route for getting a musician by its id `app.get('/musicians/:id', musicians.findById)`. Here is the handler method:

```
1
2
3
4
5
6``` | ```
exports.findById = function(req, res){
 var id = req.params.id;
 Musician.findOne({'_id':id},function(err, result) {
 return res.send(result);
 });
};```
------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------

This route's path uses a parameter pattern for _id_ `/musicians/:id` which we can refer to in `req`. Pass this id to Mongoose to look up and return just one document. Restart the server. At your find all endpoint, copy one of the super long ids and paste it in at the end of the current url in the browser. Refresh your browser. You'll get a single JSON object for that one musician's document. Nice.

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/browser-by-id.gif "Musician Found")

### Update

PUT HTTP actions in a REST API correlate to an Update method. The route for Update also uses an `:id` parameter.

```
1
2
3
4
5
6
7
8
9
10
11``` | ```
exports.update = function(req, res) {
 var id = req.params.id;
 var updates = req.body;
 Musician.update({"_id":id}, req.body,
 function (err, numberAffected) {
 if (err) return console.log(err);
 console.log('Updated %d musicians', numberAffected);
 return res.send(202);
 });
}```
------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Notice the `updates` variable storing the `req.body`. `req.body` is useful when you want to pass in larger chunks of data such as a single JSON object. Here we will pass in a JSON object following the schema of only the model's properties you want to change.

The model's `update()` takes three parameters:

- JSON object of matching properties to look up the doc with to update
- JSON object of just the properties to update
- callback function that returns the number of documents updated

### Test with cURL

PUT actions are not easy to test in the browser, so I used cURL in Terminal after restarting the server.

```
1``` | ```
$ curl -i -X PUT -H 'Content-Type: application/json' -d '{"band": "BBQ Brawlers"}' <http://localhost:3001/musicians/535fe13ac688500000c2b28b>```
-------- | ----------------------------------------------------------------------------------------------------------------------------------------------------

This sends a JSON Content-Type PUT request to our update endpoint. That JSON object is the request body, and the long hash at the end of the URL is the id of the musician we want to update. Terminal will output a JSON object of the response to the cURL request and _Updated 1 musicians_ from our callback function.

Visit this same URL from the cURL request in the browser to see the changes.

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/browser-put.gif "Musician Updated")

### Add

We used `create()` earlier to add multiple documents to our Musicians Mongo collection. Our POST handler uses the same method to add one new Musician to the collection. Once added, the response is the full new Musician's JSON object.

```
1
2
3
4
5
6``` | ```
exports.add = function(req, res) {
 Musician.create(req.body, function (err, musician) {
 if (err) return console.log(err);
 return res.send(musician);
 });
}```
------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------

Restart the server. Use cURL to POST to the add endpoint with the full Musician JSON as the request body.

```
1``` | ```
$ curl -i -X POST -H 'Content-Type: application/json' -d '{"name": "Joe", "band": "Abita Boys", "instrument":"voice"}' <http://localhost:3001/musicians>```
-------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------

Visit [localhost:3001/musicians](http://localhost:3001/musicians) to see the new Musician at the end of the array.

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/browser-post.gif "Musician Updated")

### Delete

```
1
2
3
4
5
6``` | ```
exports.delete = function(req, res){
 var id = req.params.id;
 Musician.remove({'_id':id},function(result) {
 return res.send(result);
 });
};```
------------------ | -----------------------------------------------------------------------------------------------------------------------------------------------------

Our final REST endpoint, delete reuses what we've learned above. Add this to _controllers/musicians.js_, restart, and check it out with:

```
1``` | ```
$ curl -i -X DELETE <http://localhost:3001/musicians/535feac1cc539500000a209f>```
-------- | -------------------------------------------------------------------------------------

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/browser-delete.gif "Musician Gone")

## Summary

Your final _controllers/musicians.js_ should look like this:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49``` | ```
var mongoose = require('mongoose'),
Musician = mongoose.model('Musician');
exports.findAll = function(req, res){
 Musician.find({},function(err, results) {
 return res.send(results);
 });
};
exports.findById = function(req, res){
 var id = req.params.id;
 Musician.findOne({'_id':id},function(err, result) {
 return res.send(result);
 });
};
exports.add = function(req, res) {
 Musician.create(req.body, function (err, musician) {
 if (err) return console.log(err);
 return res.send(musician);
 });
}
exports.update = function(req, res) {
 var id = req.params.id;
 var updates = req.body;
 Musician.update({"_id":id}, req.body,
 function (err, numberAffected) {
 if (err) return console.log(err);
 console.log('Updated %d musicians', numberAffected);
 res.send(202);
 });
}
exports.delete = function(req, res){
 var id = req.params.id;
 Musician.remove({'_id':id},function(result) {
 return res.send(result);
 });
};
exports.import = function(req, res){
 Musician.create(
 { "name": "Ben", "band": "DJ Code Red", "instrument": "Reason" },
 { "name": "Mike D.","band": "Kingston Kats", "instrument": "drums" },
 { "name": "Eric", "band": "Eric", "instrument": "piano" },
 { "name": "Paul", "band": "The Eyeliner", "instrument": "guitar" }
 , function (err) {
 if (err) return console.log(err);
 return res.send(202);
 });
};```
------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

And your project directory should look like this:

![picture alt](http://brittneykernan.github.io/noderest-presentation/img/directory.gif "Directory Structure")

Final project files can be found in the [presentation repo](https://github.com/brittneykernan/noderest-presentation/tree/master/src).

In my opinion, setting up a REST API with Node.js and Mongo is super fast compared to with PHP and MySQL. The [Express.js](http://expressjs.com/), [Node.js](http://nodejs.org/api/http.html) and [Mongoose.js API](http://mongoosejs.com/docs/api.html) documentation is all easy to reference. There's much more powerful things you can do than I have outlined, so don't stop here.

Shout out to [Bespoke.js](http://markdalgleish.com/projects/bespoke.js/) for the beautiful presentation micro-framework. I'm thinking about adding my fake CLI and fake Directory JS into a Bespoke plugins (arrow through [the presentation](http://brittneykernan.github.io/noderest-presentation/) to see those).
