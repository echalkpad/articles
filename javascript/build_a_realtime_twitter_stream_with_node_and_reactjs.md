# Build A Real-Time Twitter Stream with Node and React.js

[Original URL](https://scotch.io/tutorials/build-a-real-time-twitter-stream-with-node-and-react-js)

> Introduction Welcome to the second installation of Learning React, a series of articles focused on becoming proficient and effective with Facebook's React library. If you haven't read...

## Introduction

Welcome to the second installation of **Learning React**, a series of articles focused on becoming proficient and effective with Facebook's React library. If you haven't read the first installation, [Getting Started and Concepts](https://scotch.io/tutorials/javascript/learning-react-getting-started-and-concepts), it is highly recommended that you do so before proceeding.

Today we are going to build an application in React using **Isomorphic Javascript**.

_Iso-what?_

Isomorphic. Javascript. It means writing one codebase that can run on _both the server side and the client side._

This is the concept behind frameworks like [Rendr](https://github.com/rendrjs/rendr), [Meteor](https://www.meteor.com/) & [Derby](http://derbyjs.com/). You can also accomplish this using [React](http://facebook.github.io/react/), and today we are going to learn how.

### Why is this awesome?

I'm an Angular fan just like everybody else, but one pain point is the potential SEO impact.

_But I thought Google executes and indexes javascript?_

Yeah, not really. They just give you an opportunity to serve up static HTML. You still have to generate that HTML with PhantomJS or a third party service.

Enter React.

[![react-site](https://cask.scotch.io/2014/10/react-site.png)](https://cask.scotch.io/2014/10/react-site.png)

React is amazing on the client side, but it's ability to be rendered on the server side makes it truly special. This is because React uses a virtual DOM instead of the real one, and allows us to render our components to markup.

### Getting Started

Alright gang, lets get down to brass tacks. We are going to build an app that shows tweets about this article, and loads new ones in real time. Here are the requirements:

- It should listen to the Twitter streaming API and save new tweets as they come in.
- On save, an event should be emitted to the client side that will update the views.
- The page should render server side initially, and the client side should take it from there.
- We should use infinity scroll pagination to load blocks of 10 tweets at a time.
- New unread tweets should have a notification bar that will prompt the user to view them.

Here is a quick look at what we'll be building. Make sure you check out the [demo](http://react-tweets.herokuapp.com/) and to see everything happen in real time.

[![react-tweets-demo](https://cask.scotch.io/2014/10/react-tweets-demo.png)](https://cask.scotch.io/2014/10/react-tweets-demo.png)

Let's take a look at some of the tools we are going to use besides React:

- **Express** – A node.js web application framework
- **Handlebars** – A templating language we are going to write our layout templates in
- **Browserify** – A dependency bundler that will allow us to use CommonJS syntax
- **Mongoose** – A mongoDB object modeling library
- **Socket.io** – Real time bidirectional event based communication
- **nTwitter** – Node.js Twitter API library

## Server Side

Lets start by building out the server side of our app. Download the project files [here](https://github.com/scotch-io/react-tweets), and follow along below:

### Directory Structure

```
components/ // React Components Directory
---- Loader.react.js // Loader Component
---- NotificationBar.react.js // Notification Bar Component
---- Tweet.react.js // Single Tweet Component
---- Tweets.react.js // Tweets Component
---- TweetsApp.react.js // Main App Component 
models/ // Mongoose Models Directory
---- Tweet.js // Our Mongoose Tweet Model
public/ // Static Files Directory
---- css
---- js
---- svg
utils/
----streamHandler.js // Utility method for handling Twitter stream callbacks
views/ // Server Side Handlebars Views
----layouts
-------- main.handlebars
---- home.handlebars
app.js // Client side main
config.js // App configuration
package.json 
routes.js // Route definitions
server.js // Server side main
```

### package.json

```
{
 "name": "react-isomorph",
 "version": "0.0.0",
 "description": "Isomorphic React Example",
 "main": "app.js",
 "scripts": {
 "watch": "watchify app.js -o public/js/bundle.js -v",
 "browserify": "browserify app.js | uglifyjs > public/js/bundle.js",
 "build": "npm run browserify ",
 "start": "npm run watch & nodemon server.js"
 },
 "author": "Ken Wheeler",
 "license": "MIT",
 "dependencies": {
 "express": "~4.9.7",
 "express-handlebars": "~1.1.0",
 "mongoose": "^3.8.17",
 "node-jsx": "~0.11.0",
 "ntwitter": "^0.5.0",
 "react": "~0.11.2",
 "socket.io": "^1.1.0"
 },
 "devDependencies": {
 "browserify": "~6.0.3",
 "nodemon": "^1.2.1",
 "reactify": "~0.14.0",
 "uglify-js": "~2.4.15",
 "watchify": "~2.0.0"
 },
 "browserify": {
 "transform": [
 "reactify"
 ]
 }
}
```

If you're following along, simply run `npm install` and go get a glass of water. When you get back, we should have all of our dependencies in place, and its time to get our build on.

We now have a couple of commands we can use:

- `npm run watch` – Running this command starts a watchify watch, so when we edit our js files, they get browserified on save.
- `npm run build` – Running this command builds our bundle.js and minifies it for production
- `npm start` – Running this command sets up a watch and runs our app via nodemon
- `node server` – This command is what we use to run our app. In a production environment, I would recommend using something like forever or pm2.

## Setting Up Our Server

For the purposes of keeping our focus on React, I am going to assume we are working with a working knowledge of Express based server configurations. If you aren't familiar with what is going on below, you can read up on any of the helpful articles on this site about the subject, most notably [ExpressJS 4.0 – New Features & Upgrading from 3.0](https://scotch.io/bar-talk/expressjs-4-0-new-features-and-upgrading-from-3-0)

In the file below, we are doing 4 specific things:

- Setting up a server via Express
- Connecting to our MongoDB database
- Initializing our socket.io connection
- Creating our Twitter stream connection

### server.js

```
// Require our dependencies
var express = require('express'),
 exphbs = require('express-handlebars'),
 http = require('http'),
 mongoose = require('mongoose'),
 twitter = require('ntwitter'),
 routes = require('./routes'),
 config = require('./config'),
 streamHandler = require('./utils/streamHandler');

// Create an express instance and set a port variable
var app = express();
var port = process.env.PORT || 8080;

// Set handlebars as the templating engine
app.engine('handlebars', exphbs({ defaultLayout: 'main'}));
app.set('view engine', 'handlebars');

// Disable etag headers on responses
app.disable('etag');

// Connect to our mongo database
mongoose.connect('mongodb://localhost/react-tweets');

// Create a new ntwitter instance
var twit = new twitter(config.twitter);

// Index Route
app.get('/', routes.index);

// Page Route
app.get('/page/:page/:skip', routes.page);

// Set /public as our static content dir
app.use("/", express.static(__dirname + "/public/"));

// Fire it up (start our server)
var server = http.createServer(app).listen(port, function() {
 console.log('Express server listening on port ' + port);
});

// Initialize socket.io
var io = require('socket.io').listen(server);

// Set a stream listener for tweets matching tracking keywords
twit.stream('statuses/filter',{ track: 'scotch_io, #scotchio'}, function(stream){
 streamHandler(stream,io);
});
```

nTwitter allows us to access the Twitter streaming API, so we use the `statuses/filter` endpoint, along with the `track` property, to return tweets that use a #scotchio hash tag or mention scotch_io. You can modify this query to your liking by using the settings outlined within the [Twitter Streaming API](https://dev.twitter.com/streaming/overview).

## Models

In our app we use Mongoose to define our Tweet model. When receiving our data from our Twitter stream, we need somewhere to store it, and a static query method to return subsets of data based upon app parameters:

### Tweet.js

```
var mongoose = require('mongoose');

// Create a new schema for our tweet data
var schema = new mongoose.Schema({
 twid : String
 , active : Boolean
 , author : String
 , avatar : String
 , body : String
 , date : Date
 , screenname : String
});

// Create a static getTweets method to return tweet data from the db
schema.statics.getTweets = function(page, skip, callback) {

 var tweets = [],
 start = (page articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 10) + (skip articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 1);

 // Query the db, using skip and limit to achieve page chunks
 Tweet.find({},'twid active author avatar body date screenname',{skip: start, limit: 10}).sort({date: 'desc'}).exec(function(err,docs){

 // If everything is cool...
 if(!err) {
 tweets = docs; // We got tweets
 tweets.forEach(function(tweet){
 tweet.active = true; // Set them to active
 });
 }

 // Pass them back to the specified callback
 callback(tweets);

 });

};

// Return a Tweet model based upon the defined schema
module.exports = Tweet = mongoose.model('Tweet', schema);
```

After defining our schema, we create a static method called `getTweets`. It takes 3 arguments, `page`, `skip` & `callback`.

When we have an application that not only renders server side, but has an active stream saving to the database behind the scenes, we need to create a way to make sure that when we request our next page of tweets, it takes into account that Tweets may have been added since the app has been running on the client.

This is where the `skip` argument comes into play. If we have 2 new tweets come in, and then request the next page, we need to skip 2 indexes forward so that our application's pages are relative to it's original count, and we don't end up with duplicate tweets.

## Stream Handling

When our Twitter stream connection sends a new Tweet event, we need a method to take that data, save it to our database, and emit an event to the client side with the tweet data:

### streamHandler.js

```
var Tweet = require('../models/Tweet');

module.exports = function(stream, io){

 // When tweets get sent our way ...
 stream.on('data', function(data) {

 // Construct a new tweet object
 var tweet = {
 twid: data['id'],
 active: false,
 author: data['user']['name'],
 avatar: data['user']['profile_image_url'],
 body: data['text'],
 date: data['created_at'],
 screenname: data['user']['screen_name']
 };

 // Create a new model instance with our object
 var tweetEntry = new Tweet(tweet);

 // Save 'er to the database
 tweetEntry.save(function(err) {
 if (!err) {
 // If everything is cool, socket.io emits the tweet.
 io.emit('tweet', tweet);
 }
 });

 });

};
```

We start by requiring our Model, and when our stream emits an event, we grab the data we want to save, save it, and emit our socket event to the client with the Tweet we just saved.

## Routes

Our routes are where the majority of the magic is going to happen today. Lets take a look at `routes.js`:

### routes.js

```
var JSX = require('node-jsx').install(),
 React = require('react'),
 TweetsApp = require('./components/TweetsApp.react'),
 Tweet = require('./models/Tweet');

module.exports = {

 index: function(req, res) {
 // Call static model method to get tweets in the db
 Tweet.getTweets(0,0, function(tweets, pages) {

 // Render React to a string, passing in our fetched tweets
 var markup = React.renderComponentToString(
 TweetsApp({
 tweets: tweets
 })
 );

 // Render our 'home' template
 res.render('home', {
 markup: markup, // Pass rendered react markup
 state: JSON.stringify(tweets) // Pass current state to client side
 });

 });
 },

 page: function(req, res) {
 // Fetch tweets by page via param
 Tweet.getTweets(req.params.page, req.params.skip, function(tweets) {

 // Render as JSON
 res.send(tweets);

 });
 }

}
```

In the code above, we have two specific requirements:

- For our index route, we want to return a full page rendered from our React source
- For our page route, we want to return a JSON string containing additional tweets based upon our params.

By requiring our React components, and calling the `renderComponentToString` method, we are converting them to a string, which is then passed into our `home.handlebars` template.

We leverage our `Tweets` model to find tweets that have been stored in the database after coming in from our stream connection. Upon receiving the results of our query, we render our component to a `String`.

Notice that we are using non-JSX syntax when defining the component we want to render. This is because we are in our routes file and it is not being transformed.

Lets take a look at our `render` method:

```
// Render our 'home' template
res.render('home', {
 markup: markup, // Pass rendered react markup
 state: JSON.stringify(tweets) // Pass current state to client side
});
```

Not only are we passing our stringified markup, but we also pass a state property. In order for our server rendered application to pick up where it left off on the client, we need to pass the last state to the client so we can keep them in sync.

## Templates

Our app has two main templates, both of which are ridiculously simple. We start with a layout view, which wraps our target template.

### main.handlebars

```
<!doctype html>
<html lang="en">
 <head>
 <meta charset="utf-8">
 <meta name="viewport" content="width=device-width, initial-scale=1">
 <title>React Tweets</title>
 <link rel="stylesheet" type="text/css" href="css/style.css">
 </head>
 <body>
 {{{ body }}}
 <script src="https://cdn.socket.io/socket.io-1.1.0.js"></script>
 <script src="js/bundle.js"></script>
 </body>
</html>
```

`{{{body}}}` is where our template `home.handlebars` is loaded into. On this page we also add script tags for socket.io and our bundle.js that Browserify outputs.

### home.handlebars

```
<section id="react-app">{{{ markup }}}</div>
<script id="initial-state" type="application/json">{{{state}}}</script>
```

In our `home.handlebars` template, we take the component markup that we generated in our routes, and insert at `{{{markup}}}`.

Directly below we transfer our state. We use a script tag to hold a JSON string of our server's state. When initializing our React components on the client side, we pull our state from here and then remove it.

## Client Side Rendering

On the server we use `renderComponentToString` to generate markup for our components, but when using Browserify, we need a client side entry point to pick up the state we just saved, and mount our application component.

### app.js

```
/** @jsx React.DOM */

var React = require('react');
var TweetsApp = require('./components/TweetsApp.react');

// Snag the initial state that was passed from the server side
var initialState = JSON.parse(document.getElementById('initial-state').innerHTML)

// Render the components, picking up where react left off on the server
React.renderComponent(
 <TweetsApp tweets={initialState}/>,
 document.getElementById('react-app')
);
```

We start by getting our intitial state from the script element that we added in `home.handlebars`. We parse the JSON data and then call `React.renderComponent`.

Because we are working with a file that will be bundled with Browserify and will have access to JSX transforms, we can use JSX syntax when passing our component as an argument.

We initialize our component by adding the state we just grabbed to an attribute on our component. This makes it available via `this.props` within our component's methods.

Finally, our second argument mounts our rendered component to our `#react-app` div element from `home.handlebars`.

## Isomorphic Components

Now that we have all of our setup out of the way, it is time to get down to business. In our previous files, we rendered a custom component named TweetsApp.

Let's create our TweetsApp class.

```
module.exports = TweetsApp = React.createClass({
 // Render the component
 render: function(){

 return (
 <div className="tweets-app">
 <Tweets tweets={this.state.tweets} />
 <Loader paging={this.state.paging}/>
 <NotificationBar count={this.state.count} onShowNewTweets={this.showNewTweets}/>
 </div>
 )

 }
});
```

Our app is going to have 4 child components. We need a Tweets display, A singular Tweet, a loading spinner for loading paged results, and a notification bar. We wrap them in a div element with the `tweets-app` class.

[![react-tweets-demo](https://cask.scotch.io/2014/10/react-tweets-demo.png)](https://cask.scotch.io/2014/10/react-tweets-demo.png)

Very similarly to the way we passed our state via component props when transferring our server's state, we pass our current state down to the child components via props.

**But where does the state come from?**

In React, setting state via props is generally considered an anti-pattern. However when setting an initial state, and transferring a state from the server, this is not the case. Because the `getInitialState` method is only called before the first mount of our component, we need to use the `componentWillReceiveProps` method to make sure that if we mount our component again, that it will receive the state:

```
// Set the initial component state
 getInitialState: function(props){

 props = props || this.props;

 // Set initial application state using props
 return {
 tweets: props.tweets,
 count: 0,
 page: 0,
 paging: false,
 skip: 0,
 done: false
 };

 },

 componentWillReceiveProps: function(newProps, oldProps){
 this.setState(this.getInitialState(newProps));
 },
```

Aside from our tweets, which we pass down from the server, our state on the client contains some new properties. We use the `count` property to track how many tweets are currently unread. Unread tweets are ones that have been loaded via socket.io, that came in after the page loaded, but are not active yet. This resets every time we call `showNewTweets`.

The `page` property keeps track of how many pages we have currently loaded from the server. When starting a page load, in between the event kicking off, and when the results are rendered to the page, our `paging` property is set to true, preventing the event from starting again until the current request has completed. The `done` property is set to true when we have run out of pages.

Our `skip` property is like `count`, but never gets reset. This gives us a value for how many tweets are now in the database that we need to skip because our initial load doesn't account for them. This prevent us from rendering duplicate tweets to the page.

As it stands, we are good to go on the server side rendering of our component. However, our client side is where our state changes from UI interaction and socket events, so we need to set up some methods to handle that.

We can use the `componentDidMount` method to accomplish this safely, because it only runs when a component is mounted on the client:

```
// Called directly after component rendering, only on client
componentDidMount: function(){

 // Preserve self reference
 var self = this;

 // Initialize socket.io
 var socket = io.connect();

 // On tweet event emission...
 socket.on('tweet', function (data) {

 // Add a tweet to our queue
 self.addTweet(data);

 });

 // Attach scroll event to the window for infinity paging
 window.addEventListener('scroll', this.checkWindowScroll);

},
```

In the code above, we set up two event listeners to modify the state and subsequent rendering of our components. The first is our socket listener. When a new tweet is emitted, we call our `addTweet` method to add it to an unread queue.

```
// Method to add a tweet to our timeline
 addTweet: function(tweet){

 // Get current application state
 var updated = this.state.tweets;

 // Increment the unread count
 var count = this.state.count + 1;

 // Increment the skip count
 var skip = this.state.skip + 1;

 // Add tweet to the beginning of the tweets array
 updated.unshift(tweet);

 // Set application state
 this.setState({tweets: updated, count: count, skip: skip});

 },
```

Tweets in the unread queue are on the page, but not shown until the user acknowledges them in the NotificationBar component. When they do, an event is passed back via `onShowNewTweets` which calls our `showNewTweets` method:

```
// Method to show the unread tweets
 showNewTweets: function(){

 // Get current application state
 var updated = this.state.tweets;

 // Mark our tweets active
 updated.forEach(function(tweet){
 tweet.active = true;
 });

 // Set application state (active tweets + reset unread count)
 this.setState({tweets: updated, count: 0});

 },
```

This method loops through our tweets and sets their active property to true, and then sets our state. This makes any unshown tweets now show (via CSS).

Our second event listens to the `window` scroll event, and fires our `checkWindowScroll` event to check whether we should load a new page.

```
// Method to check if more tweets should be loaded, by scroll position
 checkWindowScroll: function(){

 // Get scroll pos & window data
 var h = Math.max(document.documentElement.clientHeight, window.innerHeight || 0);
 var s = document.body.scrollTop;
 var scrolled = (h + s) > document.body.offsetHeight;

 // If scrolled enough, not currently paging and not complete...
 if(scrolled && !this.state.paging && !this.state.done) {

 // Set application state (Paging, Increment page)
 this.setState({paging: true, page: this.state.page + 1});

 // Get the next page of tweets from the server
 this.getPage(this.state.page);

 }
 },
```

In our `checkWindowScroll` method, if we have reached the bottom of the page, aren't currently in the paging process, and haven't reached the last page, we call our `getPage` method:

```
// Method to get JSON from server by page
 getPage: function(page){

 // Setup our ajax request
 var request = new XMLHttpRequest(), self = this;
 request.open('GET', 'page/' + page + "/" + this.state.skip, true);
 request.onload = function() {

 // If everything is cool...
 if (request.status >= 200 && request.status < 400){

 // Load our next page
 self.loadPagedTweets(JSON.parse(request.responseText));

 } else {

 // Set application state (Not paging, paging complete)
 self.setState({paging: false, done: true});

 }
 };

 // Fire!
 request.send();

 },
```

In this method we pass our incremented page index, along with our skip property of our state object to our `/page` route. If there are no more tweets, we set `paging` to false and `done` to true, ending our ability to page.

If tweets are returned, we will return JSON data based upon the given arguments, which we then load with the `loadPagedTweets` method:

```
// Method to load tweets fetched from the server
 loadPagedTweets: function(tweets){

 // So meta lol
 var self = this;

 // If we still have tweets...
 if(tweets.length > 0) {

 // Get current application state
 var updated = this.state.tweets;

 // Push them onto the end of the current tweets array
 tweets.forEach(function(tweet){
 updated.push(tweet);
 });

 // This app is so fast, I actually use a timeout for dramatic effect
 // Otherwise you'd never see our super sexy loader svg
 setTimeout(function(){

 // Set application state (Not paging, add tweets)
 self.setState({tweets: updated, paging: false});

 }, 1000);

 } else {

 // Set application state (Not paging, paging complete)
 this.setState({done: true, paging: false});

 }
 },
```

This method takes our current set of tweets in our state object, and pushes our new tweets onto the end. I use a setTimeout before calling setState, so that we can actually see the loader component for at least a little while.

Check out our finished component below:

### TweetsApp

```
/** @jsx React.DOM */

var React = require('react');
var Tweets = require('./Tweets.react.js');
var Loader = require('./Loader.react.js');
var NotificationBar = require('./NotificationBar.react.js');

// Export the TweetsApp component
module.exports = TweetsApp = React.createClass({

 // Method to add a tweet to our timeline
 addTweet: function(tweet){

 // Get current application state
 var updated = this.state.tweets;

 // Increment the unread count
 var count = this.state.count + 1;

 // Increment the skip count
 var skip = this.state.skip + 1;

 // Add tweet to the beginning of the tweets array
 updated.unshift(tweet);

 // Set application state
 this.setState({tweets: updated, count: count, skip: skip});

 },

 // Method to get JSON from server by page
 getPage: function(page){

 // Setup our ajax request
 var request = new XMLHttpRequest(), self = this;
 request.open('GET', 'page/' + page + "/" + this.state.skip, true);
 request.onload = function() {

 // If everything is cool...
 if (request.status >= 200 && request.status < 400){

 // Load our next page
 self.loadPagedTweets(JSON.parse(request.responseText));

 } else {

 // Set application state (Not paging, paging complete)
 self.setState({paging: false, done: true});

 }
 };

 // Fire!
 request.send();

 },

 // Method to show the unread tweets
 showNewTweets: function(){

 // Get current application state
 var updated = this.state.tweets;

 // Mark our tweets active
 updated.forEach(function(tweet){
 tweet.active = true;
 });

 // Set application state (active tweets + reset unread count)
 this.setState({tweets: updated, count: 0});

 },

 // Method to load tweets fetched from the server
 loadPagedTweets: function(tweets){

 // So meta lol
 var self = this;

 // If we still have tweets...
 if(tweets.length > 0) {

 // Get current application state
 var updated = this.state.tweets;

 // Push them onto the end of the current tweets array
 tweets.forEach(function(tweet){
 updated.push(tweet);
 });

 // This app is so fast, I actually use a timeout for dramatic effect
 // Otherwise you'd never see our super sexy loader svg
 setTimeout(function(){

 // Set application state (Not paging, add tweets)
 self.setState({tweets: updated, paging: false});

 }, 1000);

 } else {

 // Set application state (Not paging, paging complete)
 this.setState({done: true, paging: false});

 }
 },

 // Method to check if more tweets should be loaded, by scroll position
 checkWindowScroll: function(){

 // Get scroll pos & window data
 var h = Math.max(document.documentElement.clientHeight, window.innerHeight || 0);
 var s = document.body.scrollTop;
 var scrolled = (h + s) > document.body.offsetHeight;

 // If scrolled enough, not currently paging and not complete...
 if(scrolled && !this.state.paging && !this.state.done) {

 // Set application state (Paging, Increment page)
 this.setState({paging: true, page: this.state.page + 1});

 // Get the next page of tweets from the server
 this.getPage(this.state.page);

 }
 },

 // Set the initial component state
 getInitialState: function(props){

 props = props || this.props;

 // Set initial application state using props
 return {
 tweets: props.tweets,
 count: 0,
 page: 0,
 paging: false,
 skip: 0,
 done: false
 };

 },

 componentWillReceiveProps: function(newProps, oldProps){
 this.setState(this.getInitialState(newProps));
 },

 // Called directly after component rendering, only on client
 componentDidMount: function(){

 // Preserve self reference
 var self = this;

 // Initialize socket.io
 var socket = io.connect();

 // On tweet event emission...
 socket.on('tweet', function (data) {

 // Add a tweet to our queue
 self.addTweet(data);

 });

 // Attach scroll event to the window for infinity paging
 window.addEventListener('scroll', this.checkWindowScroll);

 },

 // Render the component
 render: function(){

 return (
 <div className="tweets-app">
 <Tweets tweets={this.state.tweets} />
 <Loader paging={this.state.paging}/>
 <NotificationBar count={this.state.count} onShowNewTweets={this.showNewTweets}/>
 </div>
 )

 }

});
```

## Child Components

Our main component uses 4 child components to compose an interface based upon our current state values. Lets review them and how they work with their parent component:

### Tweets

```
/** @jsx React.DOM */

var React = require('react');
var Tweet = require('./Tweet.react.js');

module.exports = Tweets = React.createClass({

 // Render our tweets
 render: function(){

 // Build list items of single tweet components using map
 var content = this.props.tweets.map(function(tweet){
 return (
 <Tweet key={tweet.twid} tweet={tweet} />
 )
 });

 // Return ul filled with our mapped tweets
 return (
 <ul className="tweets">{content}</ul>
 )

 }

});
```

Our Tweets component is passed our current state's tweets via its `tweets` prop and is used to render our tweets. In our `render` method, we build a list of tweets by executing the map method on our array of tweets. Each iteration creates a new rendering of a child `Tweet` component, and the results are inserted into an unordered list.

### Tweet

```
/** @jsx React.DOM */

var React = require('react');

module.exports = Tweet = React.createClass({
 render: function(){
 var tweet = this.props.tweet;
 return (
 <li className={"tweet" + (tweet.active ? ' active' : '')}>
 <img src={tweet.avatar} className="avatar"/>
 <blockquote>
 <cite>
 <a href={"http://www.twitter.com/" + tweet.screenname}>{tweet.author}</a> 
 <span className="screen-name">@{tweet.screenname}</span> 
 </cite>
 <span className="content">{tweet.body}</span>
 </blockquote>
 </li>
 )
 }
});
```

Our singular Tweet component renders each individual tweet as a list item. We conditionally render an `active` class based upon the tweet's active status, that helps us hide it while it is still in the queue.

Each tweet's data is then used to fill in the predefined tweet template, so that our tweet display looks legit.

### NotificationBar

```
/** @jsx React.DOM */

var React = require('react');

module.exports = NotificationBar = React.createClass({
 render: function(){
 var count = this.props.count;
 return (
 <div className={"notification-bar" + (count > 0 ? ' active' : '')}>
 <p>There are {count} new tweets! <a href="#top" onClick={this.props.onShowNewTweets}>Click here to see them.</a></p>
 </div>
 )
 }
});
```

Our Notification Bar is fixed to the top of the page, and displays the current count of unread tweets, and when clicked, shows all the tweets currently in the queue.

We conditionally display an active class based upon whether we actually have any unread tweets, using the `count` prop.

On our anchor tag, an onClick handler calls our components own prop `onShowNewTweets` which is bound to `showNewTweets` in it's parent. This allows us to pass the event back upwards so it can be handled in our parent component, where we keep our state management.

### Loader

```
/** @jsx React.DOM */

var React = require('react');

module.exports = Loader = React.createClass({
 render: function(){
 return (
 <div className={"loader " + (this.props.paging ? "active" : "")}>
 <img src="svg/loader.svg" />
 </div>
 )
 }
});
```

Our loader component is a fancy svg loading animation. It is used during paging to indicate that we are loading a new page. An `active` class is set using our `paging` prop, that controls whether our component is shown or not (via CSS).

## Wrap Up

All that's left to do now is to run `node server` on your command line! You can run this locally or just check out the live demo below. If you want to see a tweet come in live, the easiest way is to just share this article with the demo open and you can see it in real time!

[![react-tweets-demo-2](https://cask.scotch.io/2014/10/react-tweets-demo-2.png)](https://cask.scotch.io/2014/10/react-tweets-demo-2.png)

In the next installment of **Learning React**, we will be learning how to leverage Facebook's **Flux Architecture** to enforce unidirectional data flow. Flux is Facebook's recommended complementary architecture for React applications. We will also be reviewing some open source Flux libraries that make implementing the Flux architecture a breeze.

Look for it soon!
