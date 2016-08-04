# Create A Custom Yeoman Generator in 4 Easy Steps

[Original URL](https://scotch.io/tutorials/create-a-custom-yeoman-generator-in-4-easy-steps)

> Yeoman is one of the web's most popular tooling frameworks with over 400 contributed reusable plugins. The open source project led by Addy Osmani makes project scaffolding an easy step. This...

[Yeoman](http://yeoman.io/) is one of the web's most popular tooling frameworks with over 400 contributed reusable plugins. The open source project led by Addy Osmani makes project scaffolding an easy step.

This means that basic project structures and dependencies can now be setup with just one or two commands from the CLI. Is that not amazing? You don't have to install `Express`, `Passport`, `Angular`, `Mongoose`, etc that your MEAN project require manually, you just need to create and configure once, and then use throughout your other projects.

[![yeoman-logo](https://scotch.io/wp-content/uploads/2015/11/yeoman-logo.png)](https://scotch.io/wp-content/uploads/2015/11/yeoman-logo.png)

> Note that Yeoman is not just for JavaScript based projects but for anything you can make out of it.

Yeoman's plugins are called `generators` and are made in such a way that they suite a particular project requirements or workflow. It is not a surprise that out of the 400 contributed generators, you might not find one that suits you.

Today, I will guide you through 4 easy steps to create yours and also provide a demo at the end. We will end up with a MEAN generator for Scotch.

## STEP 1: Installation and Setup

As always, the first step is to install the dependencies and create a new generator project. The concept is actually using a yeoman generator to generate a generator template. That sounds silly right?

The Yeoman team built a generator plugin that makes building a Yeoman custom generator quite easy. So install both Yeoman (`yo`) and the generator (`generator-generator` is the name):

```
npm install -g yo generator-generator
```

This command installs both Yeoman and the generator plugin. Installing these tools does not create a project, they just download them to your file system. To setup a generator project, run:

```
yo generator
```

You will be asked two or more questions from the CLI including your GitHub username and your generator base name. Yeoman generators naming convention is in such a way that the generators are prefixed with `generator-` and the name of the custom generator (base name) added right after the `-`.

In our case, I chose, `scotchmean` so that at the end, the generator will be used as `generator-scotchmean`.

You do not have to be intimidated by the file structure. Actually we just need one folder which is the `generators` folder. The `generators` folder has an `app` directory with `templates` and `index.js`. The `templates` are files that will be generated when we use this generators to scaffold a project and the index.js folder holds all the logics involved in generating a template.

Before we move to step 2, clear the contents of `app/templates/index.js` and replace with:

```
'use strict';
//Require dependencies
var yeoman = require('yeoman-generator');
var chalk = require('chalk');
var yosay = require('yosay');


module.exports = yeoman.generators.Base.extend({
//Configurations will be loaded here.
});
```

## STEP 2: Asking For Input

One awesome feature of Yeoman is it's ability to scaffold a project using the user's decision from inputs. This means that we can configure a lot of options like the project's name, dependencies we need, and much more. Yeoman uses [Inquire.js](https://github.com/SBoudrias/Inquirer.js) to accomplish this feature. To keep it minimal, we just need to get the project's name from input:

```
//Configurations will be loaded here.
//Ask for user input
prompting: function() {
 var done = this.async();
 this.prompt({
 type: 'input',
 name: 'name',
 message: 'Your project name',
 //Defaults to the project's folder name if the input is skipped
 default: this.appname
 }, function(answers) {
 this.props = answers
 this.log(answers.name);
 done();
 }.bind(this));
},
//Writing Logic here
```

We are using the `async()` method to make sure that the function does not exit before the actual work gets completed.

The `prompt` method is used to get the user's input which is of type `input` as there are other [types](https://github.com/SBoudrias/Inquirer.js#prompts-type).

The `name` property of the prompt is used to access the field's value from the project and the `message` is the instruction. We also want it to default to the folder name if we do not provide a name while scaffolding.

The second argument of `prompt` is a callback which holds the values from the prompt(s). We have set `this.props` up with the answers so it can be accessed from the "writing" logic.

## STEP 3: Scaffolding With Templates

Template files are files generated when you use Yeoman with your custom generator to generate a project. These files are located within the `templates` folder in the `app` directory. It works like common view engines like EJS. You can dynamically fill in data into the templates with the inputs that were generated. We will use a basic MEAN folder structure for our templates:

```
|---public
 |-----css
 |-----app.css 
 |-----js
 |-----app.js
|---routes
 |-----all.js
|---model
 |-----todo.js
|---views
 |-----index.ejs
|---server.js
|---bower.json
|---package.json
|---etc
```

Just like a normal application, replace the above project structure with the contents of the `templates` folder and then we will gradually populate them in a stepwise manner or create them along the process. All template logic are wrapped inside the `writing` object in index.js:

```
//Writing Logic here
writing: {
 //Copy the configuration files

 //Copy application files

 //Install Dependencies
},
```

The files to be generated are better classified into configuration files and application files. The config files include `package.json`, `bower.json`, and other utility files for our project while the app files include `server.js`, our routes, public directory and every other major files.

We will first create the configuration files templates and then copy them. To create the files, open `app/templates/_package.json` and replace with:

```
{
 "name": "<%= name %>", 
 "scripts": {
 "start": "node server.js"
 },
 "dependencies": {
 "express": "^4.13.3",
 "ejs": "^2.3.4",
 "body-parser": "^1.14.0",
 "cookie-parser": "~1.0.1",
 "mongoose":"^4.2.4"
 } 
}
```

Also replace `app/templates/_bower.json` with:

```
{
 "name": "<%= name %>",
 "version": "0.0.0",
 "dependencies": {
 "bootstrap": "3.3.5",
 "angular": "1.4.6"
 }
}
```

Now create a `bowerrc` in `app/templates/` and fill it with:

```
{
 "directory" : "app/public/libs"
}
```

Update `app/index.js`:

```
//Writing Logic here
writing: {
 //Copy the configuration files
 config: function () {
 this.fs.copyTpl(
 this.templatePath('_package.json'),
 this.destinationPath('package.json'), {
 name: this.props.name
 }
 );
 this.fs.copyTpl(
 this.templatePath('_bower.json'),
 this.destinationPath('bower.json'), {
 name: this.props.name
 }
 );
 this.fs.copy(
 this.templatePath('bowerrc'),
 this.destinationPath('.bowerrc')
 );
 },

 //Copy application files

 //Install Dependencies
},
```

Two methods, `copy()` and `copyTpl()` are used to the copy the templates from our template path to a destination path which Yeoman abstracts from us the implementation but helps determine the path we intend to copy the files to.

The difference between the two methods is that `copyTpl()` takes a third parameter which is a list of data to be bound to the template file after it is generated while `copy()` is used when there are no bindings required in the template.

Now we have the setup files prepared and we can now go ahead and create the application files. I suggest we begin with the `server.js` as it is our entry point:

```
//Dependencies
var express = require('express');
var app = express();
var path = require('path');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');
var mongoose = require('mongoose');

//Create a mongoose connection
mongoose.connect('mongodb://127.0.0.1:27017/scotchmean');
//Load custom dependencies
var routes = require('./routes/all');


//View engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

//Configure Body Pareser and Cookie Parser
app.use(bodyParser.json());
app.use(bodyParser.urlencoded());
app.use(cookieParser());

//Use the public folder for static files
app.use(express.static(path.join(__dirname, 'public')));

console.log(routes.getTodo)
//Create the routes
app.get('/', routes.index);
app.get('/todo', routes.getTodo);
app.post('/todo', routes.postTodo);

//Set port to env.Port or default to 8080
app.set('port', process.env.PORT || 8080);
//Listen to port for connections
app.listen(app.get('port'), function() {
 console.log('App listening at port ' + app.get('port'));
});
```

The above snippet is a basic server file for Node. We just ended up loading routes (though yet to be implemented), configuring body and cookie parser, creating a connection to MongoDB and listening to a port.

Next up is to create the routes which include: `index`, `getTodo` and `postTodo`. Create a `_routes` folder inside the `templates` directory. Add `_all.js` with the following content:

```
//_routes/_all.js
var Todo = require('../model/todo').todo;
exports.index = function(req, res){
 res.render('index');
};


exports.getTodo = function(req, res){
 Todo.find().exec(function(err, todo){
 if (err)
 return res.send(err);
 return res.json(todo);
 });
};

exports.postTodo = function(req, res){
 Todo.create(req.body, function(err, todo){
 if (err)
 return res.send(err);
 return res.json(todo);
 });
};
```

We can now create the Todo model that the route loaded. Create a `_model` folder in `templates` directory and add a file named `_todo.js` in it with the following content:

```
//_model/_todo.js
var mongoose = require('mongoose');
var todo = mongoose.model('Todo', {
 content: String
 });

exports.todo = todo;
```

Time to develop our views and public files. We need just one EJS view which is `index` for our home page as seen in the routes. This file will live in the `_views` folder in the `templates` directory:

```
<html ng-app="app">

 <body ng-controller="TodoController">
 <div class="container">
 <div class="col-md-4 col-md-offset-4">
 <input type="text" ng-model="todo.content" class="form-control">
 <button type="button" ng-click="addTodo()" class="btn btn-primary">Add</button>
 <hr>
 <ul>
 <li ng-repeat="todo in todos">{{todo.content}}</li>
 </ul>
 </div>
 </div>
 <script src="libs/angular/angular.js"></script>
 <script src="js/app.js"></script>
 </body>
</html>
```

See how we are binding the `name` value to the view in the `title`. That is the most interesting part of this file. The rest is just basic HTML with Bootstrap and Angular codes.

Moving further, create the `_public` folder with `_css` and `_js` directories. In the `_js` folder add a file `_app.js` to hold our Angular logic:

```
angular.module('app', [])
 .controller('TodoController', function($scope, $http) {
 $scope.todos = [];
 $scope.todo = {};
 $http.get('/todo').success(function(data) {
 $scope.todos = data;
 });
 $scope.addTodo = function() {
 $http.post('/todo', $scope.todo).success(function(data) {
 $scope.todos.unshift(data);
 });
 $scope.todo = {};
 };
 });
```

Now create a file in the `_css` directory named `_app.css` with this content:

```
body{
 padding-top:50px;
}
```

We have created all the template files that our project need but yet to update the `index.js` to be able to scaffold and serve them at project scaffold. Let us do so:

```
//Copy application files
app: function() {
 //Server file
 this.fs.copyTpl(
 this.templatePath('_server.js'),
 this.destinationPath('server.js'),
 this.destinationPath('/views/index.ejs'), {
 name: this.props.name
 }
 );
 /////Routes
 this.fs.copy(
 this.templatePath('_routes/_all.js'),
 this.destinationPath('routes/all.js'));


 // Model
 this.fs.copy(
 this.templatePath('_model/_todo.js'),
 this.destinationPath('model/todo.js'));

 // Views
 this.fs.copyTpl(
 this.templatePath('_views/_index.ejs'),
 this.destinationPath('/views/index.ejs'), {
 name: this.props.name
 }
 );

 // Public/
 this.fs.copy(
 this.templatePath('_public/_css/_app.css'),
 this.destinationPath('public/css/app.css')
 );
 this.fs.copy(
 this.templatePath('_public/_js/_app.js'),
 this.destinationPath('public/js/app.js')
 );
}
},
//Install Dependencies
```

Finally on step 3 is to install all the dependencies once the scaffold is completed by updating `index.js` to:

```
//Install Dependencies
install: function() {
 this.installDependencies();
}
```

## STEP 4: Testing

The most reasonable way to carry out integration test is not to publish to npm registry and then install before we can run `yo scotchmean` to create a project.

We can utilize npm's `link` to assist us with linking the generator in the global node modules. To understand better, on the generator's project folder, run:

```
npm link
```

Navigate to a folder you would love to scaffold a new MEAN project and run:

```
yo scotchmean
```

Respond to the prompts and watch Yo do its magic. Do have in mind that you do not need to keep running `npm link` command to update changes before testing. Just edit and save then your project will be ready for another scaffold.

When you are satisfied with what you have built, you can [publish](https://docs.npmjs.com/cli/publish) to npm.

## Conclusion

Hopefully, you can start making your life easier with Yeoman and it's generators. It doesn't end with a basic example; try something crazy, publish on npm, share on GitHub and earn some credits. Feel free to leave a comment below and Scotch will always give you all the attention yo deserve.

[Demo](https://github.com/christiannwamba/generator-scotchmean)
