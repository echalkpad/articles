# Creating an Audio Calling App with Ionic and PhoneRTC

[Original URL](http://www.sitepoint.com/creating-an-audio-calling-app-with-ionic-and-phonertc/)

> In this tutorial I'm going to create an audio calling app with Ionic. We will be implementing it using PhoneRTC, a Cordova plugin which allows you to use WebRTC on Android and iOS devices. In...

In this tutorial I'm going to create an audio calling app with [Ionic](http://ionicframework.com/). We will be implementing it using [PhoneRTC](http://phonertc.io/), a Cordova plugin which allows you to use WebRTC on Android and iOS devices. In this tutorial I'm only going to show deploying on the Android platform because I personally use Linux. Only the deploying the app will be different.

## Set Up

I'm going to assume that you have already used Cordova to develop hybrid mobile apps. If not, I suggest you read the [Cordova Platform Guide](http://cordova.apache.org/docs/en/5.0.0/guide_platforms_index.md.html#Platform%20Guides). It has all the information you need to get yourself setup on how to work with Cordova.

Once you're done setting up the SDK's for your platform. You can now install Ionic and Cordova. Do this by executing the following command in terminal:

```
npm install -g cordova ionic
```

The command above installs Cordova and Ionic globally so that these tools can be used from any terminal window.

We will be using [bower](http://bower.io/) for installing front-end dependencies so if you don't have it installed, run the following command:

```
npm install -g bower
```

Once that's complete, create a new Ionic project.

```
ionic start koler blank
```

Breaking the command above down, `ionic` is the command line tool, `start` is the command for starting new projects, `koler` is the name of the app and `blank` is one of the starter project templates provided by Ionic. The `blank` template keeps things simple.

The following are the front-end dependencies for this project:

Here's the command for installing the above.

```
cd koler
bower install random socket.io angular-socket-io
```

## Building the App

You can find the code for the complete app we will be building on [Github](https://github.com/sitepoint-editors/koler).

Open the _index.html_ file in the _www_ directory of the project and add the following right before the `script` tag for linking the _cordova.js_ file. This allows us to use the libraries just installed.

```
<script src="lib/sio-client/socket.io.js"></script>
<script src="lib/angular-socket-io/socket.js"></script>

<script src="lib/random/lib/random.min.js"></script>
```

While still inside the _www_ directory create a _templates_ folder. This is where we will store HTML templates to be used for views. The first of those views is _call.html_. Add the following code to that file.

```
<ion-header-bar class="bar-stable">
 <h1 class="title">Koler</h1>
</ion-header-bar>
<ion-content class="padding" ng-controller="CallController">

 <div class="card">
 <div class="item item-text-wrap">
 Your User ID: <strong>{{ id }}</strong>
 </div>
 </div>

 <div class="list">
 <label class="item item-input">
 <span class="input-label">Peer ID</span>
 <input type="text" ng-model="peer_id">
 </label>
 </div>

 <button class="button button-positive button-block" ng-click="startCall()">
 Call
 </button>
</ion-content>
```

Breaking this down. The header region is created by using the `ion-header-bar` directive. This angular directive is built in to Ionic and specifically used for adding a fixed header bar above the main content.

```
<ion-header-bar class="bar-stable">
 <h1 class="title">Koler</h1>
</ion-header-bar>
```

Next is the wrapper for the main content. The `ng-controller` attribute is used to specify which controller this view uses.

```
<ion-content class="padding" ng-controller="CallController">
</ion-content>
```

Inside the main content is a card for showing the random ID of the current user.

```
<div class="card">
 <div class="item item-text-wrap">
 Your User ID: <strong>{{ id }}</strong>
 </div>
</div>
```

Below is the form for calling a user with a specific ID. Take note of the value of the `ng-model` attribute of the text field. You can get the current value of this field from the controller by using its name. In this case it's `peer_id`. Below the text field, is the button for initiating the call. The `startCall` function is called when the button is clicked.

```
<div class="list">
 <label class="item item-input">
 <span class="input-label">Peer ID</span>
 <input type="text" ng-model="peer_id">
 </label>
</div>

<button class="button button-positive button-block" ng-click="startCall()">
 Call
</button>
```

Now we can start coding the `CallController`. Inside the _js_ directory, create a _controllers_ folder, this is where controllers will be stored. Create a _CallController.js_ file inside and add the following code:

```
(function(){
 angular.module('starter')
 .controller('CallController', ['$scope', '$state', '$timeout', '$ionicModal', 'SocketService', CallController]);

 function CallController($scope, $state, $timeout, $ionicModal, SocketService){
 ...
 }

})();
```

The code above creates a new controller for the starter module. We then inject the following into it:

- `$scope` – Access to the current scope.
- `$state` – Access to the current state.
- `$timeout` – Used for executing a function after a specified number of milliseconds.
- `$ionicModal` – Enables the use of the Ionic modal component inside the controller.
- `SocketService` – The service for using Socket.io.

Inside the controller, generate a random integer between 10,000 and 99,999 and assign it as an ID for the current user. Store it in the `$scope` variable so it can be accessed from within the view.

```
var r = new Random();

var id = r.integer(10000, 99999);
$scope.id = id;
```

Create an object for storing the current contact. This is the user whom the current user is trying to call.

```
$scope.contact = {};
```

Create variables for storing the current call status. By default, a call shouldn't be in progress, ignored or ended so those are all set to `false`.

```
$scope.callInProgress = false;
$scope.callIgnored = false;
$scope.callEnded = false;
```

Send a login message to the server along with the random ID generated earlier. This allows the server to assign a socket to the user ID so it can be used to later.

```
SocketService.emit('login', {'id': id});
```

Create a modal for calls. This will be shown to the user when a call is started, in progress, ended or ignored.

```
$ionicModal.fromTemplateUrl('templates/call-modal.html', {
 scope: $scope,
 animation: 'slide-in-up'
}).then(function(modal){
 $scope.call_modal = modal;
});
```

Create the function for calling. This function will be called when a user initiates or answers a call. It accepts 2 arguments, the `isInitiator` which is a boolean value that allows phoneRTC to determine the user who initiated the call. And the `peer_id` which is used for specifying the ID of the user that the current user is connecting to. I'll be referring to the current user or the user who is calling as the _initiator_, and the user who is the peer or accepting the calls as the _joiner_ for the rest of the tutorial.

```
function call(isInitiator, peer_id){

 var config = {
 isInitiator: isInitiator,
 stun: {
 host: 'stun:stun.l.google.com:19302'
 },
 turn: {
 host: 'turn:numb.viagenie.ca',
 username: 'webrtc@live.com',
 password: 'muazkh'
 },
 streams: {
 audio: true,
 video: false
 }
 };

 var session = new cordova.plugins.phonertc.Session(config);

 session.on('sendMessage', function(data){

 SocketService.emit('sendMessage', {
 'id': id,
 'peer_id': $scope.peer_id,
 'type': 'phonertc_handshake',
 'data': JSON.stringify(data)
 });
});

 session.on('disconnect', function(){
 SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, 'type': 'ignore' });
 $scope.call_modal.hide();
 });

 session.call();
 $scope.contact = session; 
}
```

Breaking down the above code. First is the phoneRTC _STUN_ and _TURN_ server configuration. This allows peer to peer connection to be established if any devices are behind a firewall or NAT. You can use the same STUN and TURN server configuration that I've used or pick from [this list of STUN servers](https://gist.github.com/zziuni/3741933). You can setup your own server, but I won't be covering that in this tutorial.

Lastly, is `streams`, this allows you to set whether you capture only the device audio, the camera, or both. This configuration is then passed as an argument to the phoneRTC session. This session will represent the connection between the 2 peers.

```
var config = {
 isInitiator: isInitiator,
 stun: {
 host: 'stun:stun.l.google.com:19302'
 },
 turn: {
 host: 'turn:numb.viagenie.ca',
 username: 'webrtc@live.com',
 password: 'muazkh'
 },
 streams: {
 audio: true,
 video: false
 }
};

var session = new cordova.plugins.phonertc.Session(config);
```

Create the event handler for when a message is sent on the current session. This event is fired when a user sends a message via Socket.io. When this event is fired, you have to respond with the `phonertc_handshake` message and pass in the same data that was passed on that message. The data is converted into a JSON string by using `JSON.stringify`. Other data such as the peer ID and the ID of the current user are passed so that the signaling server knows where the message came from and where is it going to.

Why do you need to pass the same data? That's how WebRTC works. Network information should be exchanged and verified between each peer so that the connection can be established. If you want to dive in deeper into this topic, I recommend reading the [Getting Started with WebRTC article on the HTML5Rocks website](http://www.html5rocks.com/en/tutorials/webrtc/basics/).

```
session.on('sendMessage', function(data){

 SocketService.emit('sendMessage', {
 'id': id,
 'peer_id': $scope.peer_id,
 'type': 'phonertc_handshake',
 'data': JSON.stringify(data)
 });
});
```

When the current session disconnects, send a message to the other peer that the call is ignored and then hide the call modal.

```
session.on('disconnect', function(){
 SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, 'type': 'ignore' });
 $scope.call_modal.hide();
});
```

Finally you make a call and set the current session to the contact variable in the current scope. This allows you to receive messages, disconnect or close the peer connection later.

```
session.call();
$scope.contact = session;
```

To initiate the call, start by setting the `isCalling` variable to `true`. This will control what is shown in the call modal. Next, inform the peer that you are trying to call them by sending a message containing the ID of the current user, the ID of the peer and the message type. Lastly, show the call modal. The function below is called when the 'call' button is clicked in the _call.html_ view.

Add this code to your controller file:

```
$scope.startCall = function(){

 $scope.isCalling = true;
 $scope.callIgnored = false;
 $scope.callEnded = false;

 SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, type: 'call'});

 $scope.call_modal.show(); 
}
```

The call modal contains the following. Add this code to a new _templates/call-modal.html_ file:

```
<ion-modal-view>
 <div class="bar bar-header item-input-inset bar-calm">
 <button class="button button-icon icon ion-ios-arrow-left" ng-click="closeModal()"></button>
 Call {{ peer_id }}
 </div>

 <ion-content class="padding has-header">

 <div class="calling-container" ng-if="isCalling && !callInProgress && !callIgnored && !callEnded">
 <p>Calling to <span class="balanced">{{ peer_id }}</span>...</p>

 <button class="button button-assertive" ng-click="ignore()">
 Nevermind
 </button>
 </div>

 <div class="calling-container" ng-if="!isCalling && !callInProgress && !callIgnored && !callEnded">
 <p><span class="balanced">{{ peer_id }}</span> is calling you</p>

 <button class="button button-positive" ng-click="answer()">
 Answer
 </button>

 <button class="button button-assertive" ng-click="ignore()">
 Ignore
 </button>
 </div>

 <div class="calling-container" ng-if="callInProgress && !callIgnored && !callEnded">
 <p>Call in progress...</p>

 <button class="button button-assertive" ng-click="end()">
 End
 </button>
 </div>

 <div ng-if="callIgnored && !callEnded && !callInProgress">
 <p>Call Ignored</p>
 <button class="button button-positive button-block" ng-click="closeModal()">Go back</button>
 </div>

 <div ng-if="callEnded && !callIgnored && !callInProgress">
 <p>Call Ended</p>
 <button class="button button-positive button-block" ng-click="closeModal()">Go back</button>
 </div>

 </ion-content>
</ion-modal-view>
```

Breaking the above code down. The header contains a button which allows the user to close the modal and a title which tells the current user the ID of the peer.

```
<div class="bar bar-header item-input-inset bar-calm">
 <button class="button button-icon icon ion-ios-arrow-left" ng-click="closeModal()"></button>
 Call {{ peer_id }}
</div>
```

This is the message that will show if the current user is the initiator. It also has a button for cancelling the call if the joiner doesn't answer.

```
<div class="calling-container" ng-if="isCalling && !callInProgress && !callIgnored && !callEnded">
 <p>Calling to <span class="balanced">{{ peer_id }}</span>...</p>

 <button class="button button-assertive" ng-click="ignore()">
 Nevermind
 </button>
</div>
```

When the joiner answers the call, the call is set to _in progress_ and it will then show the following. This allows either of the users to end the call.

```
<div class="calling-container" ng-if="callInProgress && !callIgnored && !callEnded">
 <p>Call in progress...</p>

 <button class="button button-assertive" ng-click="end()">
 End
 </button>
</div>
```

Once a call is ended by either of the 2 users, the call is mark as ended. The user can then choose to close the call modal.

```
<div ng-if="callIgnored && !callEnded && !callInProgress">
 <p>Call Ignored</p>
 <button class="button button-positive button-block" ng-click="closeModal()">Go back</button>
</div>

<div ng-if="callEnded && !callIgnored && !callInProgress">
 <p>Call Ended</p>
 <button class="button button-positive button-block" ng-click="closeModal()">Go back</button>
</div>
```

Returning to the _CallController.js_ file, here's the code for closing the modal.

```
$scope.closeModal = function(){
 $scope.call_modal.hide();
};
```

When a call is ignored by the initiator, the connection to the joiner is disconnected. If the joiner is the one who ignored the call then you have to send a message to the initiator to inform them that the call was ignored.

```
$scope.ignore = function(){

 if(JSON.stringify($scope.contact) === '{}'){
 $scope.contact.disconnect();
 }else{
 SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, 'type': 'ignore' });
 $scope.call_modal.hide();
 }

};
```

When the call is ended by either of the users, close the connection to the peer and send a message to the other user so that they will be informed the call has been ended. Update the scope variables so that the UI will also be updated.

```
$scope.end = function(){
 $scope.contact.close();
 $scope.contact = {};

 SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, 'type': 'end' });
 $scope.callInProgress = false;
 $scope.callEnded = true;
 $scope.call_modal.hide();
};
```

When the call is answered by the peer, set the call to _in progress_ and make the connection to the initiator by calling the `call` method. Since the user answering the call is always the joiner, you have to pass `false` for the `isInitiator` parameter. Next, send a message to the initiator that the joiner has answered the call. You have to give padding time of at least 1.5 seconds before sending the message to give time for the connection to be established between the two peers. Because once the initiator receives the answer message, the UI is immediately updated. We don't want the initiator to start talking if the connection hasn't been established yet.

```
$scope.answer = function(){

 if($scope.callInProgress){
 return;
 }

 $scope.callInProgress = true;

 call(false, $scope.peer_id);

 setTimeout(function(){
 SocketService.emit('sendMessage', { 'id': id, 'peer_id': $scope.peer_id, 'type': 'answer' });
 }, 1500);
};
```

Next, create the function that will handle things on the front-end when a message is received by either of the users. A switch statement is used to determine what type of message was received. Each case will then handle the type of message accordingly.

```
function onMessageReceive(message){

 switch(message.type){

 case 'answer':

 $scope.$apply(function(){
 $scope.callInProgress = true;
 });

 call(true, message.id);
 break;

 case 'ignore':
 $scope.callInProgress = false;
 $scope.callIgnored = true;
 $scope.callEnded = false;
 break;

 case 'phonertc_handshake':
 $scope.contact.receiveMessage(JSON.parse(message.data));
 break;

 case 'call':
 $scope.isCalling = false;
 $scope.callIgnored = false;
 $scope.callEnded = false;

 $scope.call_modal.show();

 $scope.peer_id = message.id;

 $scope.current_modal = 'call_modal';
 break;

 case 'end':
 $scope.callInProgress = false;
 $scope.callEnded = true;
 $scope.callIgnored = false;
 break;

 }
}
```

The `answer` message can only be received by the initiator. As seen earlier, this message is sent right after the `call` method is called, which tries to establish the connection between the joiner and the initiator. The first thing it does is set the call to _in progress_ so the UI is updated. Then the connection is established to the joiner by calling the `call` method. The `message.id` contains the ID of joiner.

```
case 'answer':

 $scope.$apply(function(){
 $scope.callInProgress = true;
 });

 call(true, message.id);
break;
```

On `ignore`, set the `callIgnored` variable in the scope to `true` so the UI is updated.

```
case 'ignore':

 $scope.callInProgress = false;
 $scope.callIgnored = true;
 $scope.callEnded = false;

break;
```

Next is `phonertc_handshake`. This message is sent every time the `call` method is called. Its sole purpose is for receiving the network information that is passed by the peer. This is important for establishing the connection between the peers.

```
case 'phonertc_handshake':

 $scope.contact.receiveMessage(JSON.parse(message.data));

break;
```

When a `call` message is received, reset the scope variables for controlling the UI to `false`, show the call modal and then set the peer ID as a scope variable. The peer ID is the ID of the joiner. So this specific message can only be received by the joiner.

```
case 'call':
 $scope.isCalling = false;
 $scope.callIgnored = false;
 $scope.callEnded = false;

 $scope.call_modal.show();
 $scope.current_modal = 'call_modal';

 $scope.peer_id = message.id;
break;
```

Once a call `end` message is received, update the UI by setting `callEnded` to `true`.

```
case 'end':
 $scope.callInProgress = false;
 $scope.callEnded = true;
 $scope.callIgnored = false;
break;
```

The `onMessageReceive` method is fired every time a message is received, add this code below the last function.

```
SocketService.on('messageReceived', onMessageReceive);
```

Finally, once the current scope is destroyed, the listener is removed. This happens when the user navigates to a new page or closes the app.

```
$scope.$on('$destroy', function(){
 SocketService.removeListener('messageReceived', onMessageReceive);
});
```

Create a _services_ folder in the _js_ directory, create a _SocketService.js_ file and add the following code.

```
(function(){

 angular.module('starter')
 .service('SocketService', ['socketFactory', SocketService]);

 function SocketService(socketFactory){
 return socketFactory({
 ioSocket: io.connect('http://yourserver.com:4000')
 });
 }
})();
```

The code above is used for connecting to the Socket.io server. This initializes Socket.io so it can be used as a service inside Angular. This uses the _angular-socket-io_ library which was installed earlier.

In the next part of this tutorial we will look at creating the server that manages calls between users. If you have any questions at this point, then please let me know.
