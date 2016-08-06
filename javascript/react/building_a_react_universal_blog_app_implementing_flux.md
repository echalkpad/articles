# Building a React Universal Blog App: Implementing Flux

[Original URL](http://www.sitepoint.com/building-a-react-universal-blog-app-implementing-flux/)

> In the first part of this mini-series we've started digging into the world of React and see how we can use it, together with Node.js, to build a React Universal Blog App. In this second and...

In [the first part of this mini-series](http://sitepoint.com/building-a-react-universal-blog-app-a-step-by-step-guide) we've started digging into the world of React and see how we can use it, together with Node.js, to build a React Universal Blog App.

In this second and last part, we'll take our blog to the next level by learning how to add and edit content. We'll also get into the real meat of how to easily scale our React Universal Blog App using React organizational concepts and the **Flux pattern**.

## Break It Down for Me

As we add more pages and content to our blog, our `routes.js` file will quickly become big. Since it's one of React's guiding principles to break things up into smaller, manageable pieces, let's separate our routes into different files.

Open your `routes.js` file and edit it so that it'll have the following code:

```
// routes.js
import React from 'react'
import { Route, IndexRoute } from 'react-router'

// Store
import AppStore from './stores/AppStore'

// Main component
import App from './components/App'

// Pages
import Blog from './components/Pages/Blog'
import Default from './components/Pages/Default'
import Work from './components/Pages/Work'
import NoMatch from './components/Pages/NoMatch'

export default (
 <Route path="/" data={AppStore.data} component={App}>
 <IndexRoute component={Blog}/>
 <Route path="about" component={Default}/>
 <Route path="contact" component={Default}/>
 <Route path="work" component={Work}/>
 <Route path="/work/:slug" component={Work}/>
 <Route path="/blog/:slug" component={Blog}/>
 <Route path="*" component={NoMatch}/>
 </Route>
)
```

We've added few different pages to our blog and significantly reduced the size of our `routes.js` file by breaking the pages up into separate components. Moreover, note that we've added a Store by including `AppStore` which is very important for the next steps in scaling out our React application.

## The Store: The Single Source of Truth

In the Flux pattern, the Store is a very important piece because it acts as the _single source of truth_ for data management. This is a crucial concept in understanding how React development works and one of the most touted benefits of React. The beauty of this discipline is that at any given state of our app we can access the `AppStore`'s data and know exactly what's going on within it. There are a few key things to keep in mind if you want to build a data-driven React application:

1. We never maniplulate the DOM directly.
2. Our UI answers to data and data live in the store.
3. If we need to change out our UI, we can go to the store and the store will create the new data state of our app.
4. New data is fed to higher level components, then passed down to the lower-level components through `props` composing the new UI based on the new data received.

With those four points, we basically have the foundation for a "one-way data flow" application. This means also that at any state in our application we can `console.log(AppStore.data)` and if we build our app correctly, we'll know exactly what we can expect to see. You'll experience how powerful this is for debugging as well.

Now let's create a store folder called `stores`. Inside it, create a file called `AppStore.js` with the following content:

```
// AppStore.js
import { EventEmitter } from 'events'
import _ from 'lodash'

export default _.extend({}, EventEmitter.prototype, {

 // Initial data
 data: {
 ready: false,
 globals: {},
 pages: [],
 item_num: 5
 },

 // Emit change event
 emitChange: function(){
 this.emit('change')
 },

 // Add change listener
 addChangeListener: function(callback){
 this.on('change', callback)
 },

 // Remove change listener
 removeChangeListener: function(callback) {
 this.removeListener('change', callback)
 }

})
```

You can see that we've attached an event emitter. This allows us to edit data in our store, then re-render our application using `AppStore.emitChange()`. This is a powerful tool that should only be used in certain places in our application. Otherwise, it can be hard to understand where `AppStore` data is being altered, which brings us to the next point...

## React Components – Higher and Lower Level

Dan Abramov wrote a [great post on the concept of smart and dumb components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.8endbt9xu). The idea is to keep data-altering actions just in the higher-level (smart) components while the lower-level (dumb) components take the data they're given through props and render UI based on that data. Any time there's an action performed on a lower-level component, that event is passed up through props to the higher level components in order to be processed into an action. Then it redistributes the data (one-way data flow) back through the application.

Said that, let's start building some components. To do that, create a folder called `components`. Inside it, create a file called `App.js` with this content:

```
// App.js
import React, { Component } from 'react'

// Dispatcher
import AppDispatcher from '../dispatcher/AppDispatcher'

// Store
import AppStore from '../stores/AppStore'

// Components
import Nav from './Partials/Nav'
import Footer from './Partials/Footer'
import Loading from './Partials/Loading'

export default class App extends Component {

 // Add change listeners to stores
 componentDidMount(){
 AppStore.addChangeListener(this._onChange.bind(this))
 }

 // Remove change listeners from stores
 componentWillUnmount(){
 AppStore.removeChangeListener(this._onChange.bind(this))
 }

 _onChange(){
 this.setState(AppStore)
 }

 getStore(){
 AppDispatcher.dispatch({
 action: 'get-app-store'
 })
 }

 render(){

 const data = AppStore.data

 // Show loading for browser
 if(!data.ready){

 document.body.className = ''
 this.getStore()

 let style = {
 marginTop: 120
 }
 return (
 <div className="container text-center" style={ style }>
 <Loading />
 </div>
 )
 }

 // Server first
 const Routes = React.cloneElement(this.props.children, { data: data })

 return (
 <div>
 <Nav data={ data } />
 { Routes }
 <Footer data={ data } />
 </div>
 )
 }
}
```

In our `App.js` component we've attached an event listener to our `AppStore` that will re-render the state when `AppStore` emits an `onChange` event. This re-rendered data will then be passed down as props to the child components. Also note that we've added a `getStore()` method that will dispatch the `get-app-store` action to render our data on the client side. Once the data has been fetched from the Cosmic JS API, it will trigger an `AppStore` change that will include `AppStore.data.ready` set to `true`, remove the loading sign and render our content.

## Page Components

To build the first page of our blog, create a `Pages` folder. Inside it, we'll create a file called `Blog.js` with the following code:

```
// Blog.js
import React, { Component } from 'react'
import _ from 'lodash'
import config from '../../config'

// Components
import Header from '../Partials/Header'
import BlogList from '../Partials/BlogList'
import BlogSingle from '../Partials/BlogSingle'

// Dispatcher
import AppDispatcher from '../../dispatcher/AppDispatcher'

export default class Blog extends Component {

 componentWillMount(){
 this.getPageData()
 }

 componentDidMount(){
 const data = this.props.data
 document.title = config.site.title + ' | ' + data.page.title
 }

 getPageData(){
 AppDispatcher.dispatch({
 action: 'get-page-data',
 page_slug: 'blog',
 post_slug: this.props.params.slug
 })
 }

 getMoreArticles(){
 AppDispatcher.dispatch({
 action: 'get-more-items'
 })
 }

 render(){

 const data = this.props.data
 const globals = data.globals
 const pages = data.pages
 let main_content

 if(!this.props.params.slug){

 main_content = 

 } else {

 const articles = data.articles

 // Get current page slug
 const slug = this.props.params.slug
 const articles_object = _.indexBy(articles, 'slug')
 const article = articles_object[slug]
 main_content = 

 }

 return (
 <div>
 <Header data={ data }/>
 <div id="main-content" className="container">
 <div className="row">
 <div className="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1">
 { main_content }
 </div>
 </div>
 </div>
 </div>
 )
 }
}
```

This page is going to serve as a template for our blog list page (home) and our single blog pages. Here we've added a method to our component that will get the page data prior to the component mounting using the React lifecyle `componentWillMount()` method. Then, once the component has mounted at `componentDidMount()`, we'll add the page title to the `<title>` tag of the document.

Along with some of the rendering logic in this higher-level component, we've included the `getMoreArticles()`method. This is a good example of call-to-action that is stored in a higher level component and made available to lower-level components through props.<br>
Let's now get into our `BlogList` component to see how this works.

Create a new folder called `Partials`. Then, inside it, create a file called `BlogList.js` with the following content:

```
// BlogList.js
import React, { Component } from 'react'
import _ from 'lodash'
import { Link } from 'react-router'

export default class BlogList extends Component {

 scrollTop(){
 $('html, body').animate({
 scrollTop: $("#main-content").offset().top
 }, 500)
 }

 render(){

 let data = this.props.data
 let item_num = data.item_num
 let articles = data.articles

 let load_more
 let show_more_text = 'Show More Articles'

 if(data.loading){
 show_more_text = 'Loading...'
 }

 if(articles && item_num <= articles.length){
 load_more = (
 <div>
 <button className="btn btn-default center-block" onClick={ this.props.getMoreArticles.bind(this) }>
 { show_more_text }
 </button>
 </div>
 )
 }

 articles = _.take(articles, item_num)

 let articles_html = articles.map(( article ) => {
 let date_obj = new Date(article.created)
 let created = (date_obj.getMonth()+1) + '/' + date_obj.getDate() + '/' + date_obj.getFullYear()
 return (
 <div key={ 'key-' + article.slug }>
 <div className="post-preview">
 <h2 className="post-title pointer">
 <Link to={ '/blog/' + article.slug } onClick={ this.scrollTop }>{ article.title }</Link>
 </h2>
 <p className="post-meta">Posted by <a href="https://cosmicjs.com" target="_blank">Cosmic JS</a> on { created }</p>
 </div>
 <hr/>
 </div>
 )
 })

 return (
 <div>
 <div>{ articles_html }</div>
 { load_more }
 </div>
 )
 }
}
```

In our `BlogList` component we've added an `onClick` event to our `Show More Articles` button. The latter executes the `getMoreArticles()` method that was passed down as props from the higher-level page component. When that button is clicked, the event bubbles up to the `Blog` component and then triggers an action on the `AppDispatcher`. `AppDispatcher` acts as the middle man between our higher-level components and our `AppStore`.

For the sake of brevity, we're not going to build out all of the `Page` and `Partial` components in this tutorial, so please [download the GitHub repo](https://github.com/sitepoint-editors/react-universal-blog/archive/master.zip) and add them from the `components` folder.

## `AppDispatcher`

The `AppDispatcher` is the operator in our application that accepts information from the higher-level components and distributes actions to the store, which then re-renders our application data.

To continue this tutorial, create a folder named `dispatcher`. Inside it, create a file called `AppDispatcher.js` having the following code:

```
// AppDispatcher.js
import { Dispatcher } from 'flux'
import { getStore, getPageData, getMoreItems } from '../actions/actions'

const AppDispatcher = new Dispatcher()

// Register callback with AppDispatcher
AppDispatcher.register((payload) => {

 let action = payload.action

 switch(action) {

 case 'get-app-store':
 getStore()
 break

 case 'get-page-data':
 getPageData(payload.page_slug, payload.post_slug)
 break

 case 'get-more-items':
 getMoreItems()
 break

 default:
 return true

 }

 return true

})

export default AppDispatcher
```

We've introduced the `Flux` module into this file to build our dispatcher. Let's add our actions now.

## Actions – Last Stop Before The Store

To start, let's create an `actions.js` file inside a newly created folder called `actions`. This file will feature the following content:

```
// actions.js
import config from '../config'
import Cosmic from 'cosmicjs'
import _ from 'lodash'

// AppStore
import AppStore from '../stores/AppStore'

export function getStore(callback){

 let pages = {}

 Cosmic.getObjects(config, function(err, response){

 let objects = response.objects

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Globals
 ======================== */
 let globals = AppStore.data.globals
 globals.text = response.object['text']
 let metafields = globals.text.metafields
 let menu_title = _.findWhere(metafields, { key: 'menu-title' })
 globals.text.menu_title = menu_title.value

 let footer_text = _.findWhere(metafields, { key: 'footer-text' })
 globals.text.footer_text = footer_text.value

 let site_title = _.findWhere(metafields, { key: 'site-title' })
 globals.text.site_title = site_title.value

 // Social
 globals.social = response.object['social']
 metafields = globals.social.metafields
 let twitter = _.findWhere(metafields, { key: 'twitter' })
 globals.social.twitter = twitter.value
 let facebook = _.findWhere(metafields, { key: 'facebook' })
 globals.social.facebook = facebook.value
 let github = _.findWhere(metafields, { key: 'github' })
 globals.social.github = github.value

 // Nav
 const nav_items = response.object['nav'].metafields
 globals.nav_items = nav_items

 AppStore.data.globals = globals

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Pages
 ======================== */
 let pages = objects.type.page
 AppStore.data.pages = pages

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Articles
 ======================== */
 let articles = objects.type['post']
 articles = _.sortBy(articles, 'order')
 AppStore.data.articles = articles

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Work Items
 ======================== */
 let work_items = objects.type['work']
 work_items = _.sortBy(work_items, 'order')
 AppStore.data.work_items = work_items

 // Emit change
 AppStore.data.ready = true
 AppStore.emitChange()

 // Trigger callback (from server)
 if(callback){
 callback(false, AppStore)
 }

 })
}

export function getPageData(page_slug, post_slug){

 if(!page_slug || page_slug === 'blog')
 page_slug = 'home'

 // Get page info
 const data = AppStore.data
 const pages = data.pages
 const page = _.findWhere(pages, { slug: page_slug })
 const metafields = page.metafields
 if(metafields){
 const hero = _.findWhere(metafields, { key: 'hero' })
 page.hero = config.bucket.media_url + '/' + hero.value

 const headline = _.findWhere(metafields, { key: 'headline' })
 page.headline = headline.value

 const subheadline = _.findWhere(metafields, { key: 'subheadline' })
 page.subheadline = subheadline.value
 }

 if(post_slug){
 if(page_slug === 'home'){
 const articles = data.articles
 const article = _.findWhere(articles, { slug: post_slug })
 page.title = article.title
 }
 if(page_slug === 'work'){
 const work_items = data.work_items
 const work_item = _.findWhere(work_items, { slug: post_slug })
 page.title = work_item.title
 }
 }
 AppStore.data.page = page
 AppStore.emitChange()
}

export function getMoreItems(){

 AppStore.data.loading = true
 AppStore.emitChange()

 setTimeout(function(){
 let item_num = AppStore.data.item_num
 let more_item_num = item_num + 5
 AppStore.data.item_num = more_item_num
 AppStore.data.loading = false
 AppStore.emitChange()
 }, 300)
}
```

There are a few methods here that are exposed by this `actions.js` file. `getStore()` connects to the Cosmic JS API to serve our blog's content. `getPageData()` gets the page data from a provided `slug` (or page key). `getMoreItems()` controls how many items will be seen in our `BlogList` and `WorkList` components.

When `getMoreItems()` is triggered, it first sets `AppStore.data.loading` to `true`. Then, 300 miliseconds later (for effect), allows 5 more items to be added to our list of blog posts or work items. Finally, it sets `AppStore.data.loading` to `false`.

## Configure Your Cosmic JS CMS

To begin recieving data from your cloud-hosted content API on [Cosmic JS](https://cosmicjs.com), let's create a `config.js` file. Open this file and paste this content:

```
// config.js
export default {
 site: {
 title: 'React Universal Blog'
 },
 bucket: {
 slug: 'react-universal-blog',
 media_url: 'https://cosmicjs.com/uploads'
 }
}
```

This means content will be coming from the Cosmic JS bucket `react-universal-blog`. To create content for your own blog or app, [sign up for a free account with Cosmic JS](https://cosmicjs.com). When asked to "Add a New Bucket", click "Install Starter Bucket" and you'll be able to follow the steps to install the "React Universal Blog". Once this is done, you can add your unique bucket's slug to this config file.

Now that we have most of our React components and Flux architecture set up, let's finish up by editing our `app-server.js` file to render everything in server-side production. This file will have this code:

```
// app-server.js
import React from 'react'
import { match, RoutingContext, Route, IndexRoute } from 'react-router'
import ReactDOMServer from 'react-dom/server'
import express from 'express'
import hogan from 'hogan-express'
import config from './config'

// Actions
import { getStore, getPageData } from './actions/actions'

// Routes
import routes from './routes'

// Express
const app = express()
app.engine('html', hogan)
app.set('views', __dirname + '/views')
app.use('/', express.static(__dirname + '/public/'))
app.set('port', (process.env.PORT || 3000))

app.get('*',(req, res) => {

 getStore(function(err, AppStore){

 if(err){
 return res.status(500).end('error')
 }

 match({ routes, location: req.url }, (error, redirectLocation, renderProps) => {

 // Get page data for template
 const slug_arr = req.url.split('/')
 let page_slug = slug_arr[1]
 let post_slug
 if(page_slug === 'blog' || page_slug === 'work')
 post_slug = slug_arr[2]
 getPageData(page_slug, post_slug)
 const page = AppStore.data.page
 res.locals.page = page
 res.locals.site = config.site

 // Get React markup
 const reactMarkup = ReactDOMServer.renderToStaticMarkup(<RoutingContext {...renderProps}/>)
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
})

app.listen(app.get('port'))

console.info('==> Server is listening in ' + process.env.NODE_ENV + ' mode')
console.info('==> Go to http://localhost:%s', app.get('port'))
```

This file uses our `getStore` action method to get our content from the Cosmic JS API server-side, then goes through React Router to determine which component will be mounted. Everything will then be rendered into static markup with `renderToStaticMarkup`. This output is then stored in a template variable to be used by our `views/index.html` file.

Once again, let's update the `scripts` section of our `package.json` file so that it looks like the one shown below:

```
"scripts": {
 "start": "npm run production",
 "production": "rm -rf public/index.html && NODE_ENV=production webpack -p && NODE_ENV=production node app.js",
 "webpack-dev-server": "NODE_ENV=development PORT=8080 webpack-dev-server --content-base public/ --hot --inline --devtool inline-source-map --history-api-fallback",
 "development": "cp views/index.html public/index.html && NODE_ENV=development webpack && npm run webpack-dev-server"
}
```

We can now run in development mode with hot reloading and we can run in production mode with server-rendered markup. Run the following command to run the full React Universal Blog Application in production mode:

```
npm start
```

Our blog is now ready to view at <http://localhost:3000>. It can be viewed on the server side, the browser side and our content can be managed through [Cosmic JS](https://cosmicjs.com), our cloud-hosted content platform.

## Conclusions

React is a very sophisticated way to manage UI and data within an application. It's also a very good choice for rendering server-side content, to appease JavaScript-depraved web crawlers and for rendering UI browser-side to keep us browsing fast. And we can get the best results of both worlds by making our application universal.

I really hope you enjoyed this article. Once again, [the full code can be downloaded from GitHub](https://github.com/sitepoint-editors/react-universal-blog/).
