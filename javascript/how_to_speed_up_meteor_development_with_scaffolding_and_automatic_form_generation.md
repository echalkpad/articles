# How to Speed Up Meteor Development with Scaffolding And Automatic Form Generation

[Original URL](https://scotch.io/tutorials/how-to-speed-up-meteor-development-with-scaffolding-and-automatic-form-generation)

> Meteor is a web development platform built on top of Node.js and MongoDB. It covers client and server development in JavaScript and fully supports real time behavior and reactivity (automatic update...

Meteor is a web development platform built on top of Node.js and MongoDB. It covers client and server development in JavaScript and fully supports real time behavior and reactivity (automatic update of components when necessary). If you are new to Meteor you can find an introduction [here](https://scotch.io/tutorials/learn-meteor-js-from-scratch-build-a-polling-app).

## Faster Development with Scaffolding

This article is about Meteor scaffolding. With the _iron-cli_ tool scaffolding of Meteor apps becomes super easy. It automatically creates project structure, files and boilerplate code to speed up your development process significantly. In this article we'll use the _iron-cli_ tool to build a simple Meteor web application from scratch. Furthermore we will use the AutoForms extension to automatically generate HTML forms.

## Getting Started

First of all you need to have Meteor installed:

1. Install on Windows: Download from _<https://install.meteor.com/windows>_
2. Install on Linux / OS X: Use command _curl <https://install.meteor.com/> | sh_

Second we'll install the scaffolding tool _iron-cli_ which is available on [GitHub](https://github.com/iron-meteor/iron-cli). Install is done via command line:

```
npm install -g iron-meteor
```

After successful install, the _iron_ command is available. You can test it by executing:

```
iron help
```

As a result you can see the following output giving you an brief overview of command parameters:

![Iron Command](https://cask.scotch.io/2015/05/meteor_scaffolding_01.png)

The most important options are:

- **create**: Creates a new project structure. The project name needs to be specified as a second parameter, e.g. `iron create myprojectname`.

- **generate**: Generates files and boilerplate code for your project. The _generate_ parameter is followed by colon and the type of component you want to create, e.g. _generate:view_ (can also be shortened to _g:view_) to create a new view. The name of the component is specified as second parameter, e.g `iron g:view myfirstview`.

- **run**: Runs the app.

## Create a Project Structure

As the _iron_ command is available we'll start by creating a new project:

```
iron create ironapp01
```

From the command output you can see that a bunch of files is created and that packages (e.g. the _iron:router_ package) are added to the new project automatically:

![New project is created with iron command line tool](https://cask.scotch.io/2015/05/meteor_scaffolding_02.png)

Change to the newly created project directory _ironapp01_ and enter command _ls -al_ to get a first impression of how the project structure looks like:

```
| .iron // iron configuration folder
| app // Meteor application folder
| bin 
| build // builds of your applications 
| config // environment configuration 
  |----- development/
 |--- env.sh
 |--- settings.json
```

As you can see, the Meteor application is generated into the subfolder _app/_. If you take a look into _app/_ you can find the following structure:

```
| app
 |----- client
 |----- lib
 |----- packages
 |----- private
 |----- public
 |----- server
```

**client/**: This is the place where the client code of the Meteor application is stored. The subfolder _templates_ is containing a default home view (_home.css_, _home.html_ and _home.js_), a application master layout (_master_layout.html_ and _master_layout.js_), a template to display a loading message (_loading.html_) and a template to display a not found error message (_not_found.html_).

```
| app
 |--- client/
 |---- head.html
 |---- collections/
 |---- lib/
 |---- stylesheets/ // main stylesheets
 |--- main.css
 |---- templates/ // all views are stored here
 |--- home/ // home view is created by default
 |-- home.css
 |-- home.html
 |-- home.js
 |--- layouts/ // contains a master layout
 |-- master_layout/
 |- master_layout.html
 |- master_layout.js
 |--- shared/ // templates for displaying loading message / not found message 
 |-- loading/
 |- loading.html
 |-- not_found/
 |- not_found.html
```

**lib/**: The _lib/_ directory contains the application code which is executed both on client and server. By default the scaffolding tool creates the subfolder _collections/_ and _controllers/_ within _lib/_. Within _app/lib/controllers_ you can find the generated file _home_controller.js_ with the following code inside:

```
HomeController = RouteController.extend({
 layoutTemplate: 'MasterLayout',

 subscriptions: function() {
 },

 action: function() {
this.render('Home');
 }
});
```

The HomeController contains a property _layoutTemplate_ which is set to _'MasterLayout'_ by default. With this setting in place it is ensured that the master layout from file _app/client/templates/layouts/master_layout/master_layout.html_ is used every time the router is calling a HomeController function to render a view template. The _subscriptions_ property of HomeController is a function which is empty by default and used later to subscribe to data collections published by the server. The _action_ function is calling `this.render('Home')` to output the Home template (stored in _app/client/templates/home)_.

Inside the _lib/_ directory there are two more files:

- **methods.js**: Used to implement client and server methods, empty by default.

- **router.js**: Contains the routing configuration.

Take a look into _router.js_ and you'll find the initial _iron:router_ configuration:

```
Router.configure({
 layoutTemplate: 'MasterLayout',
 loadingTemplate: 'Loading',
 notFoundTemplate: 'NotFound'
});

Router.route('/', {
 name: 'home',
 controller: 'HomeController',
 action: 'action',
 where: 'client'
});
```

_Note:_ The _iron:router_ package is added to the iron project by default. It is one of the most popular Meteor packages and makes it extremely easy to configure URLs you want to use in your Meteor application. The _iron:router_ is able to handle both, client and server routes at the same time.

First, _Router.configure()_ is used to set the _general layout_, _loading_ and _not found template_. Second, the default application route '/' is configured. Route property _controller_ is set to value 'HomeController' to ensure that this controller is used for processing the request. The _action_ property defines the function of the controller which is called to render the browser output.

_Note_: As seen before, the _action_ method of HomeController is displaying the home view, so all the different parts of the application are wired together by the routing configuration.

**public/**: This folder is used to store any static assets of your applications like images etc.

**server/**: Place to store all your Meteor server code. The following structure can be found in _server/_:

```
| app
 |--- server/
 |---- collections/ // folder to store store collection code 
 |---- controllers/ 
 |---- bootstrap.js // Meteor startup code can be placed here
 |---- methods.js // contains all server methods 
 |---- publish.js // use to publish data collections to the client
```

Now, that you have a basic understanding of the predefined project structure and the automatically created files we can move on with implementing our sample application. First of all we'll extend the project by adding some Meteor packages.

## Adding, Removing Some Packages

Meteor packages are an easy way to add functionality or external libraries to your project. In the following we'll use the Meteor package manager to add three packages to our project:

- aldeed:autoform
- aldeed:collection2
- twbs:bootstrap

**aldeed:autoform**: Easily create forms with automatic insert and update, and automatic reactive validation.

**aldeed:collection2**: Automatic validation of insert and update operations on the client and server.

**twbs:bootstrap**: Contains the Bootstrap framework. One of the most popular front-end frameworks for developing responsive, mobile first projects on the web.

Execute the following commands to add the packages:

```
iron add aldeed:autoform
iron add aldeed:collection2
iron add twbs:bootstrap
```

_Note_: Executing _iron add_ in the top level folder of the iron project has the same effect as executing _meteor add_ in the _app/_ subfolder. In this case the _iron_ command is just acting as a proxy to the _meteor_ command to make it a little bit easier for you (you do not have to change directories and you do not have to remember in which case use _meteor_ and in which case use _iron_ command). In fact you can use the _iron_ command with all parameters which are available for the _meteor_ command.

To ensure packages are installed successfully execute the following command:

```
iron list
```

As a result you will see a list of all installed packages in the current project.

![Get a list of all Meteor packages installed in project](https://cask.scotch.io/2015/05/meteor_scaffolding_03.png)

_Note:_ From the list of packages you can also see that the _autopublish_ and _insecure_ packages are installed. The _autopublish_ package publishes the entire database to all clients. The _insecure_ package allows all database writes by default. Both, automatic publishing and allowing all database writes by default, is not recommendable for a production app. We'll remove both packages and take back control of data publishing and write access in the next step:

```
iron remove autopublish
iron remove insecure
```

## Creating a Cars Collection

As the main purpose of the application is to manage a list of cars we'll start the implementation by creating a cars collection. We'll do this by using the iron scaffolding command line tool again:

```
iron g:collection cars
```

A new file _app/lib/collection/cars.js_ is created. Open this file, delete the _Cars.deny_ rules and return _true_ for the _Cars.allow_ rules (insert, update and remove):

```
Cars = new Mongo.Collection('cars');

if (Meteor.isServer) {
 Cars.allow({
 insert: function (userId, doc) {
 return true;
 },

 update: function (userId, doc, fieldNames, modifier) {
 return true;
 },

 remove: function (userId, doc) {
 return true;
 }
 });
}
```

With this configuration in place all actions (insert, update and remove) are allowed for Cars collection on the server.

## Attaching a Schema and Publishing Cars Collection

Next step is to define a data schema for our cars collection. Insert the following schema code in file _cars.js_:

```
Cars.attachSchema(new SimpleSchema({
 brand: {
 type: String,
 label: "Brand",
 max: 100
 },
 model: {
 type: String,
 label: "Model",
 max: 100
 },
 fueltype: {
 type: String,
 label: "Fuel Type",
 allowedValues: ['Petrol', 'Diesel', 'Hybrid', 'Electric'],
 },
 bodystyle: {
 type: String,
 label: "Body Style",
 allowedValues: ['Convertibles', 'Coupes', 'Hatchbacks', 'Vans', 'Sedans', 'Suvs', 'Trucks', 'Wagons'],
 optional: true
 },
 topspeed: {
 type: Number,
 label: "Top Speed (mph)",
 optional: true
 },
 power: {
 type: Number,
 label: "Power (HP)",
 optional: true
 }
}));
```

The data schema is the prerequisite for automatic form generation and automatic form validation which we'll use in a moment.

## Publish Collection

As we have removed the _autopublish_ package from our project we now need to publish our data collection explicitly to make data available on the client. Again, we will accomplish this task by using the iron scaffolding command line tool:

```
iron g:publish cars
```

This command automatically generates the following call of method _Meteor.pubish()_ in file _app/server/publish.js_:

```
Meteor.publish('cars', function (/* args */) {
 return Cars.find();
});
```

As you can see, publishing on server is quite simple. The call of method _publish_ takes as a first parameter a string which contains the name of the publication. The second parameter is a function returning the data which are published.

## Creating an Insert Form

As our newly created car collection is empty right now there is nothing to display so far. To enable the user to insert new cars we'll create an insert form next. With the Cars schema in place and the _aldeed:autoforms_ package installed it's a simple process. First, create a new template for the insert form:

```
iron g:template cars/create_car
```

This will create three new files in _app/client/template/cats/create_car_:

- create_car.css
- create_car.html
- create_car.js

Open _create_car.html_ and insert the following code:

```
<template name="CreateCar">
 <h1>Create New Car</h1>
 {{> quickForm collection="Cars" id="insertCarForm" type="insert" buttonContent="Create"}}
</template>
```

That's it! No more code needed to get an insert form. Quite simple, isn't it? The _quickForms_ inclusion is doing all the work for us generating a html form with validation (based on the Cars schema) for us.

Next, let's add a new controller to our application:

```
iron g:controller Cars
```

This command creates the file _cars_crontroller.js_ in _app/lib/controllers_. Insert a new method _create_ and subscribe to the _cars_ publication:

```
CarsController = RouteController.extend({
 subscriptions: function () {
 this.subscribe('cars');
 },
 data: function () {
 },
 create: function() {
 this.render('CreateCar', {});
 }
});
```

This _create_ methods renders the _CreateCar_ template to the browser. Subscriping to the _Cars_ collection is done by calling `this.subscribe('cars')` in the _subscription_ function.<br>
In the next step we will connect the _create_ method to the new route `/cars/create`. Open file _route.js_ and add the following route configuration:

```
Router.route('/cars/create', {
 name: 'createCar',
 controller: 'CarsController',
 action: 'create',
 where: 'client'
});
```

With this configuration in place we can start the application by executing the following command:

```
iron run
```

![Meteor server is running](https://cask.scotch.io/2015/05/meteor_scaffolding_04.png)

If you now open the browser and access URL `http://localhost:3000/cars/create` you can see the form:

![Automatically generated form](https://cask.scotch.io/2015/05/meteor_scaffolding_05.png)

Without writing HTML form code the output was automatically generated by the _AutoForms_ package. The best thing about it – it's not only the form's HTML code, it's also the logic behind which is automatically generated. You can start right away creating records in the database by filling out the form.

To test if everything is working correctly lets first try to submit the form without filling in a _brand_ value (remember: the brand information is mandatory in our schema):

![Error message because of missing value](https://cask.scotch.io/2015/05/meteor_scaffolding_06.png)

As you can see an error message is printed out stating that a value for field _brand_ is required. The form is not submitted and further processing is aborted.

Next add some car records by filling in all input fields and submitting the form. If no validation errors occur the form is cleared after successful submission. To check if data records have been created first open the browser console end enter the following command:

```
Cars.find().fetch()
```

An array object is returned containing items for every car record created:

![Query collection in browser console](https://cask.scotch.io/2015/05/meteor_scaffolding_07.png)

The same check can be done on the server. Go to console and enter

```
meteor mongo
```

in _app/_ directory of the project and query the server collection by using the following command:

```
db.cars.find().pretty()
```

![Query collection in MongoDB console](https://cask.scotch.io/2015/05/meteor_scaffolding_08.png)

Again, as a result we get a list of all car records. This shows that the content of the client collection is automatically submitted to the server and stored in the MongoDB database correctly.

## Changing the Master Layout

So far our application only consists of one site, displaying a form for creating new car records. Before moving on and adding additional functionality (and templates) we'll change the _MasterLayout_ to contain some Bootstrap elements to display an application menu. The default _MasterLayout_ template is available in _app/client/templates/layouts/master_layout/master_layout.html_ and contains the following code by default:

```
<template name="MasterLayout">
 {{> yield}}
</template>
```

The `{{> yield}}` inclusion is placed in the template code and a generic element which is replaced by the content of the route-specific template. To make our application look a little bit nicer we add a Bootstrap application menu (responsive) in the header and a footer area at the bottom. Replace the content of _master_layout.html_ with the following template code:

```
<template name="MasterLayout">
 <nav class="navbar navbar-inverse navbar-fixed-top" role="navigation">
 <div class="container">
 <div class="navbar-header">
 <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#navigationbar">
 <span class="sr-only">Toggle navigation</span>
 <span class="icon-bar"></span>
 <span class="icon-bar"></span>
 <span class="icon-bar"></span>
 </button>
 <a class="navbar-brand" href="#">Meteor Car Manager</a>
 </div>
 <div class="collapse navbar-collapse" id="navigationbar">
 <ul class="nav navbar-nav navbar-right">
 <li>{{#linkTo route="home"}}Home{{/linkTo}}</li>
 <li>{{#linkTo route="carsList"}}Cars{{/linkTo}}</li>
 <li>{{#linkTo route="createCar"}}Create New Car{{/linkTo}}</li>
 </ul>
 </div>
 </div>
 </nav>
 <div class="container" style="margin-top:40px">
 {{> yield}}
 </div>
 <nav class="navbar navbar-default navbar-fixed-bottom">
 <div class="container">
 <ul class="nav navbar-nav navbar-right">
 <li>Built with Meteor</li>
 </ul>
 </div>
 </nav>
</template>
```

Looks much better now:

![Master layout with menu and footer](https://cask.scotch.io/2015/05/meteor_scaffolding_09.png)

The menu on top contains three links: _Home_, _Cars_ and _Create New Car_. The _Create New Car_ link is pointing to the _CreateCar_ template and the _Cars_ link is pointing to the the _carsList_ route which we'll create later on. To create a link we simply use the `{{#linkTo route="[Enter Template Name]"}}[Enter link Text]{/linkTo}}` block helper provided by the Iron Router package. The block helper takes the name of the route as parameter and creates an corresponding HTML element in the output.

## Displaying a List of Cars

Now, that we have the menu in place we can move on to the next step and create a new template for outputting a list of all cars. Create the template by using scaffolding:

```
iron g:template cars/list_cars
```

Next we add a _/cars_ route in _routes.js_:

```
Router.route('/cars', {
 name: 'carsList',
 controller: 'CarsController',
 action: 'list',
 where: 'client'
});
```

The _list_ action method is not existing in the _CarsController_ so far. Lets create this method to output the rendered version of our _ListCars_ template:

```
list: function() {
 this.render('ListCars', {});
}
```

If you now click on _Cars_ link in the menu the default template is rendered as you can see in the following screenshot:

![New ListCars template is rendered after clicking on link Cars in menu](https://cask.scotch.io/2015/05/meteor_scaffolding_10.png)

Next we'll replace the content of _list_cars.html_ with the following code:

```
<template name="ListCars">
 <h1>Cars List</h1>
 <table class="table table-hover">
 <thead>
 <tr>
 <th>Brand</th>
 <th>Model</th>
 <th>Fuel Type</th>
 <th>Body Style</th>
 <th>Top Speed</th>
 <th>Power</th>
 </tr>
 </thead>
 <tbody>
 {{#each cars}}
 <tr>
 <td>{{brand}}</td>
 <td>{{model}}</td>
 <td>{{fueltype}}</td>
 <td>{{bodystyle}}</td>
 <td>{{topspeed}}</td>
 <td>{{power}}</td>
 </tr>
 {{/each}}
 </tbody>
 </table>
</template>
```

That's the template code which is needed to print out the table of cars. To iterate over all objects in the cars collection a block helper is used: `{{#each cars}} ... {{/each}}`. The code inside this block helper is rendered for every item in objects _cars_. But what about _cars_? This object has not been defined yet, so we need to change that and make the _cars_ object available to the template. Open file _list_cars.js_ and add _cars_ as a Template Helper:

```
Template.ListCars.helpers({
 cars: function() {
 return Cars.find();
 }
});
```

With that code in place the _cars_ object is available in the template and the `{{#each cars}} ... {{/each}}` block will be iterating over all objects in _cars_. To make sure _cars_ is filled with all data records a function is assigned returning the result of _Cars.find()_.

Now you should see a table with all car records in the browser by accessing URL `http://localhost:3000/cars`:

![List of all car entries in database](https://cask.scotch.io/2015/05/meteor_scaffolding_11.png)

## Editing and Deleting

To complete our line of business web application we need to add editing and delete capabilities finally. Let's add one more view template to the project:

```
iron g:template cars/edit_car
```

This view will contain an editing form. Again, we'll use the `{{> quickForm ...}}` inclusion to generate an editing form on the fly. Insert the following code into file _edit_car.html_:

```
<template name="EditCar">
 <h1>Edit</h1>
 {{> quickForm collection="Cars" doc=this id="editCarForm" type="update" buttonContent="Update"}}
</template>
```

In this case the parameter _type_ is set to value _update_. This tells quickForm to generate a editing form for updating existing datasets. To access the _EditCar_ template we also need to configure a new route in file _app/lib/routes.js_:

```
Router.route('/cars/:_id', {
 name: 'editCar',
 controller: 'CarsController',
 action: 'edit',
 where: 'client'
});
```

In this case the route string _/cars/:_id_ contains a routing parameter named __id_. This parameter takes the identifier of a specific car records as part of the URL. With this information available we can set the data context to this single car records in the _CarsController_:

```
data: function () {
 return Cars.findOne({_id: this.params._id});
}
```

The __id_ parameter can be accessed via the _this.params_ object. To retrieve the corresponding car entry from the _Cars_ collection the _findOne_ method is called. The resulting data object is returned and assigned to the data property of the controller. Next the _edit_ function is added to the _CarsController_ as this is the function which was assigned to the _action_ property of the _editCar_ routing configuration:

```
edit: function() {
 this.render('EditCar', {});
}
```

With the changes made we are now able to access the new edit form in the browser by pointing to URL `http://localhost:3000/cars/[ID]`. But how to retrieve the identifier of a specific dataset we must include in the URL? You already learned how to query the Cars collection on server by using the Meteor MongoDB Console:

```
cd app
meteor mongo
db.cars.find().pretty()
```

The resulting list contains all car records including the __id_ value:

![Use MongoDB Console to retrieve IDs](https://cask.scotch.io/2015/05/meteor_scaffolding_12.png)

Just copy one single ID and complete the URL in browser. As a result you get the prefilled editing form:

![Form for editing car records](https://cask.scotch.io/2015/05/meteor_scaffolding_13.png)

Ok, great so far! But maybe we should make it a little bit easier to access the edit form for a specific record. Let's add direct links in the _ListCars_ template:

```
<template name="ListCars">
 <h1>Cars List</h1>
 <table class="table table-hover">
 <thead>
 <tr>
 <th>Brand</th>
 <th>Model</th>
 <th>Fuel Type</th>
 <th>Body Style</th>
 <th>Top Speed</th>
 <th>Power</th>
 <th>Edit</th>
 </tr>
 </thead>
 <tbody>
 {{#each cars}}
 <tr>
 <td>{{brand}}</td>
 <td>{{model}}</td>
 <td>{{fueltype}}</td>
 <td>{{bodystyle}}</td>
 <td>{{topspeed}}</td>
 <td>{{power}}</td>
 <td>{{#linkTo route='editCar'}}<span class="glyphicon glyphicon-edit" aria-hidden="true"></span>{{/linkTo}}</td>
 </tr>
 {{/each}}
 </tbody>
 </table>
</template>
```

Another column is added to contain a separate link to the edit form for each table row. Again we are using the _linkTo_ block helper. This time _linkTo_ is pointing to the _editCar_ route, so that a valid URL is generated to access the _EditCar_ form template. What is not obvious at first sight, is that the route parameter is set automatically to the current dataset in the generated links. No need to write further code. The result is shown in the following screenshot:

![Table now includes links to edit form for every dataset](https://cask.scotch.io/2015/05/meteor_scaffolding_14.png)

That was really easy, wasn't it? Last thing to do is to add delete function so that the user is able to delete existing cars from database. In this case the _quickForms_ generator is no option, as no input form is needed. Instead we just want to add an additional button in the _EditCars_ template enabling the user to delete the current dataset. Let's see what other packages may help us completing that task quickly. A search for „delete button" on <http://atmospherejs.com> shows that there is exactly one package which matches our requirements: _aldeed:delete-button_.

![Search for "delete button" on atmospherejs.com](https://cask.scotch.io/2015/05/meteor_scaffolding_15.png)

The package is installed by using the following command:

```
iron add aldeed:delete-button
```

After the package is added successfully the new inclusion `{{> quickRemoveButton}}` becomes available and can be used in the existing _EditCar_ template (_edit_car.html_):

```
<template name="EditCar">
 <h1>Edit</h1>
 {{> quickForm collection="Cars" doc=this id="editCarForm" type="update" buttonContent="Update"}}
 <hr>
 {{> quickRemoveButton collection="Cars" _id=this._id beforeRemove=beforeRemove class="btn btn-danger"}}
</template>
```

As you can see the `{{> quickRemoveButton}}` inclusions takes four parameters: _collection_, __id_, _beforeRemove_ and _class_.

- **collection**: The collection for which a delete button should be generated.
- **_id**: The ID of the dataset which should be deleted by clicking on the button.
- **beforeRemove**: A function which should be executed everytime a delete operation is executed.
- **class**: CSS classes to be applied.

The _beforeRemove_ function is added because we want to display a confirm message before the record is deleted. The function is implemented as template helper in _edit_car.js_:

```
Template.EditCar.helpers({
 beforeRemove: function () {
 return function (collection, id) {
 var doc = collection.findOne(id);
 if (confirm('Really delete car: "' + doc.brand + " " + doc.model + '"?')) {
 this.remove();
 Router.go('carsList');
 }
 };
 }
});
```

That's it. The result can be seen in the following screenshot:

![Deleting a car record](https://cask.scotch.io/2015/05/meteor_scaffolding_16.png)

[View Live Demo](http://scotchio-meteor-car.meteor.com/ "View Live Demo")

[View Code on GitHub](https://github.com/seeschweiler/scotchio-meteor-car "View Code on GitHub")

## Conclusion

Meteor is a great platform for web and mobile development which is easy to learn and fun to use. With iron scaffolding Meteor developments becomes even more fun. Generating project structures, components and boilerplate code with just one command gives a boost to your development process.

Hopefully this article has given you a good starting point to dive deeper into Meteor scaffolding with iron-cli and helps you to create great realtime, reactive web apps.
