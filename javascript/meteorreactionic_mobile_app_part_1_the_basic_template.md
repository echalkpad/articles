# Meteor-React-Ionic Mobile App Part 1: The Basic Template

[Original URL](https://medium.com/@SamCorcos/meteor-react-ionic-mobile-app-part-1-the-basic-template-9355ebf3397f#.szcjpw1xx)

> Meteor now has reasonable support for React.js, so it's about time to start using it for hybrid mobile apps, which is one of the huge advantages of using Meteor. This template app (repo) will use...

![](https://cdn-images-2.medium.com/max/2000/1*9dF3HOObBwWX8MHGThid3A.png)

Meteor now has reasonable support for React.js, so it's about time to start using it for hybrid mobile apps, which is one of the huge advantages of using Meteor. This template app ([repo](https://github.com/samcorcos/meteor-react-ionic-basic)) will use **React.js** in place of Meteor's built-in templating engine **Blaze**, **ReactRouter** instead of **iron:router** or **meteorhacks:flow-router**, and will use [**Ionic**](http://ionicframework.com/), which is a widely used hybrid mobile app framework. _This should take about 10 minutes to complete._

![](https://cdn-images-2.medium.com/max/600/1*tS83xErk43qjtWBXepymFA.gif)

We are going to build the most basic possible template, on which we can build bigger and better apps in the future. I found that simply getting started with the **Meteor-React-Ionic** **stack** was surprisingly difficult, so this should make it easier. Your app will look like the gif to the left when you are finished.

From this base template we can add additional functionality, which I intend to write about in subsequent articles, including: [passing through data](https://medium.com/@SamCorcos/meteor-react-ionic-mobile-app-part-2-passing-in-data-a0478b17a53d) with Mongo.Collections, [user accounts](https://medium.com/@SamCorcos/meteor-react-ionic-mobile-app-part-4-1-users-and-settings-4fd3950b829f), [tabs and modals](https://medium.com/@SamCorcos/meteor-react-ionic-mobile-app-part-3-tabs-and-modals-9d26f51c59ff), chat functionality, Cordova integration, animations and transitions, a Tinder interface, and perhaps others.

First, create a project with the following file structure:

```
├── client
│ ├── components
│ │ ├── AppBody.jsx
│ │ ├── AppLoading.jsx
│ │ ├── AppNotFound.jsx
│ │ ├── Home.jsx
│ │ ├── Other.jsx
│ │ ├── Settings.jsx
│ │ └── main.html
│ ├── router.jsx
│ └── styles
│ └── styles.scss
├── lib
│ ├── app.browserify.js
│ └── app.browserify.options.json
└── server
│ └── server.jsx
└── packages.json
```

You can do this easily by copy-pasting the following code:

```
$ mkdir client client/components client/styles lib server

$ touch client/router.jsx client/components/AppBody.jsx client/components/AppLoading.jsx client/components/AppNotFound.jsx client/components/Home.jsx client/components/Other.jsx client/components/Settings.jsx client/components/main.html client/styles/styles.scss server/server.jsx lib/app.browserify.js lib/app.browserify.options.json packages.json
```

Then add the following packages:

```
$ meteor add cosmos:browserify fourseven:[email protected] meteorhacks:npm meteoric:ionic-sass meteoric:ionicons-sass react
```

Then, you have to set a few options and add a couple npm packages. In **app.browserify.options.json** add the following code:

```
{
 "transforms": {
 "externalify": {
 "global": true,
 "external": {
 "react": "React.require"
 }
 }
 }
}
```

In **app.browserify.js**, add the following:

```
ReactRouter = require("react-router");
```

In **packages.json**, add the following two npm packages:

```
{
 "react-router": "0.13.3",
 "externalify": "0.1.0"
}
```

Then, we need to add a couple lines to make sure our **meteoric:ionic-sass** and **meteoric:ionicons-sass** packages work. In **styles.scss**, paste the following:

```
@import ".meteor/local/build/programs/server/assets/packages/meteoric_ionic-sass/ionic";
@import ".meteor/local/build/programs/server/assets/packages/meteoric_ionicons-sass/ionicons";
```

Now that we have all this in place, it's time to run meteor. NOTE: You will have to start it multiple times. First, the **meteorhacks:npm** package will kill the process, then due to some quirks with the **meteoric** packages, you will have to run it again. When in doubt, kill the process and run it again.

Our next step is to create the components for each of our three views. But first, in **client/components/main.html**, add the following code:

```
<head>
 <title>React Mobile Template</title>
 <meta charset="utf-8">
 <meta name="description" content="Template React-Meteor-Ionic app">
 <meta name="viewport" content="user-scalable=no, initial-scale=1, minimal-ui, maximum-scale=1, minimum-scale=1" />
</head>

<body>
 <div id="app"></div>
</body>
```

Then we will create our views. In **client/components/AppLoading.jsx**, add the following:

```
AppLoading = React.createClass({
 render() {
 return <h1>Loading…</h1>
 }
});
```

In **client/components/AppNotFound.jsx**, add:

```
AppNotFound = React.createClass({
 render() {
 return <h1>Not Found</h1>
 }
});
```

In **client/components/Home.jsx**, add:

```
Home = React.createClass({
 render() {
 return <h1>Home</h1>
 }
});
```

In **client/components/Settings.jsx**, add:

```
Settings = React.createClass({
 render() {
 return <h1>Settings</h1>
 }
});
```

In **client/components/Other.jsx**, add:

```
Other = React.createClass({
 render() {
 return <h1>Other</h1>
 }
});
```

And finally, we build the part that ties them all together in **client/components/AppBody.jsx**:

```
AppBody = React.createClass({
 render() {
 return (
 <div className="ionic-body">
 <div className="bar bar-header bar-light">
 <ReactRouter.Link className="button button-icon icon ion-gear-a" to={"/settings"}></ReactRouter.Link>
 <ReactRouter.Link className="h1 title" to={"/"}>App Name</ReactRouter.Link>
 <ReactRouter.Link className="button button-icon icon ion-heart" to={"/other"}></ReactRouter.Link>
 </div>

 <div className="view">
 <div className="scroll-content ionic-scroll">
 <div className="content overflow-scroll has-header">
 <ReactRouter.RouteHandler />
 </div>
 </div>
 </div>
 </div>
 )
 }
});
```

The next thing we will do is set up our router in **client/router.jsx**. Since we will only have 3 views, this will be pretty simple:

```
const routes = (
 <ReactRouter.Route name="root" handler={AppBody}>
 <ReactRouter.Route name="home" path="/" handler={Home} />
 <ReactRouter.Route name="other" path="/other" handler={Other} />
 <ReactRouter.Route name="settings" path="/settings" handler={Settings} />
 <ReactRouter.DefaultRoute handler={AppLoading} />
 <ReactRouter.NotFoundRoute handler={AppNotFound} />
 </ReactRouter.Route>
);

Meteor.startup(function () {
 ReactRouter.run(routes, ReactRouter.HistoryLocation, function (Handler, state) {
 React.render(<Handler />, document.getElementById("app"));
 });
});
```

And now you're done! Your app is pretty lame at this point, but it's a good place to start.
