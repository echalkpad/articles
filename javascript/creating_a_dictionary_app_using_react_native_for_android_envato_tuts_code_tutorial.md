# Creating a Dictionary App Using React Native for Android - Envato Tuts+ Code Tutorial

[Original URL](http://code.tutsplus.com/tutorials/creating-a-dictionary-app-using-react-native-for-android--cms-24969)

> Introduction Facebook's React Native is a powerful framework that allows you to quickly and effortlessly build Android and iOS apps using just JavaScript and JSX. Apps built using React Native...

## Introduction

Facebook's [React Native](http://facebook.github.io/react-native/) is a powerful framework that allows you to quickly and effortlessly build Android and iOS apps using just JavaScript and [JSX](https://facebook.github.io/jsx/). Apps built using React Native make use of native user interface components and are thus indistinguishable from apps built directly using the SDKs of Android and iOS.

Their performance too is not too far behind that of native apps, because almost all the JavaScript code runs in the background on an embedded instance of [JavaScriptCore](http://trac.webkit.org/wiki/JavaScriptCore), the same JavaScript engine that powers Apple's Safari.

In this tutorial, I am going to help you get started with React Native for Android by showing you how to build a simple English-German dictionary app.

## Prerequisites

Before you begin, make sure you have the following installed on your computer:

- the latest version of the [Android SDK](https://developer.android.com/sdk/index.html) and Android Support Library
- the latest version of [Node.js](https://nodejs.org/en/)

As of September 2015, React Native is only supported on OS X. However, with the help of [a few scripts](https://github.com/facebook/react-native/pull/2406), React Native v0.11.4 works just fine on Ubuntu 14.04.

## 1\. Installing React Native

React Native is available as a Node.js package and can be quickly installed using `npm`, [Node Package Manager](https://www.npmjs.com).

```
npm install -g react-native-cli
```

To use React Native for developing Android apps, you should set the value of an environment variable called **ANDROID_HOME** to the absolute path of the directory containing the Android SDK. If you are using Bash shell, you can set the variable using `export`.

```
export ANDROID_HOME=/path/to/Android/Sdk
```

## 2\. Creating a New Project

To create a React Native project, you should use React Native's command line interface or CLI, which can be accessed using the `react-native` command. We are creating a dictionary app in this tutorial so let's call the project **Dictionary**.

```
react-native init Dictionary
```

Once the command completes, you will have a new directory called **Dictionary**, containing a starter React Native app. Enter the new directory using `cd`.

```
cd Dictionary
```

Before you proceed, I suggest you run the starter app to make sure that your development environment has everything React Native needs. To do so, type in the following command:

```
react-native run-android
```

You will now find an app called **Dictionary** installed on your emulator. Click on its icon to start it. If everything went well, you should see a screen that looks like this:

![Starter App](https://cms-assets.tutsplus.com/uploads%2Fusers%2F362%2Fposts%2F24969%2Fimage-1443831406494.png)

## 3\. Preparing the Entry Point of Your App

By default, the entry point of a React Native Android app is a JavaScript file called **index.android.js**. When you created the project using the React Native's CLI, this file was created automatically. However, it contains code that belongs to the starter app. You can modify and use parts of that code for your app or you can simply delete all of it and start from scratch. For this tutorial, I suggest you do the latter.

Once you have deleted the contents of **index.android.js**, use `require` to load a module called **react-native**. This module contains all the React Native functions and objects you'll need to create your app.

```
var React = require('react-native');
```

## 4\. Creating a React Component

React components are JavaScript objects that are responsible for rendering and automatically updating the user interface of a React Native app. In fact, almost every user interface element of a React Native app is a React component. This means that, to create the user interface of your app, you need to create your own custom React component. To do so, use the `createClass` function of `React`. The following code creates a component called **Dictionary**:

```
var Dictionary = React.createClass({

});
```

You can think of this component as the first screen of your app.

### Step 1: Defining the Layout

React Native automatically calls the `render` function every time it needs to draw or update a component. Therefore, you must add this function to your component. Inside the function, you can define the layout of the component using JSX, a JavaScript syntax extension that allows you to easily mix XML tags with JavaScript code.

React Native offers several components you can use to compose the layout. For now, we will be using a `React.View` as a container, a `React.Text` to display text, and a `React.TextInput` to accept user input. Add the following code to the component:

```
render: function() {
 var layout =
 <React.View style = { styles.parent } >

 <React.Text>
 Type something in English:
 </React.Text>

 <React.TextInput />

 <React.Text style = { styles.germanLabel } >
 Its German equivalent is:
 </React.Text>

 <React.Text style = { styles.germanWord } > 
 </React.Text>

 </React.View>
 ;
    return layout;
},
```

If you are familiar with HTML, you can think of the `View` as an HTML `div`, the `Text` as an HTML `span`, and the `TextInput` as an HTML `input` element.

### Step 2: Adding Styles

In the above code snippet, several components have a `style` attribute. The `style` attribute is quite similar to the HTML `class` attribute. However, instead of referring to a CSS class in a stylesheet, it refers to a JSON object in an instance of `React.StyleSheet`.

To create a `React.StyleSheet` object for your app, you need to use the `React.StyleSheet.create` function. As its only argument, it expects a JSON object containing the styles of the individual components. Here are the styles I used for our example app:

```
var styles = React.StyleSheet.create({

    // For the container View
 parent: {
 padding: 16
 },

    // For the Text label
 germanLabel: {
 marginTop: 20,
 fontWeight: 'bold'
 },

    // For the Text meaning
 germanWord: {
 marginTop: 15,
 fontSize: 30,
 fontStyle: 'italic'
 }
});
```

### Step 3: Registering the Component

To let React Native know that it should render your component when your app starts, you must register it using the `React.AppRegistry.registerComponent` function. To do so, add the following code at the end of **index.android.js**:

```
React.AppRegistry.registerComponent('Dictionary', () => Dictionary);
```

If you aren't familiar with ES6 arrow functions, you can simply use the following conventional JavaScript code instead:

```
React.AppRegistry.registerComponent('Dictionary', function() {
    return Dictionary;
});
```

If you want to, you can now reload your app to see the new layout. To do so, press the menu button of your emulator and click on **Reload JS**.

![The layout](https://cms-assets.tutsplus.com/uploads%2Fusers%2F362%2Fposts%2F24969%2Fimage-1443916994335.png)

## 4\. Controlling the State of the Component

All components have a special member variable called **state**, which is a JSON object. It's special, because as soon as the `state` of a component changes, React Native automatically re-renders the component to reflect the change. This is a very useful feature and by using it correctly you can do away with manually fetching or updating the contents of your app's user interface elements.

Let's add two keys, **input** and **output**, to the `Dictionary` component's `state`. To do so, you'll have to use a function called `getInitialState`. The return value of this function becomes the `state` of the component.

```
getInitialState: function() {
 return {
 input: '',
 output: ''
 };
},
```

You can now associate the `TextInput` with `input` and the last `Text` component with `output`. After doing so, your layout should look like this:

```
<React.View style = { styles.parent } >

 <React.Text>
 Type something in English:
 </React.Text>

 <React.TextInput text = { this.state.input } />

 <React.Text style = { styles.germanLabel } >
 Its German equivalent is:
 </React.Text>

 <React.Text style = { styles.germanWord } >
 { this.state.output }
 </React.Text>

</React.View>
```

As you might have guessed, `input` will contain the English word the user enters while `output` will contain its German equivalent.

Though changes in the `state` are automatically pushed to the user interface, the reverse is not true. This means, our component's `state` does not change if the user enters something into the `TextInput`. To update the `state` manually, you should use the component's `setState` method.

To send the value of the `TextInput` to `input`, you can add an `onChangeText` listener to the `TextInput` and make a call to `setState` inside it. Using ES6, the `TextInput` tag will look like this:

```
<React.TextInput text = { this.state.input }
    onChangeText={(e) => this.setState({input: e})}
/>
```

At this point, anything the user types into your app's `TextInput` is immediately available in `input`. All that's left for us to do is map the `input` to its German equivalent and update `output`. To do that, you can use a dictionary called [Mr. Honey's Beginner's Dictionary (German-English) by Winfried Honig](http://www.gutenberg.org/ebooks/3212). Download the JSON equivalent of the dictionary from [GitHub](https://github.com/hathibelagal/German-English-JSON-Dictionary) and add it to your project.

To load the dictionary inside **index.android.js**, use `require`.

```
var english_german = require('./english_german.json');
```

As `english_german` is nothing more than a global JSON object where the English words are keys and their German equivalents are values, all you have to do now is check if `input` is available as a key, and, if yes, call `setState` to assign the associated value to `output`. The code to do so could look like this:

```
showMeaning: function() {
    // Use the ternary operator to check if the word 
    // exists in the dictionary.
    var meaning = this.state.input in english_german ? 
         english_german[this.state.input] : 
         "Not Found";

    // Update the state
    this.setState({
         output: meaning 
    });
},
```

You can now assign `showMeaning` to the `onSubmitEditing` listener of the `TextInput` so that it is called only when the user has finished typing.

```
<React.TextInput
 onChangeText={(e) => this.setState({input: e})}
 text = { this.state.input }
 onSubmitEditing = { this.showMeaning }
/>
```

Your dictionary app is ready. You can reload it and type in an English word to immediately see its German translation.

![English-German Dictionary](https://cms-assets.tutsplus.com/uploads%2Fusers%2F362%2Fposts%2F24969%2Fimage-1443915893393.png)

## Conclusion

In this tutorial, you learned how to install React Native and use it to create your first Android app, an English-German dictionary, using just JavaScript and JSX. While doing so, you learned how to compose a custom component, style it, and use its `state` to control what it shows.

To learn more about React Native, you can go through its [documentation](https://facebook.github.io/react-native/docs/getting-started.html).
