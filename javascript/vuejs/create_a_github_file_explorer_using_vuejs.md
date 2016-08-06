# Create a GitHub File Explorer Using Vue.js

[Original URL](https://scotch.io/tutorials/create-a-github-file-explorer-using-vue-js)

> Vue.js makes everything easy. Creating seemingly complex applications becomes super simple and is something that can be done in much less time than previously. We will demonstrate this by creating a...

Vue.js makes everything easy. Creating seemingly complex applications becomes super simple and is something that can be done in much less time than previously. We will demonstrate this by creating a Github file explorer that will allow us to explore files in public Github repos.

All the code for this post can be found [on Github](https://github.com/scotch-io/vuejs-github-explorer). You can view a [demo here](https://scotch.io/demos/demo-create-a-github-file-explorer-using-vue-js).

## Setup Development Environment

The repo for this post has a good starting point tagged. We need to pull the code down and check out this tag. Then we need to download all the dependencies using npm. Run the following commands in your terminal of choice.

```
 git clone https://github.com/scotch-io/vuejs-github-explorer.git
 cd vuejs-github-explorer
 git checkout start-here
 npm install
 ./node_modules/.bin/gulp serve
```

This will get your local environment setup and serve the starting point files on your localhost on port 8080\. Open up a browser window and go to `localhost:8080`. You should see something like below.

[![starting-point](https://scotch.io/wp-content/uploads/2015/11/starting-point.jpg)](https://scotch.io/wp-content/uploads/2015/11/starting-point.jpg)

As you can see, there isn't anything special going on here just yet. So far, all we have done is compile Twitter Bootstrap, Font Awesome, and some other basic styles that will be used in the application. We have also compiled our JavaScript using Browserify. You can see the JavaScript in `src/js/app.js`. We are only logging a simple "Hello World" to the console. All this is handled by Gulp. If you are interested in seeing what all is going on, take a look at the `Gulpfile.js` in the root directory of the project.

Now that we have a development environment setup, let's make something cool.

## Creating the Vue instance

First things first. We need to create the HTML for the main part of our application. Input the following HTML into `src/index.html`.

```
<div id="container">
 <div class="row">
 <div class="col-md-6 col-md-offset-3">
 <form id="changeRepoForm" @submit.prevent="changeRepo()" class="form-inline">
 <div class="form-group">
 <label for="fullRepoName">Full Repo Name</label>
 <input type="text" name="fullRepoName" v-model="fullRepoName" class="form-control">
 </div>
 <input type="submit" class="btn btn-default" value="Get repo filesystem!">
 </form>
 <hr>
 </div>
 </div>
</div>
```

Here, we are simply creating a form and marking it up with some basic Bootstrap code. On the form, we have used the `@submit.prevent` directive to add a submit listener to the form. The `@submit` adds the listener while the `.prevent` part will automatically run `preventDefault` for us. When we submit the form, Vue will fire the `changeRepo` method. We will get to this method in a minute.

The form itself is pretty basic. It has a text field with a label and a submit button. The input has the `v-model` directive on it. This creates a two-way binding between the value of the input and the value of `fullRepoName`, which is a piece of data on our Vue instance.

Now that we have this set up, let's create our Vue instance. Remove all the code from `src/app.js` and input the following:

```
 var Vue = require('vue');
 Vue.config.debug = true;
```

Here we are `require`ing Vue into our file using "Node syntax." We are able to do this due to our use of Browserify as part of our Gulp pipeline. The next thing we do is set a global config option to put our application in "debug mode." This just means that if we get an error, we will get more information about it in the developer console.

```
 new Vue({
 el: '#container',
 data: {
 fullRepoName: '',
 username: '',
 repo: ''
 },
 methods: {
 changeRepo: function() {
 var splitData = this.fullRepoName.split('/');
 this.username = splitData[0];
 this.repo = splitData[1];

 console.group("Vue Data");
 console.log("fullRepoName:", this.fullRepoName);
 console.log("username:", this.username);
 console.log("repo:", this.repo);
 console.groupEnd("Vue Data");
 }
 }
 });
```

This is the code that creates our Vue instance. We bind the instance to the element with the ID of `container`. Then we initialize our instance with some data. It is best to initialize Vue instances with the data that you plan on using in it. This ensures that Vue binds to all the data we want it to. Lastly, we define the methods we will use in our Vue instance. Here is where we will define the `changeRepo` method that we used in our HTML earlier.

The `changeRepo` method simply splits the value of the `fullRepoName` piece of data and assigns the first part to `username` and the second part to `repo`. This is possible due to the two-way binding I mentioned earlier. We also print the data on our instance to the console so we can see it change as we submit our form.

[![only-form](https://scotch.io/wp-content/uploads/2015/11/only-form.jpg)](https://scotch.io/wp-content/uploads/2015/11/only-form.jpg)

Go ahead and view the page in your browser. The Gulp task compiles the code as you write it and will refresh the browser for you also. Open dev tools and view the console. Now type the name of a public Github repository. For example, try typing `laravel/laravel` in the text box and hit submit. You should see the data spit out in a nice, readable form in the console. Pretty neat, huh?

This all is fine and dandy, but let's start displaying the files for the repository we type in.

## The File Explorer

We will implement the explorer as a separate component. Separating applications into smaller components makes code easier to comprehend and maintain. First, let's create a file at `src/js/components/github-file-explorer/index.js`. This will be the main entry for our component. Before we define anything, let's tell our Vue instance about this new component. Add the following code to our Vue instance.

```
 components: {
 githubFileExplorer: require('./components/github-file-explorer')
 }
```

This brings in our component from the given file. Since no specific file is provided, the default used is `index.js`.

In the `index.js` file of our component, we need to export an object that has all the configurations for our component. Put the following in that file.

```
 module.exports = {
 template: require('./template.html'),
 data: function() {
 return {
 path: '/',
 files: []
 };
 },
 props: {
 username: {
 type: String,
 required: true
 },
 repo: {
 type: String,
 required: true
 }
 },
 created: function() {
 if (this.username && this.repo) this.getFiles();
 }
 };
```

Here we are exporting the configuration object. We are defining a template and bringing in code from a `template.html` file in the same directory. We can bring in HTML files like this thanks to a Browserify transform called [Partialify](https://github.com/bclinkinbeard/partialify). Browserify has [a ton of other transforms](https://github.com/substack/node-browserify/wiki/list-of-transforms) you can use in your projects. We will create this template file in a minute. Next we define the data this component will need. It will track the current path and the files we receive from Github.

We then define the [props](http://vuejs.org/guide/components.html#Props) this component will accept from its parent. Because we don't want to make requests to Github if the username or repo are blank, we define them in the component as objects. This allows us to add validations that the props must pass for the data in the Vue instance to be updated. We define both these props as strings and required.

Lastly, we add a `created` method that calls `getFiles` if `username` and `repo` are set. This `created` method is a [lifecycle method](http://vuejs.org/guide/instance.html#Lifecycle_Diagram). This means it will be called when that particular point in the component's lifecycle is hit. Since it calls `getFiles`, let's create this method. Add the following code to this component.

```
 methods: {
 getFiles: function() {
 this.$http.get('https://api.github.com/repos/' + this.fullRepoUrl + '/contents' + this.path,
 function(data) {
 this.files = data;
 }
 );
 }
 }
```

Here we are defining `getFiles`. This method hits the Github API for us and returns the files in the repository for the path specified. We are using `this.$http` to make the call. That is provided by `vue-resource`, a plugin for Vue that allows us to make RESTful calls from inside our Vue instance. We could also use jQuery or something similar to do the same thing. However, I prefer to use `vue-resource` since it is made for Vue. For it to work though, we need to tell Vue to use it. We have already brought it in as a dependency in the `package.json` in this project. Let's tell Vue about it. Put the following in your `src/app.js` underneath where we turned on debugging.

```
 Vue.use(require('vue-resource'));
```

In `getFiles`, we call the `get` method on `this.$http`, which will make a GET request to the URL provided and then run the callback supplied and pass the data returned from the GET request. We build the URL by concatenating the base of the Github API with the `fullRepoUrl`, which we will create in a moment, and then adding the `path` to the end. For more information on this endpoint, see [the Github documentation](https://developer.github.com/v3/repos/contents/#get-contents). In the callback, we are setting `this.files` to the data returned.

Let's create the `fullRepoUrl` property now. Since it isn't part of our data, we can create it as a computed property, meaning Vue will calculate it on the fly whenever we need to use it. Add the following to the configuration object of our component.

```
 computed: {
 fullRepoUrl: function() {
 return this.username + '/' + this.repo;
 },
 }
```

This simply creates a computed object with a single method in it. The name of the method becomes the name of the property we access. This method simply concatenates the username and repo together for convenience.

Let's put some of this to use. Let's create our template so we can see something. Create a file at `src/js/components/github-file-explorer/template.html` and add the following into it.

```
 <div class="row">
 <div class="col-md-12">
 <table class="table">
 <caption>{{ path }}</caption>
 <thead>
 <tr>
 <th>Name</th>
 <th class="text-right"><button class="btn btn-default btn-xs" @click="goBack()" v-if="path !== '/'">Go Back</button></th>
 </tr>
 </thead>
 <tbody>
 <tr v-for="file in files">
 <td>
 <div class="file" v-if="file.type === 'file'">
 <i class="fa fa-file-o"></i>
 {{ file.name }}
 </div>
 <div class="directory" v-if="file.type === 'dir'">
 <i class="fa fa-folder-o"></i>
 <a @click="changePath(file.path)">{{ file.name }}</a>
 </div>
 </td>
 <td class="text-right">
 <a href="{{ file.download_url }}" download v-if="file.type === 'file'">
 <i class="fa fa-cloud-download"></i>
 </a>
 </td>
 </tr>
 </tbody>
 </table>
 </div>
 </div>
```

Most of this should make sense. We are creating a table with a caption. The caption is the `path` data property on this component. There is also a back button that fires the `goBack` method when clicked. We will make this in a minute. Then we iterate through each of the files using `v-for` and create rows in the table for each one. We use `v-if` to only show certain icons and links based on the type of the file. Files from Github are generally either files (`file`) or directories (`dir`). Then we display a download link but only if it is of type `file`. Notice here I am using the HTML5 attribute `download` to tell the browser to download the file and not go to the page pointed to by the `href` attribute. The availability on this [is spotty right now](http://caniuse.com/#feat=download) but should be well supported in the future.

Check out the page in the browser ... and there's nothing. Why? Well we have created the component, but we never added it to our `index.html`. So let's do that! Add the following to the `index.html` under the `hr`.

```
<github-file-explorer username="laravel" repo="laravel"></github-file-explorer>
```

This adds the component to our HTML and Vue will display it using the logic we have just created. Notice here we have used [kebab-case](http://vuejs.org/guide/components.html#camelCase_vs-_kebab-case) syntax when we put the component in HTML, but when we defined it in our Vue instance, we used camel-case. This is because JavaScript can't have hyphens in variable names. Don't worry! Vue will make the conversion for us!

Now, view your application in the browser. Voila! You should be able to see the files that are in the Laravel repository. Let's get `changePath` and `goBack` working so we can navigate the whole file structure. Input the following as methods in our component.

```
 changePath: function(path) {
 this.path = '/' + path;
 this.getFiles();
 },
 goBack: function() {
 this.path = this.path.split('/').slice(0, -1).join('/');
 if (this.path === '') this.path = '/';

 this.getFiles();
 }
```

Change path takes in a path, which is passed in from our template, sets the `path` data property equal to this path with a slash on the front, and then calls `getFiles` to update the files our Vue instance is watching. `goBack` removes the right-most portion of the URL and then calls `getFiles` also.

Check out our application in the browser. Now we can navigate through the file structure! Woohoo! Also give downloading a file a try. Clicking on the link will just download the file to your computer. Super simple.

Let's do one last thing to change the look of our explorer. Right now the files are spit out in whatever order Github returns them. Let's take back control from Github and reorder them! Let's put the directories on top in alphabetical order and then the files underneath them in alphabetical order. Put the following in the `computed` object in our component.

```
 sortedFiles: function() {
 return this.files.slice(0).sort(function(a, b) {
 if (a.type !== b.type) {
 if (a.type === 'dir') {
 return -1;
 } else {
 return 1;
 }
 } else {
 if (a.name < b.name) {
 return -1;
 } else {
 return 1;
 }
 }
 });
 }
```

This piece of code gives us a `sortedFiles` property on our Vue instance that we can use in our template. Since `sort` changes an array "in place," we create a copy using `slice(0)`. We then call `sort` on this copy and pass it a custom sorting function that gets passed a pair of our files to compare. This function checks if they are the same type. If they are, then it checks if the type of the first one is a directory. If it is, then it returns -1, which means it should be before the other one in the array. If it's not a directory, then it returns 1, meaning it should be after the other one. If they are the same type, then it will check to see if the name of the first one is less than the name of the second. Easiest way to think about this is uppercase letters are less than lowercase letters and then it goes in order of the alphabet. So "a" is less than "b" and "A" is less than "a."

Now we need to update our template to use this array instead of the raw `files` array. Update the `v-for` to use this array like so.

```
 <tr v-for="file in sortedFiles">
```

[![file-explorer](https://scotch.io/wp-content/uploads/2015/11/file-explorer.jpg)](https://scotch.io/wp-content/uploads/2015/11/file-explorer.jpg)

Now refresh the page if it hasn't already and view your work! Boom!

## Making it Interactive

So we have a nice Github explorer here, but it isn't all that great if we can't update the repo shown without updating the HTML. Let's make it work with the text field we created earlier. First we need to tell our component to watch the repo prop and update our files when we do. Add the following to our component.

```
 watch: {
 repo: function(newVal, oldVal) {
 this.getFiles();
 }
 },
```

Here we define a `watch` object. The keys of this object are properties on the component that should be watched by Vue, and if updated, the callback will be run. In this one, all we do is call our trusty method `getFiles` to update the files we have locally.

Now that we have the component watching the `repo` prop, we now need to make sure that the prop is dynamic instead of being static as it is now. Change the declaration of the component in our `index.html` to look like the following.

```
<github-file-explorer :username="username" :repo="repo"></github-file-explorer>
```

Here we are setting up a one-way binding between username on the component and username on the parent. `:username` references the data attribute `username` on the component. When we pass in `username` to it, this means any time the parent's username is updated, then the `username` property on the component will update also.

[![final](https://scotch.io/wp-content/uploads/2015/11/final.jpg)](https://scotch.io/wp-content/uploads/2015/11/final.jpg)

Now check it out in the browser. In the text box, type the name of your favorite repo on Github. If you can't think of one try using `jakeboyles/MadeInCincy`. You will see the files populate. You can now fully navigate the file structure of any public Github repository.

## Wrapping up

We have done alot. There was alot to talk through. However, think about how little code was actually necessary to write, and the code we did write was not all that crazy. Vue handles all the data-binding stuff for us so that it's one less thing we have to worry about. If you have any questions or just want to say, "Hello," please leave a comment below.
