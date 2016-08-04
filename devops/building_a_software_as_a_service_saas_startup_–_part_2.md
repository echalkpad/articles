# Building a Software as a Service (SaaS) Startup – Part 2

[Original URL](http://slatepeak.com/guides/building-a-software-as-a-service-saas-startup-pt-2/)

> In the first part of this series, I covered starting a basic server with Node and Express, as well as downloading some of the dependencies we will need while we're building our software as a...

In the [first part of this series](http://slatepeak.com/guides/building-a-software-as-a-service-saas-startup/), I covered starting a basic server with Node and Express, as well as downloading some of the dependencies we will need while we're building our software as a service site in 100% JavaScript. In this part, we will start building our authentication model. I had to dig around the web to find the implementation I was looking for, and it got pretty convoluted at times. I think I hacked together a pretty decent API authentication system by combining different aspects from several different sources. I am going to walk you through the process I followed to make this much easier for you than it was for me, but I will also provide links to some of the tutorials I found helpful in case you want to dig deeper. We are going to use [Passport](http://passportjs.org/docs) to create a [JSON Web Token](https://jwt.io/) based, stateless, and (I think) scalable authentication system.

I want to kind of point out why we are starting here and how this is all going to (hopefully) come together. Basically, we will use Node, Express, and MongoDB to create our back-end. We will be creating a RESTful API, which our front-end (React and perhaps React Native for iOS/Android mobile apps) will interact with. I'm pretty much seeing the back-end and front-end as two separate things. We could have a bunch of different front-ends if we wanted. We could code an iOS app with Swift, an Android app with Java, or a game with C++ (if that even made sense to do with the API we're building), and they could all send actions and retrieve data from our back-end API. With that in mind, it seems important to me to build out at least the authentication part of our app first, so we can start with a secure back-end and have something for the front-end(s) we create to communicate with. We _could_ build our front end in React first with dummy data and come back to build out a back-end, but that's not how I'm going to steer this guide.

The code can all be found on the GitHub repository I have done a great job of updating so far, which can be found [here](https://github.com/joshuaslate/saas-tutorial).

Let's scaffold a bit and add some more packages with NPM.

Let's start by creating some folders. Firstly, make sure you're in the main _saas_ folder in your Terminal. By the way, [iTerm2](https://www.iterm2.com/) is great and free to use. Now let's get a move on. Punch the following into your terminal:

```
mkdir app config public src
cd app
mkdir models
cd ..
```

Now we have our folders set up how we need them to be. Let's get those packages installed. Passport is pretty much the de facto standard for Node applications that don't roll their own authentication or use paid solutions like StormPath or Auth0 (probably easier to implement for some, but I won't cover this).

In your terminal, you should be back in the root project directory, _saas_. Type in the following:

```
npm install body-parser jsonwebtoken passport passport-jwt bcrypt morgan --save
```

_(Note: Windows users, use bcrypt-nodejs instead of bcrypt and refer to [this tutorial](https://scotch.io/tutorials/easy-node-authentication-setup-and-local) for that implementation. Bcrypt doesn't play nicely with Windows)_

Now that we have installed these dependencies, we need to require them in our main _server.js_ file. Add the following between the mongoose import and port declaration:

```
var bodyParser = require('body-parser');
var morgan = require('morgan');
var passport = require('passport');
var jwt = require('jsonwebtoken');
```

Now let's start logging requests and start retrieving data from POST requests. Directly below the dependency imports is where this will go.

```
// Use body-parser to get POST requests for API use
// Use body-parser to get POST requests for API use
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());

// Log requests to console
app.use(morgan('dev'));
```

We will add a quick home page route so I can give a quick demonstration of what morgan does. Add this next.

```
// Home route. We'll end up changing this to our main front end index later.
app.get('/', function(req, res) {
 res.send('Relax. We will put the home page here later.');
});
```

Before we start our server back up this time, we are going to install nodemon, which will watch our files for changes. Without [nodemon](http://nodemon.io/), we would have to manually start and stop our server each time we make changes. What a pain! Oh well, that's not what we have to do from here out. In your terminal:

```
npm install -g nodemon
```

_(Note: The -g means global and this will allow you to use nodemon in any future node projects you start on the machine you're using. It will not only be installed for this saas project, in other words. [Read more here](https://nodejs.org/en/blog/npm/npm-1-0-global-vs-local-installation/).)_

Now, instead of starting the server with the node or npm command, you can start it with:

```
nodemon server.js
```

Great! That will streamline development a bit. Now open up your browser and go to <http://localhost:3000/>

You should see the following:

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-15-at-8.23.52-PM-300x181.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-15-at-8.23.52-PM-768x463.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-15-at-8.23.52-PM-1024x618.png%201024w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-15-at-8.23.52-PM.png%201392w" class="aligncenter size-full wp-image-312" width="1392">

This isn't very exciting, but check out your terminal!

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-15-at-8.27.20-PM-300x172.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-15-at-8.27.20-PM-768x441.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-15-at-8.27.20-PM.png%20773w" class="aligncenter size-full wp-image-313" width="773">

_Note: Did you know that Cmd + K will clear your terminal? It can come in handy for clearing out the messy wall of text you'll see when you get an error._

You can see the type of requests made to your local server, the destination (/ or root in this case), and the response time. This is the morgan package at work. If you refresh, or change pages when we add more routes, you will see those requests here as well.

Let's set up our main config file next. Navigate to the config folder (use 'cd' [**c**hange **d**irectory] in the terminal from here out). Create /config/main.js and /config/passport.js by entering the following in terminal:

```
touch main.js passport.js
```

Open up main.js in your code editor and type the following:

```
module.exports = {
'secret': 'putsomethingtopsecrethere',
'database': 'mongodb://127.0.0.1:27017/saas-tutorial'
};
```

_Note: If you would rather use a more similar setup to what you may use in production, you can get 500mb of MongoDB storage for free from [MongoLab](https://mongolab.com/) and store your test data on a different server. Please also note that in production, it is probably advisable to use [environment/config variables](http://stackoverflow.com/questions/4870328/how-to-read-environment-variable-in-node-js) for security purposes._

Next, you will add the config file we just created to your imports so we can use the properties we created in it. This can go right under the passport import in the server.js file.

```
var config = require('./config/main');
```

Now we will be able to connect to our database and sign/verify our tokens with our secret key. You should never share your secret key. Please make your own and don't use this silly one! Create a [long and strong password](http://passwordsgenerator.net/) and use that instead. Time to connect to the database. Put this right above the home route we created in our server.js file.

```
// Connect to database
mongoose.connect(config.database);
```

Now, in our /app/models folder, let's create a new file, _user.js_. In this file, we will define the schema for our users. We will also hash the users' passwords here with bcrpyt. Keep in mind, we will be using email addresses to register and log users in, not usernames. Feel free to switch that for your own app. Open the new file up and enter the following:

```
var mongoose = require('mongoose');
var bcrypt = require('bcrypt');

// Schema defines how the user data will be stored in MongoDB
var UserSchema = new mongoose.Schema({
 email: {
 type: String,
 lowercase: true,
 unique: true,
 required: true
 },
 password: {
 type: String,
 required: true
 },
 role: {
 type: String,
 enum: ['Client', 'Manager', 'Admin'],
 default: 'Client'
 }
});

// Saves the user's password hashed (plain text password storage is not good)
UserSchema.pre('save', function (next) {
 var user = this;
 if (this.isModified('password') || this.isNew) {
 bcrypt.genSalt(10, function (err, salt) {
 if (err) {
 return next(err);
 }
 bcrypt.hash(user.password, salt, function(err, hash) {
 if (err) {
 return next(err);
 }
 user.password = hash;
 next();
 });
 });
 } else {
 return next();
 }
});

// Create method to compare password input to password saved in database
UserSchema.methods.comparePassword = function(pw, cb) {
 bcrypt.compare(pw, this.password, function(err, isMatch) {
 if (err) {
 return cb(err);
 }
 cb(null, isMatch);
 });
};

module.exports = mongoose.model('User', UserSchema);
```

Phew. That was a lot, but it should be pretty understandable. It is requiring the user's information to be structured in a specific way. The schema expects the email, password, and role to be stored as a string. The email and password will be required. The role will default to client. The email will be passed in as lowercase and must be unique in order to be saved. This will prevent duplicate accounts from being created. To learn more about Mongoose schemas, read their [documentation](http://mongoosejs.com/docs/guide.html). The .pre hook from Mongoose will allow us to hash the user's password before saving it in the database. This is essential for security. The comparePassword method we created at the bottom will compare authentication post input to the password stored in the database to determine whether or not a valid password was passed through. If it matches, it will return true with isMatch. If it does not match, it will return an error. Next, we will set up our passport strategy. Create _passport.js_ in /config. Enter the following in the file and save it:

```
var JwtStrategy = require('passport-jwt').Strategy;
var ExtractJwt = require('passport-jwt').ExtractJwt;
var User = require('../app/models/user');
var config = require('../config/main');

// Setup work and export for the JWT passport strategy
module.exports = function(passport) {
 var opts = {};
 opts.jwtFromRequest = ExtractJwt.fromAuthHeader();
 opts.secretOrKey = config.secret;
 passport.use(new JwtStrategy(opts, function(jwt_payload, done) {
 User.findOne({id: jwt_payload.id}, function(err, user) {
 if (err) {
 return done(err, false);
 }
 if (user) {
 done(null, user);
 } else {
 done(null, false);
 }
 });
 }));
};
```

All right! With this configured, we can finish getting this set up by moving back to our _server.js_ file to initialize passport, import our user model, and create a few routes. Underneath our config import, enter the following:

```
var User = require('./app/models/user');
```

Now, we can initialize passport. Enter the following between the morgan request logging initialization and the home route we created:

```
// Initialize passport for use
app.use(passport.initialize());
```

And now we can import our JWT passport strategy. Enter this below our mongoose connection:

```
// Bring in defined Passport Strategy
require('./config/passport')(passport);
```

Now we can start on our routes. We will start by creating the route group called apiRoutes. We will now be working down without jumping all over the place in the code. That said, this goes beneath the passport strategy import we just did:

```
// Create API group routes
var apiRoutes = express.Router();
```

Next, we can create our registration route:

```
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
```

As you can see, this first checks to make sure the 'email' and 'password' fields in the post data are not empty. If they contain inputs, it passes them through our User object, which validates the input with the schema we created. Keep in mind, this will make sure there are no other users with the email address that was entered. If everything checks out, a new user will be saved. Remember, the password will be hashed thanks to the .pre hook in our User model.

Next, we will create our authentication route. This is where things get cool. Let's type the code out first. Try to figure out what it will do as you're typing it, and then read what I have to say about it once you have thought about it for yourself.

```
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
```

First, we use Mongoose's findOne to search for the user requested in the post body. If no user is found, an error will be thrown. Next is where we use our comparePassword method to verify the user's password input matches the data we have stored in our database. If the passwords match and no errors have been thrown, our jsonwebtoken package will encode a JSON Web Token (JWT) that expires in a week. It will return JSON output that can be used for authenticating our user.

Here is the last bit of code! Let's type it out and then talk about it:

```
// Protect dashboard route with JWT
apiRoutes.get('/dashboard', passport.authenticate('jwt', { session: false }), function(req, res) {
 res.send('It worked! User id is: ' + req.user._id + '.');
});

// Set url for API group routes
app.use('/api', apiRoutes);
```

We use our JWT strategy and disable sessions (we don't need them; RESTful APIs are stateless). If our token is accepted, we will see a short message and the requested user's "_id" field from MongoDB. Finally, we tie it up by setting the URL for the apiRoutes to /api. For example, authentication will be at <http://localhost:3000/api/authentication>

The only thing below this should be where we are starting our server. Save it all and let's get to the fun part: testing.

You will need Postman (for Chrome), which can be downloaded for free [here](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=en). Make sure your server is running (with nodemon server.js in case we need to debug), and then open up Postman. Change the URL to <http://localhost:3000/api/register> and set the request type to POST. Click the x-www-form-urlencoded button and set two keys, one for email and one for password. Enter the values you want to use. Press send. You should get this:

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-8.58.26-PM-300x181.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-8.58.26-PM-768x463.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-8.58.26-PM-1024x618.png%201024w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-8.58.26-PM.png%201392w" class="aligncenter size-full wp-image-321" width="1392">

Now try it again without changing anything-

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.00.56-PM-300x181.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.00.56-PM-768x463.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.00.56-PM-1024x618.png%201024w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.00.56-PM.png%201392w" class="aligncenter size-full wp-image-322" width="1392">

Yay, our error checking is working! Now let's try to authenticate and get a JWT! Change the URL to <http://localhost:3000/api/authenticate> and click send again. You should get a JWT.

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.02.44-PM-300x181.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.02.44-PM-768x463.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.02.44-PM-1024x618.png%201024w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.02.44-PM.png%201392w" class="aligncenter size-full wp-image-323" width="1392">

Now copy that JWT and change the URL to <http://localhost:3000/api/dashboard> and change your request type to GET. Click on the headers tab and set the first header to Authorization and paste your JWT into the value field. If everything works, you should see your success message and the _id field from your user in MongoDB (you can browse through your MongoDB with [Robomongo](https://robomongo.org/) or MongoLab's dashboard).

<embed src="http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.05.40-PM-300x181.png%20300w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.05.40-PM-768x463.png%20768w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.05.40-PM-1024x618.png%201024w,%20http://slatepeak.com/wp-content/uploads/2016/02/Screen-Shot-2016-02-16-at-9.05.40-PM.png%201392w" class="aligncenter size-full wp-image-324" width="1392">

We can play around a bit and test things out. If you change even a single character of your JWT and try to send your request again, you will get an "Unauthorized" error. What if you change your expiresIn property to 20 seconds? You won't be authorized with that token anymore after 20 seconds! Go on and test it. I'll wait.

Congratulations! We now have the foundations of an authentication system with JWT we can use, but it is not complete yet. In later parts of this tutorial, we will complete this. If you want to think ahead, consider the following:

- Our authenticated routes require a JSON Web Token to be passed with each request. How will we store the token to send it for verification with each request from the client side? (Look into cookies, localStorage, and sessionStorage)
- How will our users logout? (Destroying the token seems like a good idea to me at this point)

As I mentioned before, these are questions that will be answered as we continue this series and dive into the front-end. We have more API building to do for now though, so we will continue with that.

I was unable to find an article online that walked me through this entire process the way I wanted, so I combined all the resources I could find to create this post. Here are some of the resources I found useful while putting this guide together (in addition to the documentation for the packages we used):

[Scotch's Easy Node Authentication](https://scotch.io/tutorials/easy-node-authentication-setup-and-local) [Jereon Pelgrim's Token Based Sessionless Auth with Express and Passport](http://jeroenpelgrims.com/token-based-sessionless-auth-using-express-and-passport/) [Devdactic's RESTful API User Authentication](https://devdactic.com/restful-api-user-authentication-1/)[](http://jeroenpelgrims.com/token-based-sessionless-auth-using-express-and-passport/)

As a reminder, I am not claiming to be an expert in Node. I am really new to it still and would love any opportunity to learn from my mistakes, so please comment below with any changes you would recommend and let's discuss them.
