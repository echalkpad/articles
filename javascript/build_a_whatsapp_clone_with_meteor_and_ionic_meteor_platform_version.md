# Build a WhatsApp clone with Meteor and Ionic - Meteor Platform version

[Original URL](http://info.meteor.com/blog/whatsapp-with-meteor-angular-and-ionic)

> Now that Angular is a first class citizen in Meteor, you can use all of its vast libraries, giving you full access to the Angular ecosystem. Also, Ionic recently added official support for...

![](http://cdn2.hubspot.net/hub/520701/hubfs/blog_header_images_2.png?t=1445014944449&width=630&height=236)

Now that Angular is a 

[<span>first class citizen in Meteor</span>](http://info.meteor.com/blog/official-angular-support-with-angular-meteor-1.0.0)

, you can use all of its vast libraries, giving you full access to the Angular ecosystem. Also, 

[<span>Ionic recently added official support for Meteor’s packaging system</span>](https://github.com/driftyco/ionic/pull/3133)

, and now their package is available on 

[<span>atmosphere</span>](https://atmospherejs.com/driftyco/ionic)

.

In this tutorial, we will build a WhatsApp clone using Meteor, Angular, and the Ionic Framework for CSS and mobile components. I've also released a [clone of this tutorial on the Ionic Blog](http://blog.ionic.io/ionic-and-meteor) that uses the Ionic CLI instead of the Meteor build system.

It's a good resource for people who wants to use Meteor for their backend and Meteor's client side libraries in a separate front end application, also a good migration strategy.

If you are using Blaze, you can still use Ionic's CSS libraries or the 

[<span>Meteoric package</span>](https://atmospherejs.com/meteoric/ionic)

.

Contents:

1. ****Installing the platform and creating a base app****
2. **WhatsApp views with static data**
3. **Create the server and share data with the client**
4. **Chat view and send messages**
5. ****Users and (SMS) authentication****
6. ****Create and remove chats****
7. **Privacy and publish/subscribe**
8. **Step 8 - User profile picture**
9. **Send image messages**

**<span>Step 1 - Installing the platform and creating a base app</span>**

Start by 

[<span>installing the Meteor platform in this link</span>](https://www.meteor.com/install)

.

Create a new project by running this commands in your Terminal:

`$ meteor create whatsapp`

`$ cd whatsapp`

Your app now contains a live and ready example app. let's delete those files:

`$ rm whatsapp.*`

To run our app simple type `meteor` on the command line:

`$ meteor`

We can also run our app inside the iOS Simulator or Android Emulator - we just need to add the platform so Meteor will build the project for the new platform.

`$ meteor add-platform ios`

`$ meteor add-platform android`

And now to run our project in a mobile environment, we just need to tell Meteor which platform we want to run (running the mobile is an addition to the server and client run):

`$ meteor run ios`

`$ meteor run android`

You can find more information about Meteor CLI and build tool here:

[<span>https://www.meteor.com/tool</span>](https://www.meteor.com/tool)

Our next step is to add the Angular and Ionic packages to the project:

`$ meteor add angular`

`$ meteor add driftyco:ionic`

**If you're familiar with the Meteor folder structure and AngularJS module initialization, you can go ahead and skip to the end of this step and just download the ZIP file.**<br>
We will start by creating the project's folder structure, Meteor has a special behavior for certain folders:

- client - these files will be available only in the client side.
- server - these files will be available only in the server side.
- public - these files will be available in the client, uses for assets, images, fonts, etc.
- lib - any folder named lib (in any hierarchy) will be loaded first!
- any other folder name will be included in both client and server and uses for code-sharing.

So this will be our folder structure to the project:

client (client side with AngularJS and Ionic code)

- scripts
- templates
- styles
- index.html

server (server side code only) public (assets, images) lib (define methods and collections in order to make them available in both client and server)

So let's start by creating our first file - the `index.html` file - we will place in under the `client` folder:

**1.2** Create index.html [client/index.html »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/ffc46c31aceffe9a3afc4cbea3827f62949d4fb3)

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

<head>

 <meta charset="utf-8">

 <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no, width=device-width">

 <title>Whatsapp Meteor</title>

</head>

<body>

<!--

 The nav bar that will be updated as we navigate between views.

-->

<ion-nav-bar class="bar-stable">

 <ion-nav-back-button>

 </ion-nav-back-button>

</ion-nav-bar>

<!--

 The views will be rendered in the <ion-nav-view> directive below

 Templates are in the /templates folder (but you could also

 have templates inline in this html file if you'd like).

-->

<ion-nav-view></ion-nav-view>

</body>
```

We used some ionic tags to achieve mobile style:

- ion-nav-bar - Create a navigation bar in the page header.
- ion-nav-view - This is a placeholder to the real content - AngularJS and ionic will put your content inside this tag automatically.

Note that we only provide the `head` and `body` tags because Meteor takes care of the full contents of the HTML file, and any tag we will use here will be added to the Meteor's main index.html file.

This feature is really useful because we do not need to take care of including our files in `index.html` and keep it updated ourselves.

Our next step is to create the AngularJS module and bootstrap it according to our platform.<br>
We will create a new file called `app.ng.js`.

We will add the `.ng` extension to any file the contains AngularJS code in order to help [angular-meteor](http://angular-meteor.com/) recognize these files as relevant for AngularJS code.

This bootstrap file should be loaded first, because any other AngularJS code will depend on the module, so we need to put this file inside a folder called `lib`, so we will create a file in this path: `client/scripts/lib/app.ng.js`.

This file will contain an AngularJS module initialization with dependencies for `angular-meteor` and `ionic`.

We will also check for the current platform (browser or mobile) and initialize the module according to the result:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

angular

 .module('Whatsapp', [

 'angular-meteor',

 'ionic'

 ]);

if (Meteor.isCordova) {

 angular.element(document).on('deviceready', onReady);

}

else {

 angular.element(document).ready(onReady);

}

function onReady() {

 angular.bootstrap(document, ['Whatsapp']);

}
```

Our next step is to create the states and routes for the views.

Our app uses Ionic to create 5 tabs: Favorites, Recents, Contacts, Chats, and Settings.

We will define our routes and states with [angular-ui-router](https://atmospherejs.com/angularui/angular-ui-router) (which is included by ionic), and for the moment we will add the main page which is the `chats` tab:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

angular

 .module('Whatsapp')

 .config(config);

function config($stateProvider, $urlRouterProvider) {

 $stateProvider

 .state('tab', {

 url: '/tab',

 abstract: true,

 templateUrl: 'client/templates/tabs.ng.html'

 })

 .state('tab.chats', {

 url: '/chats',

 views: {

 'tab-chats': {

 templateUrl: 'client/templates/chats.ng.html'

 }

 }

 });

 $urlRouterProvider.otherwise('tab/chats');

}
```

And this is the HTML template for the footer that included with the tabs view:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

<ion-tabs class="tabs-stable tabs-icon-top tabs-color-positive" ng-cloak>

 <ion-tab title="Favorites" icon-on="ion-ios-star" icon-off="ion-ios-star-outline" href="#/tab/favorites">

 <ion-nav-view name="tab-favorites"></ion-nav-view>

 </ion-tab>

 <ion-tab title="Recents" icon-on="ion-ios-clock" icon-off="ion-ios-clock-outline" href="#/tab/recents">

 <ion-nav-view name="tab-recents"></ion-nav-view>

 </ion-tab>

 <ion-tab title="Contacts" icon-on="ion-ios-person" icon-off="ion-ios-person-outline" href="#/tab/contacts">

 <ion-nav-view name="tab-contacts"></ion-nav-view>

 </ion-tab>

 <ion-tab title="Chats" icon-on="ion-ios-chatbubble" icon-off="ion-ios-chatbubble-outline" href="#/tab/chats">

 <ion-nav-view name="tab-chats"></ion-nav-view>

 </ion-tab>

 <ion-tab title="Settings" icon-on="ion-ios-cog" icon-off="ion-ios-cog-outline" href="#/tab/settings">

 <ion-nav-view name="tab-settings"></ion-nav-view>

 </ion-tab>

</ion-tabs>
```

Create the stub for the main page - the chats file:

**1.6** Create basic view for the chats list [client/templates/chats.ng.html »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/dc1c67877a0a3a0730cf685f2aa6aeefb665ca32)

```
<ion-view view-title="Chats">

 <ion-content>

 </ion-content>

</ion-view>
```

And this is what it looks at the moment, inside a browser:

![](http://cdn2.hubspot.net/hub/520701/hubfs/1_-_empty_screen.png?t=1445014944449&width=630&height=311)

If you want to view your app in a better way, with mobile layout, you can add a mobile platform as we described in the beginning of the step. I've added the iOS platform, and when we can run it inside a mobile emulator, and it looks like this:

![](http://cdn2.hubspot.net/hubfs/520701/2_-_empty_screen_in_emulator.png?t=1445014944449)

**You can download the end result of this step as ZIP file from** 

[**<span>here</span>**](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/archive/38d72ea93e90b39929aae7f568bc4179f66079af.zip)

**.**

**<span>Step 2 - WhatsApp views with static data</span>**

Our next step includes creating basic views with some static data using ionic and SASS.

First, let's create an AngularJS controller that we will later connect to the chats view, we will call it `ChatsCtrl` and create a new file:

**1.8** Create ChatsCtrl [client/scripts/controllers/chats.controller.ng.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/ffd790cd42522f6fa41b5737df942194e3e725be)

```
angular

 .module('Whatsapp')

 .controller('ChatsCtrl', ChatsCtrl);

function ChatsCtrl ($scope) {

}
```

Now we want to add some static data to this controller, we will use `moment` package to easily create time object, so let's add it to the project using this command:

`$ meteor add momentjs:moment`

Now let's add the static data, we will create a stub schema for chats and messages:

```
3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

48

49

50

51

52

53

 .controller('ChatsCtrl', ChatsCtrl);

function ChatsCtrl ($scope) {

 $scope.chats = [

 {

 _id: 0,

 name: 'Ethan Gonzalez',

 picture: 'https://randomuser.me/api/portraits/thumb/men/1.jpg',

 lastMessage: {

 text: 'You on your way?',

 timestamp: moment().subtract(1, 'hours').toDate()

 }

 },

 {

 _id: 1,

 name: 'Bryan Wallace',

 picture: 'https://randomuser.me/api/portraits/thumb/lego/1.jpg',

 lastMessage: {

 text: 'Hey, it\'s me',

 timestamp: moment().subtract(2, 'hours').toDate()

 }

 },

 {

 _id: 2,

 name: 'Avery Stewart',

 picture: 'https://randomuser.me/api/portraits/thumb/women/1.jpg',

 lastMessage: {

 text: 'I should buy a boat',

 timestamp: moment().subtract(1, 'days').toDate()

 }

 },

 {

 _id: 3,

 name: 'Katie Peterson',

 picture: 'https://randomuser.me/api/portraits/thumb/women/2.jpg',

 lastMessage: {

 text: 'Look at my mukluks!',

 timestamp: moment().subtract(4, 'days').toDate()

 }

 },

 {

 _id: 4,

 name: 'Ray Edwards',

 picture: 'https://randomuser.me/api/portraits/thumb/men/2.jpg',

 lastMessage: {

 text: 'This is wicked good ice cream.',

 timestamp: moment().subtract(2, 'weeks').toDate()

 }

 }

 ];

}
```

Connect the chats view to the `ChatsCtrl`:

```
 url: '/chats',

 views: {

 'tab-chats': {

 templateUrl: 'client/templates/chats.ng.html'

 templateUrl: 'client/templates/chats.ng.html',

 controller: 'ChatsCtrl'

 }

 }

 });
```

Modify the chats list view to use the stub data.

We will use ionic's tags to create a container with a list view (`ion-list` and `ion-item`), and add `ng-repeat` to iterate over the chats:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

<ion-view view-title="Chats">

 <ion-content>

 <ion-list>

 <ion-item

 ng-repeat="chat in chats | orderBy:'-lastMessage.timestamp'"

 class="item-chat item-remove-animate item-avatar item-icon-right"

 type="item-text-wrap">

 <img ng-src="">

 <h2></h2>

 <p></p>

 <span class="last-message-timestamp"></span>

 <i class="icon ion-chevron-right icon-accessory"></i>

 </ion-item>

 </ion-list>

 </ion-content>

</ion-view>
```

And this is how is looks like:

![](http://cdn2.hubspot.net/hubfs/520701/3_-_fake_data_no_date_format.png?t=1445014944449)

You might notice that the dates are not formatted, so let's create a simple AngularJS filter that use `moment` package to convert the date into formatted text, we will place it in a file named `client/scripts/filters/calendar.filter.ng.js`:

**1.13** Create calendar filter [client/scripts/filters/calendar.filter.ng.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/1ec068d61884885f4df1e767af9603e58d984efa)

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

angular

 .module('Whatsapp')

 .filter('calendar', calendar);

function calendar () {

 return function (time) {

 if (! time) return;

 return moment(time).calendar(null, {

 lastDay : '[Yesterday]',

 sameDay : 'LT',

 lastWeek : 'dddd',

 sameElse : 'DD/MM/YY'

 });

 }

}
```

And let's use it in our view:

```
 <img ng-src="">

 <h2></h2>

 <p></p>

 <span class="last-message-timestamp"></span>

 <span class="last-message-timestamp"></span>

 <i class="icon ion-chevron-right icon-accessory"></i>

 </ion-item>

 </ion-list>
```

And this how it looks like now:

![](http://cdn2.hubspot.net/hubfs/520701/4_-_fake_data_with_date_format.png?t=1445014944449)

To add a delete button to our view, we will use `ion-option-button` which is a button that's visible when we swipe over the list item!

```
10

11

12

13

14

15

16

17

18

 <p></p>

 <span class="last-message-timestamp"></span>

 <i class="icon ion-chevron-right icon-accessory"></i>

 <ion-option-button class="button-assertive" ng-click="remove(chat)">

 Delete

 </ion-option-button>

 </ion-item>

 </ion-list>

 </ion-content>
```

Implement the `remove(chat)` method inside our ChatsCtrl:

```
50

51

52

53

54

55

56

57

58

59

60

61

 }

 }

 ];

 $scope.remove = remove;

 ////////////

 function remove (chat) {

 $scope.chats.splice($scope.chats.indexOf(chat), 1);

 }

}
```

And this is the result:

![](http://cdn2.hubspot.net/hubfs/520701/5_-_fake_data_with_delete_button.png?t=1445014944449)

Now we want to add some styles and make some small CSS modifications to make it look more like WhatsApp.

We want to use SASS in our project, so we need to add the sass package to our project:

`$ meteor add momentjs:moment`

And now we will create our first SASS file, we will place it under `client/styles/chats.scss`, and add some CSS rules:

```
.item-chat {

 .last-message-timestamp {

 position: absolute;

 top: 16px;

 right: 38px;

 font-size: 14px;

 color: #9A9898;

 }

}
```

And we are done with this view! It look just like WhatsApp!

![](http://cdn2.hubspot.net/hubfs/520701/6_-_fake_data_final_design.png?t=1445014944449)

You can download a ZIP file with the project at this point 

[<span>here</span>](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/archive/5091e7e89eb23f9a98badb30011b234b240ecfaa.zip)

.

**<span>Step 3 - Create the server and share data with the client</span>**

In this step we are going to add several features to our project:

- Create and server and move the static data to the server.
- Connect the client to the server.

So let's start by creating the Meteor collection that will later store all of our data.

Meteor collection need to be available in both client and server in order to share data - so we will create the collections definition in a folder named `lib` under the project's root (`lib/collections.js`).

```
Chats = new Mongo.Collection('chats');

Messages = new Mongo.Collection('messages');
```

Now we need to create our server's first file, so let's create a directory named `server` and create the server startup file named `bootstrap.js` (`server/bootstrap.js`).

This file should be run first because we want to run some initialization code there, so we can use `Meteor.startup` to define our logic:

**2.2** Create the server's bootstrap file [server/bootstrap.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/fc43d71f886848617fd3a817df99f28e3c69100f)

```
Meteor.startup(function () {

});
```

Our next step is to move the static data to the server, so let's add it in the `bootstrap.js` file we just created, we also want this code to run only once - when there is no data at all inside the collections.

**2.3** Add the stub data to the server [server/bootstrap.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/f6ab03fb363c07a7d168cd029897b96e3c6dc26a)

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

48

49

50

51

52

53

54

55

56

57

58

59

60

61

62

Meteor.startup(function () {

 if (Chats.find().count() === 0) {

 Messages.remove({});

 var messages = [

 {

 text: 'You on your way?',

 timestamp: moment().subtract(1, 'hours').toDate()

 },

 {

 text: 'Hey, it\'s me',

 timestamp: moment().subtract(2, 'hours').toDate()

 },

 {

 text: 'I should buy a boat',

 timestamp: moment().subtract(1, 'days').toDate()

 },

 {

 text: 'Look at my mukluks!',

 timestamp: moment().subtract(4, 'days').toDate()

 },

 {

 text: 'This is wicked good ice cream.',

 timestamp: moment().subtract(2, 'weeks').toDate()

 }

 ];

 messages.forEach(m => {

 Messages.insert(m);

 });

 var chats = [

 {

 name: 'Ethan Gonzalez',

 picture: 'https://randomuser.me/api/portraits/thumb/men/1.jpg'

 },

 {

 name: 'Bryan Wallace',

 picture: 'https://randomuser.me/api/portraits/thumb/lego/1.jpg'

 },

 {

 name: 'Avery Stewart',

 picture: 'https://randomuser.me/api/portraits/thumb/women/1.jpg'

 },

 {

 name: 'Katie Peterson',

 picture: 'https://randomuser.me/api/portraits/thumb/women/2.jpg'

 },

 {

 name: 'Ray Edwards',

 picture: 'https://randomuser.me/api/portraits/thumb/men/2.jpg'

 }

 ];

 chats.forEach(chat => {

 let message = Messages.findOne({chatId: {$exists: false}});

 chat.lastMessage = message;

 let chatId = Chats.insert(chat);

 Messages.update(message._id, {$set: {chatId: chatId}})

 });

 }

});
```

Now we need to remove the static data from the client and get it from the server.

So let's use angular-meteor's API for this - we just need to wrap our collection object with [$meteorCollection](http://angular-meteor.com/api/meteorCollection) and we the data is synced:

```
 .controller('ChatsCtrl', ChatsCtrl);

function ChatsCtrl ($scope) {

 $scope.chats = [

 {

 _id: 0,

 name: 'Ethan Gonzalez',

 picture: 'https://randomuser.me/api/portraits/thumb/men/1.jpg',

 lastMessage: {

 text: 'You on your way?',

 timestamp: moment().subtract(1, 'hours').toDate()

 }

 },

 {

 _id: 1,

 name: 'Bryan Wallace',

 picture: 'https://randomuser.me/api/portraits/thumb/lego/1.jpg',

 lastMessage: {

 text: 'Hey, it\'s me',

 timestamp: moment().subtract(2, 'hours').toDate()

 }

 },

 {

 _id: 2,

 name: 'Avery Stewart',

 picture: 'https://randomuser.me/api/portraits/thumb/women/1.jpg',

 lastMessage: {

 text: 'I should buy a boat',

 timestamp: moment().subtract(1, 'days').toDate()

 }

 },

 {

 _id: 3,

 name: 'Katie Peterson',

 picture: 'https://randomuser.me/api/portraits/thumb/women/2.jpg',

 lastMessage: {

 text: 'Look at my mukluks!',

 timestamp: moment().subtract(4, 'days').toDate()

 }

 },

 {

 _id: 4,

 name: 'Ray Edwards',

 picture: 'https://randomuser.me/api/portraits/thumb/men/2.jpg',

 lastMessage: {

 text: 'This is wicked good ice cream.',

 timestamp: moment().subtract(2, 'weeks').toDate()

 }

 }

 ];

 $scope.chats = $scope.$meteorCollection(Chats, false);

 $scope.remove = remove;

 ////////////
```

Now that the data comes from the server, we need to modify the `remove` method in order to use $meteorCollection API that removes the object from both client and server:

```
 ////////////

 function remove (chat) {

 $scope.chats.splice($scope.chats.indexOf(chat), 1);

 $scope.chats.remove(chat);

 }

}
```

You can download a ZIP file with the project at this point 

[<span>here</span>](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/archive/ebd3254cab8f46c435540f5876b8322cac1fce7c.zip)

.

**<span>Step 4 - Chat view and send messages</span>**

In this step we will add the chat view and the ability to send messages.

We still won't have an identity for each user - we will add it later, but we can still send messages to existing chats.

So just like any other page, first we need to add a route and a state.

Let's call it `chat-details` and we will load a template and a controller which we will add later.

```
17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

 controller: 'ChatsCtrl'

 }

 }

 })

 .state('tab.chat-detail', {

 url: '/chats/:chatId',

 views: {

 'tab-chats': {

 templateUrl: 'client/templates/chat-detail.ng.html',

 controller: 'ChatDetailCtrl'

 }

 }

 });

 $urlRouterProvider.otherwise('tab/chats');
```

Let's add a very basic view with the chat's details - the file will located in `client/templates/chat-detail.ng.html`:

```
<ion-view title="">

 <ion-nav-buttons side="right">

 <button class="button button-clear"><img class="header-picture" ng-src=""></button>

 </ion-nav-buttons>

</ion-view>
```

Now we need to implement the logic in the controller, so let's create it in `client/scripts/controllers/chat-detail.controller.ng.js` and call it ChatDetailCtrl.

We will use the $stateParams to get the chat id and then we will use angular-meteor's [$meteorObject](http://angular-meteor.com/api/meteorObject) to create a reactive object:

```
angular

 .module('Whatsapp')

 .controller('ChatDetailCtrl', ChatDetailCtrl);

function ChatDetailCtrl ($scope, $stateParams) {

 var chatId = $stateParams.chatId;

 $scope.chat = $scope.$meteorObject(Chats, chatId, false);

}
```

We use the Chats collection as the first param, the chat id as the second params, and we will use `false` as the third param so that we will have to explicitly update the object and not autobind the client changes to the server.<br>
So now we have the chat details page, all we need to do is to add a link from the chats list to this view:

```
 <ion-item

 ng-repeat="chat in chats | orderBy:'-lastMessage.timestamp'"

 class="item-chat item-remove-animate item-avatar item-icon-right"

 type="item-text-wrap">

 type="item-text-wrap"

 href="#/tab/chats/">

 <img ng-src="">

 <h2></h2>

 <p></p>
```

So this is what we have at the moment, if we click on a chat in the list:

![](http://cdn2.hubspot.net/hubfs/520701/7_-_Chat_details_with_bad_picture.png?t=1445014944449)

Now let's add some CSS rules and let's add the messages view!

Let's create a new SASS file for our new view at `client/styles/chat-detail.scss`, and first we will take care of the chat image that look weird:

```
.header-picture {

 max-width: 33px;

 max-height: 33px;

 width: 100%;

 height: 100%;

 border-radius: 50%;

}
```

Our next step is about getting the chat messages on the controller, we will use $meteorCollection again, but instead of using the collection object - we will fetch only the relevant messages for the current chat:

```
function ChatDetailCtrl ($scope, $stateParams) {

 var chatId = $stateParams.chatId;

 $scope.chat = $scope.$meteorObject(Chats, chatId, false);

 $scope.messages = $scope.$meteorCollection(function () {

 return Messages.find({ chatId: chatId });

 }, false);

}
```

And now to add it to the view, we use `ng-repeat` to iterate the messages:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

<ion-view title="">

<ion-view title="">

 <ion-nav-buttons side="right">

 <button class="button button-clear"><img class="header-picture" ng-src=""></button>

 <button class="button button-clear"><img class="header-picture" ng-src=""></button>

 </ion-nav-buttons>

 <ion-content class="chat" delegate-handle="chatScroll">

 <div class="message-list">

 <div ng-repeat="message in messages" class="message-wrapper">

 <div class="message" ng-class-even="'message-mine'" ng-class-odd="'message-other'">

 <div class="message-text"></div>

 <span class="message-timestamp"></span>

 </div>

 </div>

 </div>

 </ion-content>

</ion-view>
```

At the moment we do not have identity for each user or message, so we will just use odd/even and this would be the indication for which message is mine and which isn't - in the next step we will add the authentication and each message will be related to a user.<br>
Now we will add some CSS to the messages list:

```
4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

48

49

50

51

52

53

54

55

56

57

58

59

60

61

62

63

64

65

66

67

68

69

70

71

72

73

74

75

76

77

78

79

80

81

82

83

84

85

86

 width: 100%;

 height: 100%;

 border-radius: 50%;

}

.chat {

 background-image: url(/chat-background.jpg);

 background-color: #E0DAD6;

 background-repeat: no-repeat;

 background-size: 100%;

}

.message-list {

 margin-top: 12px;

 padding: 0 5%;

}

.message-wrapper {

 margin-bottom: 9px;

 &::after {

 content: "";

 display: table;

 clear: both;

 }

}

.message {

 display: inline-block;

 position: relative;

 max-width: 236px;

 border-radius: 7px;

 box-shadow: 0 1px 2px rgba(0, 0, 0, .15);

 &.message-mine {

 float: right;

 background-color: #DCF8C6;

 }

 &.message-other {

 float: left;

 background-color: #FFF;

 }

 &.message-other::before, &.message-mine::before, {

 content: "";

 position: absolute;

 bottom: 3px;

 width: 12px;

 height: 19px;

 background-position: 50% 50%;

 background-repeat: no-repeat;

 background-size: contain;

 }

 &.message-other::before {

 left: -11px;

 background-image: url(/message-other.png)

 }

 &.message-mine::before {

 right: -11px;

 background-image: url(/message-mine.png)

 }

 .message-text {

 padding: 5px 7px;

 word-wrap: break-word;

 &::after {

 content: " \00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0";

 display: inline;

 }

 }

 .message-timestamp {

 position: absolute;

 bottom: 2px;

 right: 7px;

 color: gray;

 font-size: 12px;

 }

}
```

We also add the images from the original WhatsApp.

Note that the images are under `public/` folder so we can use them in the client side from the root directory (in the CSS file).

You can copy them form [here](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/tree/master/public).

And this is the result:

![](http://cdn2.hubspot.net/hubfs/520701/8_-_Chat_details_without_date_format.png?t=1445014944449)

Now we just need to take care of the message timestamp and format it.

We will use `moment` like before, but now let's add another package called [angular-moment](https://github.com/urish/angular-moment) that provides us the UI filters.

So adding the package is just like any other package we added so far:

`$ meteor add jasonaibrahim:angular-moment`

And because it's an AngularJS extension, we need to add a dependency in our module definition:

**3.11** Add angularMoment dependency [client/scripts/lib/app.ng.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/13aae085b4476eea7d10ca4e3551ea67a90eeeb4)

```
angular

 .module('Whatsapp', [

 'angular-meteor',

 'ionic'

 'ionic',

 'angularMoment'

 ]);

if (Meteor.isCordova) {
```

And now we will use a filter from this package in our view:

```
 <div ng-repeat="message in messages" class="message-wrapper">

 <div class="message" ng-class-even="'message-mine'" ng-class-odd="'message-other'">

 <div class="message-text"></div>

 <span class="message-timestamp"></span>

 <span class="message-timestamp"></span>

 </div>

 </div>

 </div>
```

And the result is:

![](http://cdn2.hubspot.net/hubfs/520701/9_-_Chat_details_with_date_format.png?t=1445014944449)

Just like WhatsApp...

Our next step is about adding the input for adding a new message to the chat, we need to add an input in the bottom of the view - `ion-footer-bar` is a perfect solution for that.

So we will add an input, Send button and some icons for sending images and sound recordings (it's just icons at the moment!)

```
12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

 </div>

 </div>

 </ion-content>

 <ion-footer-bar keyboard-attach class="bar-stable footer-chat item-input-inset">

 <button class="button button-clear button-icon button-positive icon ion-ios-upload-outline"></button>

 <label class="item-input-wrapper">

 <input

 ng-model="data.message"

 dir="auto"

 type="text"/>

 </label>

 <span ng-if="data.message.length > 0">

 <button ng-click="sendMessage()" class="button button-clear button-positive">Send</button>

 </span>

 <span ng-if="!data.message || data.message.length === 0">

 <button class="button button-clear button-icon button-positive icon ion-ios-camera-outline"></button>

 <i class="buttons-seperator icon ion-android-more-vertical"></i>

 <button class="button button-clear button-icon button-positive icon ion-ios-mic-outline"></button>

 </span>

 </ion-footer-bar>

</ion-view>
```

Let's add the `data` object to our controller, and add a stub method for `sendMessage`, we will implement it later.

```
9

10

11

12

13

14

15

16

17

18

19

20

21

 $scope.messages = $scope.$meteorCollection(function () {

 return Messages.find({ chatId: chatId });

 }, false);

 $scope.data = {};

 $scope.sendMessage = sendMessage;

 ///

 function sendMessage () {

 // TODO: Implement this logic

 }

}
```

And this is what we got so far:

![](http://cdn2.hubspot.net/hubfs/520701/10_-_Chat_details_with_camera_icons.png?t=1445014944449)

To improve the user experience in our app, we want some extra events to our input because we want to move it up when the keyboard comes from the bottom of the screen and we want to know if `return` (or Enter) was clicked.

We will implement a new directive that extends the regular `input` tag and add those events to the directive:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

48

49

50

51

52

53

54

angular

 .module('Whatsapp')

 .directive('input', input);

// The directive enable sending message when tapping return

// and expose the focus and blur events to adjust the view

// when the keyboard opens and closes

function input ($timeout) {

 var directive = {

 restrict: 'E',

 scope: {

 'returnClose': '=',

 'onReturn': '&',

 'onFocus': '&',

 'onBlur': '&'

 },

 link: link

 };

 return directive;

 ////////////

 function link (scope, element, attrs) {

 element.bind('focus', function (e) {

 if (scope.onFocus) {

 $timeout(function () {

 scope.onFocus();

 });

 }

 });

 element.bind('blur', function (e) {

 if (scope.onBlur) {

 $timeout(function () {

 scope.onBlur();

 });

 }

 });

 element.bind('keydown', function (e) {

 if (e.which == 13) {

 if (scope.returnClose) {

 element[0].blur();

 }

 if (scope.onReturn) {

 $timeout(function () {

 scope.onReturn();

 });

 }

 }

 });

 }

}
```

And now we can use those useful events in our view:

```
18

19

20

21

22

23

24

25

26

 <label class="item-input-wrapper">

 <input

 ng-model="data.message"

 on-return="sendMessage(); closeKeyboard()"

 on-focus="inputUp()"

 on-blur="inputDown()"

 dir="auto"

 type="text"/>

 </label>
```

And implement the $scope method that handles those events:

```
2

3

4

5

6

7

8

9

10

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

 .module('Whatsapp')

 .controller('ChatDetailCtrl', ChatDetailCtrl);

function ChatDetailCtrl ($scope, $stateParams) {

function ChatDetailCtrl ($scope, $stateParams, $ionicScrollDelegate, $timeout) {

 var chatId = $stateParams.chatId;

 var isIOS = ionic.Platform.isWebView() && ionic.Platform.isIOS();

 $scope.chat = $scope.$meteorObject(Chats, chatId, false);

 $scope.messages = $scope.$meteorCollection(function () {

...some lines skipped...

 $scope.data = {};

 $scope.sendMessage = sendMessage;

 $scope.inputUp = inputUp;

 $scope.inputDown = inputDown;

 $scope.closeKeyboard = closeKeyboard;

 ///

 function sendMessage () {

 // TODO: Implement this logic

 }

 function inputUp () {

 if (isIOS) {

 $scope.data.keyboardHeight = 216;

 }

 $timeout(function() {

 $ionicScrollDelegate.$getByHandle('chatScroll').scrollBottom(true);

 }, 300);

 }

 function inputDown () {

 if (isIOS) {

 $scope.data.keyboardHeight = 0;

 }

 $ionicScrollDelegate.$getByHandle('chatScroll').resize();

 }

 function closeKeyboard () {

 // cordova.plugins.Keyboard.close();

 }

}
```

We will also add some CSS to this view:

```
83

84

85

86

87

88

89

90

91

92

93

94

95

96

97

98

99

100

101

102

 color: gray;

 font-size: 12px;

 }

}

.footer-chat {

 .item-input-wrapper {

 background-color: #FFF;

 }

 .button.button-icon {

 margin: 0 10px;

 }

 .buttons-seperator {

 color: gray;

 font-size: 18px;

 line-height: 32px;

 }

}
```

So now when the user focuses on the input, it goes up, like that:

![](http://cdn2.hubspot.net/hubfs/520701/11_-_Chat_details_with_keyboard_open.png?t=1445014944449)

So now it's time to implement the `sendMessage` method in our controller.

We will use [$meteor.call](http://angular-meteor.com/api/methods) method to call the Meteor method:

```
2

3

4

5

6

7

8

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

 .module('Whatsapp')

 .controller('ChatDetailCtrl', ChatDetailCtrl);

function ChatDetailCtrl ($scope, $stateParams, $ionicScrollDelegate, $timeout) {

function ChatDetailCtrl ($scope, $stateParams, $ionicScrollDelegate, $timeout, $meteor) {

 var chatId = $stateParams.chatId;

 var isIOS = ionic.Platform.isWebView() && ionic.Platform.isIOS();

 $scope.chat = $scope.$meteorObject(Chats, chatId, false);

...some lines skipped...

 ///

 function sendMessage () {

 // TODO: Implement this logic

 if (_.isEmpty($scope.data.message)) {

 return;

 }

 $meteor.call('newMessage', {

 text: $scope.data.message,

 chatId: chatId

 });

 delete $scope.data.message;

 }

 function inputUp () {
```

Now let's create our Method in `lib/methods.js`:

**3.20** Add sendMessage method to the server [lib/methods.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/33e9c397a04dae4ea043883afc0660762fbbe1f1)

```
Meteor.methods({

 newMessage: function (message) {

 message.timestamp = new Date();

 var messageId = Messages.insert(message);

 Chats.update(message.chatId, { $set: { lastMessage: message } });

 return messageId;

 }

});
```

Let's add validation to our method.

Meteor provides us a useful package named `check` that validates data types and scheme.

Add it by running:

`$ meteor add check`

And now let's use it in the `newMessage` method:

**3.22** Add usage of check package to the newMessage method [lib/methods.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/d03d1c909c660903dd52a1cac1d957bf0cf269f1)

```
Meteor.methods({

 newMessage: function (message) {

 check(message, {

 text: String,

 chatId: String

 });

 message.timestamp = new Date();

 var messageId = Messages.insert(message);
```

And now we just need to send some messages in our chat!

![](http://cdn2.hubspot.net/hubfs/520701/12_-_Chat_details_with_reply.png?t=1445014944449)

You can download a ZIP file with the project at this point 

[<span>here</span>](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/archive/d03d1c909c660903dd52a1cac1d957bf0cf269f1.zip)

.

**<span>Step 5 - **</span>**

<span>Users and (SMS) authentication</span>

******

On this step we will authenticate and identify users in our app.

We will use Meteor's authentication packages, the basic package called Accounts and it has many extensions for Google, Facebook, Phone and many more.

We will use Accounts-phone package that verifies the user using a phone number with SMS messages.

To add this package, run this command:

`$ meteor add okland:accounts-phone`

We just need to add some configuration to the server side in order to make it work, so let's create `server/sms.js` and this is his content:

**4.2** Add SMS configuration for Accounts-Phone package [server/sms.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/357eb04681601ed55b400ff8bb0d82c55b9518c0)

```
if (Meteor.settings && Meteor.settings.ACCOUNTS_PHONE) {

 Accounts._options.adminPhoneNumbers = Meteor.settings.ACCOUNTS_PHONE.ADMIN_NUMBERS;

 Accounts._options.phoneVerificationMasterCode = Meteor.settings.ACCOUNTS_PHONE.MASTER_CODE;

}
```

Let's add configuration is for debug purposes:

`"TWILIO": {`

`"FROM": "meteor-whatsapp",`

`"SID: "",`

`"TOKEN": ""`

`"ACCOUNTS_PHONE": {`

`"ADMIN_NUMBERS": ["123456789", "987654321"],`

`"MASTER_CODE": "1234",`

So these flow is created by 3 new views: login, confirmation and profile.<br>
This means we can use the numbers `123456789` and `987654321` and they won't send real SMS message, and then in the confirmation modal, we can always use `1234` and it will work.<br>
Now let's create the same flow of WhatsApp for authentication: first we need to ask for the user's phone number, verify it with SMS message and then ask the user to pick his name.

Let's add these states, each with HTML template and controller:

```
26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

 controller: 'ChatDetailCtrl'

 }

 }

 })

 .state('login', {

 url: '/login',

 templateUrl: 'client/templates/login.ng.html',

 controller: 'LoginCtrl'

 })

 .state('confirmation', {

 url: '/confirmation/:phone',

 templateUrl: 'client/templates/confirmation.ng.html',

 controller: 'ConfirmationCtrl'

 })

 .state('profile', {

 url: '/profile',

 templateUrl: 'client/templates/profile.ng.html',

 controller: 'ProfileCtrl'

 });

 $urlRouterProvider.otherwise('tab/chats');
```

We will now add the view of login state - it includes an input and a save button and later we will add a modal dialog to verify the user's phone:

**4.4** Create the login view [client/templates/login.ng.html »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/6079825d4b77e421966dac9124fd01a17d2073f5)

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

<ion-view title="Your phone number">

 <ion-nav-buttons side="right">

 <button ng-click="login()" ng-disabled="!data.phone || data.phone.length === 0" class="button button-clear button-positive">Done</button>

 </ion-nav-buttons>

 <ion-content class="login">

 <div class="text-center instructions">

 Please confirm your country code and enter your phone number

 </div>

 <div class="list">

 <label class="item item-input">

 <input ng-model="data.phone" on-return="login()" type="text" placeholder="Your phone number">

 </label>

 </div>

 </ion-content>

</ion-view>
```

And the controller - the logic is simple - we ask the user to check again his phone number, and then we will use Accounts API in order to ask for SMS verification:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

48

49

50

51

52

53

54

55

56

angular

 .module('Whatsapp')

 .controller('LoginCtrl', LoginCtrl);

function LoginCtrl($scope, $state, $ionicLoading, $ionicPopup, $log) {

 $scope.data = {};

 $scope.login = login;

 ////////////

 function login() {

 if (_.isEmpty($scope.data.phone)) {

 return;

 }

 var confirmPopup = $ionicPopup.confirm({

 title: 'Number confirmation',

 template: '<div>' + $scope.data.phone + '</div><div>Is your phone number above correct?</div>',

 cssClass: 'text-center',

 okText: 'Yes',

 okType: 'button-positive button-clear',

 cancelText: 'edit',

 cancelType: 'button-dark button-clear'

 });

 confirmPopup.then(function (res) {

 if (!res) {

 return;

 }

 $ionicLoading.show({

 template: 'Sending verification code...'

 });

 Accounts.requestPhoneVerification($scope.data.phone, function (err) {

 $ionicLoading.hide();

 if (err) {

 return handleError(err);

 }

 $state.go('confirmation', {phone: $scope.data.phone});

 });

 });

 }

 function handleError(err) {

 $log.error('Login error ', err);

 $ionicPopup.alert({

 title: err.reason || 'Login failed',

 template: 'Please try again',

 okType: 'button-positive button-clear'

 });

 }

}
```

Note the we did not provide all the settings for Account-Phone - so it will run in debug mode - so real SMS won't be sent now - but to check your app you can see the confirmation in the Meteor's app log.<br>
Our next step is limit the current views to logged in users only - we will use angular-meteor's API for that - we will limit the `tab` and `profile` states using `$meteor.requiredUser()` which return a promise that resolves only if the user is logged in:

```
7

8

9

10

11

12

13

14

15

16

17

18

45

46

47

48

49

50

51

52

53

54

55

56

 .state('tab', {

 url: '/tab',

 abstract: true,

 templateUrl: 'client/templates/tabs.ng.html'

 templateUrl: 'client/templates/tabs.ng.html',

 resolve: {

 user: ['$meteor', function ($meteor) {

 return $meteor.requireUser();

 }]

 }

 })

 .state('tab.chats', {

 url: '/chats',

...some lines skipped...

 .state('profile', {

 url: '/profile',

 templateUrl: 'client/templates/profile.ng.html',

 controller: 'ProfileCtrl'

 controller: 'ProfileCtrl',

 resolve: {

 user: ['$meteor', function ($meteor) {

 return $meteor.requireUser();

 }]

 }

 });

 $urlRouterProvider.otherwise('tab/chats');
```

And now we want to handle a case that this promise does not resolves (in case that the user is not logged in), so let's create new file - `client/scripts/auth.js` that uses Angular's config phase:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

angular

 .module('Whatsapp')

 .run(run);

function run($rootScope, $state) {

 $rootScope.$on('$stateChangeError', function (event, toState, toParams, fromState, fromParams, error) {

 // We can catch the error thrown when the $requireUser promise is rejected

 // and redirect the user back to the main page

 if (error === 'AUTH_REQUIRED') {

 $state.go('login');

 }

 });

}
```

And now let's add some CSS:

**4.8** Add some CSS for better login view [client/styles/login.scss »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/6c6e84ee1d02cd09121582469e1bbcc1a390653d)

```
.login {

 .instructions {

 margin: 50px 0;

 padding: 0 15px;

 }

}
```

And this is how it looks like:

![](http://cdn2.hubspot.net/hubfs/520701/13_-_Enter_phone_screen.png?t=1445014944449)

The next step is to add the confirmation view, starting with the HTML:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

<ion-view title="">

 <ion-nav-buttons side="right">

 <button ng-click="verify()" ng-disabled="!data.code || data.code.length === 0" class="button button-clear button-positive">Done</button>

 </ion-nav-buttons>

 <ion-content>

 <div class="text-center padding">

 We have sent you an SMS with a code to the number above

 </div>

 <div class="text-center padding">

 To complete your phone number verification WhatsApp, please enter the 4-digit activation code.

 </div>

 <div class="list padding-top">

 <label class="item item-input">

 <input ng-model="data.code" on-return="verify()" type="text" placeholder="Code">

 </label>

 </div>

 </ion-content>

</ion-view>
```

And the controller:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

angular

 .module('Whatsapp')

 .controller('ConfirmationCtrl', ConfirmationCtrl);

function ConfirmationCtrl($scope, $state, $ionicPopup, $log) {

 $scope.phone = $state.params.phone;

 $scope.data = {};

 $scope.verify = verify;

 ////////////

 function verify() {

 if (_.isEmpty($scope.data.code)) {

 return;

 }

 Accounts.verifyPhone($scope.phone, $scope.data.code, function (err) {

 if (err) {

 return handleError(err);

 }

 $state.go('profile');

 });

 }

 function handleError(err) {

 $log.error('Verfication error ', err);

 $ionicPopup.alert({

 title: err.reason || 'Verfication failed',

 template: 'Please try again',

 okType: 'button-positive button-clear'

 });

 }

}
```

We will use Accounts API again to verify the user and in case of successful authentication we will transition to the `profile` state, which we add in the next step.

This is the `profile` view, which provides the ability to enter the user's nickname and profile picture (which we will add in the next step).

**4.11** Add the profile settings view [client/templates/profile.ng.html »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/e9e44be8b70c8b0db48d1c554a0b8c2c07558cf9)

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

<ion-view title="Profile">

 <ion-nav-buttons side="right">

 <button ng-click="updateName()" ng-disabled="!data.name || data.name.length === 0" class="button button-clear button-positive">Done</button>

 </ion-nav-buttons>

 <ion-content class="profile">

 <a class="profile-picture positive">

 <div class="upload-placehoder">

 Add photo

 </div>

 </a>

 <div class="instructions">

 Enter your name and add an optional profile picture

 </div>

 <div class="list profile-name">

 <label class="item item-input">

 <input ng-model="data.name" on-return="updateName()" type="text" placeholder="Your name">

 </label>

 </div>

 </ion-content>

</ion-view>
```

And the controller:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

angular

 .module('Whatsapp')

 .controller('ProfileCtrl', ProfileCtrl);

function ProfileCtrl ($scope, $state, $meteor, $ionicPopup, $log) {

 var user = Meteor.user();

 var name = user && user.profile ? user.profile.name : '';

 $scope.data = {

 name: name

 };

 $scope.updateName = updateName;

 ////////////

 function updateName () {

 if (_.isEmpty($scope.data.name)) {

 return;

 }

 $meteor.call('updateName', $scope.data.name)

 .then(function () {

 $state.go('tab.chats');

 })

 .catch(handleError);

 }

 function handleError (err) {

 $log.error('profile save error ', err);

 $ionicPopup.alert({

 title: err.reason || 'Save failed',

 template: 'Please try again',

 okType: 'button-positive button-clear'

 });

 }

}
```

And some CSS:

**4.13** Add CSS for the profile settings view [client/styles/profile.scss »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/114ae6750d4943cc8416e57df3e0476a5b795f41)

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

.profile {

 padding-top: 20px;

 .profile-picture {

 position: absolute;

 top: 0;

 left: 20px;

 text-align: center;

 img {

 display: block;

 max-width: 50px;

 max-height: 50px;

 width: 100%;

 height: 100%;

 border-radius: 50%;

 }

 .upload-placehoder {

 width: 50px;

 height: 50px;

 padding: 5px;

 border: 1px solid #808080;

 border-radius: 50%;

 line-height: 18px;

 font-size: 12px;

 }

 }

 .instructions {

 min-height: 60px;

 padding: 10px 20px 20px 90px;

 font-size: 14px;

 color: gray;

 }

 .profile-name {

 margin-top: 20px;

 }

}
```

As you can see, the controller uses a server method - `updateName` which we need to implement in the `lib/methods.js`:

**4.14** Add updateName method to the server [lib/methods.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/267bb76c7e73a8b95d6725b104ea14facc7bb689)

```
11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

 Chats.update(message.chatId, { $set: { lastMessage: message } });

 return messageId;

 },

 updateName: function (name) {

 if (! this.userId) {

 throw new Meteor.Error('not-logged-in',

 'Must be logged in to update his name.');

 }

 check(name, String);

 if (name.length === 0) {

 throw Meteor.Error('name-required', 'Must proive user name');

 }

 return Meteor.users.update(this.userId, { $set: { 'profile.name': name } });

 }

});
```

Meteor sets the user identity in case of a logged in user into the `this.userId` variable, so we can check if this variable exists in order to verify that the user is logged in.

Now let's add this validation to the `newMessage` we created earlier, and also add the identity of the user to each message he sends.

**4.15** Verify and add userId for each message that sent [lib/methods.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/5476c7080840a0325566fa029c3f26d8459d1e38)

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

Meteor.methods({

 newMessage: function (message) {

 if (! this.userId) {

 throw new Meteor.Error('not-logged-in',

 'Must be logged in to send message.');

 }

 check(message, {

 text: String,

 chatId: String

 });

 message.timestamp = new Date();

 message.userId = this.userId;

 var messageId = Messages.insert(message);

 Chats.update(message.chatId, { $set: { lastMessage: message } });
```

Great, now the last missing feature is logout - let's add a state for the settings view:

**4.16** Add settings route definition [client/scripts/routes.ng.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/4ce70e86e79049761c806e0bdce1c5aa9132f24b)

```
51

52

53

54

55

56

57

58

59

60

61

62

63

64

65

 return $meteor.requireUser();

 }]

 }

 })

 .state('tab.settings', {

 url: '/settings',

 views: {

 'tab-settings': {

 templateUrl: 'client/templates/settings.ng.html',

 controller: 'SettingsCtrl'

 }

 }

 });

 $urlRouterProvider.otherwise('tab/chats');
```

And create the view - it only contains the logout button which calls a $scope method:

```
<ion-view view-title="Settings">

 <ion-content>

 <div class="padding text-center">

 <button ng-click="logout()" class="button button-clear button-assertive">Logout</button>

 </div>

 </ion-content>

</ion-view>
```

And let's implement this method inside the `SettingsCtrl`:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

angular

 .module('Whatsapp')

 .controller('SettingsCtrl', SettingsCtrl);

function SettingsCtrl($scope, $meteor, $state) {

 $scope.logout = logout;

 ////////////

 function logout() {

 $meteor.logout().then(function () {

 $state.go('login');

 });

 }

}
```

And this is our settings view:

![](http://cdn2.hubspot.net/hubfs/520701/14_-_Logout_screen.png?t=1445014944449)

We also need to modify the way we identify our users inside the messages list, so let's do it:

```
 <ion-content class="chat" delegate-handle="chatScroll">

 <div class="message-list">

 <div ng-repeat="message in messages" class="message-wrapper">

 <div class="message" ng-class-even="'message-mine'" ng-class-odd="'message-other'">

 <div class="message-text"></div>

 <span class="message-timestamp"></span>

 <div class="message" ng-class="message.userId === $root.currentUser._id ? 'message-mine' : 'message-other'">

 <div class="message-text"></div>

 <span class="message-timestamp"></span>

 </div>

 </div>

 </div>
```

And the last missing feature is about adding auto-scroll to the messages list in order to keep the view scrolled down when new message arrives!

```
11

12

13

14

15

16

17

18

19

20

21

 return Messages.find({ chatId: chatId });

 }, false);

 $scope.$watchCollection('messages', function (oldVal, newVal) {

 var animate = oldVal.length !== newVal.length;

 $ionicScrollDelegate.$getByHandle('chatScroll').scrollBottom(animate);

 });

 $scope.data = {};

 $scope.sendMessage = sendMessage;

 $scope.inputUp = inputUp;
```

You can download a ZIP file with the project at this point 

[<span>here</span>](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/archive/978d20a9823aa412b0f08299d59dd7a84d7dca17.zip)

.

**<span>Step 6 - Create and remove chats</span>**

Our next step is about adding the ability to create new chats - so far we have the chats list and the users feature - we just need to connect them.

We will open the new chat view using Ionic's modal dialog, so first let's add a button that opens this dialog to the chats list:

```
<ion-view view-title="Chats">

 <ion-nav-buttons side="right">

 <button ng-click="openNewChatModal()" class="button button-clear button-positive button-icon ion-ios-compose-outline"></button>

 </ion-nav-buttons>

 <ion-content>

 <ion-list>

 <ion-item
```

This button calls a $scope function, which we will implement now in the controller:

```
2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

 .module('Whatsapp')

 .controller('ChatsCtrl', ChatsCtrl);

function ChatsCtrl ($scope) {

function ChatsCtrl ($scope, $ionicModal) {

 $scope.chats = $scope.$meteorCollection(Chats, false);

 $ionicModal.fromTemplateUrl('client/templates/new-chat.ng.html', {

 scope: $scope

 }).then(function (modal) {

 $scope.modal = modal;

 });

 $scope.$on('$destroy', function () {

 $scope.modal.remove();

 });

 $scope.openNewChatModal = openNewChatModal;

 $scope.remove = remove;

 ////////////

 function openNewChatModal () {

 $scope.modal.show();

 }

 function remove (chat) {

 $scope.chats.remove(chat);

 }
```

Note that we first create the modal dialog with a template, and then later we open it in the button function.<br>
Now let's add the view of this modal dialog, which is just a list of users:

**5.3** Add the view of the new chat modal [client/templates/new-chat.ng.html »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/232e1715b1a07ae6b9fa9e1b9414c79f2c40d28f)

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

<ion-modal-view ng-controller="NewChatCtrl">

 <ion-header-bar>

 <h1 class="title">New Chat</h1>

 <div class="buttons">

 <button class="button button-clear button-positive" ng-click="hideModal()">Cancel</button>

 </div>

 </ion-header-bar>

 <ion-content>

 <div class="list">

 <a ng-repeat="user in users" ng-click="newChat(user._id)" class="item">

 <h2></h2>

 <p>

 Hey there! I am using meteor-Whatsapp with meteor.

 </p>

 </a>

 </div>

 </ion-content>

</ion-modal-view>
```

And now we will add the controller of this view:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

angular

 .module('Whatsapp')

 .controller('NewChatCtrl', NewChatCtrl);

function NewChatCtrl($scope, $state, $meteor) {

 $scope.users = $scope.$meteorCollection(function () {

 return Meteor.users.find({_id: {$ne: Meteor.userId()}});

 }, false);

 $scope.hideModal = hideModal;

 $scope.newChat = newChat;

 ////////////

 function hideModal() {

 $scope.modal.hide();

 }

 function newChat(userId) {

 var chat = Chats.findOne({type: 'chat', userIds: {$all: [Meteor.userId(), userId]}});

 if (chat) {

 return goToChat(chat._id);

 }

 $meteor.call('newChat', userId).then(goToChat);

 }

 function goToChat(chatId) {

 hideModal();

 return $state.go('tab.chat-detail', {chatId: chatId});

 }

}
```

It includes the Users collection and a function for creating a new chat - this function is not yet implemented in the server, so let's create it in the server:

**5.5** Add server side logic for creating new chat [lib/methods.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/99088da6f15f6e127277e07a5270299b41a14b38)

```
30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

48

49

50

51

52

53

54

55

56

57

 }

 return Meteor.users.update(this.userId, { $set: { 'profile.name': name } });

 }

 },

 newChat: function (otherId) {

 if (! this.us
```
erId) {

```
 throw new Meteor.Error('not-logged-in',

 'Must be logged to create a chat.');

 }

 check(otherId, String);

 var otherUser = Meteor.users.findOne(otherId);

 if (! otherUser) {

 throw new Meteor.Error('user-not-exists',

 'Chat\'s user not exists');

 }

 var chat = {

 userIds: [this.userId, otherId],

 createdAt: new Date()

 };

 var chatId = Chats.insert(chat);

 return chatId;

 },

});
```

We will also change the logic of `removeChat` function in the `ChatsCtrl` - we also call a server method (I will explain the reason for this change soon):

```
 }

 function remove (chat) {

 $scope.chats.remove(chat);

 $meteor.call('removeChat', chat._id);

 }

}
```

And we will implement the method on the server:

**5.7** Add server side logic for removing chats [lib/methods.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/c9c3a132c43c1ce6372b5a231c0c8fc4ad752b0e)

```
54

55

56

57

58

59

60

61

62

63

64

65

66

67

68

69

70

71

72

73

74

75

 return chatId;

 },

 removeChat: function (chatId) {

 if (! this.userId) {

 throw new Meteor.Error('not-logged-in',

 'Must be logged to create a chat.');

 }

 check(chatId, String);

 var chat = Chats.findOne(chatId);

 if (! chat || ! _.include(chat.userIds, this.userId)) {

 throw new Meteor.Error('chat-not-exists',

 'Chat not exists');

 }

 Messages.remove({ chatId: chatId });

 return Chats.remove({ _id: chatId });

 }

});
```

The next messages won't include the username, only the user id, so we need to change the logic of username display - we will add a filter that fetches the user object from the Users collection according to the `userId` property of the chat object:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

angular

 .module('Whatsapp')

 .filter('chatName', chatName);

function chatName() {

 return function (chat) {

 if (!chat) return;

 var otherId = _.without(chat.userIds, Meteor.userId())[0];

 var otherUser = Meteor.users.findOne(otherId);

 var hasName = otherUser && otherUser.profile && otherUser.profile.name;

 return hasName ? otherUser.profile.name : chat.name || 'NO NAME';

 }

}
```

And we will also create the same logic for fetching the user's image:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

angular

 .module('Whatsapp')

 .filter('chatPicture', chatPicture);

function chatPicture () {

 return function (chat) {

 if (! chat) return;

 var otherId = _.without(chat.userIds, Meteor.userId())[0];

 var otherUser = Meteor.users.findOne(otherId);

 var hasPicture = otherUser && otherUser.profile && otherUser.profile.picture;

 return hasPicture ? otherUser.profile.picture : chat.picture || '/user-default.svg';

 }

}
```

And we will add the usage of this filter in the chats list:

**5.10** Add filters usage in the chats list [client/templates/chats.ng.html »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/f459fde81a91cefd867cab18243ef68b1fc0fc14)

```
 class="item-chat item-remove-animate item-avatar item-icon-right"

 type="item-text-wrap"

 href="#/tab/chats/">

 <img ng-src="">

 <h2></h2>

 <img ng-src="">

 <h2></h2>

 <p></p>

 <span class="last-message-timestamp"></span>

 <i class="icon ion-chevron-right icon-accessory"></i>
```

And in the chat detail view:

```
<ion-view title="">

<ion-view title="">

 <ion-nav-buttons side="right">

 <button class="button button-clear"><img class="header-picture" ng-src=""></button>

 <button class="button button-clear"><img class="header-picture" ng-src=""></button>

 </ion-nav-buttons>

 <ion-content class="chat" delegate-handle="chatScroll">

 <div class="message-list">
```

Now we want to get rid of the current data we have - which is just a static data.

So let's stop our Meteor's server and reset the whole app by running:

`$ meteor reset`

Let's add some users to the server instead of the old static data:

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

Meteor.startup(function () {

 if (Chats.find().count() === 0) {

 Messages.remove({});

 var messages = [

 {

 text: 'You on your way?',

 timestamp: moment().subtract(1, 'hours').toDate()

 },

 {

 text: 'Hey, it\'s me',

 timestamp: moment().subtract(2, 'hours').toDate()

 },

 {

 text: 'I should buy a boat',

 timestamp: moment().subtract(1, 'days').toDate()

 },

 {

 text: 'Look at my mukluks!',

 timestamp: moment().subtract(4, 'days').toDate()

 },

 {

 text: 'This is wicked good ice cream.',

 timestamp: moment().subtract(2, 'weeks').toDate()

 if (Accounts.users.find().count() === 0) {

 Accounts.createUserWithPhone({

 phone: '+972501234567',

 profile: {

 name: 'My friend 1'

 }

 ];

 messages.forEach(m => {

 Messages.insert(m);

 });

 var chats = [

 {

 name: 'Ethan Gonzalez',

 picture: 'https://randomuser.me/api/portraits/thumb/men/1.jpg'

 },

 {

 name: 'Bryan Wallace',

 picture: 'https://randomuser.me/api/portraits/thumb/lego/1.jpg'

 },

 {

 name: 'Avery Stewart',

 picture: 'https://randomuser.me/api/portraits/thumb/women/1.jpg'

 },

 {

 name: 'Katie Peterson',

 picture: 'https://randomuser.me/api/portraits/thumb/women/2.jpg'

 },

 {

 name: 'Ray Edwards',

 picture: 'https://randomuser.me/api/portraits/thumb/men/2.jpg'

 Accounts.createUserWithPhone({

 phone: '+972501234568',

 profile: {

 name: 'My friend 2'

 }

 ];

 });

 chats.forEach(chat => {

 let message = Messages.findOne({chatId: {$exists: false}});

 chat.lastMessage = message;

 let chatId = Chats.insert(chat);

 Messages.update(message._id, {$set: {chatId: chatId}})

 Accounts.createUserWithPhone({

 phone: '+972501234569',

 profile: {

 name: 'My friend 3'

 }

 });

 }

});
```

Run it again, this is the result of the new Modal we created:

![](http://cdn2.hubspot.net/hubfs/520701/15_-_New_chat_screen.png?t=1445014944449)

Cool ! and now clicking a user will open a chat with that user!<br>
Our last part of this step is to remove Meteor's package named `insecure`.

This package provides the ability to run `remove` method from the client side in our collection - this is behavior we do not want to use because removing data and creating data should be done in the server side and only after certain validations - and this is the reason for implementing the `removeChat` method in the server.

Meteor includes this package only for prototyping purpose the it should be removed when our app is ready!

So removing this package is only running this command:

`$ meteor remove insecure`

You can download a ZIP file with the project at this point 

[<span>here</span>](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/archive/59f69393dca4b25e11cefdaaca7dd6e652c62923.zip)

.

**<span>Step 7 - Privacy and Publish/Subscribe</span>**

Right now all the chats are published to all the clients. that not very private - let's fix that.

First thing we need to do to stop all the automatic publication of information is to remove the `autopublish` package from the Meteor server. in the Meteor command line:

`$ meteor remove autopublish`

We will add now the [publish-composite](https://atmospherejs.com/reywood/publish-composite) package, which we will use later.

`$ meteor add reywood:publish-composite`

Now we need to explicitly define our publications - let's start with sending the Users information.

Create a file named `publications.js` under the `server` folder and define the query we want to send to our clients inside:

**6.3** Add publication to the users [server/publications.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/32d753750e12e2660bbf8908d756eecbe86f0a5d)

```
Meteor.publish('users', function () {

 return Meteor.users.find({}, { fields: { profile: 1 } });

});
```

And of course we need to modify some of the client side code, we need to make sure that the client side subscribed to the Users data, so let's add in the new chat page controller, because this view needs the users' data:

```
3

4

5

6

7

8

9

10

11

12

13

 .controller('NewChatCtrl', NewChatCtrl);

function NewChatCtrl($scope, $state, $meteor) {

 $scope.users = $scope.$meteorCollection(function () {

 return Meteor.users.find({_id: {$ne: Meteor.userId()}});

 }, false);

 $scope.$meteorSubscribe('users').then(function () {

 $scope.users = $scope.$meteorCollection(function () {

 return Meteor.users.find({ _id: { $ne: Meteor.userId() } });

 }, false);

 });

 $scope.hideModal = hideModal;

 $scope.newChat = newChat;
```

Now let's do a more complex publication, let's send each client only the Chats and Messages he is a part of:<br>
`Meteor.publish('chats', function () {`

`if (! this.userId) {`

`return;`

`return Chats.find({ userIds: this.userId });`

`});`

and now, let's add the Messages from the those chats into the publication.

To do that, we need to do a joined collections publication.

To do it more easily, let's use the [reywood:publish-composite](https://atmospherejs.com/reywood/publish-composite) package we added previously.

And now let's change the publication to add the Messages and the Users that are related to the Chats the users in participating in:

**6.5** Add publication to chats and messages data [server/publications.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/f1225326e5a6df3f75baeb2de4fbb2e334b2cf9b)

```
1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

Meteor.publish('users', function () {

 return Meteor.users.find({}, { fields: { profile: 1 } });

});

Meteor.publishComposite('chats', function () {

 if (! this.userId) {

 return;

 }

 return {

 find: function () {

 return Chats.find({ userIds: this.userId });

 },

 children: [

 {

 find: function (chat) {

 return Messages.find({ chatId: chat._id });

 }

 },

 {

 find: function (chat) {

 var query = { _id: { $in: chat.userIds } };

 var options = { fields: { profile: 1 } };

 return Meteor.users.find(query, options);

 }

 }

 ]

 }

});
```

And we will add the subscription to the `chats` data in the client side:

```
11

12

13

14

15

16

17

18

19

 resolve: {

 user: ['$meteor', function ($meteor) {

 return $meteor.requireUser();

 }],

 chats: ['$meteor', function ($meteor) {

 return $meteor.subscribe('chats');

 }]

 }

 })
```

Notice that the `$meteorSubscribe` function resolve a promise when the data arrives so you know when you can use it.

You can download a ZIP file with the project at this point 

[<span>here</span>](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/archive/91b36046acce0f4fdc11a3f39cbe5ad79bfab4f7.zip)

.

**<span>Step 8 - User profile picture</span>**

So now we will add an ability to add a user profile image - using the device's camera (mobile phone or laptop camera).

The first part is to add the Meteor package that provide us this ability:

`$ meteor add okland:camera-ui`

We will add now a server method for updating the user's profile image, which is just like updating any other string field of the user's profile:

**7.2** Add updatePicture method to the server [lib/methods.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/1fed673bffba9f6b5fa03490fe6b667aa8d13563)

```
71

72

73

74

75

76

77

78

79

80

81

82

83

84

85

 Messages.remove({ chatId: chatId });

 return Chats.remove({ _id: chatId });

 },

 updatePicture: function (data) {

 if (! this.userId) {

 throw new Meteor.Error('not-logged-in',

 'Must be logged in to update his picture.');

 }

 check(data, String);

 return Meteor.users.update(this.userId, { $set: { 'profile.picture': data } });

 }

});
```

The next step is to add the button for adding/editing the user's profile image, we will add it in the `profile` state, so update the view first:

```
4

5

6

7

8

9

10

11

12

13

14

15

 </ion-nav-buttons>

 <ion-content class="profile">

 <a class="profile-picture positive">

 <div class="upload-placehoder">

 <a class="profile-picture positive" ng-click="updatePicture()">

 <div ng-if="currentUser.profile.picture">

 <img ng-src="" alt="profile picture">

 edit

 </div>

 <div ng-if="!currentUser.profile.picture" class="upload-placehoder">

 Add photo

 </div>

 </a>
```

And now we will implement the controller methods, which users Camera-UI API for getting the image from the device, and then we will use that image and run the server method for updating the image:

```
2

3

4

5

6

7

8

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

 .module('Whatsapp')

 .controller('ProfileCtrl', ProfileCtrl);

function ProfileCtrl ($scope, $state, $meteor, $ionicPopup, $log) {

function ProfileCtrl ($scope, $state, $meteor, $ionicPopup, $log, $ionicLoading) {

 var user = Meteor.user();

 var name = user && user.profile ? user.profile.name : '';

...some lines skipped...

 };

 $scope.updateName = updateName;

 $scope.updatePicture = updatePicture;

 ////////////

 function updatePicture () {

 MeteorCameraUI.getPicture({ width: 60, height: 60 }, function (err, data) {

 if (err && err.error == 'cancel') {

 return;

 }

 if (err) {

 return handleError(err);

 }

 $ionicLoading.show({

 template: 'Updating picture...'

 });

 $meteor.call('updatePicture', data)

 .finally(function () {

 $ionicLoading.hide();

 })

 .catch(handleError);

 });

 }

 function updateName () {

 if (_.isEmpty($scope.data.name)) {

 return;
```

We will add now some CSS for better layout of the profile page:

**7.5** Add some CSS style to the profile page [client/styles/profile.scss »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/ea1a45fbbb24823442799959a09c7a3272b60f49)

```
 display: block;

 max-width: 50px;

 max-height: 50px;

 min-width: 50px;

 min-height: 50px;

 width: 100%;

 height: 100%;

 border-radius: 50%;
```

And this is an example for taking an image from the browser:

![](http://cdn2.hubspot.net/hubfs/520701/16-_Take_photo_screen.png?t=1445014944449)

Now to ease the access to the profile page, we will add a link in the Settings view:

```
3

4

5

6

7

8

9

10

11

12

13

 <div class="padding text-center">

 <button ng-click="logout()" class="button button-clear button-assertive">Logout</button>

 </div>

 <ion-list>

 <ion-item href="#/profile" class="item-icon-right">

 Profile

 <i class="icon ion-chevron-right icon-accessory"></i>

 </ion-item>

 </ion-list>

 </ion-content>

</ion-view>
```

You can download a ZIP file with the project at this point 

[<span>here</span>](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/archive/2c97fa41b5c2f106099f9ec988a0d4129c3a4fd7.zip)

.

**<span>Step 9 - Send image messages</span>**

Our last step is adding ability to send image message in the chat. We will use the same package from the previous step!

So we will use the same logic of taking the picture in the controller, and call the same `newMessage` server method:

```
2

3

4

5

6

7

8

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

48

49

50

51

52

53

54

55

56

57

 .module('Whatsapp')

 .controller('ChatDetailCtrl', ChatDetailCtrl);

function ChatDetailCtrl ($scope, $stateParams, $ionicScrollDelegate, $timeout, $meteor) {

function ChatDetailCtrl ($scope, $stateParams, $ionicScrollDelegate, $timeout, $meteor, $ionicPopup, $log) {

 var chatId = $stateParams.chatId;

 var isIOS = ionic.Platform.isWebView() && ionic.Platform.isIOS();

 $scope.chat = $scope.$meteorObject(Chats, chatId, false);

...some lines skipped...

 $scope.inputUp = inputUp;

 $scope.inputDown = inputDown;

 $scope.closeKeyboard = closeKeyboard;

 $scope.sendPicture = sendPicture;

 ///

 function sendPicture () {

 MeteorCameraUI.getPicture({}, function (err, data) {

 if (err && err.error == 'cancel') {

 return;

 }

 if (err) {

 return handleError(err);

 }

 $meteor.call('newMessage', {

 picture: data,

 type: 'picture',

 chatId: chatId

 });

 });

 }

 function handleError (err) {

 $log.error('profile save error ', err);

 $ionicPopup.alert({

 title: err.reason || 'Save failed',

 template: 'Please try again',

 okType: 'button-positive button-clear'

 });

 }

 function sendMessage () {

 if (_.isEmpty($scope.data.message)) {

 return;
```

And now we need to add the `ng-click` to the image button on the view:

```
 <button ng-click="sendMessage()" class="button button-clear button-positive">Send</button>

 </span>

 <span ng-if="!data.message || data.message.length === 0">

 <button class="button button-clear button-icon button-positive icon ion-ios-camera-outline"></button>

 <button ng-click="sendPicture()" class="button button-clear button-icon button-positive icon ion-ios-camera-outline"></button>

 <i class="buttons-seperator icon ion-android-more-vertical"></i>

 <button class="button button-clear button-icon button-positive icon ion-ios-mic-outline"></button>

 </span>
```

In the server, we need to add support for sending image messages - it's just another validation scheme for the `newMessage` method:

**8.3** Add validation for image messages in the sendMessage method [lib/methods.js »](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/commit/6f275f7b91da109bd2718e83acbfa1c4a6b48afe)

```
5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

 'Must be logged in to send message.');

 }

 check(message, {

 text: String,

 chatId: String

 });

 check(message, Match.OneOf(

 {

 text: String,

 type: String,

 chatId: String

 },

 {

 picture: String,

 type: String,

 chatId: String

 }

 ));

 message.timestamp = new Date();

 message.userId = this.userId;
```

Our next step is to add the view of the image messages in the chat detail view:

```
6

7

8

9

10

11

12

13

14

15

16

17

 <div class="message-list">

 <div ng-repeat="message in messages" class="message-wrapper">

 <div class="message" ng-class="message.userId === $root.currentUser._id ? 'message-mine' : 'message-other'">

 <div class="message-text"></div>

 <ng-switch on="message.type">

 <div ng-switch-when="text" class="text"></div>

 <div ng-switch-when="picture" class="picture">

 <img ng-src="">

 </div>

 </ng-switch>

 <span class="message-timestamp"></span>

 </div>

 </div>
```

And some CSS to limit the images size:

```
66

67

68

69

70

71

72

76

77

78

79

80

81

82

83

84

85

86

87

88

89

90

91

 background-image: url(/message-mine.png)

 }

 .message-text {

 .text {

 padding: 5px 7px;

 word-wrap: break-word;

...some lines skipped...

 }

 }

 .picture {

 padding: 4px 4px 0;

 img {

 width: 220px;

 height: 130px;

 border-radius: 6px;

 }

 }

 .message-timestamp {

 position: absolute;

 bottom: 2px;
```

We also want to add image icon on the chats list in case of the last message is an image message, so let's add it:

```
12

13

14

15

16

17

18

19

20

21

 href="#/tab/chats/">

 <img ng-src="">

 <h2></h2>

 <p></p>

 <ng-switch on="chat.lastMessage.type">

 <p ng-switch-when="text"></p>

 <p ng-switch-when="picture">image</p>

 </ng-switch>

 <span class="last-message-timestamp"></span>

 <i class="icon ion-chevron-right icon-accessory"></i>

 <ion-option-button class="button-assertive" ng-click="remove(chat)">
```

And this is the result:

![](http://cdn2.hubspot.net/hubfs/520701/17_-_Photo_in_chat_message.png?t=1445014944449)

You can download a ZIP file with the project at this point 

[<span>here</span>](https://github.com/dotansimha/meteor-whatsapp-clone-step-by-step/archive/8b3e83fcb112bca6f1f90caf822a8d8938f39c30.zip)

.

**<span>Summary</span>**

Congratulations! You've created a WhatsApp app!

Thanks a lot to [Idan Wender](https://github.com/idanwe/) and [Dotan Simha](https://github.com/dotansimha) who helped me a lot with this post.

You are welcome to add more features and extend this tutorial by pull requesting 

[<span>this repository</span>](https://github.com/idanwe/meteor-whatsapp)

.

Next steps:
