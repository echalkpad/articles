# How to Develop iOS Apps on Linux using React Native

[Original URL](http://www.proreactnative.com/How-to-Develop-iOS-Apps-on-Linux-Using-React-Native/)

> Hey there, Linux user! Are you trying to develop iOS apps, but don't want to spring for a Mac? Or, perhaps you have plenty of cash, but using a closed-source operating system brings you pain? I know...

Hey there, Linux user!

Are you trying to develop iOS apps, but don't want to spring for a Mac?

Or, perhaps you have plenty of cash, but using a closed-source operating system brings you pain?

I know what you're going through.

The bad news is that Apple, officially speaking, requires that all iOS apps be built on Mac OS X.

The good news is that there are a few **loopholes** to get around this.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Pics/Steve.jpg) _Would Steve still be smug if he knew we were writing apps on Linux?_

## Requirements

For the sake of this article, I'm going to **presume** that:

I'll also show you how to get up and running using two approaches:

**Solution A:** You have one-time access to a Mac.<br>
**Solution B:** You don't have access to a Mac, at all.

## Solution A: One-time Access to a Mac

The approach that we are going to take is to point your iOS app at your Linux machine so that you can write your Javascript code on the Linux machine and run your code on your iOS device.

In order to get up and running, you'll need a Mac in order to build the native code and deploy the app onto the device for the first time, but once the app is deployed, you'll be able to use Javascript to do anything doesn't involve changing the native code.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Diagram/Overview.png) _Mac for native code, Linux for Javascript_ So, borrow a Mac from your neighbor and follow these steps:

### Getting Started

#### Launch Xcode

First, you'll need to launch Xcode by opening the following file: `PROJECT_ROOT/ios/PROJECT_NAME.xcodeproj`.

You can open it using the Finder, the Terminal, or by using File > Open within Xcode.

_NOTE_: This file should have been generated when you ran `react-native init` to create your project.

Once you open the project, you should be greeted by the following screen:

