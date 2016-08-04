# Flux and Meteor harmony

[Original URL](https://medium.com/@borellvi/flux-and-meteor-harmony-7180680257e8#.o9f27pa28)

> Meteor's integration with npm is a game changerA month ago I wrote a short article and I did a short

![](https://cdn-images-1.medium.com/max/2000/1*RvDwHF0g2rvMEN-PMXlIFg.jpeg)

> **Meteor's integration with npm is a game changer**

A month ago I wrote a short [article](https://medium.com/@borellvi/meteor-meets-npm-a5cc48d90abe#.p4fjeukyy) and I did a short [talk](https://www.youtube.com/watch?v=XTEe4kgVTwU) in Meteor London explaining how to use npm with Meteor and how good it will be for any Meteor developer or even any Javascript developer. Today I want to explain how you can build anything that you can imagine using this new feature and I will show you an example using Meteor and Flux.

[Flux](https://facebook.github.io/flux/) is an application architecture created by Facebook which encourages one-way data flow. This architecture is really good to keep your application sane with more predictable behaviours.

Flux is probably not the best architecture out there (will we ever have a perfect architecture? probably not) but it is good enough for this example. I have been working with it in production environments and it is better than anything I tried before.

[Meteor](https://www.meteor.com/) is Javascript full stack application platform that let you build your apps fast. And when I say fast, is very fast. You don't need to worry anymore about ES2015 and how to set it up. No need to worry about how to create Javascript and CSS bundles or reload your browser tab when your code change. Meteor does it for you. And now you can use npm modules as well, so you have thousands of libraries that you can use.

The app is a shared playlist where you can just copy the SoundCloud URL of a song (e.g. <https://soundcloud.com/dariusofficial/hot-hands>) and add it to the list. It will be updated across all clients thanks to the Meteor reactivity. To make this app I have used [SoundCloud Javascript SDK](https://developers.soundcloud.com/docs/api/sdks). Here is how it looks:

![](https://cdn-images-1.medium.com/max/800/1*IQSYGGYOsA4GmskvMvdP4g.png)

You can find it as a Github repo:<br>
<https://github.com/borellvi/meteor-flux>

You may find some bugs and there is a lot of stuff that can be polished. If you think that you can improve it, please, make a pull request or even a fork.

Meteor npm integration will come in the next Meteor release, but if you want to use it now you will have to run:

```
meteor update --release 1.3-modules-beta.4
```

Now you can install the dependencies that you specified in your _package.json_.

```
{
 “dependencies”: { 
 “flux”: “^2.1.1”, 
 “events”: “^1.1.0”
 }
}
```

And run:

```
npm install
```

For Flux, I mostly followed the patterns found in the [TodoMVC](https://github.com/facebook/flux/tree/master/examples/flux-todomvc/) example but using ES2015.

To understand how Flux works you have to take a look at this diagram and keep it in your mind:

![](https://cdn-images-1.medium.com/max/800/1*AuVQqYolW6O60GTHTSgEyQ.png)

<https://facebook.github.io/react/blog/2014/07/30/flux-actions-and-the-dispatcher.html> Lets explain how all this works with examples. First of all we have React components, for example, the play button:

```
import PlayerActions from '../actions/PlayerActions';

export default PlayButton = React.createClass({
 handleClick() {
 PlayerActions.play();
 },

 render() {
 return (
 <div className=”play-button” onClick={this.handleClick}>...
 </div>
 );
 }
});
```

This is a normal React component that on click event calls a function in PlayerActions:

```
import AppDispatcher from ‘../dispatcher/AppDispatcher’;

const PlayerActions = {
 play() {
 AppDispatcher.dispatch({
 actionType: ‘PLAY’
 });
 },

 pause() {
 AppDispatcher.dispatch({
 actionType: ‘PAUSE’
 });
 },

 ...
};
```

PlayerActions simply calls dispatch on the AppDispatcher.

AppDispatcher handles that call and triggers actions. The store is listening for these dispatched actions. You can listen to the actions that you are interested in:

```
...

function play() {
 SoundCloudService.play(currentTrack());
}
...

AppDispatcher.register((action) => {
 switch (action.actionType) {
 case ‘PLAY’:
 play();
 PlayerStore.emitChange();
 break;
 ...
});
```

Then, the one-way flow is finished, because React views listen stores for changes, e.g. the buttons component is listening for changes in isPlaying from the PlayerStore.

```
...

function getPlayerState() {
 return {
 isPlaying: PlayerStore.isPlaying()
 };
}

export default Buttons = React.createClass({
 getInitialState() {
 return getPlayerState();
 },

 componentDidMount() {
 PlayerStore.addChangeListener(this._onChange);
 },

 componentWillUnmount() {
 PlayerStore.removeChangeListener(this._onChange);
 },

 _onChange() {
 this.setState(getPlayerState());
 },

 render() {
 ...
```

If you take a look at the diagram again you will see some boxes named "Web API" and "Web API Utils". These two names are really generic but they can be understood as asynchronous services. In our case our service is SoundCloudService which calls the SDK and triggers some actions.

```
SC.stream(`/tracks/${track.id}`).then((player) => {
 SC.player = player;
 SC.player.play();
 SC.player.on(‘finish’, ()=> {
 PlayerActions.next();
 });
});
```

All of this makes tests easy to isolate and the state of the application more predictable.

How does Meteor fit in to all of this? We need Meteor to dispatch actions like services and React components do. To archive this we need to observe the changes in our collections and dispatch actions. Here is how we do it in this project:

```
Tracks.find().observe({
 added: () => {
 ServerActions.tracksChanged();
 }
});
```

Since Meteor can be called globally we don't need to trigger actions for adding songs. We can just call this from our React component.

```
Meteor.call(‘addTrack’, text);
```

But we could trigger actions as well.

Another interesting part is that since we use React as a Meteor package and not as a npm module we don't need to have the store to populate the component, we can use the build-in method:

```
getMeteorData() {
 return {
 tracks: Tracks.find({}).fetch()
 };
}
```

We use ES2015 modules syntax. This is optional but I consider it a good practice.

```
import { Dispatcher } from ‘flux’;

const AppDispatcher = new Dispatcher();

export default AppDispatcher;
```

This is just an example of how easy can be to create an app with Meteor and any front-end framework that you can find out there. I think that it is really developer friendly since it require almost no set up compared with any other modern project. Moreover, Meteor still getting better every day and it is a platform to keep in mind, even for more serious projects.
