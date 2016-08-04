# Building a Software as a Service (SaaS) Startup – Part 3

[Original URL](http://slatepeak.com/guides/building-a-saas-startup-pt-3-restful-api/)

> This tutorial is part of a series. Part 1: How to Start a Node/Express Server Part 2: How to Authenticate an API with Passport and JWT If you have followed along with the first two parts of this...

This tutorial is part of a series.

[Part 1: How to Start a Node/Express Server](http://slatepeak.com/guides/building-a-software-as-a-service-saas-startup/)<br>
[Part 2: How to Authenticate an API with Passport and JWT](http://slatepeak.com/guides/building-a-software-as-a-service-saas-startup-pt-2/)

If you have followed along with the first two parts of this Building a Software as a Service (SaaS) Startup tutorial, you will have the beginning of an API built. You will have functional API authentication thanks to Passport and JSON Web Tokens. Now that we have the authentication in place, let's give it something to protect aside from a generic /dashboard route.

As always, if you would rather just see the full code, the GitHub repository has been updated and can be viewed [here](https://github.com/joshuaslate/saas-tutorial).

Before we get started with creating new routes, we need to do some cleanup work. We saw how long our _server.js_ file was starting to get, and we really don't want things to start getting too long and messy. When we're writing our web apps, we want to compartmentalize as much as we possibly can. With that said, we want to start separating our routes into their own file. In the /app folder, create _routes.js_ and open it up in your code editor. We will have to import the dependencies, models, and configuration our routes require, so let's do that first. Right at the top of the file, enter the following:

```
// Import dependencies
var passport = require('passport');
var express = require('express');
var config = require('../config/main');
var jwt = require('jsonwebtoken');

// Load models
var User = require('./models/user');
```

Right below this, we will use [module.exports](http://www.sitepoint.com/understanding-module-exports-exports-node-js/) to export our routes for our server.js file to use. At the same time, we are going to cut our existing route code and passport configuration from our _server.js_ file and paste it into our _routes.js_ file.

```
// Export the routes for our app to use
module.exports = function(app) {
 // API Route Section

 // Initialize passport for use
 app.use(passport.initialize());

 // Bring in defined Passport Strategy
 require('../config/passport')(passport);

 // Create API group routes
 var apiRoutes = express.Router();

 // Register new users
 apiRoutes.post('/register', function(req, res) {
 if(!req.body.email || !req.body.password) {
 res.json({ success: false, message: 'Please enter email and password.' });
 } else {
 var newUser = new User({
 email: req.body.email,
 password: req.body.password
 });

 // Attempt to save the user
 newUser.save(function(err) {
 if (err) {
 return res.json({ success: false, message: 'That email address already exists.'});
 }
 res.json({ success: true, message: 'Successfully created new user.' });
 });
 }
 });

 // Authenticate the user and get a JSON Web Token to include in the header of future requests.
 apiRoutes.post('/authenticate', function(req, res) {
 User.findOne({
 email: req.body.email
 }, function(err, user) {
 if (err) throw err;

 if (!user) {
 res.send({ success: false, message: 'Authentication failed. User not found.' });
 } else {
 // Check if password matches
 user.comparePassword(req.body.password, function(err, isMatch) {
 if (isMatch && !err) {
 // Create token if the password matched and no error was thrown
 var token = jwt.sign(user, config.secret, {
 expiresIn: 10080 // in seconds
 });
 res.json({ success: true, token: 'JWT ' + token });
 } else {
 res.send({ success: false, message: 'Authentication failed. Passwords did not match.' });
 }
 });
 }
 });
 });

 // Protect dashboard route with JWT
 apiRoutes.get('/dashboard', passport.authenticate('jwt', { session: false }), function(req, res) {
 res.send('It worked! User id is: ' + req.user._id + '.');
 });

 // Set url for API group routes
 app.use('/api', apiRoutes);
};
```

Great! We managed to shrink down our _server.js_ file pretty substantially. Note, however, that we had to change some of the [relative paths](http://www.iusmentis.com/technology/www/relativeurls/) in our code, since we are moving snippets from the root directory to the /app directory. We can also remove the following from _server.js_:

```
var User = require('./app/models/user');
var jwt = require('jsonwebtoken');
```

We also need to import our routes into our _server.js_ file. To do that, we will add the following between our mongoose.connect and where we start the server:

```
require('./app/routes')(app);
```

Cleanup is done for now! Since we are building a RESTful API, we need to consider the endpoints we will be building. At a high level, [RESTful APIs](https://en.wikipedia.org/wiki/Representational_state_transfer) use the standard HTTP requests (GET, POST, PUT, DELETE) to interact with objects. Seeing as there is an infinite pool of possibilities for what your SaaS could do, this is where I can teach you the basics but will have to leave you to do a lot of the footwork for yourself. In this tutorial, we will learn to make a sample of each type of request. You will be able to generalize and apply these samples to create your own API endpoints. We will be creating the backend for a simple chat system, because that's something a lot of SaaS companies can use. For example, maybe you want to provide your users with live chat support. From a learning perspective, this will allow us to create each of the main types of routes we will need. We will create a POST route to create new chat messages. We will create several GET routes (retrieve listing of all messages by user for an inbox view, and retrieve a single message by using request parameters). We will create a PUT route to allow the user to edit their message. Finally, we will create a DELETE route to erase the message entirely. In production, maybe you wouldn't want all of these features. An edit and delete feature might not be necessary, for example. That's fine! We are just going to build these routes for practice and generalization's sake.

Let's get started by building a message model. Navigate to your models folder (app/models) and create _chat.js_. We are going to create another Mongoose schema here, similar to how we did it in the previous tutorial on [API authentication using Passport and JSON web tokens](http://slatepeak.com/guides/building-a-software-as-a-service-saas-startup-pt-2/).

```
var mongoose = require('mongoose');

// Schema defines how chat messages will be stored in MongoDB
var ChatSchema = new mongoose.Schema({
 from: {
 type: String,
 required: true
 },
 to: {
 type: String,
 required: true
 },
 message_body: {
 type: String,
 required: true
 }
},
{
 timestamps: true // Saves createdAt and updatedAt as dates. createdAt will be our timestamp.
});

module.exports = mongoose.model('Chat', ChatSchema);
```

This should be pretty self explanatory by now, but we are basically telling Mongoose how our messages should be stored. If a request is made to add a new message that defies the schema we set up here, it will send back an error rather than saving the message.

Let's import our message model into our _routes.js_ file, right underneath our import of the user model.

```
var Chat = require('./models/chat');
```

Now we can get to work on building our messaging routes! Since our dashboard route was just a test for the previous part of the tutorial, let's change it to be:

```
 // Protect chat routes with JWT
 // GET messages for authenticated user
 apiRoutes.get('/chat', passport.authenticate('jwt', { session: false }), function(req, res) {
 Chat.find({$or : [{'to': req.user._id}, {'from': req.user._id}]}, function(err, messages) {
 if (err)
 res.send(err);

 res.json(messages);
 });
 });
```

This route allows us to send a GET request to <http://localhost:3000/api/chat>. It is important to note that our route is protected by the Passport-JWT strategy we set up in the last tutorial. If the authentication checks out, we [use Mongoose to query our MongoDB database](http://mongoosejs.com/docs/queries.html) for messages that are either to or from the authenticated user. If there is an error, that will be sent, otherwise the messages will be sent in JSON format for use by our front-end application (once we make it). Now let's build the POST route right below the GET route:

```
 // POST to create a new message from the authenticated user
 apiRoutes.post('/chat', passport.authenticate('jwt', { session: false }), function(req, res) {
 var chat = new Chat();
 chat.from = req.user._id;
 chat.to = req.body.to;
 chat.message_body = req.body.message_body;

 // Save the chat message if there are no errors
 chat.save(function(err) {
 if (err)
 res.send(err);

 res.json({ message: 'Message sent!' });
 });
 });
```

This route creates a new instance of the Chat model and sets its properties. Our _chat.from_ property is equal to the __id_ of the authenticated user. The _chat.to_ and _chat.message_body_ properties are set to the values passed through in the POST request's body. If there is an error in saving the message to the database (perhaps it didn't pass validation), the error will be returned. If everything works as it should, a message will be returned in JSON format letting you know the message was sent.

Now we can PUT, or update, our message. Right below the POST route, type the following:

```
 // PUT to update a message the authenticated user sent
 apiRoutes.put('/chat/:message_id', passport.authenticate('jwt', { session: false }), function(req, res) {
 Chat.findOne({$and : [{'_id': req.params.message_id}, {'from': req.user._id}]}, function(err, message) {
 if (err)
 res.send(err);

 message.message_body = req.body.message_body;

 // Save the updates to the message
 message.save(function(err) {
 if (err)
 res.send(err);

 res.json({ message: 'Message edited!' });
 });
 });
 });
```

This route will be accessed at <http://localhost:3000/api/chat/> Keep in mind, needs to be replaced with the MongoDB _id field of the message you want to access. You can find this in Robomongo pretty easily.

In addition to the JWT verification, this route uses Mongoose to query MongoDB for an ID that matches the message ID passed in the URL parameter that was also sent by the authenticated user. What is a URL parameter, you ask? If you have experience with PHP, you have probably seen '?id=xx' appended to a URL before. In PHP you can use $_GET['id'] to grab parameters (xx, in this example) from the url. Here, we can use req.params.name_of_param if we included /:name_of_param in our route. This query makes it so users can only edit their own messages. It wouldn't be good if anyone could edit any message!

Our last step will be to create a DELETE route. This will go right under your PUT route:

```
 // DELETE a message
 apiRoutes.delete('/chat/:message_id', passport.authenticate('jwt', { session: false }), function(req, res) {
 Chat.findOneAndRemove({$and : [{'_id': req.params.message_id}, {'from': req.user._id}]}, function(err) {
 if (err)
 res.send(err);

 res.json({ message: 'Message removed!' });
 });
 });
```

This route is similar to the PUT route above. It uses the Mongoose findOneAndRemove method to search your MongoDB for a document (the MySQL equivalent of a row) with the ID from the parameter set in the URL and the 'from' property equal to the ID of the authenticated user and then delete it.

Now that we have built our basic RESTful endpoints for a basic chat function, let's test them out! Open up [Postman](https://www.getpostman.com/) again and let's start out by hitting a GET request on our authentication endpoint to get a new JSON Web Token to authenticate our chat requests. If you need a quick refresher, you will need to click on the 'Body' tab, then click the bullet for 'x-www-form-urlencoded' and then fill out the keys, 'email' and 'password' and put in the login information you created in the second tutorial. Send the request and copy the token from the response.

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.05.50-PM-300x181.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.05.50-PM-768x463.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.05.50-PM-1024x618.png%201024w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.05.50-PM.png%201392w" class="aligncenter size-full wp-image-354" width="1392">

Let's test the post route at <http://localhost:3000/api/chat> (Note: you will need to populate the Authorization header with the new JWT as we did in the previous part of this tutorial). Next, click on the 'Body' button and make sure you have x-www-form-urlencoded selected. In the 'Key' fields, you will need 'to' and 'message_body'. Recognize these from our schema? For the value of 'to' either put the '_id' field of the user you previously created (this will be sending a message to and from the same user), or create a new user and enter their '_id' here. For the 'message_body', type any string you would like. If everything worked, you should see a success message.

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.22.30-PM-300x181.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.22.30-PM-768x463.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.22.30-PM-1024x618.png%201024w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.22.30-PM.png%201392w" class="aligncenter size-full wp-image-355" width="1392">

Now we have a message we will be able to see in our GET route! Switch the HTTP request type to GET and click send. Now we should see our message!

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.24.18-PM-300x181.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.24.18-PM-768x463.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.24.18-PM-1024x618.png%201024w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.24.18-PM.png%201392w" class="aligncenter size-full wp-image-356" width="1392">

Okay, I'm tired of seeing 'test message', how about you? Let's update it with a PUT request. For this, you will need to add the post ID as a parameter, so in my case, I will send my PUT request to <http://localhost:3000/api/chat/56c7b1b7f5fb7cac33781acc> with the Authorization header still set with the JWT from before and the 'x-www-form-urlencoded' in 'Body' set to have the key 'message_body' with the value set to whatever you want to change the message to say. If it works, you should see a message saying the message was edited. If you want, you can check the GET request on /api/chat to see the changes reflected.

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.30.42-PM-300x181.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.30.42-PM-768x463.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.30.42-PM-1024x618.png%201024w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.30.42-PM.png%201392w" class="aligncenter size-full wp-image-357" width="1392">

Come to think of it, this was a boring message to begin with. Let's just DELETE it entirely. Change the HTTP request type to DELETE and press send. You should see a message that the message was deleted.

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.33.27-PM-300x181.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.33.27-PM-768x463.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.33.27-PM-1024x618.png%201024w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-19-at-6.33.27-PM.png%201392w" class="aligncenter size-full wp-image-358" width="1392">

We have now covered and tested all of the simple routes we need to build a RESTful API! With that said, you will need to start building out the back-end of your application. We will likely end up revisiting the server-side in a bit, but we will be moving on to the client-side in our next tutorial. This means we will be diving into React.
