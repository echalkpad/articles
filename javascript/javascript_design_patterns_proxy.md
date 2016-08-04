# JavaScript Design Patterns: Proxy

[Original URL](http://www.joezimjs.com/javascript/javascript-design-patterns-proxy/)

> This is the 9th installment of the JavaScript Design Patterns series and today we're featuring the Proxy pattern. The word "proxy" can be defined as a substitute, and that...

This is the 9th installment of the JavaScript Design Patterns series and today we're featuring the Proxy pattern. The word "proxy" can be defined as a substitute, and that essentially explains what a proxy is. A proxy is an object that has the same interface as another object and is used in place of that other object. The only question is why we would use a proxy instead of the original object.

Before I answer that question, I wish to remind/inform everyone that this is part of a long series of posts. You can access the list of other posts in this series at the [bottom of the post](http://www.joezimjs.com/javascript/javascript-design-patterns-proxy/#series "List of JavaScript Design Patterns series Posts"). It might not be a bad idea to review through them before (or after) you read this post. The proxy pattern doesn't really depend on the knowledge of any of the other patterns, so you can wait until after you're done here if you'd like. If you've already read all the other posts in the series then you deserve some brownie points! Too bad I don't really have any to give away.

## Why Use a Proxy?

Returning to the question of why we would bother to use a proxy, we can give a few different scenarios where the proxy can come in handy: delaying instantiation of a large object, accessing a remote object, and access control.

Before we get into each of those situations, we'll look at an example of a proxy with no purpose, just so you get the gist of what a proxy is. First we need to create a class – `CarList` – and then we'll just make the proxy class that wraps around it.

```
/* For the sake of keeping us on track, we won't
 show implementation code in great detail. */
var CarList = function() {
 //creation
};

CarList.prototype = {
 getCar: function(...) {
 // get a vehicle from the list using the 
 // given parameters
 },

 search: function(...) {
 // search through the cars using the query
 },

 addCar: function(...) {
 // add a car to the database
 },
 .
 .
 .
};

var CarListProxy = function() {
 this.carList = new CarList();
};

CarListProxy.prototype = {
 getCar: function(...) {
 return this.carList.getCar(...);
 },

 search: function(...) {
 return this.carList.search(...);
 },

 addCar: function(...) {
 return this.carList.addCar(...);
 },
 .
 .
 .
};
```

## The Virtual Proxy

I'm sure everyone reading this has some bit of imagination, so let's pretend that `CarList` has 10 times as many methods and most of them are very large and complicated. I know this circumstance might be a bit extreme, but I'm just exaggerating a bit to make a point. The point is that we have a large object that would use up quite a few CPU cycles just to instantiate it. Wouldn't it make sense to put off that instantiation until we're sure it'll be used? Well, that's the point of the Virtual Proxy. Someone can instantiate a proxy and the normal large object won't be instantiated until a method is called that requires its creation. Let's convert our old useless proxy to a virtual proxy.

```
var CarListProxy = function() {
 // Don't initialize the CarList yet.
 this.carList = null;
};
CarListProxy.prototype = {
 // this function is called any time any other
 // function gets called in order to initialize
 // the CarList only when needed.
 _init: function() {
 if (!this.carList) {
 this.carList = new CarList();
 }
 },

 getCar: function(...) {
 // every function makes this call to _init()
 this._init();
 return this.carList.getCar(...);
 },

 search: function(...) {
 this._init();
 return this.carList.search(...);
 },

 addCar: function(...) {
 this._init();
 return this.carList.addCar(...);
 },
 .
 .
 .
}
```

Of course this may not necessarily be the best method of delaying the initialization of a large object. Let's imagine (again) that `CarList` didn't have countless complex methods, but just the data that it contains is large, such as an entire database of the make and model of every single commercially-produced vehicle in existence. In this case, we can just create a method that initiates all of that data, but only call that method when we are in need of that data. I'm not saying to put down the power of the proxy pattern, but more so to help you be a better programmer by teaching you that a proxy isn't the answer to all problems.

## Remotes Proxies in JavaScript

The second scenario I mentioned was accessing a remote object. This scenario makes more sense when using something along the lines of Java and SOAP, mostly because when we talk about something being remote, we're generally talking about something on the other end of an ethernet cable and it's highly unlikely we'll be accessing JavaScript objects on a server, though with [Node.js](http://nodejs.org/ "Official site for Node.js") gaining in popularity, it's becoming more and more likely. For our JavaScript proxy, we'll just use an object that gives a simplified way of accessing a web service's API. This may go a bit against the definition of the proxy implementing the same interface as the object it's being substituted for, but it'll have to do right now. I'd consider this to be more like a [facade](http://www.joezimjs.com/javascript/javascript-design-patterns-facade/ "JavaScript Design Patterns: Facade"), though others have called this a form of proxy before.

This time our `CarListProxy` isn't implementing the interface of an object, but instead just pulling information from the fictional web service on www.WeSeriouslyListEverySingleVehicleModelEver.com.

```
// The real CarList is contained on the server, so
// CarListProxy gets the information from the server
var CarListProxy = function (){};

CaListProxy.prototype = {
 getCar: function(...) {
 // Once again we don't show implementation code
 // so that you don't get distracted by it
 ajax('http://www.weseriouslylisteverysinglevehiclemodelever.com/getCar/'
 + args);
 },

 search: function(...) {
 ajax('http://www.weseriouslylisteverysinglevehiclemodelever.com/search/'
 + args);
 },

 addCar: function(...) {
 ajax('http://www.weseriouslylisteverysinglevehiclemodelever.com/addCar/'
 + args);
 },
 .
 .
 .
}
```

That's about all I can show you for the Remote Proxy for JavaScript. So what about that final situation: controlling access to the original object? That's up next!

## Controlling Access to a JavaScript Object via Proxy

There can be any number of reasons that we don't want to allow access to the original object, but for the sake of this example we're waiting for a specific date, because the site that this script is on isn't technically supposed to be live yet. I'm not sure why the developers felt it was necessary for all of the JavaScript files to be loaded on an "Under Construction" page, but who am I to argue with fictional characters?

In order to truly control access, we have to make sure that there is no way to access the original object in any way except through the proxy, so we'll close it all up in a self-invoking anonymous function, but attach the proxy as a property to `window` in order to give access to it from the outside world.

```
// self invoking anonymous function
(function() {
// We already know what the CarList looks like, so I
// won't rewrite it here
var CarList = ...

var CarListProxy = function() {
 // Don't initialize the CarList yet.
 this.carList = null;
 this.date = new Date();
};
CarListProxy.prototype = {
 // this function is called any time any other
 // function gets called in order to initialize
 // the CarList only when needed. The CarList will
 // not be initialized if it isn't time to yet.
 _initIfTime: function() {
 if (this._isTime() && !this.carList) {
 this.carList = new CarList();
 return true;
 }
 return false;
 },
 // Check to see if we've reached the right date yet
 _isTime() {
 return this.date > plannedReleaseDate;
 },

 getCar: function(...) {
 // if _initIfTime returns a falsey value, getCar will 
 // return a falsey value, otherwise it will continue
 // and return the expression on the right side of the 
 // && operator
 return this._initIfTime() && this.carList.getCar(...);
 },

 search: function(...) {
 return this._initIfTime() && this.carList.search(...);
 },

 addCar: function(...) {
 return this._initIfTime() && this.carList.addCar(...);
 },
 .
 .
 .
}

// Make the CarListProxy publicly available 
window.CarListProxy = CarListProxy;

// you could also do the below statement so people don't even
// know they're using a proxy:
window.CarList = CarListProxy;
}());
```

And there you have it: all three flavors of the proxy pattern with examples. Personally I don't see a whole lot of circumstances where a proxy will be all that useful, except maybe as a facade for web services, especially with the growing popularity of the use of web services. I hope that you all can find a reason to use it because knowledge is power, but only if it can be applied.

As usual, your comments and sharing are greatly appreciated. In fact, this time I'll give a bit of incentive. We're drawing nearer to the end of the JavaScript Design Patterns series and after that I might be a bit hard-pressed to find material. If there's any JavaScript-related topic or subject you'd particularly like to read about, let me know. I might just take your suggestion. Also, while you're thinking about what you'd like to ask me to post, go ahead and share this post with your friends via the sharing buttons below. Finally, if you have a bit of time, maybe you'd like to read some of the other JavaScript Design Patterns posts. The list of them should be immediately below:

JavaScript Design Patterns series:

### _Related_
