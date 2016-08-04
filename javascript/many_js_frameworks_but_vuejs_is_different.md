# Many JS Frameworks but Vue.js Is Different

[Original URL](http://taha-sh.com/blog/many-js-frameworks-but-vuejs-is-different)

> Nowadays, we have countless Javascript libraries available for us to use. Every once in a while we get a new framework/library for Javascript. I've used many Javascript frameworks, but...

Nowadays, we have countless Javascript libraries available for us to use. Every once in a while we get a new framework/library for Javascript. I've used many Javascript frameworks, but I've never seen something as simple and flexible as [Vue.js](http://vuejs.org/).

You'll be amazed by how a few lines you need to write to accomplish common tasks in Javascript with Vue.js.

![Vue.js website](http://taha-sh.com/images/blog/1438789978many-js-frameworks-but-vue-js-is-different-1.png) In this tutorial, I'm going to show you all of that by building a simple tasks app. To see what we're going to build, check out the [demo](http://jsfiddle.net/dvvzhLh4/). You can also get the source code on [github](https://github.com/TahaSh/Tasks-App-With-VueJS).

![Tasks App](http://taha-sh.com/images/blog/1438790026many-js-frameworks-but-vue-js-is-different-2.png) **Note:** If you feel that you don't understand everything here, don't worry, this tutorial is just intended to show you how awesome Vue.js is. Maybe, in the future, there'll be more tutorials that review the basics of Vue.js.

## Setting Up The Project

For this app, we need three files: `index.html`, `app.js` and `app.css`.

As a start, here's how `index.html` should look like:

### index.html

```
<!DOCTYPE html>
<html lang="en">
<head>
 <meta charset="UTF-8">
 <title>Tasks App</title>
 <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap.min.css"/>
 <link rel="stylesheet" href="app.css">
</head>
<body>


 <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/0.12.9/vue.min.js"></script>
 <script src="app.js"></script>
</body>
</html>
```

All we have here is a basic HTML5 boilerplate. For this tutorial, I pulled in `vue.js` via CDN -- but feel free to use bower if you want. Also for better styling, I included bootstrap.

## Define a Vue Instance

To start things off, we have to declare a Vue instance and tell it which DOM element it controls.

In `index.html`, add `<div id="app"></div>` which will be the view for our Vue instance.

### index.html

```
<!-- ... -->

<body>

<div id="app"></div>

<!-- ... -->
```

Now to create the Vue instance, go to `app.js` and put this in.

### app.js

```
new Vue({
 el: '#app'
});
```

With that, we have created a Vue instance and associated it with the `div#app` tag from `index.html`. This means the Vue instance will control the tag `div#app` and everything it houses.

For this app we need a little bit of styling. So go to `app.css` and add this code.

```
#app {
 padding: 2em;
}

#app input[type=text] {
 margin-bottom: 1em
}

#app h1 {
 margin-bottom: 0.5em;
}

.task:hover .remove-btn {
 display: block;
}

.remove-btn {
 display: none;
 background: transparent;
 border: 0;
 color: darkred;
}

.completed {
 color: silver;
 text-decoration: line-through;
}
```

## Show a List of Tasks

Since Vue uses two-way binding, like angular, we need a way to specify what data Vue will use (as a model). We can do that by declaring an object named `data` in the Vue instance. Like this:

```
new Vue({
 el: '#app',

 data: {

 }
});
```

We need a list of tasks, so we have to declare an array of tasks in that `data` object.

```
new Vue({
 el: '#app',

 data: {
 tasks: []
 }
});
```

Each task will have a `title` and `completed` property to specify if the task is completed. So each task object will look like this: `{ title: 'My task', completed: false }`.

For now, let's just add a bunch of tasks in that array as hard-coded values.

```
new Vue({
 el: '#app',

 data: {
 tasks: [
 { title: 'Go to grocery store', completed: false },
 { title: 'Do the dishes', completed: false },
 { title: 'Call John', completed: false }
 ]
 }
});
```

We have defined the tasks, but how would we display them? Well, we have two ways: **directives** or **mustache binding**.

For now, I want you to think of directives as html attributes that are prefixed with `v-`. Examples: `v-model`, `v-text`, `v-on`, etc....

Another way is to use mustache-like syntax. Like this: `{{ data }}`.

Let's use the latter one. To see how it works, open `index.html`, and put this `{{ tasks[0].title }}` inside `div#app`. After that, if you view the browser, you should see the title of the first task: **Go to grocery store**.

This is to show a single value. However, to show all tasks we have to use the `v-repeat` directive. Like this:

### index.html

```
<div id="app">
 <h1>Tasks</h1>
 <ul class="list-group">
 <li class="list-group-item task"
 v-repeat="task : tasks"
 >
 <input type="checkbox">
 {{ task.title }}
 </li>
 </ul>
</div>
```

You can think of the `v-repeat` directive as a for-in loop. So we access each element in the `tasks` array as `task`.

Now if you get back to the browser, you should see all tasks displayed in a list group.

![Showing a list of tasks](http://taha-sh.com/images/blog/1438790055many-js-frameworks-but-vue-js-is-different-3.png)

## Deleting Tasks

We need a button for each task, when it's clicked, the task should be deleted.

### index.html

```
<div id="app">
 <ul class="list-group">
 <li class="list-group-item task"
 v-repeat="task : tasks"
 >
 <input type="checkbox">
 {{ task.title }}

 <button class="remove-btn pull-right" v-on="click: removeTask(task)"><span class="glyphicon glyphicon-remove"></span></button>

 </li>
</ul>
</div>
```

That button has a `v-on` directive. This directive is used to bind event listeners to DOM events. So when the button is clicked, we should call `removeTask()` method passing in the task to be deleted.

But where to define that method? The answer is in a `methods` object in the Vue instance.

So add this in the Vue instance:

### app.js

```
new Vue({
 el: '#app',

 data: {
 tasks: [
 { title: 'Go to grocery store', completed: false },
 { title: 'Do the dishes', completed: false },
 { title: 'Call John', completed: false }
 ]
 },

 methods: {
 removeTask: function(task) {
 this.tasks.$remove(task);
 }
 }
});
```

Now when you put the cursor on the task, a delete button should be displayed. When you click on it, the task should be deleted.

![Showing delete button](http://taha-sh.com/images/blog/1438790078many-js-frameworks-but-vue-js-is-different-4.png)

## Completing Tasks

Now let's see how we can complete tasks. The idea is to toggle a class on each task item. So when the task is completed, we should add a `.completed` class to it. And when it's uncompleted, we should remove that class.

That action should happen when the user click on the checkbox for each task. So we need to associate some data with each checkbox. For that we use the directive `v-model`. This directive is used to associate data with form input. The data we should associate is the `completed` property for each task (`task.completed`).

So what we want do is to change `task.completed` to **true** when the checkbox is checked. And back to **false** when it's unchecked.

All we have to do to accomplish this is to add `v-model="task.completed"` for the checkbox.

```
<input type="checkbox" v-model="task.completed">
```

With that, the `completed` property is changed with the checkbox. But how to determine if we should add or remove (i.e. toggle) the class `.completed`. This is done simply by using the directive `v-class`. This directive takes the class and the condition to show that class.

So add `v-class="completed: task.completed"` to the list item. In this case, the class `.completed` will be set only when `task.completed` is a truthy value.

```
<div id="app">
 <ul class="list-group">
 <li class="list-group-item task"
 v-repeat="task : tasks"
 v-class="completed: task.completed"
 >
 <input type="checkbox">
 {{ task.title }}

 <button class="remove-btn pull-right" v-on="click: removeTask(task)"><span class="glyphicon glyphicon-remove"></span></button>

 </li>
 </ul>
</div>
```

Now your tasks can be completed and uncompleted.

![Completing tasks](http://taha-sh.com/images/blog/1438790097many-js-frameworks-but-vue-js-is-different-5.png)

## Adding New Tasks

For adding new tasks, we need a text input to write in the task's title. By default, each new task should be uncompleted.

Add the input above `ul#list-group`.

### index.html

```
<input type="text"
 class="form-control"
 placeholder="New Task"
 v-model="newTask.title"
 v-on="keyup: addTask | key 'enter'"
>
```

The new task's data will be represented by the property `newTask`. So we have to declare it in the Vue instance.

### app.js

```
data: {
 newTask:{ title: '', completed: false},
 //...
}
```

Notice that I've associated an event listener with the input which will be triggered when the user presses the enter key. I've specified the key using filters -- which you probably know from angular.

Now let's define the `addTask` handler in the methods object.

### app.js

```
methods: {
 addTask: function() {
 if ( ! this.newTask.title) {
 return;
 }

 this.tasks.push({
 title: this.newTask.title,
 completed: false
 });

 this.newTask = { title: '', completed: false};
 },

 // ...
}
```

In the `addTask` method, we first make sure that the task to be added is not empty. If not, then we add the new task to the tasks array using the push method. After that, we clear out the text input by resetting the values of `this.newTask`.

If you switch back to the browser, you should be able to add a new task by typing in its title and pressing enter.

![Adding new task](http://taha-sh.com/images/blog/1438790135many-js-frameworks-but-vue-js-is-different-6.png)

## Editing Tasks

The last feature we're going to add is the ability to edit tasks. The way we want to edit tasks is by double-clicking a task from the list. Then that task's title will be editable in the same text input we use to add tasks. After we're done with our changes, we press enter to commit the changes and clear out the text input.

It seems that it's a tough one, but really isn't. All you have to do in `index.html` is to add this event listener to the task item: `v-on="dblclick: startEditingTask(task)"`.

```
<li class="list-group-item task"
 v-repeat="task : tasks"
 v-class="completed: task.completed"
 v-on="dblclick: startEditingTask(task)"
>
```

Then define that handler in the Vue instance.

```
methods: {
 startEditingTask: function(task) {
 this.newTask = task;
 this.editingTask = true;
 },
 // ...
}
```

In that handler we assign the task that we want to edit to the `newTask` property -- so we can use the same text input to update it. But what about the `editingTask` property?

Well, this one is used to determine whether the task in the text input is a new one to be added or a task to be updated. But for that to work, we have to define it in the `data` object.

```
data: {
 newTask:{ title: '', completed: false},

 editingTask: false,

 tasks: [
 { title: 'Go to grocery store', completed: false },
 { title: 'Do the dishes', completed: false },
 { title: 'Call John', completed: false }
 ]
}
```

The last change we'll do in `index.html` is to change the event handler of the text input (when the enter is pressed) to another method -- called `pressedEnter`.

```
<input type="text"
 class="form-control"
 placeholder="New Task"
 v-model="newTask.title"
 v-on="keyup: pressedEnter | key 'enter'"
>
```

Now in your Vue instance add this method.

```
pressedEnter: function() {
 if ( ! this.editingTask) {
 this.addTask();
 }

 this.newTask = { title: '', completed: false};
 this.editingTask = false;
}
```

Notice that I moved `this.newTask = { title: '', completed: false};` from the `addTask` method to the `pressedEnter`. Also, in the last line, I changed `editingTask` back to false so users would be able to add more tasks in case they were editing a task before that.

Now we're able to edit tasks.

![Edit tasks](http://taha-sh.com/images/blog/1438790156many-js-frameworks-but-vue-js-is-different-7.png)

## Removing Tasks When They Are Empty

Now what if the user provided an empty string when editing the task. In this case we should just remove that task because empty tasks will help nobody.

![Update task with empty string](http://taha-sh.com/images/blog/1438790195many-js-frameworks-but-vue-js-is-different-8.png) To tackle that, update `pressedEnter` accordingly:

```
pressedEnter: function() {
 if (this.editingTask) {
 this.removeIfEmpty();
 } else {
 this.addTask(); 
 }

 this.newTask = { title: '', completed: false};
 this.editingTask = false;
}
```

Then create that `removeIfEmpty` method and put this into it.

```
removeIfEmpty: function() {
 if ( ! this.newTask.title) {
 this.removeTask(this.newTask);
 }
}
```

## All The Code

Here are the three files that we've been working on.

### index.html

```
<!DOCTYPE html>
<html lang="en">
<head>
 <meta charset="UTF-8">
 <title>Tasks App</title>
 <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap.min.css"/>
 <link rel="stylesheet" href="app.css">
</head>
<body>

<div id="app">
 <h1>Tasks</h1>

 <input type="text"
 class="form-control"
 placeholder="New Task"
 v-model="newTask.title"
 v-on="keyup: pressedEnter | key 'enter'"
 >

 <ul class="list-group">
 <li class="list-group-item task"
 v-repeat="task : tasks"
 v-class="completed: task.completed"
 v-on="dblclick: startEditingTask(task)"
 >
 <input type="checkbox" v-model="task.completed">
 {{ task.title }}

 <button class="remove-btn pull-right" v-on="click: removeTask(task)"><span class="glyphicon glyphicon-remove"></span></button>
 </li>
 </ul>
</div>


<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/0.12.9/vue.min.js"></script>
<script src="app.js"></script>
</body>
</html>
```

### app.js

```
new Vue({
 el: '#app',

 data: {
 newTask:{ title: '', completed: false},

 tasks: [
 { title: 'Go to grocery store', completed: false },
 { title: 'Do the dishes', completed: false },
 { title: 'Call John', completed: false }
 ]
 },

 methods: {
 pressedEnter: function() {
 if (this.editingTask) {
 this.removeIfEmpty();
 } else {
 this.addTask(); 
 }

 this.newTask = { title: '', completed: false};
 this.editingTask = false;
 },

 addTask: function() {
 if ( ! this.newTask.title) {
 return;
 }

 this.tasks.push({
 title: this.newTask.title,
 completed: false
 });
 },

 removeTask: function(task) {
 this.tasks.$remove(task);
 },

 startEditingTask: function(task) {
 this.newTask = task;
 this.editingTask = true;
 },
 removeIfEmpty: function() {
 if ( ! this.newTask.title) {
 this.removeTask(this.newTask);
 }
 }
 }
});
```

### app.css

```
#app {
 padding: 2em;
}

#app input[type=text] {
 margin-bottom: 1em
}

#app h1 {
 margin-bottom: 0.5em;
}

.task:hover .remove-btn {
 display: block;
}

.remove-btn {
 display: none;
 background: transparent;
 border: 0;
 color: darkred;
}

.completed {
 color: silver;
 text-decoration: line-through;
}
```

## Wrap Up

With that, we're done with our little tasks app. I hope you can see how easy it is to accomplish things in Vue.js.

Actually, we've barely scratched the surface in this tutorial. Vue has many more things to learn. Anyway, the purpose of this tutorial was to show you the basic workflow of building apps in Vue.js.

As I said at the beginning, don't worry if it doesn't all make sense to you. This tutorial was just to whet your appetite to learn more about Vue. So if you found this library very interesting to you, and you want to learn it from scratch, please let me know this in the comments.
