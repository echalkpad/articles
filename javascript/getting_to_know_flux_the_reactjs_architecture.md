# Getting To Know Flux, the React.js Architecture

[Original URL](https://scotch.io/tutorials/getting-to-know-flux-the-react-js-architecture)

> So what's this Dispatcher all about? The Dispatcher is basically the manager of this entire process. It is the central hub for your application. The dispatcher receives actions and dispatches...

_So what's this Dispatcher all about?_

The Dispatcher is basically the manager of this entire process. It is the central hub for your application. The dispatcher receives actions and dispatches the actions and data to registered callbacks.

_So it's essentially pub/sub?_

Not exactly. The dispatcher broadcasts the payload to **ALL** of its registered callbacks, and includes functionality that allows you to invoke the callbacks in a specific order, even waiting for updates before proceeding. There is only ever **one** dispatcher, and it acts as the central hub within your application.

Check out what one looks like below:

```
var Dispatcher = require('flux').Dispatcher;
var AppDispatcher = new Dispatcher();

AppDispatcher.handleViewAction = function(action) {
 this.dispatch({
 source: 'VIEW_ACTION',
 action: action
 });
}

module.exports = AppDispatcher;
```

In the above example, we create an instance of our Dispatcher and create a handleViewAction method. This abstraction is helpful if you are looking to distinguish between view triggered actions v.s. server/API triggered actions.

Our method calls the dispatch method, which will broadcast the `action` payload to all of its registered callbacks. This action can then be acted upon within Stores, and will result in a state update.

The diagram below illustrates this process:

[![hKbN2q6](https://cask.scotch.io/2014/10/hKbN2q6.png)](https://cask.scotch.io/2014/10/hKbN2q6.png)

## Dependencies

One of the coolest parts of the provided Dispatcher module is the ability to define dependencies and marshall the callbacks on our Stores. So if one part of your application is dependent upon another part being updated first, in order to render properly, the Dispatcher's `waitFor` method will be mighty useful.

In order to utilize this feature, we need to store the return value of the Dispatcher's registration method on our Store as `dispatcherIndex`, as shown below:

```
ShoeStore.dispatcherIndex = AppDispatcher.register(function(payload) {

});
```

Then in our Store, when handling a dispatched action, we can use the Dispatcher's `waitFor` method to ensure our ShoeStore has been updated:

```
case 'BUY_SHOES':
 AppDispatcher.waitFor([
 ShoeStore.dispatcherIndex
 ], function() {
 CheckoutStore.purchaseShoes(ShoeStore.getSelectedShoes());
 });
 break;
```
