# How to Create a News Reader With React Native: Setup and News Item Component - Envato Tuts+ Code Tutorial

[Original URL](http://code.tutsplus.com/tutorials/how-to-create-a-news-reader-with-react-native-setup-and-news-item-component--cms-25935)

> In this tutorial, we'll be creating a news reader app with React Native. In this two-part series, I'm going to assume that this isn't your first React Native app and I won't go too much...

In this tutorial, we'll be creating a news reader app with React Native. In this two-part series, I'm going to assume that this isn't your first React Native app and I won't go too much into detail regarding setting up your machine and running the app on a device. That said, I explain the actual development process in detail.

Even though we'll be deploying to Android, the code used in this tutorial should work on iOS as well. Here's what the final result looks like.

![This is what youll be creating](https://cms-assets.tutsplus.com/uploads/users/41/posts/25935/image/figure-hn-reader.jpg) You can find the source code used in this tutorial on [GitHub](http://github.com/anchetaWern/react-native-hnreader).

## Prerequisites

If you're new to React Native and haven't set up your machine yet, be sure to check out the [getting started guide](https://facebook.github.io/react-native/docs/getting-started.html#content) of the React Native documentation or read [Ashraff's introductory tutorial](http://code.tutsplus.com/tutorials/creating-a-dictionary-app-using-react-native-for-android--cms-24969) on Envato Tuts+. Don't forget to [install the Android SDK](https://facebook.github.io/react-native/docs/android-setup.html) if you want to deploy to Android or install [Xcode and the SDK](https://developer.apple.com/xcode/downloads/) for iOS.

Once you're done, [install NodeJS](https://nodejs.org/en/download/) and the React Native command line tool using [npm](https://www.npmjs.com/).

```
npm install -g react-native-cli
```

## 1\. Project Setup

We're now ready to build the project. Before we start, I'd like to provide a brief overview on how the project is put together. We create two custom components:

- `NewsItems` which renders the news items
- `WebPage` which renders the web page when the user taps a news item

These are then imported to the main entry point file for Android (**index.android.js**) and for iOS (**index.ios.js**). That's all you need to know for now.

### Step 1: Creating a New App

Start by navigating to your working directory. Open a new terminal window inside that directory and execute the following command:

```
react-native init HnReader
```

This creates a new folder named **HnReader** and it contains the files needed to build the app.

React Native already comes with a few default components, but there are also custom ones built by other developers. You can find those on the [react.parts website](https://react.parts/native). Not all components work on both Android and iOS, though. Even some of the default components aren't cross-platform. That's why you have to be careful when picking components since they might differ on each platform or might not work properly on every platform.

It's a good idea to go into the issues page of the GitHub repository of the component that you plan to use and search for either **android support** or **ios support** to quickly check whether the component works on both platforms.

### Step 2: Installing Dependencies

The app that we're going to build depends on a few third party libraries and React components. You can install them by opening **package.json** in the root of your working directory. Add the following to **package.json**:

```
{
 "name": "HnReader",
 "version": "0.0.1",
 "private": true,
 "scripts": {
 "start": "react-native start"
 },
 "dependencies": {
 "lodash": "^4.0.1",
 "moment": "^2.11.1",
 "react-native": "^0.18.1",
 "react-native-button": "^1.3.1",
 "react-native-gifted-spinner": "0.0.3"
 }
}
```

Next, open a terminal window in the working directory and execute `npm install` to install the dependencies specified in **package.json**. Here's a brief description of what each library does in the project:

- **lodash** is used for truncating strings. It may be a bit overkill, but one less line of code that you have to write means one less liability.
- **moment** is used for determining if the news items in the local storage are already there for one day.
- **react-native** is the React Native framework. This is installed by default when you executed `react-native init` earlier.
- **react-native-button** is a react native component used for creating buttons.
- **react-native-gifted-spinner** is used as an activity indicator when making network requests.

## <span class="sectionnum">2.</span>

 Main Component

As I mentioned earlier, the entry point for all React Native projects is **index.android.js** and **index.ios.js**. That is the focus of this section. Replace the contents of these files with the following:

```
'use strict';
var React = require('react-native');

var {
 AppRegistry,
 StyleSheet,
 Navigator
} = React;


var NewsItems = require('./components/news-items');
var WebPage = require('./components/webpage');

var ROUTES = {
 news_items: NewsItems,
 web_page: WebPage
};

var HnReader = React.createClass({ 

 renderScene: function(route, navigator) {

 var Component = ROUTES[route.name];
 return (
 <Component route={route} navigator={navigator} url={route.url} />
 );
 },

 render: function() {
 return (
 <Navigator 
 style={styles.container} 
 initialRoute={{name: 'news_items', url: ''}}
 renderScene={this.renderScene}
 configureScene={() => { return Navigator.SceneConfigs.FloatFromRight; }} />
 );

 },


});


var styles = StyleSheet.create({
 container: {
 flex: 1
 }
});

AppRegistry.registerComponent('HnReader', () => HnReader);
```

Let me break it down. First, we enable strict mode by using the `use script` directive. This makes the parser do more checking on your code. For example, it will complain if you initialize a variable without adding the `var` keyword.

```
'use strict';
```

Next, we import the React Native framework. This allows us to create custom components and add styling to the app.

```
var React = require('react-native');
```

We then extract all the functionality that we need out of the `React` object.

```
var {
 AppRegistry,
 StyleSheet,
 Navigator
} = React;
```

If you're new to ES6 (ECMAScript 6), the above snippet is identical to:

```
var AppRegistry = React.AppRegistry;
var StyleSheet = React.StyleSheet;
var Navigator = React.Navigator;
```

It is syntactic sugar introduced in ES6 to make assigning object properties to variables easier. This is called [destructuring assignment](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

Here's a brief description of what each of the properties that we've extracted does:

- `AppRegistry` is used for registering the main component of the app.
- `StyleSheet` is used for declaring styles to be used by the components.
- `Navigator` is used for switching between different pages of the app.

Next, we import the custom components used by the app. We will be creating these later.

```
var NewsItems = require('./components/news-items');
var WebPage = require('./components/webpage');
```

Create a `ROUTES` variable and assign an object using the above two components as the value for its properties. This allows us to display the component by referring to each of the keys we've defined.

```
var ROUTES = {
 news_items: NewsItems,
 web_page: WebPage
};
```

Create the main component of the app by calling the `createClass` method from the `React` object. The `createClass` method accepts an object as its argument.

```
var HnReader = React.createClass({ 
 ...
});
```

Inside the object is the `renderScene` method, which gets called whenever the route changes. The `route` and `navigator` are passed in as argument to this method. The `route` contains information about the current route (for example, the name of the route).

The `navigator` contains methods that can be used to navigate between different routes. Inside the `renderScene` method, we get the component that we want to render by passing in the name of the current route to the `ROUTES` object. Next, we render the component and pass along the `route`, `navigator`, and the `url` as attributes. Later on, you'll see how these are being used inside each of the components. For now, just remember that when you want to pass data from the main component to a child component, all you have to do is add a new attribute and use the data that you want to pass as the value.

```
renderScene: function(route, navigator) {

 var Component = ROUTES[route.name]; //get the component for this specific route

 //render the component and pass along the route, navigator and the url
 return (
 <Component route={route} navigator={navigator} url={route.url} />
 );
},
```

The `render` method is a required method when creating components because it is responsible for rendering the user interface of the component. In this method, we render the `Navigator` component and pass along a few attributes.

```
render: function() {
 return (
 <Navigator 
 style={styles.container} 
 initialRoute={{name: 'news_items', url: ''}}
 renderScene={this.renderScene}
 configureScene={() => { return Navigator.SceneConfigs.FloatFromRight; }} />
 );

},
```

Let me explain what each attribute does:

- `style` is used for adding styles to the component.
- `initialRoute` is used for specifying the initial route to be used by the navigator. As you can see, we've passed along an object containing a `name` property with its value set to `news_items`. This object is what's being passed to the `route` argument of the `renderScene` method, which we defined earlier. This means that this particular code would render the `NewsItems` component by default.

<!--  -->

```
var Component = ROUTES[route.name]; 
```

The `url` is set to an empty string because we don't have a webpage to render by default.

- `renderScene` is responsible for rendering the component for a specific route.
- `configureScene` is responsible for specifying the animations and gestures to be used when navigating between routes. In this case, we're passing in a function that returns the `FloatFromRight` animation. This means that, when navigating to a route with a higher index, the new page floats from right to left. And when going back, it floats left to right. This also adds a gesture of swiping to the left as a means of going back to the previous route.

<!--  -->

```
() => { return Navigator.SceneConfigs.FloatFromRight; }
```

The styles are defined after the definition of the main component. We call the `create` method from the `StyleSheet` object and pass in an object containing the styles. In this case, we only have one, defining that it's going to occupy the entire screen.

```
var styles = StyleSheet.create({
 container: {
 flex: 1
 }
});
```

Lastly, we register the component.

```
AppRegistry.registerComponent('HnReader', () => HnReader);
```

## 3\. `NewsItem` Component

The `NewsItem` component is used for rendering the news items. The custom components are stored in the **components** directory. Inside this directory, create **news-items.js** and add the following code to it:

```
'use strict';
var React = require('react-native');

var {
 AppRegistry,
 StyleSheet,
 Text,
 ListView,
 View,
 ScrollView,
 TouchableHighlight,
 AsyncStorage
} = React;

var Button = require('react-native-button');
var GiftedSpinner = require('react-native-gifted-spinner');

var api = require('../src/api.js');

var moment = require('moment');

var TOTAL_NEWS_ITEMS = 10;

var NewsItems = React.createClass({

 getInitialState: function() {
 return {
 title: 'HN Reader',
 dataSource: new ListView.DataSource({
 rowHasChanged: (row1, row2) => row1 !== row2,
 }),
 news: {},
 loaded: false
 } 
 },

 render: function() {

 return (
 <View style={styles.container}>
 <View style={styles.header}>
 <View style={styles.header_item}>
 <Text style={styles.header_text}>{this.state.title}</Text>
 </View>
 <View style={styles.header_item}>
 { !this.state.loaded && 
 <GiftedSpinner />
 }
 </View>
 </View>
 <View style={styles.body}>
 <ScrollView ref="scrollView">
 {
 this.state.loaded && 

 <ListView initialListSize={1} dataSource={this.state.news} style={styles.news} renderRow={this.renderNews}></ListView>

 }
 </ScrollView>
 </View>
 </View>
 ); 

 },

 componentDidMount: function() {

 AsyncStorage.getItem('news_items').then((news_items_str) => {

 var news_items = JSON.parse(news_items_str);

 if(news_items != null){

 AsyncStorage.getItem('time').then((time_str) => {
 var time = JSON.parse(time_str);
 var last_cache = time.last_cache;
 var current_datetime = moment();

 var diff_days = current_datetime.diff(last_cache, 'days');

 if(diff_days > 0){
 this.getNews();
 }else{
 this.updateNewsItemsUI(news_items);
 }

 });


 }else{
 this.getNews();
 }

 }).done();

 },

 renderNews: function(news) {
 return (
 <TouchableHighlight onPress={this.viewPage.bind(this, news.url)} underlayColor={"#E8E8E8"} style={styles.button}>
 <View style={styles.news_item}>
 <Text style={styles.news_item_text}>{news.title}</Text>
 </View>
 </TouchableHighlight>
 );
 },

 viewPage: function(url){
 this.props.navigator.push({name: 'web_page', url: url});
 },

 updateNewsItemsUI: function(news_items){

 if(news_items.length == TOTAL_NEWS_ITEMS){

 var ds = this.state.dataSource.cloneWithRows(news_items);
 this.setState({
 'news': ds,
 'loaded': true
 });

 }

 },

 updateNewsItemDB: function(news_items){

 if(news_items.length == TOTAL_NEWS_ITEMS){
 AsyncStorage.setItem('news_items', JSON.stringify(news_items));
 }

 },

 getNews: function() { 

 var TOP_STORIES_URL = 'https://hacker-news.firebaseio.com/v0/topstories.json';
 var news_items = [];

 AsyncStorage.setItem('time', JSON.stringify({'last_cache': moment()}));

 api(TOP_STORIES_URL).then(
 (top_stories) => {

 for(var x = 0; x <= 10; x++){

 var story_url = "https://hacker-news.firebaseio.com/v0/item/" + top_stories[x] + ".json";

 api(story_url).then(
 (story) => {

 news_items.push(story);
 this.updateNewsItemsUI(news_items);
 this.updateNewsItemDB(news_items);

 }
 );

 }


 }



 );


 }

});



var styles = StyleSheet.create({
 container: {
 flex: 1
 },
 header: {
 backgroundColor: '#FF6600',
 padding: 10,
 flex: 1,
 justifyContent: 'space-between',
 flexDirection: 'row'
 },
 body: {
 flex: 9,
 backgroundColor: '#F6F6EF'
 },
 header_item: {
 paddingLeft: 10,
 paddingRight: 10,
 justifyContent: 'center'
 },
 header_text: {
 color: '#FFF',
 fontWeight: 'bold',
 fontSize: 15
 },
 button: {
 borderBottomWidth: 1,
 borderBottomColor: '#F0F0F0'
 },
 news_item: {
 paddingLeft: 10,
 paddingRight: 10,
 paddingTop: 15,
 paddingBottom: 15,
 marginBottom: 5
 },
 news_item_text: {
 color: '#575757',
 fontSize: 18
 }
});

module.exports = NewsItems;
```

### Step 1: Importing Components & Libraries

First, we import the components and libraries that we need for the `NewsItem` component. We also create a global variable that stores the total number of news items to be cached.

```
'use strict';
var React = require('react-native');

var {
 AppRegistry,
 StyleSheet,
 Text,
 ListView,
 View,
 ScrollView,
 TouchableHighlight,
 AsyncStorage
} = React;

var Button = require('react-native-button');
var GiftedSpinner = require('react-native-gifted-spinner');

var api = require('../src/api.js');

var moment = require('moment');

var TOTAL_NEWS_ITEMS = 10;
```

We use a few components that we haven't used earlier.

- `Text` is used for displaying text in React Native.
- `View` is the basic building block for creating components. Think of it as a `div` in web pages.
- `ListView` is used for rendering an array of objects.
- `ScrollView` is used for adding scroll bars. React Native isn't like web pages. Scroll bars aren't automatically added when the content is larger than the view or screen. That is why we need to use this component.
- `TouchableHighlight` is used for making a component respond to touch events.
- `AsyncStorage` isn't really a component. It's an API used for storing local data in React Native.
- `Button` is a third party component for creating buttons.
- `GiftedSpinner` is used for creating spinners when loading data from the network.
- `api` is a custom module that wraps `fetch`, React Native's way for making network requests. There's a lot of boilerplate code needed to get hold of the data returned by a network request and that is why we are wrapping it inside a module. This lest us write less code when making network requests.
- `moment` is a library used for anything related to time.

### Step 2: Creating `NewsItems` Component

Next, we create the `NewsItems` component:

```
var NewsItems = React.createClass({
 ...
});
```

In this component is the `getInitialState` function, which is used for specifying the default state for this component. In React Native, the state is used for storing data that is available throughout the whole component. Here, we are storing the title of the app, the `dataSource` for the `ListView` component, the current `news` items and a boolean value, `loaded`, which tells whether the news items are currently being loaded from the network or not. The `loaded` variable is used to determine whether or not to display the spinner. We set it to `false` so the spinner is visible by default.

Once the news items are loaded, either from the local storage or from the network, it is set to `true` to hide the spinner. The `dataSource` is used to define the blueprint of the data source to be used for the `ListView` component. Think of it as a parent class in which every data source that you will be defining will inherit from. This requires an object containing the `rowHasChanged` function, which tells the `ListView` to re-render when a row has changed.

Lastly, the `news` object contains the initial value for the data source of the `ListView`.

```
getInitialState: function() {
 return {
 title: 'HN Reader',
 dataSource: new ListView.DataSource({
 rowHasChanged: (row1, row2) => row1 !== row2,
 }),
 news: {},
 loaded: false
 } 
},
```

### Step 3: Implementing the `render` Function

The `render` function renders the user interface for this component. First, we wrap everything in a `View`. Then, inside we have the header and the body. The header contains the title and the spinner. The body contains the `ListView`. Everything inside the body is wrapped inside a `ScrollView` so that a scroll bar is automatically added if the content exceeds the available space.

```
render: function() {

 return (
 <View style={styles.container}>
 <View style={styles.header}>
 <View style={styles.header_item}>
 <Text style={styles.header_text}>{this.state.title}</Text>
 </View>
 <View style={styles.header_item}>
 { !this.state.loaded && 
 <GiftedSpinner />
 }
 </View>
 </View>
 <View style={styles.body}>
 <ScrollView ref="scrollView">
 {
 this.state.loaded && 

 <ListView initialListSize={1} dataSource={this.state.news} style={styles.news} renderRow={this.renderNews}></ListView>

 }
 </ScrollView>
 </View>
 </View>
 ); 

},
```

Inside the header are two views:

- one containing the title
- one containing the spinner

We're doing it this way instead of outputting the text and the spinner directly so that we can control the styling by using **flexbox**. You can see how this is done in the styling section, later on.

We can refer to the title stored in the state by using `this.state`, followed by the property name. As you may have noticed, every time we need to refer to an object, we wrap it in curly braces. On the other view, we're checking if the `loaded` property in the state is set to `false` and, if it is, we output the spinner.

```
<View style={styles.header_item}>
 <Text style={styles.header_text}>{this.state.title}</Text>
</View>
<View style={styles.header_item}>
{ !this.state.loaded && 
 <GiftedSpinner />
}
</View>
```

Next is the body.

```
<ScrollView ref="scrollView">
{
 this.state.loaded && 

 <ListView initialListSize={1} dataSource={this.state.news} style={styles.news} renderRow={this.renderNews}></ListView>

}
</ScrollView>
```

Notice that we have passed a `ref` attribute to the `ScrollView`. `ref` is a predefined attribute in React Native that allows us to assign an identifier to a component. We can use this identifier to refer to the component and call its methods. Here's an example of how this works:

```
scrollToTop: function(){
 this.refs.scrollView.scrollTo(0);
}
```

You can then have a button and have it call the function on press. This will automatically scroll the `ScrollView` to the very top of the component.

```
<Button onPress={this.scrollToTop}>scroll to top</Button>
```

We won't be using this in the app, but it's good to know that it exists.

Inside the `ScrollView`, we check if the `loaded` property in the state is already set to `true`. If it's `true`, it means that the data source is already available for use by the `ListView` and we can render it.

```
{
 this.state.loaded && 

 <ListView initialListSize={1} dataSource={this.state.news} renderRow={this.renderNews}></ListView>

}
```

We have passed the following attributes in the `ListView`:

- `initialListSize` is used to specify how many rows to render when the component is initially mounted. We've set it to `1`, which means it will take one frame to render each row. I've set it to `1` as a form of performance optimization so that the user sees something as soon as possible.
- `dataSource` is the data source to be used.
- `renderRow` is the function used for rendering each row in the list.

### Step 4: Implementing the `componentDidMount` Function

Next, we have the `componentDidMount` function, which gets called when this component is mounted:

```
componentDidMount: function() {

 AsyncStorage.getItem('news_items').then((news_items_str) => {

 var news_items = JSON.parse(news_items_str);

 if(news_items != null){

 AsyncStorage.getItem('time').then((time_str) => {
 var time = JSON.parse(time_str);
 var last_cache = time.last_cache;
 var current_datetime = moment();

 var diff_days = current_datetime.diff(last_cache, 'days');

 if(diff_days > 0){
 this.getNews();
 }else{
 this.updateNewsItemsUI(news_items);
 }

 });


 }else{
 this.getNews();
 }

 }).done();

},
```

Inside the function, we try to fetch the news items that are currently stored in local storage. We use the `getItem` method from the `AsyncStorage` API. It returns a promise so we can get access to the returned data by calling the `then` method and passing in a function:

```
AsyncStorage.getItem('news_items').then((news_items_str) => {
 ...
}).done();
```

`AsyncStorage` can only store string data so we use `JSON.parse` to convert the JSON string back to a JavaScript object. If it is `null`, we call the `getNews` method, which fetches the data from the network.

```
var news_items = JSON.parse(news_items_str);

if(news_items != null){
 ...
}else{
 this.getNews();
}
```

If it isn't empty, we use `AsyncStorage` to fetch the last time the news items were stored in the local storage. We then compare it with the current time. If the difference is at least a day (24 hours), we fetch the news items from the network. If it is not, we use the ones in the local storage.

```
AsyncStorage.getItem('time').then((time_str) => {
 var time = JSON.parse(time_str);
 var last_cache = time.last_cache; //extract the last cache time
 var current_datetime = moment(); //get the current time

 //get the difference in days
 var diff_days = current_datetime.diff(last_cache, 'days');

 if(diff_days > 0){
 this.getNews(); //fetch from the network
 }else{
 this.updateNewsItemsUI(news_items); //use the one in the cache
 }

});
```

### Step 5: Implementing the `renderNews` Function

Next is the function for rendering each row in the list. Earlier in the `ListView`, we have defined a `renderRow` attribute, which has a value of `this.renderNews`. This is that function.

The current item in the iteration is passed along as an argument to this function. This allows us to access the `title` and the `url` of each news item. Everything is wrapped inside the `TouchableHighlight` component and inside we output the title of each news item.

The `TouchableHighlight` component accepts the `onPress` attribute, which specifies which function to execute when the user taps the item. Here we call the `viewPage` function and bind the URL to it. The `underlayColor` specifies the background color of the component when it is tapped.

```
renderNews: function(news) {
 return (
 <TouchableHighlight onPress={this.viewPage.bind(this, news.url)} underlayColor={"#E8E8E8"} style={styles.button}>
 <View style={styles.news_item}>
 <Text style={styles.news_item_text}>{news.title}</Text>
 </View>
 </TouchableHighlight>
 );
},
```

In the `viewPage` function, we get a hold of the `navigator` attribute that we've passed earlier from **index.android.js** via the props. In React Native, props are used to access the attributes that are passed from the parent component. We refer to it as `this.props`, followed by the attribute name.

Here, we are using `this.props.navigator` to refer to the `navigator` object. We then call the `push` method to push the `web_page` route to the navigator along with the URL of the web page to be opened by the `WebPage` component. This makes the app transition to the `WebPage` component.

```
viewPage: function(url){
 this.props.navigator.push({name: 'web_page', url: url});
},
```

### Step 6: Implementing the `updateNewsItemsUI` Function

The `updateNewsItemsUI` function updates the data source and the state based on the array of news items that was passed as an argument. We only do so if the total `news_items` is equal to the value that we set earlier for the `TOTAL_NEWS_ITEMS`. In React Native, updating the state triggers the user interface to re-render. This means that calling `setState` with the a new data source refreshes the user interface with the new items.

```
updateNewsItemsUI: function(news_items){

 if(news_items.length == TOTAL_NEWS_ITEMS){

 var ds = this.state.dataSource.cloneWithRows(news_items); //update the data source

 //update the state
 this.setState({
 'news': ds,
 'loaded': true
 });

 }

},
```

### Step 7: Updating Local Storage

The `updateNewsItemDB` function updates the news items that are stored in the local storage. We use the `JSON.stringify` function to convert the array into a JSON string so that we can store it using `AsyncStorage`.

```
updateNewsItemDB: function(news_items){

 if(news_items.length == TOTAL_NEWS_ITEMS){
 AsyncStorage.setItem('news_items', JSON.stringify(news_items));
 }

},
```

### Step 8: Fetching News Items

The `getNews` function updates the local storage item that stores the last time the data was cached, fetches the news items from the [Hacker News API](https://github.com/HackerNews/API), updates the user interface and the local storage based on the new items that were fetched.

```
getNews: function() { 

 var TOP_STORIES_URL = 'https://hacker-news.firebaseio.com/v0/topstories.json';
 var news_items = [];

 AsyncStorage.setItem('time', JSON.stringify({'last_cache': moment()}));

 api(TOP_STORIES_URL).then(
 (top_stories) => {

 for(var x = 0; x <= 10; x++){

 var story_url = "https://hacker-news.firebaseio.com/v0/item/" + top_stories[x] + ".json";

 api(story_url).then(
 (story) => {

 news_items.push(story);
 this.updateNewsItemsUI(news_items);
 this.updateNewsItemDB(news_items);

 }
 );

 }
 }
 );

}
```

The [top stories resource](https://hacker-news.firebaseio.com/v0/topstories.json) in the Hacker News API returns an array that looks like this:

```
[ 10977819, 10977786, 10977295, 10978322, 10976737, 10978069, 10974929, 10975813, 10974552, 10978077, 10978306, 10973956, 10975838, 10974870...
```

These are the identifiers of the top items posted on Hacker News. That's why we need to loop through this array and make a network request for each item in order to get the actual details, such as title and URL.

We then push it to the `news_items` array and call the `updateNewsItemsUI` and `updateNewsItemDB` functions to update the user interface and the local storage.

```
for(var x = 0; x <= 10; x++){

 var story_url = "https://hacker-news.firebaseio.com/v0/item/" + top_stories[x] + ".json";

 api(story_url).then(
 (story) => {

 news_items.push(story);
 this.updateNewsItemsUI(news_items);
 this.updateNewsItemDB(news_items);

 }
 );

}
```

### Step 9: Styling

Add the following styles:

```
var styles = StyleSheet.create({
 container: {
 flex: 1
 },
 header: {
 backgroundColor: '#FF6600',
 padding: 10,
 flex: 1,
 justifyContent: 'space-between',
 flexDirection: 'row'
 },
 body: {
 flex: 9,
 backgroundColor: '#F6F6EF'
 },
 header_item: {
 paddingLeft: 10,
 paddingRight: 10,
 justifyContent: 'center'
 },
 header_text: {
 color: '#FFF',
 fontWeight: 'bold',
 fontSize: 15
 },
 button: {
 borderBottomWidth: 1,
 borderBottomColor: '#F0F0F0'
 },
 news_item: {
 paddingLeft: 10,
 paddingRight: 10,
 paddingTop: 15,
 paddingBottom: 15,
 marginBottom: 5
 },
 news_item_text: {
 color: '#575757',
 fontSize: 18
 }
});
```

Most of it is standard CSS, but note that we have replaced the dashes with camel case syntax. This isn't because we get a syntax error if we use something like `padding-left`. It's because it's required by React Native. Also note that [not all css properties can be used](https://facebook.github.io/react-native/docs/stylesheet.html#content).

That said, here are some declarations that might not be that intuitive, especially if you haven't used **flexbox** before:

```
container: {
 flex: 1
},
header: {
 backgroundColor: '#FF6600',
 padding: 10,
 flex: 1,
 justifyContent: 'space-between',
 flexDirection: 'row'
},
body: {
 flex: 9,
 backgroundColor: '#F6F6EF'
},
```

Here is a simplified version of the markup for the `NewsItems` component to help you visualize it:

```
<View style={styles.container}>
 <View style={styles.header}>
 ...
 </View>
 <View style={styles.body}>
 ...
 </View>
</View>
```

We have set `container` to `flex: 1`, which means it occupies the entire screen. Inside the `container` we have the `header` and the `body`, which we have set to `flex: 1` and `flex: 9`, respectively. In this case, `flex: 1` won't occupy the entire screen since the `header` has a sibling. These two will share the entire screen. This means that the entire screen will be divided into ten sections since we have `flex: 1` and `flex: 9`. The values for `flex` for each of the siblings are added up.

The `header` occupies 10% of the screen and the `body` occupies 90% of it. The basic idea is picking a number that will represent the height or width of the whole screen and then each sibling takes a piece from this number. Don't go overboard with this, though. You don't want to use 1000 unless you want to deploy your app in a movie theatre. I find ten to be the magic number when working with height.

For the `header` we have set the following styles:

```
header: {
 backgroundColor: '#FF6600',
 padding: 10,
 flex: 1,
 justifyContent: 'space-between',
 flexDirection: 'row'
},
```

And to refresh your memory, here is the simplified markup of what is inside the header:

```
<View style={styles.header_item}>
 ...
</View>
<View style={styles.header_item}>
 ...
</View>
```

And the style added to those:

```
header_item: {
 paddingLeft: 10,
 paddingRight: 10,
 justifyContent: 'center'
},
```

We have set `flexDirection` to `row` and `justifyContent` to `space-between` in their parent, which is `header`. This means that its children will be evenly distributed, with the first child at the start of the line and the last child at the end of the line.

By default `flexDirection` is set to `column`, which means that each child occupies the entire line since the movement is horizontal. Using `row` would make the flow vertical so that each child would be side by side. If you're still confused about Flexbox or you want to learn more about it, then check out [CSS: Flexbox Essentials](http://webdesign.tutsplus.com/courses/css-flexbox-essentials).

Lastly, expose the component to the outside world:

```
module.exports = NewsItems;
```

## Conclusion

At this point, you should have a good idea on how to do things the React Native way. Specifically, you have learned how to create a new React Native project, install third party libraries through **npm**, use various components, and add styling to the app.

In the next article, we'll continue by adding the `WebPage` component to the News Reader app. Feel free to leave any questions or comments in the comments section below.
