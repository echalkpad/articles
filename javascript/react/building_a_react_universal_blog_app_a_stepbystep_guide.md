# Building a React Universal Blog App: A Step-by-Step Guide

[Original URL](http://www.sitepoint.com/building-a-react-universal-blog-app-a-step-by-step-guide/)

> When we think of single page applications (SPAs) we think browsers, JavaScript, speed and, in my case, invisibility to search engines. This is because a SPA renders a page's content using...

When we think of single page applications (SPAs) we think browsers, JavaScript, speed and, in my case, invisibility to search engines. This is because a SPA renders a page's content using JavaScript and since web crawlers do not use a browser to view web pages, they cannot view and index the content. Or, to better say, most of them can't. This is a problem that some developers have tried to solve in various ways:

1. Adding an escaped fragment version of a website which requires all pages to be available in static form and adds a lot of extra work ([now deprecated](https://developers.google.com/webmasters/ajax-crawling/docs/specification?hl=en)).
2. Using a paid service to un-browserify a SPA into static markup for search engine spiders to crawl.
3. Trust that search engines are now advanced enough to read our JavaScript-only content (I wouldn't just yet).

Using Node.js on the server and React on the client, we can build our JavaScript app to be **universal** (or **isomorphic**). This could offer several benefits from server-side and browser-side rendering, allowing both search engines and humans using browsers to view our SPA content.

In this step-by-step tutorial, made of two parts, I will show you how to build a React Universal Blog App that will first render markup on the server side to make our content available to search engines. Then, it will let the browser take over in a single page application that is both fast and responsive.

## Getting Started

Our universal blog app will make use of the following technologies and tools:

To start, run the following commands:

```
mkdir react-universal-blog
cd react-universal-blog
```

Now create a `package.json` file and add the following content:

```
{
 "name": "react-universal-blog",
 "version": "1.0.0",
 "description": "",
 "main": "app-server.js",
 "dependencies": {
 "babel": "^5.8.29",
 "babel-core": "^5.8.32",
 "babel-loader": "^5.3.2",
 "cosmicjs": "^2.0.0",
 "events": "^1.1.0",
 "express": "^4.13.3",
 "flux": "^2.1.1",
 "history": "^1.14.0",
 "hogan-express": "^0.5.2",
 "lodash": "^3.10.1",
 "react": "^0.14.1",
 "react-dom": "^0.14.1",
 "react-router": "^1.0.1",
 "webpack": "^1.12.2"
 },
 "scripts": {
 "development": "cp views/index.html public/index.html && NODE_ENV=development webpack && webpack-dev-server --content-base public/ --hot --inline --devtool inline-source-map --history-api-fallback"
 },
 "author": "",
 "license": "ISC",
 "devDependencies": {
 "react-hot-loader": "^1.3.0",
 "webpack-dev-server": "^1.12.1"
 }
}
```

In this file you will notice that we've added:

1. [Babel](https://babeljs.io/) to package our CommonJS modules and convert our ES6 and React JSX into browser-compatible JavaScript.
2. The Cosmic JS official Node.js client to easily serve our blog content from the Cosmic JS cloud-hosted content API.
3. Flux for app data management (which is a very important element in our React application).
4. React for UI managment on server and browser.
5. React Router for routes on server and browser.
6. [webpack](https://github.com/webpack/webpack) for bringing everything together into a `bundle.js` file.

We've also added a script in our `package.json` file. When we run `npm run development`, the script copies the `index.html` file from our `views` folder into our `public` folder. Then, it sets the content base for our `webpack-dev-server` to `public/` and enables hot reloading (on `.js` file save). Finally, it helps us debug our components at the source and gives us a fallback for pages it can't find (falls back to `index.html`).

Now let's set up our webpack configuration file by editing the file `webpack.config.js`:

```
// webpack.config.js
if(process.env.NODE_ENV === 'development'){
 var loaders = ['react-hot','babel']
} else {
 var loaders = ['babel']
}
module.exports = {
 devtool: 'eval',
 entry: './app-client.js',
 output: {
 path: __dirname + '/public/dist',
 filename: 'bundle.js',
 publicPath: '/dist/'
 },
 module: {
 loaders: [{
 test: /\.js$/,
 loaders: loaders,
 exclude: /node_modules/
 }]
 }
};
```

You'll notice that we've added an `entry` property with a value of `app-client.js`. This file serves as our app client entry point, meaning that from this point webpack will bundle our application and output it to `/public/dist/bundle.js` (as specified in the `output` property). We also use loaders to let Babel work its magic on our ES6 and JSX code. React Hot Loader is used for hot-loading (no page refresh!) during development.

Before we jump into React-related stuff, let's get the look-and-feel of our blog ready to go. Since I'd like you to focus more on functionality than style in this tutorial, here we'll use a pre-built front-end theme. I've chosen one from [Start Bootstrap](http://startbootstrap.com/) called [Clean Blog](http://startbootstrap.com/template-overviews/clean-blog/).

Create a folder called `views` and inside it an `index.html` file. Open the HTML file and add the following code:

```
<!DOCTYPE html>
<html lang="en">
<head>
 <meta charset="utf-8">
 <meta http-equiv="X-UA-Compatible" content="IE=edge">
 <meta name="viewport" content="width=device-width, initial-scale=1">
 <meta name="description" content="">
 <meta name="author" content="">
 <title>{{ site.title }}{{# page }} | {{ page.title }}{{/ page }}</title>
 <!-- Bootstrap Core CSS -->
 <link href="/css/bootstrap.min.css" rel="stylesheet">
 <!-- Custom CSS -->
 <link href="/css/clean-blog.min.css" rel="stylesheet">
 <link href="/css/cosmic-custom.css" rel="stylesheet">
 <!-- Custom Fonts -->
 <link href="//maxcdn.bootstrapcdn.com/font-awesome/4.1.0/css/font-awesome.min.css" rel="stylesheet" type="text/css">
 <link href="//fonts.googleapis.com/css?family=Lora:400,700,400italic,700italic" rel="stylesheet" type="text/css">
 <link href="//fonts.googleapis.com/css?family=Open+Sans:300italic,400italic,600italic,700italic,800italic,400,300,600,700,800" rel="stylesheet" type="text/css">
 <!-- HTML5 Shim and Respond.js IE8 support of HTML5 elements and media queries -->
 <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
 <!--[if lt IE 9]>
 <script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
 <script src="https://oss.maxcdn.com/libs/respond.js/1.4.2/respond.min.js"></script>
 <![endif]-->
</head>
<body class="hidden">
 <div id="app">{{{ reactMarkup }}}</div>
 <script src="/js/jquery.min.js"></script>
 <script src="/js/bootstrap.min.js"></script>
 <script src="/js/clean-blog.min.js"></script>
 <script src="/dist/bundle.js"></script>
</body>
</html>
```

To get all of the JS and CSS files included in `public`, you can get them from the [GitHub repository](https://github.com/sitepoint-editors/react-universal-blog). [Click here to download the files](https://github.com/sitepoint-editors/react-universal-blog/archive/master.zip).

Generally I would use the fantastic [React Bootstrap](https://react-bootstrap.github.io/) package and refrain from using jQuery. However, for the sake of brevity we'll keep the theme's pre-built jQuery functionality.

In our `index.html` file, we will have our React mount point set up at the `div` where `id="app"`. The template variable `{{{ reactMarkup }}}` will be converted into our server-rendered markup and then once the browser kicks in, our React application will take over and mount to the `div` with `id="app"`. To improve the user experience while our JavaScript loads everything, we add `class="hidden"` to our body. Then, we remove this class once React has mounted. It might sound a bit complicated, but I'll show you how we'll do this in a minute.

At this point, your app should have the following structure:

```
package.json
public
 |-css
 |-bootstrap.min.css
 |-cosmic-custom.css
 |-js
 |-jquery.min.js
 |-bootstrap.min.js
 |-clean-blog.min.js
views
 |-index.html
webpack.config.js
```

Now that we have our static pieces done, let's start building some React Components.

## Our Blog App Components (Basic Example)

Let's begin building the UI for our app by setting up the pages for our blog. Because this is going to be a portfolio blog for a creative professional, our blog will have the following pages:

Let's start by creating a file called `app-client.js` and add the following content to it:

```
// app-client.js
import React from 'react'
import { render } from 'react-dom'
import { Router } from 'react-router'
import createBrowserHistory from 'history/lib/createBrowserHistory'
const history = createBrowserHistory()

// Routes
import routes from './routes'

const Routes = (
 <Router history={history}>
 { routes }
 </Router>
)

const app = document.getElementById('app')
render(Routes, app)
```

To better understand how React Router works, you can visit [their GitHub repo](https://github.com/rackt/react-router). The gist here is that we have in `app-client.js` our `Router` component that has a browser history for our client-side routing. Our server-rendered markup won't need browser history, so we'll create a separate `routes.js` file to be shared between our server and client entry points.

Add the following to the `routes.js`file:

```
// routes.js
import React, { Component } from 'react'
import { Route, IndexRoute, Link } from 'react-router'

// Main component
class App extends Component {
 componentDidMount(){
 document.body.className=''
 }
 render(){
 return (
 <div>
 <h1>React Universal Blog</h1>
 <nav>
 <ul>
 <li><Link to="/">Home</Link></li>
 <li><Link to="/about">About</Link></li>
 <li><Link to="/work">Work</Link></li>
 <li><Link to="/contact">Contact</Link></li>
 </ul>
 </nav>
 { this.props.children }
 </div>
 )
 }
}

// Pages
class Home extends Component {
 render(){
 return (
 <div>
 <h2>Home</h2>
 <div>Some home page content</div>
 </div>
 )
 }
}
class About extends Component {
 render(){
 return (
 <div>
 <h2>About</h2>
 <div>Some about page content</div>
 </div>
 )
 }
}
class Work extends Component {
 render(){
 return (
 <div>
 <h2>Work</h2>
 <div>Some work page content</div>
 </div>
 )
 }
}
class Contact extends Component {
 render(){
 return (
 <div>
 <h2>Contact</h2>
 <div>Some contact page content</div>
 </div>
 )
 }
}
class NoMatch extends Component {
 render(){
 return (
 <div>
 <h2>NoMatch</h2>
 <div>404 error</div>
 </div>
 )
 }
}

export default (
 <Route path="/" component={App}>
 <IndexRoute component={Home}/>
 <Route path="about" component={About}/>
 <Route path="work" component={Work}/>
 <Route path="contact" component={Contact}/>
 <Route path="*" component={NoMatch}/>
 </Route>
)
```

From here we have a pretty basic working example of a blog app with a few different pages. Now, let's run our application and check it out! In your terminal run the following commands:

```
mkdir public
npm install
npm run development
```

Then navigate to <http://localhost:8080> in your browser to see your basic blog in action.

These things done, it's now time to get this to run on the server. Create a file called `app-server.js` and add this content:

```
// app-server.js
import React from 'react'
import { match, RoutingContext } from 'react-router'
import ReactDOMServer from 'react-dom/server'
import express from 'express'
import hogan from 'hogan-express'

// Routes
import routes from './routes'

// Express
const app = express()
app.engine('html', hogan)
app.set('views', __dirname + '/views')
app.use('/', express.static(__dirname + '/public/'))
app.set('port', (process.env.PORT || 3000))

app.get('*',(req, res) => {

 match({ routes, location: req.url }, (error, redirectLocation, renderProps) => {

 const reactMarkup = ReactDOMServer.renderToStaticMarkup()

 res.locals.reactMarkup = reactMarkup

 if (error) {
 res.status(500).send(error.message)
 } else if (redirectLocation) {
 res.redirect(302, redirectLocation.pathname + redirectLocation.search)
 } else if (renderProps) {

 // Success!
 res.status(200).render('index.html')

 } else {
 res.status(404).render('index.html')
 }
 })
})

app.listen(app.get('port'))

console.info('==> Server is listening in ' + process.env.NODE_ENV + ' mode')
console.info('==> Go to http://localhost:%s', app.get('port'))
```

In `app-server.js` we are loading the basic routes that we've set up. These are converting the rendered markup into a string and then passing it as a variable to our template.

The following step will be creating a file, called `app.js`, that will allow us to use ES6 in Node.js. Such file will have the following code:

```
// app.js
require('babel/register')
require('./app-server.js')
```

We're ready to start our server and view our code on it, but first, let's create a script to do so.

Open your `package.json` file and edit the `script` section to look like the following:

```
// ...
"scripts": {
 "development": "cp views/index.html public/index.html && NODE_ENV=development webpack && webpack-dev-server --content-base public/ --hot --inline --devtool inline-source-map --history-api-fallback",
 "production": "rm -rf public/index.html && NODE_ENV=production webpack -p && NODE_ENV=production node app.js",
 "start": "npm run production"
}
// ...
```

Now that we have our `production` script set up, we can run our code on both the server side and the client side. In your terminal execute:

```
npm start
```

Navigate in your browser to <http://localhost:3000>. You should see your simple blog content and be able to quickly and easily navigate through the pages in SPA mode.

Go ahead and hit `view source`. Notice our SPA code is there for all robots to find as well. We get the best of both worlds!

## Conclusions

In this first part we've started digging into the world of React and see how we can use it, together with Node.js, to build a React Universal Blog App.

If you wish to take your blog to the next level and know how to add and edit content, don't forget to read the second part. We'll get into the real meat of how to easily scale our React Universal Blog App using React organizational concepts and the **Flux pattern**.
