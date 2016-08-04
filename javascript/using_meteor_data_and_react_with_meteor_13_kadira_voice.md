# Using Meteor Data and React with Meteor 1.3 -- KADIRA VOICE

[Original URL](https://voice.kadira.io/using-meteor-data-and-react-with-meteor-1-3-13cb0935dedb?gi=9ab61467c15b)

> In a previous post, I talked about how to use React with Meteor 1.3\. In that post, I showed how to get React from NPM and use FlowRouter to build a simple app.Getting Started with Meteor 1.3 and

In a [previous post](https://voice.kadira.io/getting-started-with-meteor-1-3-and-react-15e071e41cd1), I talked about how to use React with Meteor 1.3\. In that post, I showed how to get React from NPM and use FlowRouter to build a simple app.

[](https://voice.kadira.io/getting-started-with-meteor-1-3-and-react-15e071e41cd1#.ix87q6ay4)[**Getting Started with Meteor 1.3 and React**<br>
_Meteor is working on the 1.3 release and it will be one of the greatest releases ever. It comes with ES2015 module..._voice.kadira.io](https://voice.kadira.io/getting-started-with-meteor-1-3-and-react-15e071e41cd1#.ix87q6ay4 "https://voice.kadira.io/getting-started-with-meteor-1-3-and-react-15e071e41cd1#.ix87q6ay4")

This is a sequel to that, where I show how to use Meteor's data system with React. In technical terms, we are trying to get data with a subscription and render a component based on that data.

Before we jump into that, we need to learn the idea behind "React Container Pattern". It will allow you to separate the data layer and the presentation layer (UI) very easily.

Have a look at the following screenshot:

![](https://cdn-images-1.medium.com/max/800/1*ips8Bv9lk2Bn-1GXSf5gKw.png)

Here we have a component(BlogPost) that contains the UI. Then we use something called a "container component" which fetches the data. Then it'll pass that data into the UI component via props.

To learn more about this pattern, have a look at the following article:

[](https://voice.kadira.io/let-s-compose-some-react-containers-3b91b6d9b7c8#.z0ek0u8he)[**Let's Compose Some React Containers**<br>
_Recently, self-sufficient containers became very popular in the React community. They're commonly used in Relay, Redux..._voice.kadira.io](https://voice.kadira.io/let-s-compose-some-react-containers-3b91b6d9b7c8#.z0ek0u8he "https://voice.kadira.io/let-s-compose-some-react-containers-3b91b6d9b7c8#.z0ek0u8he")

> Now we have enough knowledge about Containers. We can continue.

Here we are trying to get a list of MongoDB documents from a collection called **Posts**. Here's the publication we'll be using:

Then we need to have a UI component to render those posts. Here it is:

Here we are using a [functional stateless component](https://medium.com/@joshblack/stateless-components-in-react-0-14-f9798f8b992d). It will get data via props and render our UI.

> We are also using some ES2015 code. If that's something new to you, follow this short course: [**Say Hello to ES2015**](https://tutor.mantrajs.com/say-hello-to-ES2015/introduction)

Now we need to create a container that fetches data. For that, we are using [react-komposer](https://github.com/kadirahq/react-komposer) via NPM. It's a universal React container composing library.

As you've just seen, we write our subscription and data fetching logic as a composer function.

This composer function runs inside a Tracker. So, you can use any reactive code within this composer function.

Once you have the data, you can call the onData function like this:

```
const posts = Posts.find({}, {sort: {_id: 1}}).fetch();
onData(null, {posts});
```

> First argument of the composer functions contains props passed to the container. You can use them to load your data.

Then, we compose the container like this.

```
export default composeWithTracker(composer)(PostList);
```

At the end you'll get a React component.

Now it's time to render our container.

> Make sure that you render the container, not the UI component.

We can do it like this:

Once it's rendered, here's how we can see it in the React Dev tools.

![](https://cdn-images-1.medium.com/max/800/1*85Ttsd8DqAW32S7N42zP_w.png)

As you can see, our UI component is wrapped by the container. The container passes data to the UI component via props.

You can compose containers like this and build your whole app. Here's a simple app you can refer to: <https://github.com/kadira-samples/meteor-data-and-react>

We created [Mantra](https://github.com/kadirahq/mantra), extending these ideas further for building maintainable, future-proof Meteor apps.

**It's an application architecture for Meteor.**

![](https://cdn-images-1.medium.com/max/800/1*GB5ezX9fCV6XzYRoS4QOtg.png)

[Give it a try](https://github.com/kadirahq/mantra). You'll love it.

[composeWithTracker](https://github.com/kadirahq/react-komposer#using-with-meteor) runs inside a tracker and it'll stop the tracker when the container is unmounting. Tracker knows how to unsubscribe subscriptions properly.

**_So, you don't need to worry about it._**

**react-komposer** has a built-in loading functionality. It handles that for you. But you can override the default loading component as shown below:

```
const MyLoading = () => (<div>Hmm…</div>);
const Container = compose(composerFunction, MyLoading)(Component);
```

When the container is starting, there is no data. So, it shows the loading screen.

Then, once you put data into the container, it'll stop the loading screen and render the UI component.

If you need to get the loading screen again, you need to send `null` as data:

```
function composer(props, onData) {
 ...
 onData(null, null);
};
```

You can handle the loading screen inside your component as well. To do that, always send some data to the container, then pass a loading indicator to the UI component via data. Here's an example:

This is also similar to the above question. Make sure you are always sending something to the container.

Yes, of course. Check this:

![](https://cdn-images-1.medium.com/max/800/1*hx2XQqlxkMfM04fNwG1PCg.png)

For that, you don't need to work with the container. Just add lifecycle hooks to your UI component. See the following example:

By default, container's displayName is something like this:

```
Container(UIComponentName)
```

Container itself is a react component. So, you can simply set a displayName to override the default displayName.

```
const Container = composeWithTracker(composer)(PostList);
Container.displayName = ‘MyContainer’;
```

You can pass an error to the container as the first argument of onData callback. Then it'll display the error on the UI. But you can easily override it and render a custom error component as shown below.

```
const MyError = ({error}) => (<div>Error: {error.message}</div>);
const Container = compose(onPropsChange, null, MyError)(UIComp);
```

> Make sure to pass a JavaScript Error object as the error.

Container is also a React component. You can create a container to a container. We have a utility function called [composeAll](https://github.com/kadirahq/react-komposer/#compose-multiple-containers) which helps you to do it.

There is no right or wrong way. But, this is how we load data into a component in modern React. This pattern is used by popular data management libraries such as [Relay](https://facebook.github.io/relay/) and Redux (via [react-redux](https://github.com/rackt/react-redux)).

[Seems like it'll be.](https://github.com/meteor/react-packages/issues/169#issuecomment-175920636)

> Follow [KADIRA VOICE](https://voice.kadira.io/) for articles like this.
