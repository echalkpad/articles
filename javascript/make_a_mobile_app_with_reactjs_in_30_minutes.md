# Make a Mobile App with ReactJS in 30 Minutes

[Original URL](https://scotch.io/tutorials/make-a-mobile-app-with-reactjs-in-30-minutes)

> Now we have an interface with no logic. Before we can link together the Button to the display of photos, we need to grab the photos from Flickr using React conventions. First, get yourself a Flickr...

Now we have an interface with no logic. Before we can link together the Button to the display of photos, we need to grab the photos from Flickr using React conventions. First, get yourself a Flickr account and API key using their quick [sign up form](https://www.flickr.com/services/apps/create/noncommercial/).

After filling it out (and signing up if necessary) copy the Public Key they give you and add it as a constant to `App.jsx`. You'll also need the URL that's used for searching for photos, which I found by using their [API explorer](<https://www.flickr.com/services/api> explore/flickr.photos.search).

It should look like this:

```
const key = '__YOUR_KEY_HERE__';
const base = 'https://api.flickr.com/services/rest/?api_key=${key}&format=rest&format=json&nojsoncallback=1';
```

Be sure to put your key instead of "**YOUR_KEY_HERE**".

_Note: `const` is a new feature in the next version of JavaScript, called ES6\. It's just like a variable, but one that can never be changed once it's set. How can we use this in our app now? Reapp has a [Webpack](https://scotch.io/tutorials/webpack.github.io) build system built in that gives you all sorts of features, including ES6 support!_

Next, define `getInitialState()` on our React class, so our component can track the photos we'll be fetching. We add this as the first property after `React.createClass`. Because we're storing photos in a list, add an array:

```
getInitialState() {
 return {
 photos: []
 }
},
```

This will give us access to `this.state.photos` in our render function. In the UI we'll need a Button and Input to use for searching:

```
import Button from 'reapp-ui/components/Button';
import Input from 'reapp-ui/components/Input';
```

And then change the `render()` function:

```
 render() {
 var { photos } = this.state;

 return (
 <View title="Flickr Search">
 <Input ref="search img-responsive" />
 <Button onTap={this.handleSearch}>Search Images</Button>

 <div className="verticalCenter">
 {!photos.length &&
 <p>No photos!</p>
 }
 </div>
 </View>
 );
 }
```

And we get this:

![no-photos](https://cask.scotch.io/2015/03/no-photos-281x500.png)

Pretty easy! There's a few things to note here. First, notice the [ref property](http://facebook.github.io/react/docs/more-about-refs.html) on the Input? Ref is short for reference, and lets us track DOM elements in our class. We'll use that in the future for getting the value of the field.

Also, note `className="verticalCenter"` on the div. Two things: Because we're using JSX that compiles to JS objects ([more reading here](<http://facebook.github.io> react/docs/jsx-in-depth.html)), we can't use the normal `class` attribute, so instead use use the JavaScript convention of `className` to set the class. The `verticalCenter` property is given to us by Reapp, that will align things centered on our page.

Finally, the `onTap` property on Button? It's pointing to `this.handleSearch`. But, we don't have any handleSearch function. React will expect that function defined on the class, so lets wire it up. First, `npm install --save superagent` which gives us the excellent [Superagent](https://github.com/visionmedia/superagent) request library. Then, import it:

```
import Superagent from 'superagent';
```

Finally, define handleSearch:

```
 handleSearch() {
 let searchText = this.refs.search.getDOMNode().value;
 Superagent
 .get(`${base}&method=flickr.photos.search&text=${searchText}&per_page=10&page=1`, res => {
 if (res.status === 200 && res.body.photos)
 this.setState({
 photos: res.body.photos.photo
 });
 });
 },
```

A few notes:

- `this.refs.search.getDOMNode()` returns the input DOM node that we put the "search" ref on earlier.
- `${base}` will grab the URL we put in the constant.
- `this.setState` will take our response photos and put them into the `this.state.photos` array we defined earlier in `getInitialState`.
