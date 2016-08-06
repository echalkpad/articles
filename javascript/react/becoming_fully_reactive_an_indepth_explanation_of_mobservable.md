# Becoming fully reactive: an in-depth explanation of Mobservable

[Original URL](https://medium.com/@mweststrate/becoming-fully-reactive-an-in-depth-explanation-of-mobservable-55995262a254#.1ue3s24h2)

> Due to popular demand (and to have a cool story for my grand-children), these are the inner workings of Mobservable. A lot of people are surprised how consis

![](https://cdn-images-1.medium.com/max/2000/1*Q89nqGHbuc27ucCL0vpWRQ.jpeg)

![](https://cdn-images-1.medium.com/max/800/1*Ca3Wp7Huxmf2aGuAxludJA.png)

Due to popular demand (and to have a cool story for my grand-children), these are the inner workings of Mobservable. A lot of people are surprised how consistent and fast Mobservable is. But rest assured, there is no magic in play!

First, let's define the core concepts of Mobservable:

1. **_Observable state_**. Any value that can be mutated and might serve as source for computed values is state. Mobservable can make most types of values (primitives, arrays, classes, objects, etc.) and even (potentially cyclic) references observable out of the box.

2. **_Computed values._** Any value that can be computed by using a function that purely operates on other observable values. Computed values can range from the concatenation of a few strings up to deriving complex object graphs and visualizations. Because computed values are observable themselves, even the rendering of a complete user interface can be derived from the observable state. Computed values might evaluate either lazily or in reaction to state changes.

3. **_Reactions._** A reaction is a bit similar to a computed value, but instead of producing a new value it produces a side effect. Reactions bridge reactive and imperative programming for things like printing to the console, making network requests, incrementally updating the React component tree to patch the DOM, etc.

4. **_Actions._** Actions are the primary means to modify the state. Actions are not a reaction to state changes but take sources of change, like user events or incoming web-socket connections, to modify the observable state.

_Computed values_ and _reactions_ are both referred to as _derivations_ in the remainder of this blog-post_._ So far, this might all sound a bit academic so let's make it concrete! In a spreadsheet all data cells that have values would form the _observable state_. Formulas and charts are _computed values_ that can be derived from the data cells and other formulas. Drawing the output of a data cell or a formula on the screen is a _reaction_. Changing a data cell or formula is an _action_.

Anyway, here are all four concepts in a small example that uses Mobservable and React:

Listing 1: Observable state, computed values, reactive Reactjs component and some actions We could draw a dependency tree based on the above listing. Intuitively it will look as follows:

![](https://cdn-images-1.medium.com/max/800/1*YPlKuWsPrVbhxUN7YhyTpg.png)

Figure 1: Dependency tree of profileView component. FullName is in reactive mode, actively observing firstName and lastName. The _state_ of this applications is captured in the _observable_ properties (blue). The green _computed value_ fullName can be derived from the state automatically by observing the firstName and the lastName. Similarly the rendering of the profileView can be derived from the nickName and the fullName. The profileView will react to state changes by producing a side effect: it updates the React component tree.

When using Mobservable the dependency tree is minimally defined. For example, as soon as the person being rendered has a nickname, the rendering will no longer be affected by the output of the fullName value, nor the first- or lastName (see listing 1). All observer relations between those values can be cleaned up and Mobservable will automatically simplify the dependency tree accordingly:

![](https://cdn-images-1.medium.com/max/800/1*SXiDJ_mQ0ctEj2QEurLpqA.png)

Figure 2: Dependency tree of the profileView component if the user has a nickname (see listing 1). In contrast to figure 1, fullName is now in lazy mode and does not observe firstName and lastName Mobservable will always try to minimize the number of computations that are needed to produce a consistent state. In the rest of this blog post, I will describe several strategies used to achieve this goal. But before diving into the magic of how computed values and reactions are kept in sync with the state, let's first describe the principle behind Mobservable:

Any imperative action that an application takes in response to a state change usually creates or updates some values. In other words, most actions manage a [local cache](https://medium.com/@mweststrate/pure-rendering-in-the-light-of-time-and-state-4b537d8d40b1#.6pt68lack). Triggering the user interface to update? Updating aggregated values? Notifying the back-end? These can all be thought of as cache invalidations in disguise. To ensure these caches will stay in sync, you need to subscribe to future state changes that will enable your actions to be triggered again.

But working with subscriptions (or cursors, lenses, selectors, connectors, etc) has a fundamental problem: as your app evolves, you will make mistakes in managing those subscriptions and either oversubscribe (continue subscribing to a value or store that is no longer used in a component) or undersubscribe (forgetting to listen for updates leading to subtle staleness bugs).

> In other words; when using manual subscriptions, your app will eventually be inconsistent.

![](https://cdn-images-1.medium.com/max/800/1*1fW10m1Cu3uwJ49hMNNofg.png)

Figure 3: Inconsistent Twitter page after updating profile. The pinned tweet is displaying stale values for both the name and the profile picture of the author. The above image is a nice example of the Twitter UI being inconsistent. As explained in my [Reactive2015 talk](https://www.youtube.com/watch?v=FEwLwiizlk0), there can only be two causes for this: Either there is no subscription that tells tweets to re-render if the profile of the associated author has changed. Or the data was normalized and the author of a tweet doesn't even relate to the profile of the currently logged-in user, despite the fact that both pieces of data try to describe the same properties of the same person.

Coarse grained subscriptions like Flux-style store subscriptions are very susceptible to oversubscribing. When using React, you can simply tell whether your components are oversubscribing by printing [wasted renderings](https://facebook.github.io/react/docs/perf.html#perf.printwastedmeasurements). Mobservable will [reduce this number to zero.](https://www.mendix.com/tech-blog/making-react-reactive-pursuit-high-performing-easily-maintainable-react-apps/) The idea is simple yet counterintuitive: More subscriptions result in fewer recomputations. Mobservable manages many thousands of observers for you. You can effectively tradeoff memory for CPU cycles.

Note that oversubscribing also exists in very subtle forms. If you subscribe to data that is used, but not under _all_ conditions, you are still oversubscribing. For example, if the profileView component subscribes to the fullName of a person that has a nickName, it is oversubscribing (see listing 1). So an important principle behind the design Mobservable is:

> A minimal, consistent set of subscriptions can only be achieved if subscriptions are determined at run-time.

The second important idea behind Mobservable is that for any app that is more complex than TodoMVC, you will often need a _data graph_, instead of a normalized tree, to store the state in a _mentally manageable_ yet optimal way. Graphs enable referential consistency and avoid data duplication so that it can be guaranteed that derived values are never stale.

The solution: don't cache, derive instead. People ask: "isn't that extremely expensive?" No, it is actually very efficient! The reason for that is, as explained above: Mobservable doesn't run all derivations, but ensures that only _computed values_ that are actually used by a _reaction_ are kept in sync with the observable state. Those derivations are called to be _reactive_. To draw the parallel with spreadsheets again: only those formulas that are currently visible or that are used indirectly by a visible formula, need to re-compute when one of the observed data cells change.

So what about computations that aren't used directly or indirectly by a reaction? You can still inspect the value of a computed value like fullName at any time. The solution is simple: if a computed value is not reactive, it will be evaluated lazily, just like a normal getter function. Lazy derivations (which never observe anything) can simply be garbage collected if they run out of scope. Remember that computed values should always be _pure functions_ of the observable app state? This is the reason why: For pure functions it doesn't matter whether they are evaluated lazily or eagerly; the evaluation of the function always yields the same result given the same observable state.

Reactions and computed values are both run by Mobservable in the same manner. When a recomputation is triggered the function is pushed onto the _derivation stack_; a function stack of currently running derivations. As long as a computation is running, every observable that is accessed will register itself as a dependency of the topmost function of the derivation stack. If the value of a computed value is needed, the value can simple be the last known value if the computed is already in the reactive state. And otherwise it will push itself on the derivation-stack, switch to reactive mode and start computing as well.

![](https://cdn-images-1.medium.com/max/800/1*Pb3Wd14BlXPgqylBN_y-hQ.png)

Figure 4: During the execution of the profileView reaction some observable state and some computed values are being observed. Computed values might recompute, this results in the the dependency tree as shown in figure 1. When a computation completes, it will have obtained a list of observables that were accessed during execution. In the profileView for example, this list will either just contain the nickName property, or the nickName and fullName properties. This list is diffed against the previous list of observables. Any removed items will be unobserved (computed values might go back from reactive to lazy mode at this point) and any added observables will be observed until the next computation. When the value of for example firstname is changed in the future, it knows that fullName needs to be recomputed. Which in turn will cause profile view to recomputed. The next paragraph explains this process in more detail.

![](https://cdn-images-1.medium.com/max/800/1*Et95GdR7OeOofl_V0Od4iA.png)

Figure 5: The effects of changing value '1' on the dependency tree. The dashed border indicates the observers that will be marked as stale. Numbers indicate the order of computation. Derivations will react to state changes automatically. All reactions happen _synchronously_ and more importantly _glitch-free_. When an observable value is modified the following algorithm is performed:

1. The observable value sends a _stale notification_ to all its observers to indicate that it has become stale. Any affected computed values will recursively pass on the notification to their observers. As a result, a part of the dependency tree will be marked as _stale_. In the example dependency tree of figure 5, the observers that will become stale when value '1' is changed are marked with an orange, dashed border. These are all the derivations that _might_ be affected by the changing value.

2. After sending the _stale notification_ and storing the new value, a _ready notification_ will be sent. This message also indicates whether the value did actually change.

3. As soon as a derivation has received a ready notification for every stale notification received in step 1, it knows that all the observed values are stable and it will start to recompute. Counting the number of ready / stale messages will ensure that, for example, computed value '4' will only re-evaluate after computed value '3' has become stable.

4. If none of the ready messages indicate that a value was changed, the derivation will simply tell its own observers that it is ready again, but without changing its value. Otherwise the computation will recompute and send a ready message to its own observers. This results in the order of execution as displayed in figure 5\. Note that (for example) the last reaction (marked with '-') will never execute if computed value '4' did re-evaluate but didn't produce a new value.

The previous two paragraph summarize how dependencies between observable values and derivations are tracked at run-time and how changes are propagated through the derivations. At this point you might also realize that a _reaction_ is basically a _computed value_ that is always in _reactive_ mode. It is important to realize that this algorithm can be implemented very efficiently without closures and just with a bunch of pointer arrays. Additionally, Mobservable applies a number of optimizations which are beyond the scope of this blog post.

People are often surprised that Mobservable runs everything synchronously (like RxJs and unlike knockout). This has two big advantages: First it becomes simply impossible to ever observe stale derivations. So a derived value can be used immediately after changing a value that influences it. Secondly it makes stack-traces and debugging easier as it avoids the useless stack-traces that are typical to Promise / async libraries.

Listing 2: Example transaction. It ensures that nobody is able to observe an intermediate value like "Mich Weststrate". (See also listing 1) However, synchronous execution also introduces the need for transactions. If several mutations are applied in immediate succession, it is preferable to re-evaluate all derivations after all changes has been applied. Wrapping an action in a _transaction_ block achieves this. Transactions simply postpone all _ready notifications_ until the transaction block has completed. Note that transactions still run & update everything synchronously.

That summarizes the most essential implementation details of Mobservable. We haven't covered everything yet, but it is good to know for example that you can compose computed values. By _composing reactive computations_ it is even possible to automatically transform one graph of data into another graph of data and keep this derivation up to date with the minimum number of patches. This makes it easy to implement complex patterns like map-reduce, state tracking using immutable shared data, or sideways data loading. But more on that in a next blog post.

1. The application state of complex applications can best be expressed using _graphs_ to achieve referential consistency and stay close to the mental model of a problem domain.

2. One should not imperatively act on state changes by using manually defined subscriptions or cursors. This will inevitably lead to bugs as a result of under- or oversubscribing.

3. Use _runtime analysis_ to determine the _smallest possible set_ of observer → observable relationships. This leads to a computational model where it can be guaranteed that the minimum amount of derivations are run without ever observing a stale value.

4. Any derivation that is not needed to achieve an active side effect can be optimized away completely.

For more info on Mobservable, just check out:

1. My [talk at Reactive2015](https://www.youtube.com/watch?v=FEwLwiizlk0)

2. The [official website](http://mweststrate.github.io/mobservable/) or [repo](https://github.com/mweststrate/mobservable)

3. A boilerplate project: [React + Babel](https://github.com/mweststrate/mobservable-react-boilerplate), [React + TypeScript](https://github.com/mweststrate/mobservable-react-typescript), [TodoMVC](https://github.com/mweststrate/todomvc/tree/master/examples/react-mobservable) or the [Flux-Challenge](https://github.com/staltz/flux-challenge/blob/master/submissions/mweststrate/index.tsx)

_P.s. you might find some inconsistencies between the terminology used in this blog-post and the current Mobservable implementation and API. This will be fixed in Mobservable 2.0._

Image by [xt0ph3r](https://www.flickr.com/photos/xt0ph3r/3591236086/in/photostream/)