![](http://www.proreactnative.com/images/2016/02/14-Linux/Xcode/Welcome.png) _Welcome to Xcode._

### Adding a Configurable Server IP Address

#### Add a iOS Settings Entry Using Settings.bundle

Now that we're in Xcode, the next thing we need to do is to add a **Settings screen** to our app.

As described on the [Apple developer documentation](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/UserDefaults/Preferences/Preferences.html), we'll add a `Settings.bundle` file.

The goal is to create an entry in the iOS Settings for our LinuxExample app, like this:

![](http://www.proreactnative.com/images/2016/02/14-Linux/Device/Settings%20-%20Menu.png) _System Settings: Menu_ To add the Settings.bundle file, you need to **right-click** the file list and click **"Add Files to ..."**

![](http://www.proreactnative.com/images/2016/02/14-Linux/Xcode/Add%20Files.png) _Add Files to Project_ Next step: you need to select a file type.

You want **Resources -> Settings.Bundle**.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Xcode/Add%20Files%20-%20Type.png) _Adding File: File Type_ Last step: tell Xcode where to put `Settings.Bundle`.

You want to make sure that this is in `PROJECT_ROOT/ios/PROJECT_NAME/`. In my case, this is `LinuxExample/ios/LinuxExample/`.

Once you've selected the location, click **create**.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Xcode/Add%20Files%20-%20Where.png) _Adding File: Where to Save_ With the file added, you should be able to run the app. Use the **play button** in the top left of Xcode. Or hit **⌘-R**.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Xcode/Play%20Button.png) _Press Play_ If everything worked, you should see your app running!

![](http://www.proreactnative.com/images/2016/02/14-Linux/Device/Welcome%20to%20React%20Native.png) _Hello World_ After running the App, hit the **home button**, go to **Settings** and **scroll all the way to the bottom**. You should see your app! In our case, it's "LinuxExample."

![](http://www.proreactnative.com/images/2016/02/14-Linux/Device/Settings%20-%20Menu.png) _Settings Menu Entry_ Click **YourApp/LinuxExample** and you'll see some default options in here.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Device/Settings%20-%20Example.png) _System Settings: Slider, etc_

#### Edit Settings.bundle

Now that we have our settings screen up and running, it's time to change our `Settings.bundle` so that we can configure our server's **host** and **port**.

First, open **`Root.plist`** inside `Settings.bundle`. You may need to click the arrow next to `Settings.bundle` to reveal `Root.plist`.

You should see four **"preference items"** inside of it. You may need to click the arrow next to "preference items" in order to reveal the preference Item 0, 1, 2, 3.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Xcode/Settings.bundle%20-%20Original.png) _Original Settings.bundle/Root.plist_ Now we want to change `Root.plist` such that we have three preference items:

- Group marker
- Text Field, with name **"host_preference"** and default value of **"localhost"**
- Text Field, with name **"port_preference"** and default value of **"8081"**

See below image for an example:

![](http://www.proreactnative.com/images/2016/02/14-Linux/Xcode/Settings.bundle%20-%20Updated.png) _Updated Settings.bundle/Root.plist_ Once you've made these changes, if you run the app again, you should be able to see Host and Port in the settings screen instead of the slider, etc.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Device/Settings%20-%20Host%20Port%20-%20Localhost.png) _System Settings: Slider, etc_ _NOTE_: you may need to uninstall and reinstall the app after changing the Settings.bundle file.

#### Edit AppDelegate.m

The next step is to actually make use of the host and port that we are setting.

So jump into `AppDelegate.m` and find the line where you set the `jsCodeLocation`.

By default, it's on line 34 and it should look like this:

```
 jsCodeLocation = [NSURL URLWithString: @"http://localhost:8081/index.ios.bundle?platform=ios&dev=true"];
```

You want to go in there and change it to the following:

```
 NSString *host = [[NSUserDefaults standardUserDefaults] stringForKey: @"host_preference"];
 NSString *port = [[NSUserDefaults standardUserDefaults] stringForKey: @"port_preference"];

 NSString * urlString = [NSString stringWithFormat: @"http://%@:%@/index.ios.bundle?platform=ios&dev=true", host, port];
 jsCodeLocation = [NSURL URLWithString: urlString];
```

This code will pull the host and port number out of the app's settings and make the server URL from them.

Finally, in order for our app to properly load its settings from iOS, we need to provide some default values for our preferences.

For this, we go to the top of `didFinishLaunchingWithOptions` and add the following code:

```
NSDictionary *appDefaults = @{
 @"host_preference": @"localhost",
 @"port_preference": @"8081",
};
[[NSUserDefaults standardUserDefaults] registerDefaults:appDefaults];
```

And that's it for code! Congratulations!

### iOS Device

You COULD build it for the **iOS simulator**, but the iOS simulator needs to run on Mac OS X. And for the sake of this tutorial, I'm presuming that you won't be able to use a Mac day-to-day.

The solution? Use an **iOS device**.

If you're developing an iOS app, you should have access to an **iPhone**, **iPad**, or **iPod Touch**.

An iPod touch, for example is only $200 USD. If you're a professional developer, you should be able to afford that. (Or get your boss to pay for it!).

#### Building the Code

First, you need to go to the **build target** selector in the top of the screen and select your iOS device.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Xcode/iPod%20Touch.png) _Target the Device_ With your device selected, build and run the code and your app should be on your iPhone!

![](http://www.proreactnative.com/images/2016/02/14-Linux/Xcode/Play%20Button.png) _Press Play_

### Run the packager on Linux

OK, we're finally nearing the finale!

Your app is now configurable and will run on any development server you point it to.

Let's _put the Mac away_ and move back to our **Linux machine**.

First thing's first: open a terminal in your project root and run the following commands:

```
npm install
./node_modules/react-native/packager/packager.sh 
```

You should see the packager start running.

It will look like this:

```
 ┌────────────────────────────────────────────────────────────────────────────┐ 
 │ Running packager on port 8081\. │ 
 │ │ 
 │ Keep this packager running while developing on any JS projects. Feel │ 
 │ free to close this tab and run your own packager instance if you │ 
 │ prefer. │ 
 │ │ 
 │ https://github.com/facebook/react-native │ 
 │ │ 
 └────────────────────────────────────────────────────────────────────────────┘ 
```

Awesome!

Now try restarting the app on your iPhone.

If it's still pointing at your Mac, you should see an error, like this:

![](http://www.proreactnative.com/images/2016/02/14-Linux/Device/Failure%20to%20Connect.png) _Failure to Connect_

### Change the iPhone's Host Settings

OK, now let's fix the problem.

On the iPhone, go into **Settings -> YourApp** and change the **"host"** to the hostname / IP address of your **Linux machine**. For me, it's `donkeyKong.local`.

Change the **port** to whatever the packager is running on, it should be `8081`, but you can look at your **terminal** to be sure.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Device/Settings%20-%20Host%20Port%20-%20DonkeyKong.png) _Changing the Host to the Linux Machine_ With the **host** and **port** set, you should restart the iPhone app.

If you see the following in the terminal, then you know for sure that the packager is working!

```
transforming [========================================] 100% 401/401
[11:33:37 AM] <END> transform (98ms)
[11:33:45 AM] <START> request:/index.ios.bundle?platform=ios&dev=true
[11:33:45 AM] <END> request:/index.ios.bundle?platform=ios&dev=true (22ms)
```

You should also see the "Welcome to React Native" screen.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Device/Welcome%20to%20React%20Native.png) _It WORKS!_

### Write your code

Now that we have the packager running, it's time for the final test: can we actually edit our code from Linux?

Let's go into `index.ios.js` and change this line:

```
<Text style={styles.welcome}>
 Welcome to React Native!
</Text>
```

To:

```
<Text style={styles.welcome}>
 Welcome to React Native on Linux!
</Text>
```

Now, **shake the phone** and select **"Reload"** from the menu that appears.

![](http://www.proreactnative.com/images/2016/02/14-Linux/Device/Shake%20Options.png) _Shake, shake, shake_ If there is a God, you should see the screen update, showing those magic words **"Welcome to React Native on Linux!"**

![](http://www.proreactnative.com/images/2016/02/14-Linux/Device/Welcome%20to%20React%20Native%20on%20Linux.png) _There is a God._

### Mission Accomplished

Congratulations. You have everything you need to get up and running with React Native and iOS on Linux!

Now, go take a walk around the block. You've earned it!

## Open Source Project

I've put a [template project](https://github.com/ProReactNative/react-native-ios-on-linux) up on GitHub, so that you can just clone that to get started.

## Solution B: Zero Access to a Mac

Finally, if you have NO access to a Mac or you have a distributed team, where some developers are iOS developers and other developers are Javascript developers, you can modify your iOS code as described in Solution A, but then use a **continuous integration service** instead of a Mac to build your native iOS code. This way you you can still write all the Javascript code on your Linux machine. If you're REALLY adventurous, there's even a way to write **Objective-C/Swift** from your **Linux machine**.

For a continuous integration service, I recommend [BuddyBuild](http://buddybuild.com).

You can easily log in with **GitHub/Bitbucket** and link them to your **code repo**.

Once it's set up, you can have the builds sent **directly to your phone**. You can even set it to build on **every push**!

If you do this, you can collaborate with a distributed team very easily! The native developers can push their native code to Buddybuild and Buddybuild will then send you an updated build of the native code. You can then write your Javascript against that native code.

I think this workflow is quite good. Give it a shot and see what you think.

Also, if you're REALLY adventurous, then you could even **edit the Objective-C code on your Linux machine** and push it to Buddybuild, let them compile it, then send it back to your phone. **Very cool**, but _not for the faint of heart_.

In closing, using Buddybuild as your **compiler** is clunkier than if you had a Mac, yes, but it's pretty damn _seamless_ when you consider than Apple doesn't want you to be working on a Linux machine in the _first place_.

Rock on, Linux users.

_NOTE_: This section is shorter than I intended because I ran short on time. Do you want to see this in more detail? Let me know in the comments and I can expand this into a full guide.

## Thanks

Thanks to Marco Argentieri, a developer from Italy who was the [inspiration](https://www.facebook.com/groups/react.native.community/permalink/719448644857383/?comment_id=719517778183803&comment_tracking=%7B%22tn%22%3A%22R2%22%7D) for this article. I ended up answering the question in a slightly different way than he asked it, but I think this solution has more broad use. Chime in if you think otherwise.

And thank YOU for reading all of this!

Was this article helpful? If so, don't miss my next post by joining the mailing list using the box below:
