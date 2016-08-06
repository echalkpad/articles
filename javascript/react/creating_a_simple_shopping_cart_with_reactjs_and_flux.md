# Creating A Simple Shopping Cart with React.js and Flux

[Original URL](https://scotch.io/tutorials/creating-a-simple-shopping-cart-with-react-js-and-flux)

> Introduction Welcome to the fourth and final installment of the Learning React series! Up to this point, we've learned how React's API allows us to create rich stateful components, how...

## Introduction

Welcome to the fourth and final installment of the **Learning React** series! Up to this point, we've learned how React's API allows us to create rich stateful components, how to use them in practice & how Facebook's Flux architecture works.

Today we are going to put all of it together to create a basic shopping cart application. In a typical e-commerce website, the product detail page has several moving parts that rely on one another and React really helps simplify and organize the co-dependency between them.

If you haven't already, I highly recommend checking out the first three parts of this series:

### ReactJS 0.12

During the writing of this series, React released version 0.12 which made some fairly significant changes. This tutorial will be written in 0.12 syntax. Some of the changes include:

- The `/** @jsx React.DOM */` header is no longer required when writing JSX syntax
- `renderComponent` is now `render`
- `renderComponentToString` is now `renderToString`

You can check out the entire changelog on the official [blog post](http://facebook.github.io/react/blog/2014/10/28/react-v0.12.html).

## Getting Started

Our first step to architecting an application is defining what it should do. We want to:

- Display a product with several options
- Change the price when selecting an option
- Add items to the cart
- Remove items from the cart
- Display the number of items in the cart
- Display the total price of the items in the cart
- Display the total price for each option in the cart based upon quantity
- Change our "Add To Cart" button caption to "Sold Out" and disable it when inventory is depleted for a given option
- Display the cart after adding a product or clicking the "View Cart" button

This is what our finished product should look like:

[![loWHKFz](https://cask.scotch.io/2014/10/loWHKFz-1001x500.png)](https://cask.scotch.io/2014/10/loWHKFz.png)

This app is going to be purely client side, so we aren't going to need a server. Instead, we will be using a mock API and mock data in order to focus on the components themselves. Lets take a look at our directory structure:

### Directory Structure

```
css/
---- app.css
img/
---- scotch-beer.png
js/
---- actions/
-------- FluxCartActions.js // Our app's action creators
---- components/
-------- FluxCart.react.js // Cart Component
-------- FluxCartApp.react.js // Main Controller View
-------- FluxProduct.react.js // Product Component
---- constants/
-------- FluxCartConstants.js // Our app's action constants
---- dispatcher/
-------- AppDispatcher.js // Our app's dispatcher
---- stores/
-------- CartStore.js // Cart Store
-------- ProductStore.js // Product Store
---- utils/
-------- CartAPI.js // Mock API
---- app.js // Main app.js file
---- ProductData.js // Mock Data
index.html
package.json
```

Below, check out our `package.json` file. We will be using the following modules:

- Browserify
- Reactify
- React
- Flux
- Watchify
- Uglify
- Underscore
- Envify

We can run `npm install` to install all of our dependencies, and then use the `npm start` command to start a process that watches our project and bundles our source on save.

### package.json

```
{
 "name": "flux-pricing",
 "version": "0.0.1",
 "description": "Pricing component with flux",
 "main": "js/app.js",
 "dependencies": {
 "flux": "^2.0.0",
 "react": "^0.12.0",
 "underscore": "^1.7.0"
 },
 "devDependencies": {
 "browserify": "~6.2.0",
 "envify": "~3.0.0",
 "react": "^0.12.0",
 "reactify": "^0.15",
 "watchify": "~2.1.0"
 },
 "scripts": {
 "start": "watchify -o js/bundle.js -v -d .",
 "build": "browserify . | uglifyjs -cm > js/bundle.min.js"
 },
 "author": "Ken Wheeler",
 "browserify": {
 "transform": [
 "reactify",
 "envify"
 ]
 }
}
```

## API & Mock Data

In the interests of keeping us focused on Flux & React, we will be using a mock API and mock data for our product that we are going to display. That said, writing our product data and our API similarly to the way we would work with a real API adds the benefit that we could have an easier time plugging in a real API down the road if we had wanted to.

Lets have a look at what our sample Product Data looks like:

### ProductData.js

```
module.exports = {
 // Load Mock Product Data Into localStorage
 init: function() {
 localStorage.clear();
 localStorage.setItem('product', JSON.stringify([
 {
 id: '0011001',
 name: 'Scotch.io Signature Lager',
 image: 'scotch-beer.png',
 description: 'The finest lager money can buy. Hints of keyboard aerosol, with a whiff of iKlear wipes on the nose. If you pass out while drinking this beverage, Chris Sevilleja personally tucks you in.',
 variants: [
 {
 sku: '123123',
 type: '40oz Bottle',
 price: 4.99,
 inventory: 1

 },
 {
 sku: '123124',
 type: '6 Pack',
 price: 12.99,
 inventory: 5
 },
 {
 sku: '1231235',
 type: '30 Pack',
 price: 19.99,
 inventory: 3
 }
 ]
 }
 ]));
 }

};
```

As you can see above, we define a product that has options called variants. Our schema mirrors the type of data you might typically get back from a call to a restful API. We go ahead and load this data into `localStorage`, so that our mock API can grab that data and load it into our app.

See below how our mock API grabs our data from `localStorage` and then uses Flux actions to send that data to our ProductStore:

### CartAPI.js

```
var FluxCartActions = require('../actions/FluxCartActions');

module.exports = {

 // Load mock product data from localStorage into ProductStore via Action
 getProductData: function() {
 var data = JSON.parse(localStorage.getItem('product'));
 FluxCartActions.receiveProduct(data);
 }

};
```

_So now that we have our sample product data, and a sample API call, how do we use this to bootstrap our application with "server" data?_

It's really as simple as just initializing our data, running our API call, and then mounting our controller view. Our main `app.js` file, shown below, is responsible for this process:

### app.js

```
window.React = require('react');
var ProductData = require('./ProductData');
var CartAPI = require('./utils/CartAPI')
var FluxCartApp = require('./components/FluxCartApp.react');

// Load Mock Product Data into localStorage
ProductData.init();

// Load Mock API Call
CartAPI.getProductData();

// Render FluxCartApp Controller View
React.render(
 <FluxCartApp />,
 document.getElementById('flux-cart')
);
```

## Dispatcher

Since we are using the Flux architecture for this application, we are going to need to create our own instance of Facebook's Dispatcher library. We also add a `handleAction` helper method to our Dispatcher instance, so that we can identify where this action came from.

While this isn't explicitly required for our current application, if we wanted to hook into a real API or handle actions from places other than views, it is nice to have this architecture in place if we needed to handle those actions differently than those that originated from views.

### AppDispatcher.js

```
var Dispatcher = require('flux').Dispatcher;

// Create dispatcher instance
var AppDispatcher = new Dispatcher();

// Convenience method to handle dispatch requests
AppDispatcher.handleAction = function(action) {
 this.dispatch({
 source: 'VIEW_ACTION',
 action: action
 });
}

module.exports = AppDispatcher;
```

In our `handleAction` method, we receive an action from an action creator and then have our Dispatcher dispatch the action with a `source` property and the action that was supplied as an argument.

## Actions

Now that we have our dependencies, data and our Dispatcher set up, it's time to start working on our project's functional requirements. Actions are a great place to start. Let's define some action constants in order to define which actions our app will perform:

### FluxCartConstants.js

```
var keyMirror = require('react/lib/keyMirror');

// Define action constants
module.exports = keyMirror({
 CART_ADD: null, // Adds item to cart
 CART_REMOVE: null, // Remove item from cart
 CART_VISIBLE: null, // Shows or hides the cart
 SET_SELECTED: null, // Selects a product option
 RECEIVE_DATA: null // Loads our mock data
});
```

After defining our constants, we need to create their corresponding action creator methods. These are methods that we can call from our views/components that will tell our Dispatcher to broadcast the action to our Stores.

The action itself consists of an object containing our desired action constant and a data payload. Our Stores then update and fire change events, which our Controller View listens to in order to know when to begin a state update.

Below, check out how we use our Dispatcher's `handleAction` method to pass an `actionType` constant and associated data to our Dispatcher:

### FluxCartActions.js

```
var AppDispatcher = require('../dispatcher/AppDispatcher');
var FluxCartConstants = require('../constants/FluxCartConstants');

// Define actions object
var FluxCartActions = {

 // Receive inital product data
 receiveProduct: function(data) {
 AppDispatcher.handleAction({
 actionType: FluxCartConstants.RECEIVE_DATA,
 data: data
 })
 },

 // Set currently selected product variation
 selectProduct: function(index) {
 AppDispatcher.handleAction({
 actionType: FluxCartConstants.SELECT_PRODUCT,
 data: index
 })
 },

 // Add item to cart
 addToCart: function(sku, update) {
 AppDispatcher.handleAction({
 actionType: FluxCartConstants.CART_ADD,
 sku: sku,
 update: update
 })
 },

 // Remove item from cart
 removeFromCart: function(sku) {
 AppDispatcher.handleAction({
 actionType: FluxCartConstants.CART_REMOVE,
 sku: sku
 })
 },

 // Update cart visibility status
 updateCartVisible: function(cartVisible) {
 AppDispatcher.handleAction({
 actionType: FluxCartConstants.CART_VISIBLE,
 cartVisible: cartVisible
 })
 }

};

module.exports = FluxCartActions;
```

## Stores

Now that we have our Actions defined, it is time to create our Stores. Each Store manages application state for a domain within an application, so we are going to create one for our product and one for our cart. Let's start with our `ProductStore`:

### ProductStore.js

```
var AppDispatcher = require('../dispatcher/AppDispatcher');
var EventEmitter = require('events').EventEmitter;
var FluxCartConstants = require('../constants/FluxCartConstants');
var _ = require('underscore');

// Define initial data points
var _product = {}, _selected = null;

// Method to load product data from mock API
function loadProductData(data) {
 _product = data[0];
 _selected = data[0].variants[0];
}

// Method to set the currently selected product variation
function setSelected(index) {
 _selected = _product.variants[index];
}


// Extend ProductStore with EventEmitter to add eventing capabilities
var ProductStore = _.extend({}, EventEmitter.prototype, {

 // Return Product data
 getProduct: function() {
 return _product;
 },

 // Return selected Product
 getSelected: function(){
 return _selected;
 },

 // Emit Change event
 emitChange: function() {
 this.emit('change');
 },

 // Add change listener
 addChangeListener: function(callback) {
 this.on('change', callback);
 },

 // Remove change listener
 removeChangeListener: function(callback) {
 this.removeListener('change', callback);
 }

});

// Register callback with AppDispatcher
AppDispatcher.register(function(payload) {
 var action = payload.action;
 var text;

 switch(action.actionType) {

 // Respond to RECEIVE_DATA action
 case FluxCartConstants.RECEIVE_DATA:
 loadProductData(action.data);
 break;

 // Respond to SELECT_PRODUCT action
 case FluxCartConstants.SELECT_PRODUCT:
 setSelected(action.data);
 break;

 default:
 return true;
 }

 // If action was responded to, emit change event
 ProductStore.emitChange();

 return true;

});

module.exports = ProductStore;
```

Above, we define two private methods, `loadProductData` and `setSelected`. We use `loadProductData` to, unsurprisingly, load our mock product data into our `_product` object. Our `setSelected` method is used to set which product variant is currently selected.

We expose this data using the public methods `getProduct` and `getSelected`, which return their respective internal objects. These methods can be called after `require`'ing our Store within a view.

Lastly, we register a callback to our `AppDispatcher` that uses a switch statement to determine if the supplied payload matches an action we want to respond to. In the event that it does, we call our private methods with the supplied action data, and fire a change event, forcing our view to retrieve the new state and update its display.

Next up, let's create our `CartStore`:

```
var AppDispatcher = require('../dispatcher/AppDispatcher');
var EventEmitter = require('events').EventEmitter;
var FluxCartConstants = require('../constants/FluxCartConstants');
var _ = require('underscore');

// Define initial data points
var _products = {}, _cartVisible = false;

// Add product to cart
function add(sku, update) {
 update.quantity = sku in _products ? _products[sku].quantity + 1 : 1;
 _products[sku] = _.extend({}, _products[sku], update)
}

// Set cart visibility
function setCartVisible(cartVisible) {
 _cartVisible = cartVisible;
}

// Remove item from cart
function removeItem(sku) {
 delete _products[sku];
}

// Extend Cart Store with EventEmitter to add eventing capabilities
var CartStore = _.extend({}, EventEmitter.prototype, {

 // Return cart items
 getCartItems: function() {
 return _products;
 },

 // Return # of items in cart
 getCartCount: function() {
 return Object.keys(_products).length;
 },

 // Return cart cost total
 getCartTotal: function() {
 var total = 0;
 for(product in _products){
 if(_products.hasOwnProperty(product)){
 total += _products[product].price articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii _products[product].quantity;
 }
 }
 return total.toFixed(2);
 },

 // Return cart visibility state
 getCartVisible: function() {
 return _cartVisible;
 },

 // Emit Change event
 emitChange: function() {
 this.emit('change');
 },

 // Add change listener
 addChangeListener: function(callback) {
 this.on('change', callback);
 },

 // Remove change listener
 removeChangeListener: function(callback) {
 this.removeListener('change', callback);
 }

});

// Register callback with AppDispatcher
AppDispatcher.register(function(payload) {
 var action = payload.action;
 var text;

 switch(action.actionType) {

 // Respond to CART_ADD action
 case FluxCartConstants.CART_ADD:
 add(action.sku, action.update);
 break;

 // Respond to CART_VISIBLE action
 case FluxCartConstants.CART_VISIBLE:
 setCartVisible(action.cartVisible);
 break;

 // Respond to CART_REMOVE action
 case FluxCartConstants.CART_REMOVE:
 removeItem(action.sku);
 break;

 default:
 return true;
 }

 // If action was responded to, emit change event
 CartStore.emitChange();

 return true;

});

module.exports = CartStore;
```

Above, we laid out our store similarly to the way we laid out our `ProductStore`. We use the `_products` object to store the products that are currently in our cart, and the `_cartVisibility` boolean to define the current visibility status of our cart.

We added some more complex public methods that allow our Controller View to retrieve application state:

- `getCartItems` – Returns the items in our cart
- `getCartCount` – Returns a count of how many items are in our cart
- `getCartTotal` – Returns the total price of all of our cart items

Now that we have our Stores built, it's time to get our hands dirty and build out our Views.

## Controller View

Our Controller View is our top level component that listens for changes on our stores and then updates our application's state by calling our Store's public methods. This state is then passed down to child components via props.

The Controller View is responsible for:

- Setting our applications state by calling Store's public methods
- Composing child components and passing state properties via props
- Listening for Store's change events

### FluxCartApp.react.js

```
var React = require('react');
var CartStore = require('../stores/CartStore');
var ProductStore = require('../stores/ProductStore');
var FluxProduct = require('./FluxProduct.react');
var FluxCart = require('./FluxCart.react');

// Method to retrieve state from Stores
function getCartState() {
 return {
 product: ProductStore.getProduct(),
 selectedProduct: ProductStore.getSelected(),
 cartItems: CartStore.getCartItems(),
 cartCount: CartStore.getCartCount(),
 cartTotal: CartStore.getCartTotal(),
 cartVisible: CartStore.getCartVisible()
 };
}

// Define main Controller View
var FluxCartApp = React.createClass({

 // Get initial state from stores
 getInitialState: function() {
 return getCartState();
 },

 // Add change listeners to stores
 componentDidMount: function() {
 ProductStore.addChangeListener(this._onChange);
 CartStore.addChangeListener(this._onChange);
 },

 // Remove change listers from stores
 componentWillUnmount: function() {
 ProductStore.removeChangeListener(this._onChange);
 CartStore.removeChangeListener(this._onChange);
 },

 // Render our child components, passing state via props
 render: function() {
 return (
 <div className="flux-cart-app">
 <FluxCart products={this.state.cartItems} count={this.state.cartCount} total={this.state.cartTotal} visible={this.state.cartVisible} />
 <FluxProduct product={this.state.product} cartitems={this.state.cartItems} selected={this.state.selectedProduct} />
 </div>
 );
 },

 // Method to setState based upon Store changes
 _onChange: function() {
 this.setState(getCartState());
 }

});

module.exports = FluxCartApp;
```

We start by creating a public method called `getCartState`. We use this method to call public methods on Stores to retrieve their current state and set our applications state with the results. We call this method first during the `getInitialState` method, and also when a Store's change event is received.

In order to receive these change events, we add listeners on our Stores during the mounting process, so that we know when they change. We remove these events when/if our component is unmounted.

In our `render` method, we compose our component using the `FluxCart` and `FluxProduct` components. Here, we pass our state props down to them using component properties, or props.

## Product View

It's time to get down to the meat and potatoes of this app, and that is our Product view. We want to take the props that got passed from our Controller View, and make a rich, interactive product display.

Lets get this party started.

### FluxProduct.react.js

```
var React = require('react');
var FluxCartActions = require('../actions/FluxCartActions');

// Flux product view
var FluxProduct = React.createClass({

 // Add item to cart via Actions
 addToCart: function(event){
 var sku = this.props.selected.sku;
 var update = {
 name: this.props.product.name,
 type: this.props.selected.type,
 price: this.props.selected.price
 }
 FluxCartActions.addToCart(sku, update);
 FluxCartActions.updateCartVisible(true);
 },

 // Select product variation via Actions
 selectVariant: function(event){
 FluxCartActions.selectProduct(event.target.value);
 },

 // Render product View
 render: function() {
 var ats = (this.props.selected.sku in this.props.cartitems) ?
 this.props.selected.inventory - this.props.cartitems[this.props.selected.sku].quantity :
 this.props.selected.inventory;
 return (
 <div className="flux-product">
 <img src={'img/' + this.props.product.image}/>
 <div className="flux-product-detail">
 <h1 className="name">{this.props.product.name}</h1>
 <p className="description">{this.props.product.description}</p>
 <p className="price">Price: ${this.props.selected.price}</p>
 <select onChange={this.selectVariant}>
 {this.props.product.variants.map(function(variant, index){
 return (
 <option key={index} value={index}>{variant.type}</option>
 )
 })}
 </select>
 <button type="button" onClick={this.addToCart} disabled={ats > 0 ? '' : 'disabled'}>
 {ats > 0 ? 'Add To Cart' : 'Sold Out'}
 </button>
 </div>
 </div>
 );
 },

});

module.exports = FluxProduct;
```

Prior to our `render` method, we define Action methods that we bind to elements in our component. By importing our Actions, we can then call them from these methods, and kick off the update process:

- `selectProduct` – Sets which product option is currently selected
- `addToCart` – Adds the currently selected product to the cart and opens the cart

Inside of our render method, we calculate how many units of the selected product are available to sell (ats) by checking how many are in the cart vs the selected products inventory. We use this to toggle the "Add To Cart" button's state.

## Cart View

Gotta have a cart. So lets put one together. In our app, when a product is added to the cart, a single line item represents the selected option. The quantity of that option can be increased, but it won't create new line items. Instead, the quantity being purchased is displayed and the line item's price adjusts accordingly.

Let's make this happen:

### FluxCart.react.js

```
var React = require('react');
var FluxCartActions = require('../actions/FluxCartActions');

// Flux cart view
var FluxCart = React.createClass({

 // Hide cart via Actions
 closeCart: function(){
 FluxCartActions.updateCartVisible(false);
 },

 // Show cart via Actions
 openCart: function(){
 FluxCartActions.updateCartVisible(true);
 },

 // Remove item from Cart via Actions
 removeFromCart: function(sku){
 FluxCartActions.removeFromCart(sku);
 FluxCartActions.updateCartVisible(false);
 },

 // Render cart view
 render: function() {
 var self = this, products = this.props.products;
 return (
 <div className={"flux-cart " + (this.props.visible ? 'active' : '')}>
 <div className="mini-cart">
 <button type="button" className="close-cart" onClick={this.closeCart}>×</button>
 <ul>
 {Object.keys(products).map(function(product){
 return (
 <li key={product}>
 <h1 className="name">{products[product].name}</h1>
 <p className="type">{products[product].type} x {products[product].quantity}</p>
 <p className="price">${(products[product].price articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii products[product].quantity).toFixed(2)}</p>
 <button type="button" className="remove-item" onClick={self.removeFromCart.bind(self, product)}>Remove</button>
 </li>
 )
 })}
 </ul>
 <span className="total">Total: ${this.props.total}</span>
 </div>
 <button type="button" className="view-cart" onClick={this.openCart} disabled={Object.keys(this.props.products).length > 0 ? "" : "disabled"}>View Cart ({this.props.count})</button>
 </div>
 );
 },

});

module.exports = FluxCart;
```

And now we have a cart! Our cart component has three event methods:

- `closeCart` – Close the cart
- `openCart` – Opens the cart
- `removeFromCart` – Removes item from the cart and closes the cart

When rendering our cart, we use the `map` method to render out our line items. Notice on the `<li>` tag, we added the `key` attribute. This is a special attribute used when adding dynamic children to a component. It is used internally in React to uniquely identify said children, so that they retain the proper order and state during reconciliation. If you remove this and open your console, you can see that React will throw a warning telling you that `key` hasn't been set, and you will likely experience rendering anomalies.

For our cart's hide and show functionality, all that we are doing is adding and removing an `active` class, and letting CSS do the rest.

## Wrap Up

If you have been following along with the source or building from scratch, hit `index.html` and you can see our app in action. Otherwise, check out the link to the demo below. Add items to the cart until inventory depletes to see our buttons' states change and the totals update in the cart.

Don't stop there though, take the demo source and try to add some new features to our cart, like a product grid display using [react-router](https://github.com/rackt/react-router) or adding more than 1 selectable option per product. The sky's the limit folks.

This marks the end of the **Learning React** series, and I hope everybody has had as much fun learning it as I did writing it. I'm of the firm belief that 2015 will be the year of React, so take what you have learned here, get out there and build some cool stuff.
