# Understanding Transducers in JavaScript

[Original URL](https://medium.com/@roman01la/understanding-transducers-in-javascript-3500d3bd9624#.h5x60x62x)

> I've found a very good article explaining Transducers. If you are familiar with Clojure, go and read it: "Understanding Transducers". But if you are JavaScript developer and not used to read lisp...

I've found a very good article explaining Transducers. If you are familiar with Clojure, go and read it: [_"Understanding Transducers"_](http://elbenshira.com/blog/understanding-transducers/). But if you are JavaScript developer and not used to read lisp code, I've translated code examples from that articles into JavaScript. So you can still read the article and check code examples here.

A quick noob intro: transducers are composable and efficient data transformation functions which doesn't create intermediate collections.

_map_ and _filter_, and how they can be combined together:

_map_ and _filter_ can be implemented using _reduce._ Here's _map_ implementation:

Let's extract _incrementing_ function to allow it to be passed into _reducer_:

More usage examples of _map reducer_:

_filter_ implementation using _reduce_:

Again, extract _predicate_ function, so it can be passed from the outside:

Combine both _reducers_ together:

Similar to what you usually do with _built-in array methods_:

Here are both _reducers_ again and both of them are using _array concatenation_ as a reducing function:

_concat_ and _+_ are both _reducing functions_:

Let's _extract reducing function_, so it can be also passed from the outside:

Here's how reducers can be used now:

The type of reducers is _result, input -> result_:

Composition of reducers has the exact same type:

So it also can be used as a reducer:

Let's use _R.compose_ from _Ramda_ library for better readability:

More complex example:

Finally let's wrap it into _transduce_ helper function:

Usage example:

Check out [_transducers-js_](https://github.com/cognitect-labs/transducers-js) library for a complete and performant transducers implementation in JavaScript. Read about [_Transducer protocol_](https://github.com/cognitect-labs/transducers-js#the-transducer-protocol) _* which allows different libraries to connect together (like_ Lodash_,_ Underscore _and_ Immutable.js*).
