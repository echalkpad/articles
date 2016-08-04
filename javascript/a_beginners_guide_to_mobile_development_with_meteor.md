# A Beginners Guide to Mobile Development with Meteor

[Original URL](http://www.sitepoint.com/beginners-guide-mobile-development-meteor/)

> Out of the box, the Meteor JavaScript framework includes Cordova, "a set of device APIs that allow a mobile app developer to access native device functions such as the camera or accelerometer...

Out of the box, the Meteor JavaScript framework includes [Cordova](http://cordova.apache.org),

> "a set of device APIs that allow a mobile app developer to access native device functions such as the camera or accelerometer from JavaScript".

If you're a web developer who wants to release their work on iOS and Android (while harnessing the features of those platforms), you don't have to learn a new language or entirely new concepts. You just need a basic grasp of Meteor, and then a basic grasp of details specific to mobile development.

Obviously, you'll need to install Meteor on your computer if you're looking to develop with it. If it's not installed, enter this command into the terminal:

```
curl https://install.meteor.com/ | sh
```

You'll need a basic grasp of Meteor, so either check out the "[Learning Resources](https://www.meteor.com/learn)" section of the official website, or [the book I wrote for beginners](http://www.amazon.com/Your-First-Meteor-Application-JavaScript-ebook/dp/B00MA8KVAY/ref=cm_cr_pr_product_top).

To develop for iOS, a copy of Xcode needs to be installed on your system. This can be downloaded for free [from the Mac App Store](https://itunes.apple.com/en/app/xcode/id497799835?mt=12).

## Step #2: Add mobile support to a project.

Cordova is included with Meteor itself but has to be manually added to any particular Meteor project. This avoids bloating every project with code it may not need. You add support for Cordova by adding specific platforms.

For example, to add support for iOS, enter the following into the terminal:

```
meteor add-platform ios
```

Or to add support for Android, enter the following into the terminal:

```
meteor add-platform android
```

When adding support for Android, you'll be prompted to install any relevant software that is not already installed.

## Step #3: Create a mobile configuration file.

Within your project folder, create a `mobile-config.js` file. Inside this file, we're able to set a number of configuration options for the mobile portion of the application, including:

- Meta-data, like the application name and description.
- Preferences, like the default orientation of the application.
- Additional preferences for specific Cordova plugins.

You can see an example of this configuration in [the official Meteor docs](http://docs.meteor.com/#/full/mobileconfigjs):

```
// This section sets up some basic app metadata,
// the entire section is optional.
App.info({
 id: 'com.example.matt.uber',
 name: 'über',
 description: 'Get über power in one button click',
 author: 'Matt Development Group',
 email: 'contact@example.com',
 website: 'http://example.com'
});

// Set up resources such as icons and launch screens.
App.icons({
 'iphone': 'icons/icon-60.png',
 'iphone_2x': 'icons/icon-60@2x.png',
 // ... more screen sizes and platforms ...
});

App.launchScreens({
 'iphone': 'splash/Default~iphone.png',
 'iphone_2x': 'splash/Default@2x~iphone.png',
 // ... more screen sizes and platforms ...
});

// Set PhoneGap/Cordova preferences
App.setPreference('BackgroundColor', '0xff0000ff');
App.setPreference('HideKeyboardFormAccessoryBar', true);

// Pass preferences for a particular PhoneGap/Cordova plugin
App.configurePlugin('com.phonegap.plugins.facebookconnect', {
 APP_ID: '1234567890',
 API_KEY: 'supersecretapikey'
});
```

For a full run-down of the available options, check out the "[The config.xml File](http://cordova.apache.org/docs/en/4.0.0/config_ref_index.md.html#The%20config.xml%20File)" section of the Cordova documentation.

## Step #4: Write Cordova-only Code.

By making mobile applications with Meteor, you can write the majority of the functionality once. Most of the code will work across platforms. But not all code _should_ run across platforms and in the same way we can control whether or not certain code runs on the client or the server with the `isClient` and `isServer` conditionals, there's also a `isCordova` conditional:

```
if(Meteor.isCordova){
 // code goes here
}
```

The above block of code will only run if it's being executed within a Cordova mobile environment. (We won't use this code in this particular tutorial but it won't take long to find a situation where it comes in handy.)

## Step #5: Add mobile packages to your application.

Currently, there are three official Meteor packages that make it easy to add mobile features to your application:

- [Camera](https://github.com/meteor/mobile-packages/blob/master/packages/mdg:camera/README.md), for taking photos on either mobile devices or desktop computers.
- [Geolocation](https://github.com/meteor/mobile-packages/blob/master/packages/mdg:geolocation/README.md), for tracking the user's location with a device's GPS.
- [Reload on Resume](https://github.com/meteor/mobile-packages/blob/master/packages/mdg:reload-on-resume/README.md), which can notify users when an update is available for the application and encourage them to restart to see the changes.

Further, unofficial packages can be found on [atmospherejs.com](https://atmospherejs.com/?q=mobile).

The links above provide documentation on how to use all of these packages and they're simple enough that you can probably understand them without further explanation. As an example, let's play around with the "Camera" package.

What we'll do is create a button that, when clicked, opens the camera on the user's device (Android, iOS, or desktop) and allows them to take a photo. If they take a photo, that data will be returned to the application and we'll be able to do whatever we want with the picture.

First, add the "Camera" package to the project:

```
meteor add mdg:camera
```

Then delete the contents of your project's default HTML file and replace it with the following:

```
<head>
 <title>Camera Example</title>
</head>
<body>
 {{> takePhoto}}
</body>

<template name="takePhoto">
 <p><input type="button" class="capture" value="Take Photo"></p>
</template>
```

Here, we're creating an interface that contains a "Take Photo" button.

Inside the default JavaScript file, delete the current contents and replace it with the following:

```
if(Meteor.isClient){
 Template.takePhoto.events({
 'click .capture': function(){
 console.log("Button clicked.");
 }
 });
}
```

Because of this event we've now created, a message will appear inside the JavaScript Console whenever the button is clicked (or in the case of a smart phone, whenever the button is _tapped_).

Within this event, write the following:

```
MeteorCamera.getPicture();
```

This is the function that's built into the "Camera" package that allows us to tap into the user's hardware to capture a photo. It accepts two parameters:

- Options, such as setting the width and height for the photo.
- A callback function, for retrieving the data of the photo.

Just for the moment, we won't pass through any options:

```
MeteorCamera.getPicture({});
```

But we will pass through a callback function as the second parameter:

```
MeteorCamera.getPicture({}, function(error, data){
 // something goes here
});
```

Because of this callback function, we can now retrieve any errors, along with the data of the captured photo. To see this in action, use a log statement:

```
MeteorCamera.getPicture({}, function(error, data){
 console.log(data);
});
```

Test the application on a webcam-enabled computer and notice that a URL appears in the Console after you've captured a photo. We can use this URL to embed the photo within the interface.

First, store the image data inside a session:

```
MeteorCamera.getPicture({}, function(error, data){
 Session.set('photo', data);
});
```

Then create a `helpers` block for the "takePhoto" template:

```
Template.takePhoto.helpers({
 'photo': function(){
 /// code goes here
 }
});
```

Here, I've created a "photo" helper that we'll embed in our template in a moment, but we'll need to return the value of the "photo" session:

```
Template.takePhoto.helpers({
 'photo': function(){
 return Session.get('photo');
 }
});
```

Then, in the "takePhoto" template, simply reference this helper:

```
<template name="takePhoto">
 <p><input type="button" class="capture" value="Take Photo"></p>
 <p>{{photo}}</p>
</template>
```

Now when we capture a photo, a similar string from before will appear inside the interface, and that string will work perfectly fine within the `src` attribute of an `img` tag:

```
<template name="takePhoto">
 <p><input type="button" class="capture" value="Take Photo"></p>
 <p><img src="{{photo}}"></p>
</template>
```

But of course, we're only executing this code on a computer at the moment, when what we really want is to execute it within a mobile application.

## Step #6: Test your application.

You can run the application within the iOS simulator by enter the following command into the command line:

```
meteor run ios
```

Note that the photo feature won't actually work within the iOS simulator. It'll work on the phone itself, and in the Android simulator, and in a desktop browser, but not in the iOS simulator. (You can, however, use the other mobile packages, so I'd suggest playing around with them.)

To run the application within the Android simulator, use this command:

```
meteor run android
```

If you haven't yet used the `meteor add-platform android` command, you will have so install some additional software, but the terminal will guide you through this process.

## Conclusion

In this tutorial, we've only covered the basics of creating a mobile-friendly application with Meteor, but I hope it's been enough to entice you to dig further. Meteor is a wonderfully fun framework and, while building mobile applications natively might make more sense in many cases, the Cordova integration nevertheless provides an elegant option for people who aren't interested in learning a whole other technology.
