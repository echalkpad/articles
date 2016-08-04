# Building news feeds & activity streams with Meteor

[Original URL](http://blog.getstream.io/post/136611889848/building-news-feeds-activity-streams-with-meteor)

> Today we are happy to announce stream-meteor. This integration library makes it easy to build scalable newsfeeds and activity streams for your Meteor app. It's powered by getstream.io and...

Today we are happy to announce [stream-meteor](http://github.com/getstream/stream-meteor). This integration library makes it easy to build scalable newsfeeds and activity streams for your [Meteor](http://www.meteor.com/) app. It's powered by getstream.io and scales to millions of users.

In this tutorial we will demonstrate how you can use the integration package. To keep this tutorial short and skip the boilerplate, we will use the awesome demo application [localmarket](https://www.meteor.com/localmarket) as a starting point and use stream-meteor to build the "What's cooking" section.

![What's Cooking page](http://i.imgur.com/XTb9SSg.jpg?1)

## Install

Lets start by installing the localmarket example app to your local machine. Run

`git clone git@github.com:matthisk/localmarket.git`

to install the example app. For the next step be sure to have [Meteor installed](https://www.meteor.com/install) on your machine. Enter the created directory and install the integration package by running:

`cd localmarket`<br>
`meteor add getstream:stream-meteor`

## Settings

Before our Meteor backend can communicate with [Stream](http://getstream.io) we need to set public and private keys in our settings.json. Create a file called `settings.json` in the root directory of your app and add the following code:

[Stream](http://getstream.io)'s free plan includes 3 million feed updates. You can login with Github and get your API key in the [dashboard](https://getstream.io/dashboard/).

## Running

You can now run the example app by executing the command

`meteor run --settings settings.json`

from the app's directory. Notice that we need to supply the settings file explicitely to the meteor command. If you forget to specify the settings file API calls to Stream will fail.

You can now open [`http://localhost:3000`](http://localhost:3000/) in your browser to have a look at the example app without the getstream.io integration. You will be able to authenticate using your Twitter account, bookmark recipes and add a photo of recipes you cooked. For the remainder of this post we will focus on the `What's Cooking` page which is the news feed page of the localmarket app.

![Localmarket homepage](http://i.imgur.com/Sj2kuAw.jpg)

## Following feeds

Real apps have many users, each following other users or topics. In this tutorial we don't have real users to follow so in order to see some activity on the "What's cooking" feed we will cheat a little and follow ourselves. This ensures that activities performed by the user show up on his own "What's cooking" feeds.

To do this we have to call followUser on the feedManager after we have created a new user. Luckily the localmarket app already includes a hook that is executed after a user is created. Open up the file 'users.js' and add the following lines to the method:

_remark_: In a real application the "What's cooking" feed would display the content from followed users.

Be sure to create a new account if you already created an account for your localmarket app, otherwise the _What's Cooking_ page will not display any activities.

## Adding entries to the feed

Feeds contain activities. Activities are Stream's main type of data and follow the [activities specification](http://activitystrea.ms/specs/json/1.0/). An example activity on localmarket is "Matt bookmarked the recipe 'Classic Ragu Bolognese'" or "Eric shared the recipe 'Lasagne'". We will explain more about this later on.

There are two different types of activity in the localmarket app: Shares and Bookmarks. Whenever a bookmark is created or recipe is shared, our application will convert that into an activity and send it to Stream's APIs.

## Activity collections

Let's start by implementing shares in the "What's cooking feed". The localmarket example app already contains a Collection which stores shares. The integration package allows us to hook into Collections so that any document added to it is converted to an activity and send to Stream's API. To do this we call the method registerActivity on the Stream object, add the following code to 'activities.js' after the Activities collection has been defined:

To be able to send valid activities to the API we need to inform the hook of which fields/values to use for the required activity properties. We set the activities _verb_ to 'cook' and retrieve the _actor_ from the 'userId' property on the inserted document. _object_ is in this case the serialized document we have inserted into our collection.

You can now create an account for you localmarket app by linking it to Twitter. Try adding a picture to a recipe and see if the activity automatically shows up in Stream's [databrowser](http://getstream.io/dashboard/).

## Manually adding activities

In the file `bookmarks.js` you will find a Meteor method `bookmarkRecipe` this method is called when a user clicks the bookmark icon on a recipe page. We do not have to alter the way the app stores the bookmark on the user's document we just have to add couple of lines to the end of the method to register an activity with Stream:

The integration package exposes all its methods on the `Stream` object, one of the properties is an instance of the `FeedManager` named `feedManager`. The FeedManager exposes methods to retrieve the different news feeds from our current user. In this case we want to add an activity to the current users' user feed.

Activities registered should have atleast the following fields to be valid: _actor_, _object_ and _verb_. Here we set _actor_ to a document reference of the current user's user document (i.e. the name of the collection colon the id of the document). The _verb_ of the action is 'bookmark' and the object is the name of the recipe.

At this point any documents added to the Activities collection and any bookmark created through the `createBookmark` method are added to the user feed. The next step is to define a way of retrieving the activities from getstream.io and showing them to the user on the 'What's cooking' page.

## Feed subscriptions

Feed activities are exposed through a publication in the 'Stream.feeds' namespace. When we subscribe to this publication we receive the feed's last 20 activities and receive reactive updates on any new activities added to the feed. Lets have a look at how to set this up for the localmarket example app.

In the file `router.js` there is already a variable called `feedSubscription`, we are going to update this subscription to point to `'Stream.feeds.flat'`:

This will retrieve the last 20 activities registered with the user's flat feed. To actually show the activities on the "What's cooking" page we have to retrieve them from a different collection in 'whats-cooking.js':

A subscription to a Stream feed stores its results in a collection on `Stream.feeds` with the name of the feed group you subscribed to.

## Templates

To display the feed activities correctly we have to make a few changes to the existing templates and add one new template to display bookmarks inside the news feed.

The feed will contain two different type of activities, 'bookmark' and 'cook', we should update the 'activity' template to be able to render both activities, change the template in 'activity.html':

The `equals` method is not available by default in Handlebars so add it to "helpers.js":

The bookmark template does not exist yet, so we should add it to the 'activity.html' file:

The cook template is now our old 'activity' template, add the following code to 'activity.html':

And add the following template helpers for the 'bookmark' template to 'activity.js':

Since we renamed the 'activity' template to 'cook' we also need to replace the helpers associated to the 'activity' template in 'activity.js'. Replace the helpers associated to this template with the following code:

Now any activities added on your account in the app should show up on the "What's cooking" page.

![What's Cooking page](http://i.imgur.com/q5ec6DP.jpg)

## Home page

As a bonus you can also show the last 3 activities of the user's flat feed on the homepage. First add the feed subscription to the page's router in `router.js` by replacing the _HomeController_ with the following code:

Then replace the `activities` method in 'home.js' to retrieve items from the flat feed Collection:

Now the last three activities will display on the home page.

![Homepage](http://i.imgur.com/tgvFrFC.jpg)

## Conclusion

Meteor is an amazing framework and I hope you enjoyed this tutorial on how to integrate Meteor with [Stream](http://getstream.io). Using getstream.io you can build, scale and personalize your newsfeeds and activity streams.

This example still misses a few features. For one, there is no way to follow other users. Also we didn't add a notification feed. We'll leave that to you as an exercise.

Be sure to read the full documentation on the [github page](https://github.com/getstream/stream-meteor). And have a look at the [Pinterest example app build with stream-meteor](https://github.com/GetStream/Stream-Example-Meteor), which does implement all of these features.

If this is your first time using getstream.io you might also want to try this [quick 5m tutorial](http://getstream.io/get_started/).
