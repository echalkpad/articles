# Creating an Ionic App with MySQL and Backand

[Original URL](http://blog.ionic.io/creating-an-ionic-app-with-mysql-and-backand/)

> Ionic makes it easy to create a user interface that ports easily across multiple devices, but most apps that do something of interest can't rely on their frontend alone. In this post,...

Ionic makes it easy to create a user interface that ports easily across multiple devices, but most apps that do something of interest can't rely on their frontend alone. In this post, we'll look at integrating Backand as the backend for your Ionic app and implementing your UI to effectively utilize a MySQL database, either by creating a new one or by connecting to an existing one.

## Starting with a Demo

Be sure you have NodeJS, NPM, and Ionic already configured on your machine.

Before we start, it's helpful to see where we will end up. To run the complete [quick-start app](http://market.ionic.io/starters/backand-simple), open a new terminal window. Navigate to a suitable directory, and execute the following command:

ionic start myApp <https://github.com/backand/backand-ionic-starter>

This will pull down the sample app into the directory "myapp". Navigate to the myapp folder and start the application with:

ionic serve

Your Backand-powered Ionic application is now available on <http://localhost:8100>. Use the user name "[[email protected]](http://blog.ionic.io/cdn-cgi/l/email-protection)" and the password "backand" to log in and experiment. The app provides a simple item tracking tool. You can add, remove, or edit items on the list. Each item has a name and a description, and are tied into an AWS database built on MySQL. In the next several sections, we'll modify the app to connect to a private instance of the demo Backand application and explore how the code ties in with Ionic.

## Backend-as-a-Service With MySQL and Backand

To start, we need to [create a Backand account](https://www.backand.com/). Backand provides the server component of your application, giving you a RESTful API on top of your data model or your MySQL database. It removes the need to build a custom application and database server, allowing you to focus on the frontend of your application. The sample app above uses a common account intended for demonstration purposes only, which means the data will be shared by everyone with access to the demo app. Thus, to build your own stand-alone application, you'll need to create a new application instance.

## Creating a New Backand Application

To create a new application, register an account with Backand and log in. Once you've logged in, you will be brought to the application dashboard. Create a new application by providing an app name and an app title. The app name will be used by your Ionic application to point at the correct Backand instance.

## Adding a Data Model

Once you've created your application, you need to provide a data model. This can be done by either providing an existing database connection to MySQL or specifying a data model in JSON, which will create a MySQL database managed by Backand. We'll create a new data model for our new application using the default JSON present in the "New Hosted Database" tab:

```
 [
 {
 "name": "items",
 "fields": {
 "name": {
 "type": "string"
 },
 "description": {
 "type": "text"
 },
 "user": {
 "object": "users"
 }
 }
 },
 {
 "name": "users",
 "fields": {
 "email": {
 "type": "string"
 },
 "firstName": {
 "type": "string"
 },
 "lastName": {
 "type": "string"
 },
 "items": {
 "collection": "items",
 "via": "user"
 }
 }
 }
]
```

The above JSON creates two objects: Items and Users. Items contains three fields: a name, a description, and a link to the User object that owns it. Users contains four fields: an email address, a firstName, a lastName, and a collection of Items associated with the user.

## Modifying the Starter Code

Once you've created the Backand application instance above, you're ready to modify the starter code. In the myApp directory you pulled down for the sample project, open the file www/js/app.js and scroll to line 26:

```
 // change here to your appName
 var appName = 'ionicstarter';
```

This line is used to configure the Backand SDK to point at the demo application. To point at your new application, simply modify this line to change the appName (replace YOURAPPNAME with your Backand application's name):

```
 // change here to your appName
 var appName = 'YOURAPPNAME';
```

A bit farther down in the file, we see how this is used by the Backand SDK:

```
 BackandProvider.setAppName(appName);
```

This configures the Backand SDK to point at your application instance.

Note: Below this, you will see a call to set the Anonymous Token for the application. The Anonymous Token allows users to connect to your application without logging in. See [our docs](http://docs.backand.com/en/latest/apidocs/security/index.html#anonymous-access) for more information.

Once this has been completed, you simply need to log out of the app and refresh the page. Your user name and password are now the user name and password that you used to register on [our site](http://www.backand.com). Simply enter those credentials, and you'll have a fresh application ready to go!

## Examining the Code

The sample app we are working with is a straightforward AngularJS application with a UI written using Ionic. Open up www/index.html in your myApp folder. The first portion of the file–up to line 29–handles the setup code. After this, starting on line 30, is where we configure the app itself:

```
 <body ng-app="SimpleRESTIonic">
 <!--
 The nav bar that will be updated as we navigate between views.
 -->
 <ion-nav-bar class="bar-stable">
 <ion-nav-back-button>
 </ion-nav-back-button>
 </ion-nav-bar>
 <!--
 The views will be rendered in the <ion-nav-view> directive below
 Templates are in the /templates folder (but you could also
 have templates inline in this HTML file if you'd like).
 -->
 <ion-nav-view></ion-nav-view>
 </body>
```

This creates an AngularJS app with the name "SimpleRESTIonic". The basic Ion infrastructure is then added – a nav bar (using ion-nav-bar) and the default view (using ion-nav-view). These are then modified by the app.js file using additional templates specified in the .config section. See line 34 of app.js:

```
 $stateProvider
 // setup an abstract state for the tabs directive
 .state('tab', {
 url: '/tabs',
 abstract: true,
 templateUrl: 'templates/tabs.html'
 })
 .state('tab.dashboard', {
 url: '/dashboard',
 views: {
 'tab-dashboard': {
 templateUrl: 'templates/tab-dashboard.html',
 controller: 'DashboardCtrl as vm'
 }
 }
 })
 .state('tab.login', {
 url: '/login',
 views: {
 'tab-login': {
 templateUrl: 'templates/tab-login.html',
 controller: 'LoginCtrl as login'
 }
 }
 });
```

This code configures the available states for your application. Each of these states points at a template file in /www/templates and is rendered based upon application transitions. The template files themselves are straightforward Ionic code. Take, for example, the default view of /www/templaes/tabs.html:

```
 <!--
 Create tabs with an icon and label, using the tabs-positive style.
 Each tab's child <ion-nav-view> directive will have its own
 navigation history that also transitions its views in and out.
 -->
 <ion-tabs class="tabs-icon-top tabs-color-active-positive">
 <ion-tab title="Items" icon="ion-home" href="#/tabs/dashboard">
 <ion-nav-view name="tab-dashboard"></ion-nav-view>
 </ion-tab>
 <ion-tab title="Login" icon="ion-log-in" href="#/tabs/login">
 <ion-nav-view name="tab-login"></ion-nav-view>
 </ion-tab>
 </ion-tabs> 
```

This is the central location for the application. The companion templates, tab-dashboard.html and tab-login.html, handle the specific angular functionality related to each application state, using Ionic directives to manage the display output. The data itself is obtained from JavaScript defined in the /www/js/services.js file. Here, for example, is the ItemService:

```
 .service('ItemsModel', function ($http, Backand) {
 var service = this,
 baseUrl = '/1/objects/',
 objectName = 'items/';

 function getUrl() {
 return Backand.getApiUrl() + baseUrl + objectName;
 }

 function getUrlForId(id) {
 return getUrl() + id;
 }

 service.all = function () {
 return $http.get(getUrl());
 };

 service.fetch = function (id) {
 return $http.get(getUrlForId(id));
 };

 service.create = function (object) {
 return $http.post(getUrl(), object);
 };

 service.update = function (id, object) {
 return $http.put(getUrlForId(id), object);
 };

 service.delete = function (id) {
 return $http.delete(getUrlForId(id));
 };
 })
```

This service uses the Backand SDK and HTTP requests to connect directly to the REST API generated for your Backand application. It gives you access to all of the data contained in your application instance, which is in turn stored in an AWS MySQL database that Backand created on your application's behalf. These services are then passed into controller objects found in /www/js/controllers.js, which populate the appropriate Angular scopes with your application's data. Try experimenting with some of the controllers, to get a feel for how the data moves through your application, and try modifying it as it goes through.

## Conclusion

The above walk-through is somewhat high-level, but should give you a suitable base with which to build a new Ionic application powered by Backand. With Ionic, you get a cross-platform UI framework that makes porting your application to any device a simple process, allowing you to maintain a consistent look and feel across all platforms. With Backand, you get a back-end server that provides security, scaling, and management tools, along with a dynamic REST API that changes to fit your database schema's needs.
