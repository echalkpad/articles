# VueJs: Components

[Original URL](http://coligo.io/vuejs-components/)

> Practical Intro to Components in VueJs In this tutorial we'll be looking at VueJs components and understanding how they work and when to use them through a bunch of practical examples to give you a...

## Practical Intro to Components in VueJs

In this tutorial we'll be looking at VueJs components and understanding how they work and when to use them through a bunch of practical examples to give you a good feel of using components in the applications you develop. Once we cover the basics of components we'll create a simplified version of Reddit where we render a set of sample posts and add the upvote and downvote functionality to put what we learned into practice. Finally, we'll wrap it up with a practical example of reusability through components by taking the Reddit-like app we created and transform it into a commenting system similar to Disqus, where users can upvote and downvote other user's comments.

If you're completely new to VueJs, don't worry, we cover the basics in our other tutorial: [VueJs: The Basics](http://coligo.io/vuejs-the-basics/).

## Understanding components

Components are a great way to break down the complexity of the application you are building by working with smaller chunks and composing them to create more complex interfaces. They are also a great way of reusing code throughout your application or even across different applications you develop.

Let's create a simple HTML page and mount our Vue instance to a DOM element so we can create a little sandbox to learn components in:

```
<html>
<head>
<title>VueJs Components - coligo</title>
</head>
<body>
 <div id="app">

 </div>


 <script src="http://cdn.jsdelivr.net/vue/1.0.16/vue.js"></script>
 <script>

 var vm = new Vue({
 el: '#app'
 });
 </script>
</body>
</html>
```

Now that we have our sandbox ready, we can start off by creating our first simple, reusable component. In VueJs you create and register a component using the `Vue.component()` constructor which takes in 2 parameters:

1. The name of your component
2. An object containing options for your component

The options object takes in pretty much the same properties as the one you pass into the `Vue()` constructor with the exception of the `data` and `el` properties which act a little differently in components as we will see in a minute.

Let's see how this works by creating a very simple component that displays "Welcome to coligo!" wrapped in `<h1>` tags. We can create this component using the **component** constructor:

- giving it a name of **greeting**
- passing it an options object which contains a template property

<!--  -->

```
Vue.component('greeting', {
 template: '<h1>Welcome to coligo!</h1>'
});
```

Now that you have created a simple component, you can use it throughout your application by simply calling it using the name you assigned to it as though it were any regular HTML element `<greeting></greeting>`

Putting this together we have:

```
...
<body>
 <div id="app">
 <greeting></greeting>
 </div>

 <script src="https://cdn.jsdelivr.net/vue/1.0.16/vue.js"></script>
 <script>
 Vue.component('greeting', {
 template: '<h1>Welcome to coligo!</h1>'
 });


 var vm = new Vue({
 el: '#app'
 });
 </script>
</body>
...
```

Which displays the greeting we would expect:

If we use the component three times, we will get the greeting three times as well:

```
...
<div id="app">
 <greeting></greeting>
 <greeting></greeting>
 <greeting></greeting>
</div>
...
```

VueJs uses the value of the template property to replace the component wherever it's used in the HTML keeping the HTML valid and much more concise.

## Using the `<template>` Tag for More Complex Components

Now you might be thinking, what if we want to create a more complex component that is more than just a single `<h1>` tag? It would get quite messy and out of hand rather quickly if you tried to define it within that `template` property in the options object.

To avoid this issue we can use the HTML5 `<template>` tag which does not get rendered when the page is loaded. Think of it as a way to store markup on the client-side and being able to grab it and use it when we need it.

You can place the `<template>` tag anywhere in the HTML page and reference it from our Vue component using it's ID:

```
...
<body>
 <div id="app">
 <greeting></greeting>
 </div>

 <template id="greeting-template">
 <h1>Welcome to coligo!</h1>
 <button>login</button>
 <button>signup</button>
 <a href="http://coligo.io">Check out the other tutorials!</a>
 </template>


 <script src="http://cdn.jsdelivr.net/vue/1.0.16/vue.js"></script>
 <script>
 Vue.component('greeting', {
 template: '#greeting-template'
 });


 var vm = new Vue({
 el: '#app'
 });
 </script>
</body>
...
```

We can now use this method to store more complex templates and reference them from our Vue component to avoid cluttering them and maintaining their readability. There are also more elegant solutions for organizing your components such as [vueify](https://github.com/vuejs/vueify) which we will cover in a later tutorial.

## Passing Data to Components with Props

Every time you create an instance of a component, it gets it's own **isolated scope** which essentially means it doesn't have access to it's parent's data. So how does a parent pass data to it's child components? Enter props.

Let's start off with a very basic and trivial example of passing data from the parent component to the child component via props.

To do this we need to **explicitly** declare the data that the child expects to receive using the `props` option in the child's component constructor:

```
Vue.component('greeting', {
 template: '<h1>{{message}}</h1>',
 props: ['message']
});
```

Here we are defining props as an array of strings which map to the fields that the child component expects to receive from it's parent. In this case we just want the `message` field so we can replace the `{{message}}` placeholder with our own message that we pass into the component like so:

```
<greeting message="Welcome to the VueJs Components Tutorial"></greeting>
```

which would output the following:

You can also define the props as objects to enforce some validation rules on them. For example, you would like the `message` prop above to only accept strings and that it is a required field:

```
Vue.component('greeting', {
 template: '<h1>{{message}}</h1>',
 props: {
 message: {
 type: String,
 required: true
 }
 }
});
```

We won't go in too much depth with prop validation since it is slightly overkill for this tutorial, but I did want to mention it for the sake of completeness. However, if you are interested, you can read through the different [prop validation](http://vuejs.org/guide/components.html#Prop_Validation) options in the VueJs guide.

Let's have a look at a slightly less trivial and more realistic scenario in which we can use props.

> Assume we run a blog and we want to display a single blog post to the visitor

We would make a call to the database and fetch the information for that blog post which might return some object that looks like this:

```
{
 author: 'Johnnie Walker',
 title: 'Aging Your Own Whisky',
 content: 'A bunch of steps and a whole lot of content'
}
```

For the sake of simplicity, we won't be making any database calls, instead we'll just hard code that object for now:

```
var vm = new Vue({
 el: '#app',
 data: {
 author: 'Johnnie Walker',
 title: 'Aging Your Own Whisky',
 content: 'A bunch of steps and a whole lot of content'
 }
});
```

Since our blog will have a lot of posts, we created a _post_ component with a template that might look like:

```
<template id="post-template">
 <h1>{{ title }}</h1>
 <h4>{{ author }}</h4>
 <p>{{ content }}</p>
</template>
```

How would we replace the title, author and content placeholders in the above template with the actual content that we fetched from the database?

First off, we need define the blog post component by referencing the template above with the id of `post-template` as we did in the previous section:

```
Vue.component('post', {
 template: '#post-template'
});
```

Next we need to **explicitly** define those 3 properties (or props) using the `props` option which takes an array of strings that define the fields that this component accepts:

```
Vue.component('post', {
 template: '#post-template',
 props: ['title', 'author', 'content']
});
```

Now we can use the `post` component throughout our blog by using it as we would with any other component:

```
<post :title="title" :author="author" :content="content"></post>
```

In this case we dynamically bound those 3 fields to the data in the parent (hence the shorthand notation `:` for `v-bind`). Now whenever the parent data changes, those changes will be reflected in these child `post` components immediately.

## Building a Simple Reddit-like Post System

Now that we got all the basics down, let's take the pieces we learned in the previous sections and put them together to build a simple Reddit-like post system.

Just to give you a visual of what we're going to be building throughout this section, here is the finished product:

I'd encourage you to follow along with your favorite text editor or open up [JSFiddle](https://jsfiddle.net/) or [CodePen](http://codepen.io/) in a new tab.

To avoid having all the markup, JavaScript and CSS in a single file, we'll create a separate one for each. Let's start off with the JavaScript file in which we will:

- create a new Vue instance
- mount it to the `#app` div
- define our data for the posts that we will be displaying

<!--  -->

```
var vm = new Vue({
 el: "#app",
 data: {
 posts: [{
 title: "A post for our reddit demo starting at 15 votes",
 votes: 15
 },
 {
 title: "Try out the upvoting, it works, I promise",
 votes: 53
 },
 {
 title: "coligo is the bomb!",
 votes: 10
 }]
 }
});
```

Our data is an array of objects that each hold the title of the post and the number of votes it has. So let's switch over to our HTML and create the root div we are going to mount to:

```
<div id="app">
 <div class="container-fluid">
 <ul class="list-group">

 </ul>
 </div>
</div>
```

Using the `<template>` tags, we can define what a **single** post will look like for our component then we can iterate over that component, using the `v-for` directive, to display all the posts.

```
<template id="post-template">
 <li class="list-group-item">
 <i class="glyphicon glyphicon-chevron-up"></i>
 <span class="label label-primary">{{ post.votes }}</span>
 <i class="glyphicon glyphicon-chevron-down"></i>
 <a>{{ post.title }}</a>
 </li>
</template>
```

There's not much to this component's template other than some basic Bootstrap for styling a list and the 2 buttons (up and down icons) that we will be adding the upvote and downvote functionality to.

So far our full HTML should look something like this:

```
<html>

<head>
 <meta charset="utf-8">
 <title>VueJs Components Tutorial - coligo.io</title>

 <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
 <link rel="stylesheet" href="style.css">
</head>

<body>

 <div id="app">
 <div class="container-fluid">
 <ul class="list-group">

 </ul>
 </div>
 </div>

 <template id="post-template">
 <li class="list-group-item">
 <i class="glyphicon glyphicon-chevron-up"></i>
 <span class="label label-primary">{{ post.votes }}</span>
 <i class="glyphicon glyphicon-chevron-down"></i>
 <a>{{ post.title }}</a>
 </li>
 </template>

 <script src="https://code.jquery.com/jquery-2.2.0.js"></script>
 <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/1.0.16/vue.js"></script>
 <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
 <script src="voter.js"></script>
</body>

</html>
```

Now that we have the template and a Vue instance defined, let's create the `post` component in our JavaScript file so we can begin using the component and rendering it on the page. Since we'll be passing a post from the parent component to a child component we need to define the post in the _props_ array.

```
Vue.component('post', {
 template: "#post-template",
 props: ['post']
})

var vm = new Vue({
 el: "#app",
 data: {
 posts: [{
 title: "A post for our reddit demo starting at 15 votes",
 votes: 15
 },
 {
 title: "Try out the upvoting, it works, I promise",
 votes: 53
 },
 {
 title: "coligo is the bomb!",
 votes: 10
 }]
 }
})
```

Using the `<post>` component we just created and the `v-for` directive we can render that list of posts onto our page:

```
<div id="app">
 <div class="container-fluid">
 <ul class="list-group">
 <post v-for="post in posts" :post="post"></post>
 </ul>
 </div>
</div>
```

All we're doing here is looping over the _posts_ array and binding the _post_ to an object from that array so we can reference the votes and title from that post object.

And thats it! We now have all the posts rendering onto the page from the array!

All we have to do now is add the logic to the upvote and downvote buttons and we're done. Whenever a user clicks the upvote button we want to set a boolean `upvoted` to true to stop them from being able to upvote it twice and the same idea with the downvote button.

So let's go ahead and define the click event listeners on the upvote/downvote buttons and assign them their appropriate event handlers:

```
<i class="glyphicon glyphicon-chevron-up" ="upvote"></i>
<i class="glyphicon glyphicon-chevron-down" ="downvote"></i>
```

Whenever someone clicks on the upvote button a method called `upvote` will be invoked and when the downvote button is clicked, `downvote` will be invoked.

```
Vue.component('post', {
 template: "#post-template",
 props: ['post'],
 data: function () {
 return {
 upvoted: false,
 downvoted: false
 };
 },
 methods: {
 upvote: function () {
 this.upvoted = !this.upvoted;
 this.downvoted = false;
 },
 downvote: function () {
 this.downvoted = !this.downvoted;
 this.upvoted = false;
 }
 }
});
```

**Notice** how the _data_ option in the post constructor above is actually a function which returns an object containing 2 booleans. This is because we want each component to have its own set of boolean values so that if a user upvotes one post it doesn't set the `upvoted` boolean to `true` for every other post.

Great! Now when a user upvotes/downvotes a post we want to increment/decrement the number of votes it has. Thankfully, computed properties in VueJs make this sort of thing very simple.

The computed property will check if a post has been upvoted/downvoted and return the original number of votes + 1 or the original number of votes - 1 accordingly. Turning this into code:

```
...
 computed: {
 votes: function () {

 if (this.upvoted) {
 return this.post.votes + 1;
 } else if (this.downvoted) {
 return this.post.votes - 1;
 } else {
 return this.post.votes;
 }

 }
 }
...
```

Now our full JavaScript file should look something like this:

```
Vue.component('post', {
 template: "#post-template",
 props: ['post'],
 data: function () {
 return {
 upvoted: false,
 downvoted: false
 };
 },
 methods: {
 upvote: function () {
 this.upvoted = !this.upvoted;
 this.downvoted = false;
 },
 downvote: function () {
 this.downvoted = !this.downvoted;
 this.upvoted = false;
 }
 },
 computed: {
 votes: function () {
 if (this.upvoted) {
 return this.post.votes + 1;
 } else if (this.downvoted) {
 return this.post.votes - 1;
 } else {
 return this.post.votes;
 }
 }
 }
});

var vm = new Vue({
 el: "#app",
 data: {
 posts: [{
 title: "A post for our reddit demo starting at 15 votes",
 votes: 15
 },
 {
 title: "Try out the upvoting, it works, I promise",
 votes: 53
 },
 {
 title: "coligo is the bomb!",
 votes: 10
 }]
 }
});
```

The last thing to do would be to replace **post.votes** in our template to just **votes** since we are no longer accessing the number of votes from the post object directly instead we want to render the result of the computed property:

```
<template id="post-template">
 <li class="list-group-item">
 <i class="glyphicon glyphicon-chevron-up" @click="upvote"></i>
 <span class="label label-primary">{{ votes }}</span>
 <i class="glyphicon glyphicon-chevron-down" @click="downvote"></i>
 <a>{{ post.title }}</a>
 </li>
</template>
```

And now we have our fully functioning Reddit-like post system!

Just as a bonus we could set the color of the upvote/downvote button to orange once the user has clicked it to serve as a nice visual cue. VueJs has a `v-bind:class` directive which takes in an object and allows you to dynamically toggle classes. You can read more about it [here](http://vuejs.org/guide/class-and-style.html), if you're interested.

For our purposes, we want to check if the upvoted/downvoted boolean is set to true, and if so, add the `disabled` class to that button. We can simply inline this expression:

```
<i class="glyphicon glyphicon-chevron-up" @click="upvote" :class="{disabled: upvoted}"></i>

<i class="glyphicon glyphicon-chevron-down" @click="downvote" :class="{disabled: downvoted}"></i>
```

and our disabled class looks like so:

```
.disabled {
 color: orange;
}
```

Our final HTML file should look this:

```
<html>

<head>
 <meta charset="utf-8">
 <title>VueJs Components Tutorial - coligo.io</title>

 <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
 <link rel="stylesheet" href="style.css">
</head>

<body>

 <div id="app">
 <div class="container-fluid">
 <ul class="list-group">
 <post v-for="post in posts" :post="post"></post>
 </ul>
 </div>
 </div>

 <template id="post-template">
 <li class="list-group-item">
 <i class="glyphicon glyphicon-chevron-up" @click="upvote" :class="{disabled: upvoted}"></i>
 <span class="label label-primary">{{ votes }}</span>
 <i class="glyphicon glyphicon-chevron-down" @click="downvote" :class="{disabled: downvoted}"></i>
 <a>{{ post.title }}</a>
 </li>
 </template>

 <script src="https://code.jquery.com/jquery-2.2.0.js"></script>
 <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/1.0.16/vue.js"></script>
 <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
 <script src="voter.js"></script>
</body>

</html>
```

We've covered the basics of components and walked through the steps of building a Reddit-like posting system. For the last part of this tutorial, we will take a look at how we can reuse the VueJs post component we created in the previous section to build a commenting system similar to the one you find here on coligo or Reddit/HackerNews etc.

The key to building reusable components is to keep the naming generic and always ask yourself: "will I need to use this component elsewhere?"

For example: we named the component _'post'_ as opposed to something like 'reddit-post', 'frontpage-post' etc... to keep it relatively generic. Of course you can always find a better way to name a component but try to find one that will make sense in the places you intend to use it.

The commenting system we're going to be building will look like this:

The post template will remain the exact same and the only change we will be making to the HTML is adding a comment box from which a user can submit their own comments:

```
...
<div id="app">
 <div class="container-fluid">

 <ul class="list-group">
 <post v-for="comment in comments" :post="comment"></post>
 </ul>

 <div id="comment-box">
 <div class="input-group">
 <input type="text" class="form-control" placeholder="Enter a comment..." v-model="comment" .enter="postComment">
 <span class="input-group-btn">
 <button class="btn btn-primary" type="button" ="postComment">Sumbit</button>
 </span>
 </div>
 </div>

 </div>

</div>
...
```

We added an input box and a submit button that has a click handler called `postComment`. The `@keyup.enter="postComment"` is a VueJs _keyup_ event listener with a modifier which basically allows the user to submit a comment by hitting enter as opposed to having to use the submit button.

Let's go ahead and create the `postComment` method that will handle submitting these comments. The JavaScript code for the `post` component will remain exactly the same and the only thing we'll be adding is comment posting functionality to the root Vue instance. We'll take what we created for the Reddit posting system and add to it:

```
...
var vm = new Vue({
 el: "#app",
 data: {
 comments: [{
 title: "Great article!",
 votes: 2
 }, {
 title: "VueJs commenting system with votes!",
 votes: 5
 }, {
 title: "The random pun that gets a lot of upvotes",
 votes: 85
 }],
 comment: ""
 },
 methods: {
 postComment: function() {
 this.comments.push({
 title: this.comment,
 votes: 0
 })
 this.comment = "";
 }
 }
});
```

The comment input box is bound with the `v-model` directive to the _comment_ string in the data object so that whatever the user enters is automatically stored in the that comments property. When a user submits a comment (either by clicking the submit button or the enter key) the `postComment` method will create a new object with that comment string and a votes integer set to 0, then push them to the comments array. This new comment will automatically be rendered to the page by Vue.

That's all that is needed to create the commenting system with voting functionality! Hopefully this demonstrated how reusing components can save you a lot of time and effort when developing, whether it is reusing components in the same application or across different applications.

## Wrapping Up

We went over the basics of components in VueJs, passing data with props, and building templates using the `<template>` tag. We then put together a simple Reddit-like posting system using the concepts we learned and demonstrated how we can reuse the component to build a commenting system.

Some common gotchas to remember:

- always remember to declare the props you want to use **explicitly** (`props: ['post']`)
- when passing data using props, be sure to bind them using `v-bind:*` syntax to avoid passing a literal string (`:post="post"`)
- the data object must be returned from a function to ensure a separate copy is created for each component
- don't forget the root Vue instance! You still need to instantiate it to display your component in it
