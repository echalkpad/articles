# Build a Music Streaming App with Electron, React & ES6

[Original URL](https://www.sitepoint.com/music-streaming-app-electron-react-es6/)

> This article was peer reviewed by Mark Brown, Dan Prince and Bruno Mota. Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be! Developed by GitHub,...

_This article was peer reviewed by [Mark Brown](https://www.sitepoint.com/author/mbrown), [Dan Prince](https://www.sitepoint.com/author/dprince) and [Bruno Mota](https://www.sitepoint.com/author/bmota/). Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be!_

Developed by GitHub, Electron is a framework that allows you to leverage your web design skills to build slick, cross-platform desktop apps. In this tutorial, I'll demonstrate how to combine the power of Electron with React, ES6 and the Soundcloud API to create a stylish music streaming app that will stream your favorite tunes right to your desktop. I'll also demonstrate how you can package the app and distribute it as a portable, OS-specific bundle.

This tutorial assumes a basic knowledge of React. If you'd like a primer before you begin, check out our [getting started tutorial](http://www.sitepoint.com/getting-started-react/). The code for this tutorial is available from our [GitHub repo](https://github.com/sitepoint-editors/electron-soundcloudplayer/).

## Overview of What We're Building

This is what our app is going to look like:

![Soundcloud player](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/03/1456922655electron-soundcloud.png)

We will use React to create the UI, the SoundCloud API to get the tracks, and Electron to allow the app to run in a browser-like environment. As you can see, it will have a search field for searching for the music to be played and the results will be the audio players for each of the results. Pretty much like what you see on the SoundCloud website.

If you want to follow along make sure you have a SoundCloud account and a [SoundCloud app](http://soundcloud.com/you/apps/new). Take note of the API key because we will use it later.

## Adding Electron and Other Dependencies

Start by cloning the [Electron Quick Start repo on Github](https://github.com/atom/electron-quick-start) into a folder titled `soundcloud-player`:

```
git clone https://github.com/atom/electron-quick-start soundcloud-player
```

Enter that folder, then open the `package.json` file and add the following dev dependencies:

```
"devDependencies": {
 "electron-prebuilt": "^1.2.0",
 "babel-preset-es2015": "^6.9.0",
 "babel-preset-react": "^6.5.0",
 "babelify": "^7.3.0",
 "browserify": "^13.0.1"
}
```

Here's a brief description of each package:

- [electron-prebuilt](https://www.npmjs.com/package/electron-prebuilt) --installs Electron prebuilt binaries for command-line use.
- [babel-preset-es2015](https://www.npmjs.com/package/babel-preset-es2015)--used for transforming ES6 code to ES5 code (which can run in any modern browser).
- [babel-preset-react](https://www.npmjs.com/package/babel-preset-react)--used for transforming JSX code to JavaScript.
- [babelify](https://www.npmjs.com/package/babelify)--the Babel transformer for Browserify.
- [browserify](https://www.npmjs.com/package/browserify)--builds a bundle you can serve up to the browser in a single `<script>` tag.

Add the following under `dependencies`:

```
"dependencies": {
 "node-soundcloud": "0.0.5",
 "react": "^0.14.8",
 "react-dom": "^0.14.8",
 "react-loading": "0.0.9",
 "react-soundplayer": "^0.3.6"
}
```

Here's a brief description of each package:

- [node-soundcloud](https://www.npmjs.com/package/node-soundcloud)--allows us to make calls to the SoundCloud API.
- [react](https://www.npmjs.com/package/react)--the React library. Allows us to create UI components.
- [react-dom](https://www.npmjs.com/package/react-dom)--allows us to render React components into the DOM.
- [react-loading](https://www.npmjs.com/package/react-loading)--used as a loading indicator for the app.
- [react-soundplayer](https://www.npmjs.com/package/react-soundplayer)--a React component that allows us to easily create custom audio players for SoundCloud.

Once you've added the `dependencies` and `devDependencies`, execute `npm install` to install all of them.

Finally, add the scripts for compiling and starting the app. This will allow you to run `npm run compile` to compile the app and `npm start` to run it.

```
"scripts": {
 "compile": "browserify -t [ babelify --presets [ react es2015 ] ] src/app.js -o js/app.js",
 "start": "electron main.js"
}
```

While we're at it, we can remove the electron-quick-start-specific stuff and add sensible defaults of our own.

```
{
 "name": "electron-soundcloud-player",
 "version": "1.0.0",
 "description": "Plays music from SoundCloud",
 "main": "main.js",
 "scripts": {
 "start": "electron main.js",
 "compile": "browserify -t [ babelify --presets [ react es2015 ] ] src/app.js -o js/app.js"
 },
 "author": "Wern Ancheta",
 ...
}
```

All in all, your `package.json` file should now [look like this](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/package.json).

## Project Structure

This is how we are intending to structure our project:

```
.
├── css
│ └── style.css
├── index.html
├── js
├── main.js
├── package.json
├── README.md
└── src
 ├── app.js
 └── components
 ├── ProgressSoundPlayer.js
 └── Track.js
```

Let's create those missing directories:

```
mkdir -p css js src/components
```

And the files they should contain:

```
touch css/style.css src/app.js src/components/ProgressSoundPlayer.js src/components/Track.js
```

The `js` directory will hold the compiled JavaScript for our app, the `css` directory our app's styles and the `src` directory the app's components.

Of the files we pulled in from the Electron Quick Start repo, we can remove the following:

```
rm renderer.js LICENSE.md
```

Which leaves `main.js` and `ìndex.html`. Of these two files, it is `main.js` which is responsible for creating a new browser window in which the app will run. However, we need to make a couple of changes to it. Firstly adjust the width on line 13:

```
mainWindow = new BrowserWindow({width: 1000, height: 600})
```

Secondly remove the following from line 19 (as otherwise our app will initialize showing the dev tools):

```
mainWindow.webContents.openDevTools()
```

When `main.js` creates the new browser window, it will load `index.html` (we'll look at this file later on in the tutorial). From here, the app will run in the same way as it would in a browser window.

## Building the App

### The Track Component

Next let's create the `Track` component for the audio player (in [src/components/Track.js](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/components/Track.js)).

First we require React and a few components provided by React SoundPlayer:

```
import React, {Component} from 'react';
import { PlayButton, Progress, Timer } from 'react-soundplayer/components';
```

Note that by using this syntax we are effectively extracting the `Component` class from React. As the name suggests, `Component` is used for creating new components.

Then we create a new component named `Track` and give it a `render` method. Note that we're exporting this class so it can be imported into another file later on.

```
export default class Track extends Component {
 render() {
 ...
 }
}
```

Inside the `render` method we extract the information about the current audio track from the `props` it received and then assign them to their own variables using [destructuring assignment](https://www.sitepoint.com/preparing-ecmascript-6-destructuring-assignment/). This way we can use `track` instead of `this.props.track`.

```
const { track, soundCloudAudio, playing, seeking, currentTime, duration } = this.props;
```

We then calculate the track's current progress:

```
const currentProgress = this.props.currentTime / this.props.duration create_a_simple_restful_api_with_golang.md data.json data_ml devops docker embedded gaming github go html javascript linux mobile png programming science social tidy tutorialzine.md url_to_filename.csv using_couchbase_server_in_a_golang_web_application.md web_dev 100 || 0;
```

And return the UI of the component.

```
return (
 <div className="player">
 <PlayButton
 className="orange-button"
 soundCloudAudio={soundCloudAudio}
 playing={playing}
 seeking={seeking} />
 <Timer
 duration={duration}
 className="timer"
 soundCloudAudio={soundCloudAudio}
 currentTime={currentTime} />
 <div className="track-info">
 <h2 className="track-title">{track && track.title}</h2>
 <h3 className="track-user">{track && track.user && track.user.username}</h3>
 </div>
 <Progress
 className="progress-container"
 innerClassName="progress"
 soundCloudAudio={soundCloudAudio}
 value={currentProgress} />
 </div>
);
```

As you can see from the above code, we have a pretty standard audio player. It has a play button, a timer (which shows the current play time/duration), the title and username of the user who uploaded the song, and a progress bar.

Here's what the [complete component looks like](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/components/Track.js).

### The ProgressSoundPlayer Component

Let's move on to the ProgressSoundPlayer component ([src/components/ProgressSoundPlayer.js](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/components/ProgressSoundPlayer.js)). This will serve as the wrapper for the `Track` component created above.

Aside from React and the `Track` component, we also need to import the `SoundPlayerContainer`. The `SoundPlayerContainer` is a higher level container that propagates its children with the props they will need to build the audio player.

```
import React, {Component, PropTypes} from 'react';
import { SoundPlayerContainer } from 'react-soundplayer/addons';
import Track from './Track';
```

Next up, we'll create the `ProgressSoundPlayer` component. All this does is render the `SoundPlayerContainer` which wraps the `Track` component. Note that we do not need to pass anything to the `Track` component since `SoundPlayerContainer` automatically does this for us behind the scenes. We do however, need to pass the `resolveUrl` and `clientId` as props for the `SoundPlayerContainer`.

```
export default class ProgressSoundPlayer extends Component {
 render() {
 const {resolveUrl, clientId} = this.props;
 return (
 <SoundPlayerContainer resolveUrl={resolveUrl} clientId={clientId}>
 <Track />
 </SoundPlayerContainer>
 );
 }
}
```

Finally we specify the props required by this component. In this case we require the `resolveUrl` and `clientId` to be passed in when this component is rendered.

```
ProgressSoundPlayer.propTypes = {
 resolveUrl: PropTypes.string.isRequired,
 clientId: PropTypes.string.isRequired
};
```

Specifying the `propTypes` is a [good practice to get into](https://facebook.github.io/react/docs/reusable-components.html#prop-validation). This will trigger warnings in the dev tools console if the props that the component requires don't get passed to it. Note that we didn't have to do this earlier in the `Track` component since the `SoundPlayerContainer` is responsible for passing in all of the necessary props.

Here's what the [complete component looks like](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/components/ProgressSoundPlayer.js).

### Main Component

The main file is [src/app.js](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/app.js). This is responsible for rendering the full UI of the app--that is the search field, and the audio players.

Breaking the code down, we first import all the libraries that we need. Each of these were mentioned earlier in the dependencies section (except for the `ProgressSoundPlayer` which we created).

```
import React, {Component} from 'react';
import ReactDOM from 'react-dom';
import ProgressSoundPlayer from './components/ProgressSoundPlayer';
import SC from 'node-soundcloud';
import Loading from 'react-loading';
```

Add your SoundCloud client ID:

```
var client_id = 'YOUR SOUNDCLOUD APP ID';
```

Note that you can use something like [dotenv](https://github.com/motdotla/dotenv) so you won't have to push this piece of data to your repository.

Initialize the node-soundcloud library by supplying an object containing your SoundCloud client ID.

```
SC.init({
 id: client_id
});
```

Create the `Main` component:

```
class Main extends Component {
 ...
}
```

Inside the class, define the constructor method. This allows us to add code for initializing this class. Inside the `constructor` method we then call `super()` to call the constructor of the `Component` class and any initialization code that the `Component` class has.

```
constructor(props){
 super();
}
```

Next we set the default state of the app:

- `query` is the default search query.
- `hasResults` is used for tracking whether the component currently has any results from the API or not.
- `searchResults` stores the current search results.
- `isLoading` is used for tracking whether the app is currently fetching results from the API or not. When this is set to `true`, the spinner becomes visible to indicate that there's something going on.

<!--  -->

```
this.state = {
 query: '',
 hasResults: false,
 searchResults: [],
 isLoading: false
};
```

Then comes the `handleTextChange` method. This is used for updating the value of `query` in the `state` and also calls the `search` method if the _Enter_ key is pressed. This method gets called when the `onKeyUp` event gets fired on the search field.

```
handleTextChange(event){
 this.setState({
 query: event.target.value
 });
 if(event.key === 'Enter'){
 this.search.call(this);
 }
}
```

After that we have the `search` method, which sends the query to the SoundCloud API and processes the response. First it sets the `isLoading` state to `true` so that the spinner becomes visible. Then it makes a `GET` request to the `tracks` endpoint of the SoundCloud API. This endpoint accepts the query as its required parameter, but we also pass in an additional `embeddable_by` parameter to specify that we only want to fetch tracks that are embeddable by everyone. Once we get a response back, we check if there are any errors and if there aren't, we update the `state` with the search results. At this point the component should now re-render to show the search results.

```
search(){
 this.setState({
 isLoading: true
 });

 SC.get('/tracks', {
 q: this.state.query,
 embeddable_by: 'all'
 }, (err, tracks) => {
 if(!err){
 this.setState({
 hasResults: true,
 searchResults: tracks,
 isLoading: false
 });
 }
 });
}
```

The `render` method renders the UI of the component. This contains a search field for entering the song name or artist and a button for submitting the search. It also contains a couple of conditional statements for rendering the `Loading` component (which only becomes visible when `isLoading` has a truthy value) and the search results (which are only displayed when `hasResults` is truthy but `isLoading` is falsy).

```
render(){
 return (
 <div>
 <h1>Electron SoundCloud Player</h1>
 <input type="search"
 onKeyUp={this.handleTextChange.bind(this)}
 className="search-field"
 placeholder="Enter song name or artist..." />
 <button className="search-button"
 onClick={this.search.bind(this)}>Search</button>
 <div className="center">
 {this.state.isLoading && <Loading type="bars" color="#FFB935" />}
 </div>
 {this.state.hasResults && !this.state.isLoading ?
 this.renderSearchResults.call(this) :
 this.renderNoSearchResults.call(this)}
 </div>
 );
}
```

Notice that we have to use `bind()` for the `handleTextChange` method and `call()` for the `renderSearchResults` and `renderNoSearchResults` methods. This is because methods in React aren't automatically bound when using the ES6 class syntax. Alternatively, you can use something like [decko](https://github.com/developit/decko) to auto-bind specific methods to the class. For example:

```
import { bind } from 'decko';

// ...

@bind
handleTextChange(event){
 this.setState({
 query: event.target.value
 });
 if(event.key == 'Enter'){
 this.search();
 }
}
```

Next, we have a method that gets called by default since there are no search results when the component first gets rendered.

```
renderNoSearchResults(){
 return (
 <div id="no-results"></div>
 );
}
```

And the method that is called when there are search results to display. This calls the `map` method in the `searchResults` to loop through all the results and execute the `renderPlayer` function for each iteration.

```
renderSearchResults(){
 return (
 <div id="search-results">
 {this.state.searchResults.map(this.renderPlayer.bind(this))}
 </div>
 );
}
```

The `renderPlayer` function accepts the individual `track` object as its argument. We use it as a source for the `key` and `resolveUrl` props. If you've worked with React in the past, you already know that when using the `map` method to render a list, we always have to pass a unique `key` or else React will complain. The other two props: `clientId` and `resolveUrl` are required by the `ProgressSoundPlayer` component. The `clientId` is the SoundCloud API key which you defined earlier and the `resolveUrl` is the unique URL that refers to that specific audio track. It's the same URL that you get when you visit the page for a specific audio track on SoundCloud.

```
renderPlayer(track){
 return (
 <ProgressSoundPlayer
 key={track.id}
 clientId={client_id}
 resolveUrl={track.permalink_url} />
 );
}
```

Finally, we render the component into the DOM.

```
var main = document.getElementById('main');
ReactDOM.render(<Main />, main);
```

Here's what the [complete component looks like](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/app.js).

### Styling the App

The styles for the app reside in [css/style.css](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/css/style.css). The stylesheet contains style declarations for each of the components (play button, search button, progress bar, and other elements that we've used).

### Index File

As mentioned earlier, when Electron's `main.js` file creates the new browser window, it will load `index.html`. There's nothing fancy here, just your standard HTML file with a style sheet and a JavaScript file.

```
<!DOCTYPE html>
<html lang="en">
 <head>
 <meta charset="UTF-8">
 <title>Electron Soundcloud Player</title>
 <link rel="stylesheet" href="css/style.css">
 </head>
 <body>
 <div id="main"></div>
 <script src="js/app.js"></script>
 </body>
</html>
```

## Compiling the App

Inside the Electron environment you can actually require stuff just as you would in a standard Node.js app. This means that you can actually use something like:

```
import fs from 'fs';

const buffer = fs.readFileSync(`${__dirname}/index.html`);
console.log(buffer.toString());
```

And Electron will happily run it for you.

But since we've used ES6 and JSX to write the app, we can't really use this feature. An option that we do have is to use Babel to transform the JSX and ES6 code into code that's readable by the browser (ES5). Earlier in the [dependencies section](https://www.sitepoint.com/music-streaming-app-electron-react-es6/#addingelectronandotherdependencies), we installed all of the necessary packages in order for this to work. So all you have to do now is execute the following command to generate the main JavaScript file:

```
npm run compile
```

## Running and Packaging the App

You can run the app by executing `npm start` in the root of your project. But that wouldn't be any fun at all. You might as well just run the app in the browser and call it a day. Instead, what we'll do is to package the app into a single folder. That folder will contain all the files necessary for the app to run. You can then create an archive from that folder to distribute your app.

To package the app we need to install electron-packager:

```
npm install electron-packager -g
```

Once installed, you can go one level up from the root of your project and execute the following command:

```
electron-packager ./soundcloud-player SoundCloudPlayer --version=1.2.4 --platform=linux --out=/home/jim/Desktop --arch=all --ignore="(node_modules|src)"
```

Breaking this command down we have:

- `./soundcloud-player`--your project directory.
- `SoundCloudPlayer`--your app name.
- `--version=1.2.0`--the version of Electron that you want to use. It's at version 1.2.0 at the time of writing of this article, so if you're reading this at a later time, you can probably use the latest version as long as there are no breaking changes in the API.
- `--platform=linux`--the platform where you want to deploy to. In this case I used Linux since I'm on Ubuntu. If you want to package for all major platforms (Windows, OSX, Linux) though, you can use the `--all` option instead.
- `--out=/home/wern/Desktop`--the output directory. This is where the package will get created.
- `--arch=all`--the processor architecture. We've specified `all` which means it will build for both 32-bit and 64-bit operating systems.
- `--ignore="(node_modules|src)"`--since the app is going to get packaged with Electron and Chrome, the size is going to be pretty big. The only thing that we could do to prevent it from further inflating is to exclude all the files that we do not need. Since we're already compiling into a single JavaScript file, we no longer need anything inside the `node_modules` and the `src` directory.

You can read more about electron-packager on [the project's homepage](https://github.com/electron-userland/electron-packager). You can read up on the other available command line arguments in [the documentation](https://github.com/electron-userland/electron-packager/blob/master/usage.txt).

## Where to Go From Here

In this tutorial we've built a pretty simple Electron app. It works but we can still improve on it. Here are some suggestions for improvements that could be made:

- Paginate the search results.
- Add a function to automatically stop a playing track once the user searches.
- Remove the button and call search directly from the `handleTextChange` method.
- [Package the app to an asar archive](http://electron.atom.io/docs/v0.36.8/tutorial/application-packaging/) to avoid exposing your source code to everyone.
- If you're serious about distributing your app to the whole world. You can create an installer for all major platforms (Windows, OSX, and Linux). There's a project called [electron-builder](https://github.com/loopline-systems/electron-builder) which allows you to do so.

To find more inspiration, check out the [SoundNode app](http://www.soundnodeapp.com/)--an Open-Source project to support SoundCloud for desktop Mac, Windows, and Linux.

If you're looking into learning more about Electron and building desktop apps using web technologies in general, I recommend you to check out the following resources:

## Conclusion

In this tutorial we've learned how to create a sleek and stylish cross-platform application using Electron. What's better is that we've done this by leveraging our existing web dev skills. We've also seen how easy it is to package and distribute this app as an OS-specific bundle.

I'd love to hear about the applications you build with Electron in the comments below.
