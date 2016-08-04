# Payments with Stripe Checkout

[Original URL](https://themeteorchef.com/recipes/payments-with-stripe-checkout/)

> What we're going to learn In this recipe, we'll learn how to integrate payments using Stripe Checkout. We'll learn how to wire the interface up on the client, how to make it accept Bitcoin, and even...

## What we're going to learn

In this recipe, we'll learn how to integrate payments using [Stripe Checkout](https://stripe.com/checkout). We'll learn how to wire the interface up on the client, how to make it accept Bitcoin, and even how to handle payments on the server.

![Slack](https://themeteorchef.com/assets/slack-logo.svg)

### Join The Meteor Chef on Slack

If you get confused or stuck while reading--or just want to talk about Meteor--join us on Slack! [Get an invite now](http://slack.themeteorchef.com).

## Additional Packages **

This recipe relies on several other packages that come as part of [Base](https://themeteorchef.com/base), the boilerplate kit used here on The Meteor Chef. The packages listed below are merely recipe-specific additions to the packages that are included by default in the kit. Make sure to reference the [Packages Included list](https://themeteorchef.com/base/packages-included) for Base to ensure you have fulfilled all of the dependencies.

## Prep

## What are we building?

The Ghostbusters are blowing up. New York is [under siege from a ton of ghosts](https://youtu.be/EwKR_y93izs?t=48s) and other spooks and the team has asked us if we can help out. Their biggest problem currently is that they cannot keep up with orders. The phone keeps ringing off the hook and Janine can't answer quickly enough. They've lost a significant amount of business as a result and want to fix things fast.

The system they need is actually pretty simple. They recently switched to a fixed set of packages that customers can purchase. Our task will be to implement a way for customers to purchase one of these packages with one click. After thinking about it for a bit, we decided to recommend Stripe Checkout to the Ghostbusters. It's free, quick to implement, and fits perfectly with what they need.

In this recipe, we're going to learn how to wire up Stripe Checkout, all the way up through creating a charge. The Ghostbusters want to be as future proof as possible, so they've asked that we add support for Bitcoin, too! Before we get to work, here's a quick example of what we're after:

![Watch out, ghosts!](https://tmc-post-content.s3.amazonaws.com/ghostbusters-checkout-demo.gif) Watch out, ghosts! Ready to get to work? Let's do it!

## Ingredients

Before we start building, make sure that you've installed the following packages and libraries in your application. We'll use these at different points in the recipe, so it's best to install these now so we have access to them later.

### Meteor packages

Terminal

```
meteor add mrgalaxy:stripe
```

We'll rely on the `mrgalaxy:stripe` package to give us access to Stripe Checkout on the client as well as [Stripe's Node.js library](https://github.com/stripe/stripe-node) on the server.

## Configuring our application settings

Before we start to write code, we'll want to set up a [settings file](https://themeteorchef.com/snippets/making-use-of-settings-json/) in our application for storing our Stripe keys. These keys--a public key and a secret key--will allow us to identify our application with Stripe and process payments. To do this, hop over to your Stripe dashboard--this assumes you've created a Stripe account, if you haven't [sign up here](https://dashboard.stripe.com/register)--and open up the [API Key Settings](https://dashboard.stripe.com/account/apikeys) section.

![Finding API keys in the Stripe dashboard.](https://tmc-post-content.s3.amazonaws.com/Screen-Shot-2015-12-15-16-19-03.png) Finding API keys in the Stripe dashboard. What we need from this page is at a minimum is our "Test Secret Key" and our "Test Publishable Key." If you intend to use the code we write in production, you will also want to make note of your "Live Secret Key" and "Live Publishable Key." Pay close attention coming up if this is the case. We'll be storing our keys in a `settings-development.json` file for local development, but for production values you'll want to use a `settings-production.json` file ([Why](https://themeteorchef.com/snippets/making-use-of-settings-json/#tmc-settingsjson-in-development-vs-production)?).

### Adding our keys to settings-development.json

Next, we'll want to take our "Test" keys from Stripe and store them in a file called `settings-development.json` in our project's root. Here's what we're after:

settings-development.json

```
{
 "public": {
 "stripe": "pk_test_<Your Key Here>"
 },
 "private": {
 "stripe": "sk_test_<Your Key Here>"
 }
}
```

That's it! By adding these, we'll have access to our keys within our application by starting our app with `meteor --settings settings-development.json`. When we do, we'll have access to our keys via `Meteor.settings.public.stripe` and `Meteor.settings.private.stripe`. So it's clear, the reason we want to store these here as opposed to in our application source is two-fold.

First, it ensures that if we have to change our keys out later, we won't have to edit multiple files. Second--and most importantly--it ensures that our code is more secure by keeping our keys in one place. If a key were to get leaked, we can swap it out quickly and easily without worrying about breaking our application.

Cool! At this point we're one step closer to wiring up the Ghostbuster's new payment system. Don't forget: make sure to start your app with this file using `meteor --settings settings-development.json` or you'll run into errors later. With this all set up, we can get into the code!

## Adding our template

First up, we need to create a template. Fortunately our template is pretty simple. Because the Ghostbusters have a fixed range of services, we won't need to worry about wiring up to a database. Let's take a look at our entire template now and then step through it. Don't worry, there's _a lot_ here but we'll explain each piece and show how it's working next.

/client/templates/public/services.html

```
<template name="services">
 <div class="row">
 <div class="col-xs-12 col-md-6">
 <img width="150" src="https://tmc-post-content.s3.amazonaws.com/ghostbusters-logo.png" alt="Ghostbusters">
 <h4 class="page-header">We're ready to believe you!</h4>
 {{#unless processing}}
 <p class="alert alert-info">We offer the following paranormal elimination services:</p>

 <ul class="list-group price-list">
 <li class="list-group-item clearfix">
 <p class="pull-left"><strong>$3,000</strong> — Full Torso Apparition Removal</p>
 <a href="#" data-service="full-torso-apparition" class="btn btn-success pull-right">Buy Now</a>
 </li>
 <li class="list-group-item clearfix">
 <p class="pull-left"><strong>$4,250</strong> — Free-Floating Repeater Removal</p>
 <a href="#" data-service="free-floating-repeater" class="btn btn-success pull-right">Buy Now</a>
 </li>
 <li class="list-group-item clearfix">
 <p class="pull-left"><strong>$5,000</strong> — Full Roaming Vapor Removal</p>
 <a href="#" data-service="full-roaming-vapor" class="btn btn-success pull-right">Buy Now</a>
 </li>
 </ul>

 <p class="alert alert-warning">To demo, use any email address along with the card number <strong>4242 4242 4242 4242</strong>, any <em>future</em> expiration date, and any 3 digit security code (e.g 555)</p>
 {{else}}
 <p class="alert alert-warning"><i class="fa fa-refresh fa-spin"></i> Processing payment...</p>
 {{/unless}}
 </div>
 </div>
</template>
```

Let's start at the top. First, notice that our list of services is wrapped in an `{{#unless}}` block pointing to a helper that we'll define later called `processing`. In a bit, we'll wire this up to a Reactive Var that we'll trigger whenever a customer clicks on the "Buy Now" button next to an item. When we do, we want to hide the services list and display a "Processing Payment..." message to acknowledge the user's action.

So it's clear, let's look at the JavaScript running our `{{#unless}}` and `{{#if}}` blocks here. From there, we'll start to build up our logic for the checkout.

### Wiring up our template

First, let's focus on our Reactive Var. What we need to do is define these and then create the helpers that they'll be wired up to. Again, these will simply be used for toggling state in our application via the `{{#unless processing}}` block.

/client/templates/public/services.js

```
Template.services.onCreated( () => {
 let template = Template.instance();

 template.processing = new ReactiveVar( false );
});

Template.services.helpers({
 processing() {
 return Template.instance().processing.get();
 }
});
```

This is all we need for now! We start by defining our Reactive Var's by using `new ReactiveVar()` in our template's `onCreated` method, assigning them to variables defined _on_ our template instance. Why? This ensures that our variables only exist for the lifecycle of the template. If we were to move to another page--effectively "destroying" the template--these values would cease to exist. Pretty wild, eh? We could do this just the same with something like Session variables, however, those will stick around (which we don't want).

Toward the bottom, we're setting up our helper `processing`. Here, we access the template instance and the our variable using `Template.instance().processing.get()`. This is part of the Reactive Var API. As we'll see in a bit, the `.get()` method here has a counterpart called `.set()`. Again, this is all very similar to Session, however, it allows us to keep everything local to the template. Just note that right now all we're doing is pulling our value in and returning it from our helper. Remember, this value is _reactive_, meaning, when we change it our helper will reflect the change (toggling the state in our interface).

Now for the fun part! Next, we need to wire up Stripe Checkout. This involves two steps: configuring the checkout and a callback for handling the token Stripe will create for us and handling the opening of the checkout window.

## Adding a call to Stripe Checkout

We need to do two things: configure Stripe checkout and then set up a way to call it. Let's get our configuration in place. We'll be working in our `services` template's JavaScript file again, focusing on the `onCreated` callback like we did up above.

/client/templates/public/services.js

```
Template.services.onCreated( () => {
 let template = Template.instance();

 [...]

 template.checkout = StripeCheckout.configure({
 key: Meteor.settings.public.stripe,
 image: 'https://tmc-post-content.s3.amazonaws.com/ghostbusters-logo.png',
 locale: 'auto',
 token( token ) {
 // We'll pass our token and purchase info to the server here.
 }
 });
});

[...]
```

Easy peasy. Notice that here, we're piggy backing our `template` variable we set up earlier (the one we assigned our Reactive Var's to). To ensure that we have access to our instance of Stripe Checkout throughout our `services` template, we call to `StripeCheckout.configure()`, assigning it to `template.checkout` (we'll see why this is helpful in a bit). For our configuration, we pass four items: our _public_ key (required), an image (optional, but nice to have), a locale (optional, configures the checkout's display language), and a callback method `token()`.

Notice that for the `key` property here, we're passing `Meteor.settings.public.stripe` which is pointing to the `public` block in our `settings-development.json` file and the `stripe` property within that. For the `locale` property here, we've selected `auto` so that the checkout will automatically match the customer's language preferences. Finally, the `token()` method is being defined here with an argument `token`. Can you guess what happens here?

Once our customer successfully completes the payment form, Stripe will send their information securely to their servers to generate a token. A [token](https://stripe.com/docs/api#tokens) is a representation of the card or Bitcoin address that is sent to Stripe. This allows us, in turn, to securely share that information with Stripe without bumping into any compliance issues. Neat! This `token()` method is what Stripe will call as soon as it has a token. For now, let's pause here. What we need to do now is wire up the actual opening of the checkout. Once we have that in place, we'll jump back up here and explain how to handle our token.

### Opening the checkout

At this point, we've initialized our checkout but we need to actually _open_ it when our customer does something. Remember, the Ghostbusters have asked that we make this as simple as possible. What we're going to do is simply listen for a click event on any button with a `data-service` attribute. When a click event is detected, we'll grab the value of `data-service` from the button--to let us know what service the customer is purchasing--and then open up our checkout.

/client/templates/public/services.js

```
Template.services.onCreated( () => {
 let template = Template.instance();

 template.selectedService = new ReactiveVar( false );
 template.processing = new ReactiveVar( false );

 template.checkout = StripeCheckout.configure(...);
});

[...]

Template.services.events({
 'click [data-service]' ( event, template ) {
 const pricing = {
 'full-torso-apparition': {
 amount: 300000,
 description: "Full Torso Apparition Removal"
 },
 'free-floating-repeater': {
 amount: 425000,
 description: "Free-Floating Repeater Removal"
 },
 'full-roaming-vapor': {
 amount: 500000,
 description: "Full Roaming Vapor Removal"
 }
 };

 let service = pricing[ event.target.dataset.service ];

 template.selectedService.set( service );
 template.processing.set( true );

 template.checkout.open({
 name: 'Ghostbusting Service',
 description: service.description,
 amount: service.amount,
 bitcoin: true
 });
 }
});
```

Quite a few things being added here, so pay close attention. First, notice that up in our `onCreated` function we're adding a new Reactive Var and assigning it to `template.selectedService`. When we wire up our `token()` method later, we'll use this to let us know which service the user has clicked on. Down below in our `events` block, we've got quite a bit going on. First, notice that we're defining our event by watching for `click` events on any element with a `data-service` attribute using `click [data-service]`.

Inside of the event handler, we define a new object `pricing` and assign it three sub-objects, each one named after the value passed to `data-service` in our template markup. Following along? For each service, we define an `amount` property and a `description`. Notice that here, we're defining amount in `cents` as this is how Stripe expects to receive the value.

A little further down, we're grabbing the name of the service that the user clicked from the "Buy Now" button using `event.target.dataset.service` and pointing to that value in the `pricing` object we just defined. See what's happening? If `event.target.dataset.service` returns `free-floating-repeater`, we'll be setting our `service` variable equal to the contents of that block, or, `{ amount: 425000, description: "Free-Floating Repeater Removal" }`. Pretty cool, eh?

/client/templates/public/services.html

```
<a href="#" data-service="full-roaming-vapor" class="btn btn-success pull-right">Buy Now</a>
```

Because this happens on the click of the button, we can be certain that it will map to the correct service (as long as our `data-service` attribute is set correctly on each button in our template). Awesome. Next, notice that we're _finally_ starting to toggle these Reactive Var's we've defined. Here, we're setting the `selectedService` so we can reference it up in our `token()` method later, as well as setting `processing` to true. Remember, when we toggle processing we're hiding our services list and revealing our "Processing payment..." message.

Finally, the good stuff. This is where we trigger Stripe Checkout to actually open up! Remember that up top, we assigned our instance of the checkout to `template.checkout`. Using this, we can simply call `template.checkout.open` and pass in our configuration. For the Ghostbuster's purposes, we just need to pass four items: the generic `name` of the service, the `description` of the service the customer clicked on (taken from our `service` variable we just set), the `amount` we're looking to charge the customer (also taken from our `service` variable), and finally `bitcoin` being set to true.

That last one is Stripe doing a magic trick. This one line tells Stripe that we want to accept Bitcoin. They handle the rest behind the scenes. Sweet! At this point, we're all wired up to receive a token from Stripe. Before we process the charge on the server, let's take a look at how we pass our token along from our `token()` callback up top.

### Getting the token from Stripe

Now for the good stuff. At this point, we've successfully configured and implemented a way to fire our checkout. Now, we need to wire up what happens after we get a token back from Stripe. Just a little more weight:

/client/templates/public/services.js

```
Template.services.onCreated( () => {
 let template = Template.instance();

 [...]

 template.checkout = StripeCheckout.configure({
 [...]
 token( token ) {
 let service = template.selectedService.get(),
 charge = {
 amount: token.amount || service.amount,
 currency: token.currency || 'usd',
 source: token.id,
 description: token.description || service.description,
 receipt_email: token.email
 };

 Meteor.call( 'processPayment', charge, ( error, response ) => {
 if ( error ) {
 template.processing.set( false );
 Bert.alert( error.reason, 'danger' );
 } else {
 Bert.alert( 'Thanks! You\'ll be ghost free soon :)', 'success' );
 }
 });
 },
 closed() {
 template.processing.set( false );
 }
 });
});

[...]
```

Inside of our `token()` method (the callback Stripe fires once it has successfully created a card token), we start by grabbing the currently selected service from our Reactive Var. Once we have it, we define a `charge` object to toss up to the server. Pay close attention here. Notice that for the `amount`, `currency` and `description` fields we're using an `||` or operator to switch between two different values. What gives?

This is because depending on whether the customer chooses to pay with a card or Bitcoin, Stripe sends us back differently shaped responses for the `token` value. To make sure that we support both--[Egon](https://tmc-post-content.s3.amazonaws.com/egon-spengler.png) was kind of a jerk about this whole Bitcoin thing--we need to account for the different response. To compensate for the missing values in the response from Stripe, notice that we just default back to the value stored on the object from our Reactive Var `selectedService`. Easy enough.

Okay! Now we're getting to the important part: actually charging a customer. Even though Stripe is giving us back a token, we technically haven't charged the customer for anything yet. That will need to happen on the server. To get it working, here, we define a method call to `processPayment`, passing up our `charge` object we just defined. Notice that in our error and success callback below, we're triggering the appropriate Reactive Var to reset our service list's state.

Last but not least, notice that we've added another callback method `closed()` which Stripe fires when the payment window is closed. This ensures that if the customer decides to _not_ go forward with the payment (or the payment is successful), we reset our "processing" state accordingly with our Reactive Var. Simple, but important so we don't frustrate the Ghostbuster's customers.

That's it for the client! Let's hop up to the server and wrap this fella up. When we finish, we'll have a fully functioning check out system with both credit card _and_ Bitcoin payments. Rad.

## Handling the payment on the server

Last step! This part is quick and easy. Let's dump out all of the code for our method and walk through it.

/server/methods/utility/stripe.js

```
let Stripe = StripeAPI( Meteor.settings.private.stripe );

Meteor.methods({
 processPayment( charge ) {
 check( charge, {
 amount: Number,
 currency: String,
 source: String,
 description: String,
 receipt_email: String
 });

 let handleCharge = Meteor.wrapAsync( Stripe.charges.create, Stripe.charges ),
 payment = handleCharge( charge );

 return payment;
 }
});
```

Starting at the top. We begin by assigning a new variable `Stripe` equal to an instance of `StripeAPI`. See what this is doing? This is giving us access to Stripe's API here on the server. To make it work, we pass in the value of `Meteor.settings.private.stripe`, which, remember, is equal to our _secret_ key. Once that's configured, we get to work defining our method.

First, we set it up taking in a `charge` argument (the object we defined on the client) and [check the passed value](https://themeteorchef.com/snippets/using-the-check-package/) to ensure it contains the values and types that we expect. Once we have it, we do a little bit of synchronous muscle flexing. Here, we're making use of Meteor's `wrapAsync` method which allows us to ["convert" an _asynchronous_ function into a _synchronous_ function](https://themeteorchef.com/snippets/synchronous-methods/#tmc-using-wrapasync). The difference? A synchronous function must complete before our method returns. An _asynchronous_ function, however, can be called but doesn't block the order of operations.

Here, we want this to be synchronous so we can confirm the payment on the client without a lot of fuss. `wrapAsync` is a bit weird at first pass. What's happening is that we pass `Stripe.charges.create` ([the Stripe method we want to call](https://stripe.com/docs/api#create_charge) on their API) and then we pass that method's _context_. Yeah, _okay_. Bear with me. What this does behind the scenes is to return a function that's automatically wrapped using two things: a future (a Node.js convention for creating synchronous function calls) and `Meteor.bindEnvironment` (a function that ensures the current Meteor environment is accessible within our asynchronous function's callback).

The point being that we get back a synchrounous function which we're assigning to our `handleCharge` variable. Because we're getting back a _function_ we can call it, which we do on the line below. Notice that when we call it `handleCharge()` we're passing in our `charge` object we sent over from the client. In a slightly convoluted--but super handy--way, we're saying something like this:

Asynchronous Version

```
Stripe.charges.create( charge, ( error, reponse ) => {
 if ( error ) {
 return error;
 } else {
 return response;
 }
});
```

The big difference between this and the above code with `Meteor.wrapAsync` is that it will indefinitely pause our method from returning `payment` until `payment` receives either an error or a response. Pretty slick, right?

With this in place, we're done! If we hop back to the client and attempt to process either a card-based or Bitcoin-based payment, they will succeed. To confirm, we can check our Stripe dashboard to see that the payment went through (make sure that you're on the "Test" side by flipping the switch in the top left of the dashboard interface):

![A credit card charge landing in Stripe's dashboard via our checkout.](https://tmc-post-content.s3.amazonaws.com/Screen-Shot-2015-12-15-18-30-57.png) A credit card charge landing in Stripe's dashboard via our checkout. Somebody better let Slimer know he won't be hanging out in the city for long. Let's call the Ghostbusters and let them know their checkout is ready!

![](https://tmc-post-content.s3.amazonaws.com/slimer-blast.gif)

## Wrap up & summary

In this recipe, we learned how to wire up Stripe's Checkout feature. We looked at configuring our API keys to work with Stripe securely, how to configure and fire an instance of Stripe checkout, and how to toggle state in our interface using Reactive Variables. To wrap up, we learned how to actually process a payment using Stripe's API and information we grabbed from the checkout window.

![Watsi](https://themeteorchef.com/assets/watsi-logo.svg)

### Enjoy this post?

I'd really appreciate it if you showed your thanks by [joining Team Meteor Chef on Watsi](https://watsi.org/team/the-meteor-chef) and donating healthcare to someone who needs it in exchange!

## Questions & Comments
