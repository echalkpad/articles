# Implementing Sign in with Twitter for Node.js

[Original URL](https://www.codementor.io/nodejs/tutorial/how-to-implement-twitter-sign-expressjs-oauth)

> More and more recently, I've been seeing small applications implementing sign in with Twitter, Google, GitHub, LinkedIn, etc. I think this is a great move for a number of reasons. First, so I...

![](https://www.filepicker.io/api/file/YufF59XRS28TBJYp5dbg) More and more recently, I've been seeing small applications implementing sign in with Twitter, Google, GitHub, LinkedIn, etc. I think this is a great move for a number of reasons. First, so I don't have to sign up for yet another account at yet another site, and secondly because app developers no longer have to implement their own user sign up scheme with all the trimmings: forgotten passwords, activation emails, and so on. I'm sure a lot of developers out there have those pieces of code as part of their toolbox, but for those who don't, OAuth sign ins are so much easier to build. I've written a guide today that describes how to implement sign in with Twitter. And because I'm really into Node.js at the moment, it's going to be written with JavaScript.

For those of you who just want to cut to the chase, [I've made the server-side code available here.](https://github.com/chrisharrington/node-twitter-sign-in-example)

There's a few things we'll need to get started.

- **An application with Twitter.** You'll need a developer application to use with Twitter. If you're unfamiliar with how OAuth sign ins work, typically what happens is that you'll click on a "sign in with Twitter" button and be directed to a Twitter page with the application's name on it, with an optional logo. This page is why you need to create an application. Go [here](https://apps.twitter.com/) to manage your Twitter applications. Once your application is created, make a note of the "consumer token" and "consumer secret" values under "Keys and Access Tokens"; we'll need them later.
- **A basic Express-backed Node.js application.** There's nothing that says we need to use Express specifically, but this guide will revolve around building a Node.js application from the ground up with Express. I'm going to presume that the reader already has Node.js installed, so now we'll just need to initialize the Node.js application, as such:

<!--  -->

```
npm init
npm install express node-twitter-api bluebird --save
```

The [node-twitter-api](https://www.npmjs.com/package/node-twitter-api) package is what we're going to use to obfuscate a bunch of the more complicated inner workings of an OAuth implementation. It's built specifically for Twitter. I've also got [bluebird](https://github.com/petkaantonov/bluebird) in there, too, which is a comprehensive Promise implementation.

## The Plan

So the idea here is that when a user shows up to your site, he or she can sign in using Twitter instead of registering using a custom implementation. This has a couple of parts.

- The user clicks the sign in with Twitter button.
- Your front-end code sends a request to your server-side code indicating that a user wants to sign in with Twitter.
- Your server-side code sends an OAuth request to Twitter, indicating that we need a **request token**. This is the step we need the **consumer token** and **consumer secret** from your Twitter application.
- If all goes well on Twitter's end, we get a **request token** and a **request secret** from them. At this point, we redirect the user to a predetermined (by Twitter) URL with that token as part of the query string.
- The page we redirect the user to requires that he or she sign into his or her Twitter account. After signing in and agreeing to the application's terms, the user is redirected back to the application with the same **request token** and _another_ value, this time the **OAuth verifier**.
- Now we use the **request token** and **request secret** along with the **OAuth verifier** to get an **access token**, also from Twitter.
- The previous request returns an **access token** and an **access secret**, both of which are used in the third and final request to Twitter, which is to verify credentials and get the authenticated user's information.

Phew. Lots of steps.

### The user clicks the sign in button

So the very first step for this flow is when the user clicks the "Sign in with Twitter!" button. Here's some really basic code which uses jQuery to send an AJAX request to our server-side code to generate a **request token** and redirect the user to the Twitter sign in page.

```
<!DOCTYPE html>

<html>
 <head>
 <script src="https://code.jquery.com/jquery-2.1.4.min.js"></script>
 <script>
 $(function() {
 $("#sign-in-with-twitter").on("click", function() {
 window.location.href = "<some domain>/request-token";
 });
 });
 </script>
 </head>
 <body>
 <button id="sign-in-with-twitter">Sign in with Twitter!</button>
 </body>
</html>
```

Once the user clicks the sign in button, a request is directed to GET <some domain>/request-token. Don't worry; this request will redirect the user to the Twitter sign in page on the server-side.

### Get the request token and redirect the user to sign in

Now, on the server-side, here's the route to handle the /request-token request.

```
"use strict";

var Twitter = require("node-twitter-api"),
 secret = include("secret");

module.exports = function(app) {
 var twitter = new Twitter({
 consumerKey: secret.twitter.consumerKey,
    consumerSecret: secret.twitter.consumerSecret,
    callback: secret.twitter.callbackUrl
 });

 var _requestSecret;

 app.get("/request-token", function(req, res) {
 twitter.getRequestToken(function(err, requestToken, requestSecret) {
 if (err)
 res.status(500).send(err);
 else {
 _requestSecret = requestSecret;
 res.redirect("https://api.twitter.com/oauth/authenticate?oauth_token=" + requestToken);
 }
 });
 });

 ...
};
```

Here, we're creating a node-twitter-api instance using the stored **consumer key**, **consumer secret** and **callback URL**, of which the first two are assigned to your Twitter application.

**Note:** Make sure you don't store these values in a publicly visible place, like GitHub. If you do, those with nefarious purposes can hijack your Twitter application.

On a successful request, we take the **request token** and send it as the **oauth_token** query string value in the redirect URL. This sends the user to a Twitter sign in page, and allows that user to accept or reject the application's permissions request. Once they've done that, Twitter will send the user to the URL specified as "callback" when creating the Twitter instance.

### Handle the result of the redirect from Twitter to your callback

After the user has signed in, he or she will be redirected to the URL we've specified when we created the Twitter instance, so now we should create that page.

```
<!DOCTYPE html>

<html>
 <head>
 <script src="https://code.jquery.com/jquery-2.1.4.min.js"></script>
 <script>
 $(function() {
 $.get("<some domain>/access-token" + location.search).done(function(user) {
 // Do something with your signed in user's information here.
 });
 });
 </script>
 </head>
 <body></body>
</html>
```

All our callback page is doing here is requesting an **access token** from our server. We're tacking on the query string from our callback page to the request because it contains the required **oauth_token** and **oauth_verifier** values, both of which are directly from Twitter.

### Handle the request for an access token

Now, we need to handle the request from our callback page for an access token.

```
"use strict";

var Twitter = require("node-twitter-api"),
 secret = include("secret");

module.exports = function(app) {
 var twitter = new Twitter({
 consumerKey: secret.twitter.consumerKey,
    consumerSecret: secret.twitter.consumerSecret,
    callback: secret.twitter.callbackUrl
 });

 var _requestSecret;

 ...

 app.get("/access-token", function(req, res) {
 var requestToken = req.query.oauth_token,
     verifier = req.query.oauth_verifier;

 twitter.getAccessToken(requestToken, _requestSecret, verifier, function(err, accessToken, accessSecret) {
 if (err)
 res.status(500).send(err);
 else
 twitter.verifyCredentials(accessToken, accessSecret, function(err, user) {
 if (err)
 res.status(500).send(err);
 else
 res.send(user);
 });
 });
 });
};
```

This code exists in the same file as the route for request-token, but I've hidden some of the other code to keep the snippet's size manageable. To get the **access token** and **access secret**, we need previously retrieved **request secret** as well as the passed in **request token** and **verifier** values. We pass those off to the `getAccessToken` function, which talks to Twitter and if all goes well, returns the sought after values. We use those to call `verifyCredentials` and retrieve the signed in user's information, direct from Twitter and send it straight back to our callback page.

## Next steps?

From here, it's usually a good idea to store the user somewhere for easy retrieval so the user doesn't have to sign in with Twitter every time he or she arrives at your site, but that's beyond the scope of this article.

As I mentioned above, I've cleaned up the server-side code and [stored it on GitHub](https://github.com/chrisharrington/node-twitter-sign-in-example) in case anyone wants to take a closer look. It's not exactly the same as what's here, as I'd put a bunch of stuff in a single file for the sake of easy teaching, but it's pretty close. If you have any questions, let me know in the comments and I'll see if I can help out. Thanks for reading!
