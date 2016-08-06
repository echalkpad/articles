# Meteor React tutorial updated - match made in heaven.

[Original URL](http://sergiotapia.me/2015/09/18/react-and-meteor-match-made-in-heaven/)

> Meteor is still kind of in that wild west style point in time where things move fast, and today's good advice is tomorrow's bad practice. (RIP IronRouter) In this article, I'm going to teach you how...

Meteor is still kind of in that wild west style point in time where things move fast, and today's good advice is tomorrow's bad practice.

_(RIP IronRouter)_

In this article, I'm going to teach you how to integrate React into a Meteor application in a clear, concise no bullshit way. It's going to use current best practices, proven packages, and easy to understand folder/file structure.

Why should you use React? [Read this article](http://reactfordesigners.com/labs/reactjs-introduction-for-people-who-know-just-enough-jquery-to-get-by/) if you want to see what the hype is about. If this article doesn't convince then you maybe React isn't for you.

Let's get started.

First create the project.

```
meteor create reactexample 
cd reactexample 
```

Add the package we're going to need.

```
meteor add react 
meteor add kadira:react-layout 
meteor add kadira:flow-router 
```

The `react` package is the official MDG Meteor package for React integration.

The `kadira:react-layout` package is going to allow us to natively invoke React components in our routes.

And finally `kadira:flow-router` is going to allow us to route urls to render specific components.

Erase the original three files generated for your brand new project, and create a folder called `client`.

In there create a `main.html` and a `main.jsx`.

```
<!-- This is inside main.html --> 
<head> 
 <title>Example React App</title>
</head>

<body> 
 <div id="react-root"></div>
</body>

// This is inside main.jsx
MainLayout = React.createClass({ 
 render() {
 return (
 <div>
 <Header />
 <div class="container">
 {this.props.content}
 </div>
 <Footer />
 </div>
 )
 }
});
```

Notice how the .html file has a `#react-root` div. That's where the MainLayout is going to be rendered, let's wire that up.

Create a folder called `lib` at your root and a file called `router.jsx` inside.

```
FlowRouter.route('/', { 
 action() {
 ReactLayout.render(MainLayout, { content: <Wall /> });
 }
});
```

Whenever someone visits the root path, we're going to render the `MainLayout` component to the `#react-root` div. Notice we're also passing along a parameter, a simple dummy React component. Let's create these components next.

Make your folders looks something like:

```
/client
 /components
 wall.jsx
 /layout
 footer.jsx
 header.jsx
/lib
 router.jsx
```

Cool stuff! So we have a folder for our components and another for our layout components such as `Header` and `Footer`.

```
// This is inside header.jsx
Header = React.createClass({ 
 render() {
 return (
 <div class="header">
 <p>This is the header.</p>
 </div>
 )
 }
})

// This is inside footer.jsx
Footer = React.createClass({ 
 render() {
 return (
 <div class="footer">
 <p>This is the footer.</p>
 </div>
 )
 }
});

// This is inside wall.jsx
Wall = React.createClass({ 
 render() {
 return (
 <div class="footer">
 <h1>Welcome to the Wall!</h1>
 </div>
 )
 }
});
```

Now run your server and you should see your React components rendered nicely.

In the next article, I'm going to be creating some Components that use Mongo data - again super simple stuff, and you'll see that by using React you've made publishing and subscribing even easier to reason about.
