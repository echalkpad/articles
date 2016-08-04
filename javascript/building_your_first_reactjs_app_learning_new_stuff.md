# Building Your First React.js App -- Learning New Stuff

[Original URL](https://medium.com/learning-new-stuff/building-your-first-react-js-app-d53b0c98dc#.a1rnyzcwv)

> This tutorial is suitable from age 3\. Credit: Donnie Ray JonesThis article will take you through every step of building the simplest React app possible. Even simpler than a to-do list.

![](https://cdn-images-1.medium.com/max/800/1*YTqou7X2QcqvyslQHkt_Kw.jpeg)

This tutorial is suitable from age 3\. Credit: [Donnie Ray Jones](https://www.flickr.com/photos/donnieray/ "Go to Donnie Ray Jones's photostream") This article will take you through **every step** of building the simplest React app possible. Even simpler than a to-do list.

Why so simple? Because I've found that when I'm trying to learn a new technology, even the simplest features can add unnecessary complexity.

> If you've never tried React before, this tutorial is for you.

There are **no pre-requisites** other than basic Javascript and HTML skills. Though if you'd like to read some basic theory before getting started with the coding, you can read the article below and then return.

To claim you'll be building an app is actually an exaggeration. It's only a profile page, as you can see below. (The image is taken randomly from <http://lorempixel.com/>)

![](https://cdn-images-1.medium.com/max/800/1*jKHL1tfIYyZ5lIXvmRwa9g.png)

React is built around **components;** everything you see on the page is a part of a component. Before we start coding, it's a good idea to create a sketch of the components, as we've done above.

The main component -- which wrap all other components -- is marked in red. We'll call this one _App_.

Once we've figured out that _App_ is our main component, we'll need to ask ourselves: which direct children does _App_ have?

I'd argue that the name and the profile image can be grouped into one component, which we'll call _Profile_ (green rectangle)_,_ and the _Hobbies_ section can be another component (blue rectangle).

The structure of our components can also be visualized like this:

```
- App
 - Profile
 - Hobbies
```

> We could split the component further; like ProfileImage and HobbyItem, though we'll stop here for the sake of simplicity.

Before you begin coding, you'll need to download the source file. It's available at [this GitHub repo.](https://github.com/perborgen/YourFirstReactProject) Simply copy or clone it and open the _index.html_ file in the browser. (The full code is available in the _finished_project.html_ file.)

I've setup the file properly, so you'll see links to the React source and the other necessary libraries in the <head/> section of the file. Your code will start at line 12.

> For a proper React project you wouldn't use this toy setup. But I don't want this tutorial to be about anything else than coding in React, so we'll keep it simple.

Let's write our first component:

![](https://cdn-images-1.medium.com/max/800/1*NLIMG74J43OveO18x19Qvg.png)

As you can see on the first line, we create a component be calling the _createClass_ method on the React object.

We pass in one parameter, the so called **specification object.** This object can have as many methods you want, though the most important one is the _render_ method. In the _render_ method, you'll return what you want React to draw on the page. In our case, we simply want a _div_ tag with the text "Hello World".

Then follow up this:

![](https://cdn-images-1.medium.com/max/800/1*ywkSGo7l3eHXcg9LGAxXWg.png)

This is how we specify where on the page we want the _App_ component to be rendered. This is done by calling _ReactDOM.render,_ passing in the _App_ component as the first argument and a reference to a _div_ with the id of _content_ as the second. **Our entire app** will go into this _content div._

> This syntax might seem a bit weird: we're taking a Javascript variable (_App_) and turn it into what looks like an HTML/XML tag. **This is called JSX.** Read more about JSX in the [React.js in 8 minutes article.](https://medium.com/learning-new-stuff/learn-react-js-in-7-min-92a1ef023003#.b2tr5yuzz)

Refresh the page and you'll see 'Hello World' printed out on the screen.

Let's add some more components. Looking back at our component overview, we see that the _App_ component has got two children called _Profile_ and _Hobbies_.

Let's write out these two components. We'll begin with _Profile:_

![](https://cdn-images-1.medium.com/max/800/1*XsBbwYFKcXKoYZ630-NQqw.png)

There is actually nothing new here. Just a bit more content i the return statement than it was in _App._

Let's write the _Hobbies_ component:

![](https://cdn-images-1.medium.com/max/800/1*N5pgeSVMeQ8gqOFvt0vlGQ.png)

If you refresh the page again though, you won't see any of these components.

This is because only the _App_ component has been rendered into the DOM.

We'll need to modify the _App_ component in order to actually turn _Profile_ and _Hobbies_ into _App's_ children.

This is what we'll need to do:

![](https://cdn-images-1.medium.com/max/800/1*eSWfi8Fp1pUxiM7ypNDmEA.png)

If you refresh the page again you'll see that all the content appears on the page. (Though the image wont appear, as we've only added a dummy link to it).

Now that we have the basic structure setup, we're ready to add the correct data to our project.

React has something called a **one directional data flow**, meaning that the data is passed down from parent to child components.

Above all the components, paste in the following code:

```
var DATA = { 
 name: 'John Smith',
 imgURL: 'http://lorempixel.com/200/200/',
 hobbyList: ['coding', 'writing', 'skiing']
}
```

You can imagine this data being fetched from an API of something.

The next thing you'll need to do is add this data to the _App_ components as its **props.**

> Data in React is either handled as **props** or **state.** We're only going to care about **props** in this tutorial. In general, **props** is immutable and can be shared across components, while **state** is mutable and private. This is better explained in [this article.](https://medium.com/learning-new-stuff/learn-react-js-in-7-min-92a1ef023003#.b2tr5yuzz)

Below, you'll see how you pass the data into the _App_ component, by simply changing a little bit on the _ReactDOM.render_ method. It's similar to how you'd add attributes in normal HTML.

![](https://cdn-images-1.medium.com/max/800/1*tbpzl5NIo9CSLzyRTtzC_A.png)

The curly bracket tells React that we're escaping out of the JSX syntax in order to add a Javascript expression (DATA).

Now we're able to access this data from within the _App_ component through _this.props.profileData._ However, the App component is simply a wrapper around the _Profile_ and _Hobbies_ components, so we're going to send the data further down the hierarchy, using the same technique as described above.

This is how we pass the data from App to its children:

![](https://cdn-images-1.medium.com/max/800/1*rUH8Y52kv5EMxT8-pxlqWA.png)

We're passing down the _profileImage_ and _name_ to the _Profile_ component, while only the _hobbyList_ array down to the _Hobbies_ component. This is because the _Hobbies_ component doesn't need the rest of the data; it's simply going to display a list of hobbies.

Let's look at how we'll need to rewrite the _Profile_ component order to use the data we've passed down to it:

![](https://cdn-images-1.medium.com/max/800/1*guE377pneicTO4uWLJNa9A.png)

We simply escape out of the JSX syntax by curly brackets and fetch the data from _this.props._

In the Hobbies component we'll need to use a technique for looping through the of hobbies.

![](https://cdn-images-1.medium.com/max/800/1*JuQ1TaTndDxfq9ZMYb17hA.png)

As you can see, we're looping through the hobbies array stored in _this.props.hobbies._ We're using the array prototype method [map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map), which creates a new array based on whatever we return within the callback function.

> Notice that we're creating a **key** attribute, an giving it the value **index**. This has to be a unique key for each element, as React needs it to properly keep track of the elements we created dynamically. However, don't use the **index** if the mapped results might change, as it'll be prone to bugs. It'll work fine in this scenario though, so we'll use it for the sake of simplicity.

Once we've stored all the list elements in the _hobbies_ variable, we simply add it to the return statement, so it'll be rendered out on the screen.

This is the full code:

![](https://cdn-images-1.medium.com/max/800/1*GwqPrFnpPal72u-7Fm8CcQ.png)

Congrats, you've just built your first React.js project!

Thanks for reading! My name is Per Borgen and I mostly write about learning new stuff.

Follow me on [Twitter](https://twitter.com/OsloKommunePer) I you want to be notified about new articles.

If you have a question, simply send me an email. I answer every mail I get: [[email protected]](https://medium.com/cdn-cgi/l/email-protection)
