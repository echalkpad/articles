# Creating a Simple RESTful Web App with Node.js, Express, and MongoDB

[Original URL](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/)

> By Christopher Buecheler Last Updated: June 7, 2015 You can find/fork the sample project on GitHub Hey! This and all my other tutorials will soon be moving to a new home at CloseBrace, a site for...

By [Christopher Buecheler](http://cwbuecheler.com)

**Last Updated:** June 7, 2015

[You can find/fork the sample project on GitHub](https://github.com/cwbuecheler/node-tutorial-2-restful-app)

Hey! This and all my other tutorials will soon be moving to a new home at [CloseBrace](http://closebrace.com), a site for JavaScript developers. You should totally click that link right now, and sign up to be alerted when the site goes live.

In [my first tutorial](http://cwbuecheler.com/web/tutorials/2013/node-express-mongo/), we looked at how to go from nothing installed, to a fully-functioning Node.js web app, using the Express framework, that reads from and writes to a MongoDB database. That's a great start, and if you're unfamiliar with those technologies, now would be an excellent time to go through the tutorial, because we're about to delve deeper. You're going to need to know how to get a webserver running with Express, and how to use app.get and app.post to communicate with both the server and the database. It's all covered in that original tutorial, and if you're a developer who's familiar with JavaScript, it's not hard. [Go check it out](http://cwbuecheler.com/web/tutorials/2013/node-express-mongo/)!

Back? ... or rolling your eyes and going, "dude, I already know that stuff"? Either way, great! Let's add some new tools to our toolbox, and create a simple little app that works without a single page refresh. Here are the goals:

- Learn what REST means in plain English
- Store and retrieve JSON data in a MongoDB collection using HTTP POST and HTTP GET
- Remove data from the collection using HTTP DELETE
- Use AJAX for all data operations
- Update the DOM with jQuery

Basically, in the first tutorial, we built a simple front-end app atop the Router/View back-end. In this tutorial, we're going to eliminate the need for page refreshing or visiting separate URIs entirely. It's all going to work out fine. But before we start building, let's get some REST ...

## Part 1 - Seriously, what the hell is REST?

Wikipedia, that infallible source, defines Representational State Transfer (REST) as: _an architectural style that abstracts the architectural elements within a distributed hypermedia system_. Everyone got that? We clear and ready to move on?

... not so much?

Yeah, me either. I have no idea what a distributed hypermedia system is, and I'm OK admitting that. Let's try to put the concepts of REST in plain English. To do that, we're going to borrow four basic design principles from [IBM's developerWorks website](http://www.ibm.com/developerworks/webservices/library/ws-restful/), and explain what they mean.

- Use HTTP methods explicitly.
- Be stateless.
- Expose directory structure-like URIs.
- Transfer XML, JavaScript Object Notation (JSON), or both.

**Use HTTP Methods Explicitly**<br>
This one's pretty straightforward. To retrieve data, you use GET. To create data, you use POST. To update or change data, you use PUT (not used in this tutorial). To delete data you use DELETE. So for example, this once-common approach is not a good one:

**<http://www.domain.com/myservice/newuser.php?newuser=bob>**

That's an HTTP GET pretending to be a POST. You're GETting the web page and giving it data to store in a DB at the same time. Instead, create a NewUser service and POST to it.

**Be Stateless**<br>
This is a complicated concept but it boils down to "don't store state information on the server". If you must save state, save it on the client side via cookies or other methods. A front-end framework like Angular (outside the scope of this tutorial, but stay tuned!) is particularly helpful here, as it creates an entire client-side MVC setup where you can save and manipulate the state of elements without hammering your server.

IBM gives a pagination example which is pretty good. A stateful design would hit a deliverPage service that's been keeping track of the page you're on, and delivers the next one. a Stateless design would populate prevPage, currPage, and nextPage data in the markup (hidden input fields, JavaScript variables, data- attributes, and so on), and then HTTP GET a newPage service using the nextPage parameter from the markup to request a specific page.

I've put together [a quick JSFiddle](http://jsfiddle.net/cwbuecheler/7fars/) illustrating what I'm talking about. Take a look and note that we're never storing any page data on the "server" side. We merely take the current page value from the DOM, and then when we get our new page, we update the DOM. That's simple, stateless programming.

**Expose directory structure-like URIs.**<br>
This one's easy. Instead of:

<http://app.com/getfile.php?type=video&game=skyrim&pid=68>

You want:

<http://app.com/files/video/skyrim/68>

**Transfer XML, JavaScript Object Notation (JSON), or both.**<br>
This one's easy too! Just make sure that your back-end is sending XML or JSON (I prefer JSON, especially in all-JavaScript setups like the one discussed in this tutorial). You can easily manipulate this data in your presentation layer without having to hit your servers, unless you need new data.

OK ... so do we get the basics of REST? It's pretty straightforward, really. You've probably already worked within systems that use it.

## Part 2 - Setup

Now that we have an idea of what REST is all about, let's put it to work for us by building a stupidly simple single-page web app that's completely valueless. Kind of like half the startups in the Valley, amirite?! Is this thing on?

Anyway, no, we're not building a to-do list, though that's become the "Hello World" of web apps. We're going to build a simple collection of usernames and emails, much like we did in our previous tutorial. So, let's get started. Make sure you've got the latest stable version of Node installed on your machine, then fire up a console window and navigate to wherever you're storing these web projects. For the purposes of this tutorial, that's C:\node\. If you're placing your work elsewhere (for example /home or /Users), adjust accordingly.

The first thing we'll want to do is update Express and the Express scaffolding generator globally, like this:

### Command C:\node\

```
npm update -g express
```

Once that's done, type the following:

### Command C:\node\

```
npm update -g express-generator
```

Finally, follow it up with:

### Command C:\node\

```
express nodetest2
```

As you'll remember from this last tutorial, this is going to auto-generate a website skeleton in a new directory called nodetest2\. Watch as it does its thing, and when it's done, open the newly created package.json file (in the newly created nodetest2 folder) in your text editor of choice, and change it so it looks like this:

### C:\node\nodetest2\package.json

```
{
 "name": "nodetest2b",
 "version": "0.0.0",
 "private": true,
 "scripts": {
 "start": "node ./bin/www"
 },
 "dependencies": {
 "body-parser": "~1.12.4",
 "cookie-parser": "~1.3.5",
 "debug": "~2.2.0",
 "express": "~4.12.4",
 "jade": "~1.9.2",
 "morgan": "~1.5.3",
 "serve-favicon": "~2.2.1",
 "mongodb": "^1.4.4",
 "monk": "^1.0.1"
 }
}
```

As usual, these versions are up to date as of the "last update" box at the top, and are proven to work with this tutorial. Use newer versions at your own peril! Note that we're adding the MongoDB and Monk packages so that we can access and control our database.

**Important Note:** this tutorial is a bit out of date at the moment. It is essential that you use this older version of the mongodb module (1.4.4) or monk will not work and your app will break. I will be doing a full update as soon as I can, but this should keep you running for now.

Switch back to your command prompt, **cd to your nodetest2 directory**, and type:

### Command C:\node\nodetest2\

```
npm install
```

Watch as all your dependencies install – it'll take a while. Very exciting! After that, there's one small thing to do. In the same command prompt, just type:

### Command C:\node\nodetest2\

```
mkdir data
```

This is where we'll store our database files when we get to that point. If you'd like to store them elsewhere, that's absolutely fine ... just know that you need to have the directory ready before running MongoDB. That's it; we're going to prepopulate the database later in this tutorial, so we're done with setup for now. It's time to get started making web pages.

One final note: this article uses four-space indentation for everything because that's fairly popular in the JavaScript community. I actually mostly work in tabs. I know this is a big debate, but honestly given how fast and easy it is to convert spaces to tabs and back again with any decent editor, I don't really see the issue. Unless your team has a standard everyone's agreed to, you should work with what you want.

## Part 3 - Starting In On HTML

If we're going to have a single-page web app, the first thing we need is a single page, right? Let's open up our views folder, and start with layout.jade. This is a template file that we're only going to make a few basic changes to. Here's how it starts:

### C:\node\nodetest2\views\layout.jade

```
doctype html
html
 head
 title= title
 link(rel='stylesheet', href='/stylesheets/style.css')
 body
 block content
```

We want to be able to do two things. One: include jQuery, and two: include our master javascript file. So let's edit the file and make it look like this:

### C:\node\nodetest2\views\layout.jade

```
doctype html
html
 head
 title= title
 link(rel='stylesheet', href='/stylesheets/style.css')
 body
 block content
 script(src='http://ajax.googleapis.com/ajax/libs/jquery/2.0.3/jquery.min.js')
 script(src='/javascripts/global.js')
```

Astute readers may note that global.js does not actually exist yet. This is true. We're going to make it in a bit. For now, it'll just quietly 404 in the background when we load our index page. If this really bothers you, feel free to create an empty file now in /public/javascripts/.

Onward to index.jade – this is the only HTML file we'll need for the rest of our webapp. We're going to put quite a lot of stuff onto this page. In the real world, we'd also need quite a lot of CSS, but for the sake of saving time, just [download this file](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/style.css) and copy it over /public/stylesheets/style.css. It'll give you a basic layout to work with, which you can modify to be as pretty as your heart desires.

Open index.jade. You'll see a VERY basic skeleton here:

### C:\node\nodetest2\views\index.jade

```
extends layout

block content
 h1= title
 p Welcome to #{title}
```

That's not very exciting. Let's get started modifying this guy. That h1= title line, and the paragraph after it, are both pulling a title variable that's set in /routes/index.js, and just says "Express". That's kind of repetetive, so let's change the paragraph to just say "Welcome to our test". Then let's add a wrapper, and add a table skeleton for displaying a list of users:

### C:\node\nodetest2\views\index.jade

```
extends layout

block content
 h1= title
 p Welcome to our test

 // Wrapper
 #wrapper

 // USER LIST
 h2 User List
 #userList
 table
 thead
 th UserName
 th Email
 th Delete?
 tbody
 // /USER LIST

 // /WRAPPER
```

You'll note that there's no data in the table. That's because we're going to populate it, via AJAX, with stuff we pull out of our MongoDB database. So, let's get that set up, and then we'll do a little javascripting to pull things together.

If you want to see what the page looks like right now, go ahead and type the following into your terminal:

### Command C:\node\nodetest2\

```
npm start
```

Then navigate to <http://localhost:3000> ... you'll see a very basic, very boring page with an empty table in it. If you've downloaded the CSS file mentioned above and overwritten the default stylesheet, it will look like this:

![Restful Web App Screenshot 1](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/restful_web_app_screenshot_01.png)

Exciting, right? Not really. Let's make it do something.

## Part 4 - The Database

We already covered getting MongoDB up and running in the previous tutorial, so I'm going to skip right to the good stuff here. In a **new** terminal or command prompt window, navigate to wherever you keep MongoDB (for example: C:\mongo\bin) and type

### Command C:\mongo\bin\

```
mongod --dbpath c:\node\nodetest2\data
```

Obviously if you've decided to store your data elsewhere, you should use that path instead. You should see the MongoDB daemon fire up and report that it's waiting for connections. So let's connect: open up a **new** terminal/command window, navigate to your Mongo directory, and type:

### Command C:\mongo\bin\

```
mongo
```

Now we've got a command interface for our database. This, too, was discussed in the previous tutorial, so again I'm going to jump right into "just doing" instead of explaining. Let's switch to a new database (for our new project) by typing the following:

### Mongo Console

```
use nodetest2
```

Now we want to create and populate the "userlist" collection within our nodetest2 database. We're going to do this in one fell swoop by just inserting into the empty collection, like this:

### Mongo Console

```
db.userlist.insert({'username' : 'test1','email' : 'test1@test.com','fullname' : 'Bob Smith','age' : 27,'location' : 'San Francisco','gender' : 'Male'})
```

Note: I like to type this stuff out in a text editor first, using tabs and all that, so I can see what I'm doing, before collapsing it down to a single line and pasting it into the MongoDB console. You should do whatever works best for you.

That's all the data we need to prepopulate, this time around. We don't even _really_ need to do that much, since we're going to be creating an add user routine anyway, but it makes it easier to see that yes, our DB connection is working, as we first start to wire up our app.

You can exit out of the MongoDB console right now and kill that terminal, if you want, but make sure to leave the MongoDB daemon running. If you close it down, our website won't be able to connect to a database, and that would be a Bad Thing.

## Part 5 - Listing Users

It's time to start making changes to app.js, the heart and soul of our application. By default Express has set it up pretty well, but we'll need to add some hooks for Monk. Here's what we start with:

### C:\node\nodetest2\app.js

```
var express = require('express');
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');

var routes = require('./routes/index');
var users = require('./routes/users');

var app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');

// uncomment after placing your favicon in /public
//app.use(favicon(__dirname + '/public/favicon.ico'));
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', routes);
app.use('/users', users);

/// catch 404 and forwarding to error handler
app.use(function(req, res, next) {
 var err = new Error('Not Found');
 err.status = 404;
 next(err);
});

/// error handlers

// development error handler
// will print stacktrace
if (app.get('env') === 'development') {
 app.use(function(err, req, res, next) {
 res.status(err.status || 500);
 res.render('error', {
 message: err.message,
 error: err
 });
 });
}

// production error handler
// no stacktraces leaked to user
app.use(function(err, req, res, next) {
 res.status(err.status || 500);
 res.render('error', {
 message: err.message,
 error: {}
 });
});


module.exports = app;
```

We're going to add a few things to the section at the very top, so that it looks like this:

### C:\node\nodetest2\app.js

```
var express = require('express');
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');
// Database
var mongo = require('mongodb');
var monk = require('monk');
var db = monk('localhost:27017/nodetest2');
```

Here we're calling the Monk module and then giving it some basic configuration parameters (including telling it where the DB lives, and which database to use – nodetest2).

We also need to make our database accessible to our various http requests, as we did in the first tutorial. To do that, first find this section:

### C:\node\nodetest2\app.js

```
app.use('/', routes);
app.use('/users', users);
```

And just **above it**, add this code:

### C:\node\nodetest2\app.js

```
// Make our db accessible to our router
app.use(function(req,res,next){
 req.db = db;
 next();
});
```

Now it's time to move on to routing. Note that Express auto-creates a /users route file. We're going to make use of that, but we won't be creating any views for it. Why? Well, because since this is a single-page app, we're using the Index route and view for display purposes. We're going to use the user route to set up our data I/O ... the services we want to create to show, add, and delete users from our database. We'll access these with JavaScript, rather than navigating to them in a browser, and display the collected data on the index page.

So to that end, let's do a little cleanup. Open up /nodetest2/routes/users.js in your text editor. It'll look like this:

### C:\node\nodetest2\routes\users.js

```
var express = require('express');
var router = express.Router();

/* GET users listing. */
router.get('/', function(req, res) {
 res.send('respond with a resource');
});

module.exports = router;
```

Go ahead and **delete the comment and the three lines below it**. We're going to replace it with our own code, for /users/userlist. So our file's going to look like this:

### C:\node\nodetest2\routes\users.js

```
var express = require('express');
var router = express.Router();

/*
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii GET userlist.
 */
router.get('/userlist', function(req, res) {
 var db = req.db;
 var collection = db.get('userlist');
 collection.find({},{},function(e,docs){
 res.json(docs);
 });
});

module.exports = router;
```

The purpose of this code is: if you do an HTTP GET to /users/userlist, our server will return JSON that lists all of the users in the database. Obviously for a large-scale project you'd want to put in limits as to how much data gets spewed out at one time, for example by adding paging to your front-end, but for our purposes this is fine.

Save your users.js file, kill your node instance if it's still running, and restart it with

### Command C:\node\nodetest2\

```
npm start
```

In your terminal. Then refresh your browser. You'll see ... nothing. Well, not nothing, but rather the exact same thing as you saw in your screenshot above. That's because we haven't wired anything up yet. If you want, you can navigate to **<http://localhost:3000/users/userlist>** where you will find the JSON output that we'll be manipulating next. It's just a single user, the one that we manually entered in the MongoDB console earlier.

Let's get Bob Smith into our HTML, shall we? We're going to create our global.js file now, so create a new text document and save it as /nodetest2/public/javascripts/global.js

A few notes on my coding style: I like braces and use them always, even for single-line if statements and the like. I like variable names that mean something, rather than trying to be short. I favor single quotes above double quotes. I prefer comments above lines, as opposed to on the right-hand side. I use a LOT of comments, because JS can always be minified so there's really no reason to worry about comments contributing to filesize. I use a decent amount of whitespace for the same reason. Oh, and I like my opening braces on the same line, not a new line.

You probably hate one or more things about my style. That's fine. Adapt as you see fit. What matters is that your code is readable (if you're working with others) and that it runs (no matter what).

Let's start by defining a function to populates our HTML table with data. I like to label the different sections of my JavaScript with big, hideous, highly-noticeable labels, so here's what we're going to do:

### C:\node\nodetest2\public\javascripts\global.js

```
// Userlist data array for filling in info box
var userListData = [];

// DOM Ready =============================================================
$(document).ready(function() {

 // Populate the user table on initial page load
 populateTable();

});

// Functions =============================================================

// Fill table with data
function populateTable() {

 // Empty content string
 var tableContent = '';

 // jQuery AJAX call for JSON
 $.getJSON( '/users/userlist', function( data ) {

 // For each item in our JSON, add a table row and cells to the content string
 $.each(data, function(){
 tableContent += '<tr>';
 tableContent += '<td><a href="#" class="linkshowuser" rel="' + this.username + '">' + this.username + '</a></td>';
 tableContent += '<td>' + this.email + '</td>';
 tableContent += '<td><a href="#" class="linkdeleteuser" rel="' + this._id + '">delete</a></td>';
 tableContent += '</tr>';
 });

 // Inject the whole content string into our existing HTML table
 $('#userList table tbody').html(tableContent);
 });
};
```

As you can see, we're using a global variable and defining our functions at the top level. This is actually not a great idea and I'm only doing it for speed and simplicity. In a real app, you want to define a single master global, which you can then populate with properties, methods, etc ... as needed, thus helping to avoid conflicts or generally pollute the namespace. If you want to learn more about this strategy, I strongly recommend picking up a copy of [JavaScript: The Good Parts](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742) by Douglas Crockford and reading through it. Actually, I strongly recommend that anyway. It's an important book.

For now, however, we'll stick with our specific global and move on to the DOM ready detect, which will fire off our table-filling method _populateTable()_ when the page is ready for scripts to run. After that, we must of course define our table-filling method. That's where things get interesting, but not too terribly complex. We make a simple AJAX call via jQuery, iterate over the return JSON to create a big ol' content string with all our new HTML in it, and then inject that HTML into our existing table.

With that JavaScript added, we can now go to our browser again and refresh or navigate to <http://localhost:3000/> ... if all is working properly, and it should be, we'll see that our table is now populated with data. Awesome!

![Restful Web App Screenshot 2](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/restful_web_app_screenshot_02.png)

That's a solid start, but it's not displaying everything we have in our database. Let's set up an info box that will display the full set of user information when we click on the username.

## Part 6 - Populating User Info

Still working in global.js, we need to add one quick line to our populateTable() function. This line will stick _all_ of our user data into the array we established earlier. Again: I don't recommend taking this route if you're dealing with thousands of users. It's not a performance-friendly approach. But for quick-n-dirty, it'll work fine.

Find this line:

### C:\node\nodetest2\public\javascripts\global.js

```
 // jQuery AJAX call for JSON
 $.getJSON( '/users/userlist', function( data ) {
```

And directly below it you'll see:

### C:\node\nodetest2\public\javascripts\global.js

```
 // For each item in our JSON, add a table row and cells to the çontent string
 $.each(data, function(){
```

**In between** those lines, add the following:

### C:\node\nodetest2\public\javascripts\global.js

```
 // Stick our user data array into a userlist variable in the global object
 userListData = data;
```

What this is doing is sticking all of our returned user data, from the database, into our global variable, so that we can access it without repeatedly whaling on the database each time we click a name in our table. I want to be clear here: for large-scale operations, this is **not a great idea**. You would not want to do this if you were loading tens of thousands of users at once (but you probably wouldn't want to load tens of thousands of users at once, either). You'd want to implement paging, and only load the data you really needed at any given time.

But for the purposes of this tutorial, it's fine. So head down to the end of the file, where we're keeping our functions, and add this code on a new line at the bottom:

### C:\node\nodetest2\public\javascripts\global.js

```
// Show User Info
function showUserInfo(event) {

 // Prevent Link from Firing
 event.preventDefault();

 // Retrieve username from link rel attribute
 var thisUserName = $(this).attr('rel');

 // Get Index of object based on id value
 var arrayPosition = userListData.map(function(arrayItem) { return arrayItem.username; }).indexOf(thisUserName);
```

OK, that's kinda some crazy stuff. Here's what we're doing: first we're using .map to apply a function to each object in our userListData array. This will spit out a **brand new array** containing only whatever the function returns. That function (the anonymous callback function using the userObj parameter) strictly returns the username. So, basically, if our original data array contained two complete user objects, then the array returned by our use of .map here would only contain usernames, and look like this: ['Bob', 'Sue'].

So once we have THAT array, provided by .map, we're chaining indexOf, in combination with the username of our choice, to get the array index of that username. So Bob would be zero, and Sue would be one. We can then use that number, stored as arrayPosition, to go back to our original user data array and start pulling data, in the following code.

If you want to see a version of this code that DOESN'T use chaining and is a bit more verbose, [check it out at this jsfiddle](http://jsfiddle.net/cwbuecheler/2qEZ2/). In the meantime, still in that showUserInfo function, add the rest:

### C:\node\nodetest2\public\javascripts\global.js

```
 // Get our User Object
 var thisUserObject = userListData[arrayPosition];

 //Populate Info Box
 $('#userInfoName').text(thisUserObject.fullname);
 $('#userInfoAge').text(thisUserObject.age);
 $('#userInfoGender').text(thisUserObject.gender);
 $('#userInfoLocation').text(thisUserObject.location);

};
```

The function in its entirety will look like this:

```
// Show User Info
function showUserInfo(event) {

 // Prevent Link from Firing
 event.preventDefault();

 // Retrieve username from link rel attribute
 var thisUserName = $(this).attr('rel');

 // Get Index of object based on id value
 var arrayPosition = userListData.map(function(arrayItem) { return arrayItem.username; }).indexOf(thisUserName);

 // Get our User Object
 var thisUserObject = userListData[arrayPosition];

 //Populate Info Box
 $('#userInfoName').text(thisUserObject.fullname);
 $('#userInfoAge').text(thisUserObject.age);
 $('#userInfoGender').text(thisUserObject.gender);
 $('#userInfoLocation').text(thisUserObject.location);

};
```

Now we need to trigger that function on a click, so back up in our DOM ready section, below the initial call to populate the table, add this code:

```
 // Username link click
 $('#userList table tbody').on('click', 'td a.linkshowuser', showUserInfo);
```

This is all pretty straightforward. We've found our user object and we're populating spans with data. The thing is ... those spans don't exist yet! Let's **save global.js** and then open up /views/index.jade. We want to add the following code right after the #wrapper line (and above our user list table):

### C:\node\nodetest2\views\index.jade

```
 // USER INFO
 #userInfo
 h2 User Info
 p
 strong Name:
 | <span id='userInfoName'></span>
 br
 strong Age:
 | <span id='userInfoAge'></span>
 br
 strong Gender:
 | <span id='userInfoGender'></span>
 br
 strong Location:
 | <span id='userInfoLocation'></span>
 // /USER INFO
```

Remember that the indents here are really important – if your indents are borked, Jade flat-out won't work. For reference, here's what your entire index.jade file should look like right now:

### C:\node\nodetest2\views\index.jade

```
extends layout

block content
 h1= title
 p Welcome to our test

 // Wrapper
 #wrapper

 // USER INFO
 #userInfo
 h2 User Info
 p
 strong Name:
 | <span id='userInfoName'></span>
 br
 strong Age:
 | <span id='userInfoAge'></span>
 br
 strong Gender:
 | <span id='userInfoGender'></span>
 br
 strong Location:
 | <span id='userInfoLocation'></span>
 // /USER INFO

 // USER LIST
 h2 User List
 #userList
 table
 thead
 th UserName
 th Email
 th Delete?
 tbody
 // /USER LIST

 // /WRAPPER
```

Got it? Cool. Save that file and let's hit our browser and take a look. Refresh <http://localhost:3000> and now you'll see an unpopulated user info box floating over to the left. Click on the "test1" username in your table, and it should populate that box with Bob's info.

![Restful Web App Screenshot 3](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/restful_web_app_screenshot_03.png)

Pretty awesome. So now let's get some more users in there by doing some AJAX posting.

## Part 7 - Adding Users

The first thing we'll need a set of form fields with which to add a new user. So let's stick around in /views/index.jade for a bit and add that code. Right below our user list, but above the closing wrapper comment, add the following:

### C:\node\nodetest2\views\index.jade

```
 // ADD USER
 h2 Add User
 #addUser
 fieldset
 input#inputUserName(type='text', placeholder='Username')
 input#inputUserEmail(type='text', placeholder='Email')
 br
 input#inputUserFullname(type='text', placeholder='Full Name')
 input#inputUserAge(type='text', placeholder='Age')
 br
 input#inputUserLocation(type='text', placeholder='Location')
 input#inputUserGender(type='text', placeholder='gender')
 br
 button#btnAddUser Add User
 // /ADD USER
```

This is all pretty straightforward (yes, I'm using a couple of break tags, which are best avoided outside of text, but it's for the sake of expediency here). We've got text inputs for each piece of information we'll need to create a new user, and we've got a button to click on. Right now that button won't do anything, but that's what we're about to fix.

This is the last time we'll need to touch our view file. Make sure it looks like this in your text editor:

### C:\node\nodetest2\views\index.jade

```
extends layout

block content
 h1= title
 p Welcome to our test

 // Wrapper
 #wrapper

 // USER INFO
 #userInfo
 h2 User Info
 p
 strong Name:
 | <span id='userInfoName'></span>
 br
 strong Age:
 | <span id='userInfoAge'></span>
 br
 strong Gender:
 | <span id='userInfoGender'></span>
 br
 strong Location:
 | <span id='userInfoLocation'></span>
 // /USER INFO

 // USER LIST
 h2 User List
 #userList
 table
 thead
 th UserName
 th Email
 th Delete?
 tbody
 // /USER LIST

 // ADD USER
 h2 Add User
 #addUser
 fieldset
 input#inputUserName(type='text', placeholder='Username')
 input#inputUserEmail(type='text', placeholder='Email')
 br
 input#inputUserFullname(type='text', placeholder='Full Name')
 input#inputUserAge(type='text', placeholder='Age')
 br
 input#inputUserLocation(type='text', placeholder='Location')
 input#inputUserGender(type='text', placeholder='gender')
 br
 button#btnAddUser Add User
 // /ADD USER

 // /WRAPPER
```

And like this in your browser:

![Restful Web App Screenshot 4](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/restful_web_app_screenshot_04.png)

One last reminder: Jade is very touchy about tabs/spaces. If you're getting errors, check all of your indents and try restarting app.js. If everything looks right, you're good to go. Close index.jade, and open /routes/users.js – it's time to do some POSTing.

Adding a user is really not too difficult. It's very similar to retrieving user info, except you're doing an insert() to the database instead of a find(). Below your user listing code (but above module.exports), you'll want to add the following:

### C:\node\nodetest2\routes\users.js

```
/*
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii POST to adduser.
 */
router.post('/adduser', function(req, res) {
 var db = req.db;
 var collection = db.get('userlist');
 collection.insert(req.body, function(err, result){
 res.send(
 (err === null) ? { msg: '' } : { msg: err }
 );
 });
});
```

This basically just says "we're going to post some data (req.body), and you're going to insert it into our 'userlist' collection in the database. If that goes well, return an empty string. If it goes poorly, return the error message that the database gives us."

For the purposes of this tutorial, that's really all there is to it. You can save /routes/users.js. That's all we need to do here. Now all that's left is making that new form/button do something. Head over to global.js and let's get started.

First let's write our event catcher, up in the DOM Ready section, just below the user list name click. It's just one line of code:

### C:\node\nodetest2\public\javascripts\global.js

```
 // Add User button click
 $('#btnAddUser').on('click', addUser);
```

You'll note that we're calling an addUser function. Obviously, we'll need to build that function. This is going to be our largest single bit of code in this entire tutorial, because it's got quite a bit going on. It has to do some rudimentary form validation and then, if the form's all filled out, compile the data and POST it via AJAX to our adduser service.

This is a big function. I've commented it thoroughly to show what's going on. Add this at the bottom of global.js:

### C:\node\nodetest2\public\javascripts\global.js

```
// Add User
function addUser(event) {
 event.preventDefault();

 // Super basic validation - increase errorCount variable if any fields are blank
 var errorCount = 0;
 $('#addUser input').each(function(index, val) {
 if($(this).val() === '') { errorCount++; }
 });

 // Check and make sure errorCount's still at zero
 if(errorCount === 0) {

 // If it is, compile all user info into one object
 var newUser = {
 'username': $('#addUser fieldset input#inputUserName').val(),
 'email': $('#addUser fieldset input#inputUserEmail').val(),
 'fullname': $('#addUser fieldset input#inputUserFullname').val(),
 'age': $('#addUser fieldset input#inputUserAge').val(),
 'location': $('#addUser fieldset input#inputUserLocation').val(),
 'gender': $('#addUser fieldset input#inputUserGender').val()
 }

 // Use AJAX to post the object to our adduser service
 $.ajax({
 type: 'POST',
 data: newUser,
 url: '/users/adduser',
 dataType: 'JSON'
 }).done(function( response ) {

 // Check for successful (blank) response
 if (response.msg === '') {

 // Clear the form inputs
 $('#addUser fieldset input').val('');

 // Update the table
 populateTable();

 }
 else {

 // If something goes wrong, alert the error message that our service returned
 alert('Error: ' + response.msg);

 }
 });
 }
 else {
 // If errorCount is more than 0, error out
 alert('Please fill in all fields');
 return false;
 }
};
```

I recommend typing all that out instead of cutting and pasting it – it'll help you see what's really going on in a way that just scanning it with your eyes won't. But either way, get that into your javascript and save the file. Now remember to **restart your node app** and then refresh <http://localhost:3000> ... you'll see the same thing you saw before, except now that form should work! Let's fill in some info.

![Restful Web App Screenshot 5](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/restful_web_app_screenshot_05.png)

Click add, and the form should submit, the data should be sent off to the database, and the table should refresh to show this fact. Click on your new username in that table, and you'll get your new user info in the info box. You'll note that all of this happened without any kind of a page refresh, which is what we're looking for here.

![Restful Web App Screenshot 6](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/restful_web_app_screenshot_06.png)

Go ahead and create a few more users. We're going to be getting rid of a couple of 'em in the next section.

## Part 8 - Deleting Users

This is easier than adding users, but it's largely the same process: update our route file and update global.js ... we don't even have to touch index.jade because we already put our delete links in there. Good times.

Let's start with the route file. **Update:** the syntax has changed since I first posted this tutorial. The **NEW**, correct syntax is shown here. Open /routes/users.js and add the following to the bottom, just above module.exports:

### C:\node\nodetest2\routes\users.js

```
/*
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii DELETE to deleteuser.
 */
router.delete('/deleteuser/:id', function(req, res) {
 var db = req.db;
 var collection = db.get('userlist');
 var userToDelete = req.params.id;
 collection.remove({ '_id' : userToDelete }, function(err) {
 res.send((err === null) ? { msg: '' } : { msg:'error: ' + err });
 });
});
```

This is pretty straightforward – we pass in an ID parameter (so the URI we'll be referencing will be /deleteuser/12345 for example, and we reference it in the code with req.params.id), and MongoDB matches it up with the unique _id field that it generates for every entry in a collection, and nukes that entry from orbit. Just like our add user routine, if all goes well it returns a blank string, and if things don't work out it sends back the error message from MongoDB.

Save /routes/users.js and then we'll wire stuff up in the JavaScript. Let's move back to global.js to finish things up. We've already populated each delete link with a rel attribute that contains the id. It's in this line from global.js:

### C:\node\nodetest2\public\javascripts\global.js

```
tableContent += '<td><a href="#" class="linkdeleteuser" rel="' + this._id + '">delete</a></td>';
```

Now we're going to add a quick delete routine in our DOM Ready section:

### C:\node\nodetest2\public\javascripts\global.js

```
 // Delete User link click
 $('#userList table tbody').on('click', 'td a.linkdeleteuser', deleteUser);
```

Note the syntax we're using: when working with jQuery's 'on' method, in order to capture dynamically inserted links, you need to reference a static element on the page first. That's why our selector is the table's tbody element – which remains constant regardless of adding or removing users – and then we're specifying the specific links we're trying to catch in the .on parameters.

Let's build that deleteUser function down at the bottom of our file:

### C:\node\nodetest2\public\javascripts\global.js

```
// Delete User
function deleteUser(event) {

 event.preventDefault();

 // Pop up a confirmation dialog
 var confirmation = confirm('Are you sure you want to delete this user?');

 // Check and make sure the user confirmed
 if (confirmation === true) {

 // If they did, do our delete
 $.ajax({
 type: 'DELETE',
 url: '/users/deleteuser/' + $(this).attr('rel')
 }).done(function( response ) {

 // Check for a successful (blank) response
 if (response.msg === '') {
 }
 else {
 alert('Error: ' + response.msg);
 }

 // Update the table
 populateTable();

 });

 }
 else {

 // If they said no to the confirm, do nothing
 return false;

 }

};
```

Remember to **restart your node server** and then hit <http://localhost:3000> to test it out. This one's more simple than the addUser function. It does a quick JavaScript confirm:

![Restful Web App Screenshot7](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/restful_web_app_screenshot_07.png)

If the user's sure they want to delete, it just hits our delete service and passes it the ID, then it updates the table to show that the user is now gone.

![Restful Web App Screenshot 8](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/restful_web_app_screenshot_08.png)

That's it. We've got delete working. You can now add, view, and delete as many users as you'd like. Since this tutorial's already the length of a dictionary, we're going to skip updating, but hopefully you have a solid idea of how to do that. First you'd GET the info and populate a form with it, then you'd PUT on submission of the form (see the beginning of this tutorial for info on POST vs. PUT), and update the table.

Actually, that's not a bad exercise: you should try to add updating user info to this page and see if you can get it working. You'll need to edit /views/index.jade, /routes/users.js, /app.js, and /public/javascripts/global.js just like for adding and deleting.

## Conclusion

Well, there we have it. We've gotten through yet another big-ass NodeJS tutorial together. I hope everything worked!

This is obviously an incredibly rudimentary RESTful web app. It gives no thought to performance or to "what happens when my table has 1,000 users instead of 10?"-type questions. It also doesn't worry a lot about flexibility, maintainability, or future additions. The purpose was merely to get you up to speed with single-page, ajax-driven data manipulation in Node / Express / MongoDB. I hope it's accomplished that!

For more on REST and the best ways to approach it, I recommend the following articles:

## Thanks & Info

I once again owe a debt of gratitude to a few people and would be remiss if I didn't give 'em some links!

Resource: the folks at Udemy have put up an incredibly extensive [MEAN stack](https://blog.udemy.com/node-js-tutorial/) introduction that's well worth checking out.

### About the author

[Christopher Buecheler](http://cwbuecheler.com) is an autodidact polymath, which is an _incredibly_ pretentious way of saying that he's a jack of all trades who didn't like college. By day he's a [front-end developer](http://cwbuecheler.com/web/) for a small San Francisco startup. By night [he's a popular novelist](http://cwbuecheler.com/writing/), with four books released. He also is an [award-winning amateur mixologist](http://www.huffingtonpost.com/tony-sachs/a-cocktail-safari-with-ta_b_867015.html) who [writes cocktail articles for Primer Magazine](http://cwbuecheler.com/writing/#nonfiction) and [runs a cocktail blog](http://drinkshouts.com), and he brews beer on occasion. He follows the NBA avidly and the NFL casually (and sometimes glances at MLB). He lives in Providence, Rhode Island, with his awesome French wife and their two cats. He is trying to learn French but wishes he could just download it from the Matrix.
